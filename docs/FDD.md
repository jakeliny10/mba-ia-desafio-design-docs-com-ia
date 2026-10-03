# FDD — Sistema de Webhooks de Notificação de Pedidos

Este documento distingue **decisão fechada** na reunião, **proposta derivada** para revisão e **pendência** sem definição. Exemplos são ilustrativos, não dados de produção. IDs remetem ao [Tracker](TRACKER.md). A reunião não informa data completa; a data dos documentos é a de elaboração, não a da decisão.

## Contexto e motivação técnica
A aplicação não dispõe de eventos externos. O caminho crítico é a transação de mudança de status. As decisões arquiteturais estão nos [ADRs](RFC.md#decisões-relacionadas); este documento especifica integração e expõe os detalhes ainda não decididos.

## Objetivos técnicos
**FDD-OBJ-01** — Persistir notificações atomicamente com a transição e executar HTTP somente após commit, isolado da API.

## Escopo e exclusões
**FDD-ESC-01** — Implementar configuração por customer, filtro por status, histórico de entregas, assinatura, retry, DLQ e replay ADMIN; sem email, dashboard ou rate limiting nesta fase.

## Modelagem e responsabilidades
**FDD-MOD-01** — Configuração guarda id UUID, customer_id, url, secret, estado ativo e lista de status; rotação exige referência à secret anterior e expiração. Nomes exatos dos campos novos são proposta para revisão.

A secret é gerada pelo servidor na criação ([09:31] Marcos); algoritmo de geração, comprimento e proteção em repouso não foram especificados e dependem da revisão de Sofia. Não tratar hash irreversível como solução de armazenamento: o emissor precisa da chave para HMAC.

**FDD-MOD-02** — Outbox guarda UUID, vínculo ao endpoint e pedido, snapshot, estado, created_at e controle persistente de tentativas/próxima execução. Estados lógicos: pendente, processando, entregue, falhou. Índices de status e created_at apoiam polling.

O vínculo ao endpoint é derivado do envio por cadastro e do header X-Webhook-Id. Proposta: uma linha por evento/destino para controlar retries independentes; decidir em revisão se event_id é compartilhado entre destinos da mesma transição ou único por entrega. Em ambos os casos, manter o mesmo ID em retry. Campo de próxima execução é derivação do backoff, não schema aprovado na reunião.

**FDD-MOD-03** — DLQ separada preserva payload, motivo e timestamp; histórico de entregas precisa preservar sucesso/falha, payload, resposta e duração para consulta. Estrutura física do histórico fica para revisão de modelagem.

O requisito de histórico e últimos 100 envios vem de [09:34] Marcos. A proposta registra uma tentativa por linha, correlacionada por event_id/webhook_id. Limite de resposta armazenada, retenção e tratamento de conteúdo sensível ainda não definidos; não copiar respostas para logs sem revisão.

## Fluxos detalhados
### Criação do evento na outbox
**FDD-FLUXO-01** — Dentro de changeStatus, após validar transição e alterar estoque/status/histórico, chamar publishWebhookEvent(tx, order, fromStatus, toStatus) antes do retorno; usar exclusivamente o tx recebido.

1. Buscar configurações ativas do customer cujo filtro contenha o status de destino, usando a transação atual.
2. Se não houver interesse, terminar sem inserir evento ([09:34] Bruno).
3. Gerar UUID e timestamp da transição; renderizar JSON com estado daquela mudança, sem items.
4. Medir bytes do corpo serializado. O teto é 64KB; não truncar. A unidade exata (64.000 ou 65.536 bytes) e o tratamento do excedente precisam de confirmação. A proposta é rejeitar publicação e deixar a transação reverter, coerente com a atomicidade.
5. Inserir snapshot na outbox pelo tx. Qualquer falha de inserção propaga o erro e reverte estoque, status, histórico e eventos.
6. Após commit, worker poderá ler; não disparar HTTP dentro de publishWebhookEvent.

Não publicar em create automaticamente: a reunião apontou changeStatus, não fechou um evento de criação PENDING. Preservar as regras reais de estoque: débito apenas PENDING→PAID; reposição no cancelamento de PAID/PROCESSING.

### Processamento pelo worker
**FDD-FLUXO-02** — Processo separado consulta pendências antigas em batch pequeno a cada 2s; marca processamento e realiza chamada com timeout de 10s.

Proposta de sequência: selecionar pendentes elegíveis pelo horário de retry; marcar processando em transação curta; liberar transação; enviar bytes do snapshot com headers; registrar resultado/duração e finalizar como entregue ou reagendar. Nunca manter transação SQL aberta durante os 10s de HTTP. Tamanho do batch e implementação do agendamento não foram definidos.

**Ponto de revisão:** sucesso por HTTP 2xx é proposta convencional derivada de sucesso/falha, não decisão expressa. Classificação de 3xx/4xx/5xx, redirects e erros de rede depende de revisão. Não assumir descarte imediato de 4xx nem seguir redirects automaticamente sem contrato aprovado.

### Retry e DLQ
**FDD-FLUXO-03** — Timeout ou falha de entrega leva a retry persistente; usar a sequência declarada 1m/5m/30m/2h/12h e, após esgotamento confirmado, mover para webhook_dead_letter.

A reunião chama isso de exponencial, mas fornece uma sequência fixa; não substituir por fórmula 2^n ou jitter. Contagem é bloqueio localizado do scheduler: cinco tentativas totais permitiriam quatro esperas; cinco retries após envio inicial resultariam em seis chamadas. Cinco esperas somam 14h36m. O código futuro só pode escolher uma interpretação após revisão.

Proposta: gravar falha e próximo horário de execução de modo persistente; aguardar via seleção por horário, sem dormir o processo por horas. Ao esgotar, persistir DLQ e remover/finalizar pendência na mesma transação para evitar evento sem destino. Não alterar status do pedido após falha externa.

### Replay manual e recuperação
**FDD-FLUXO-04** — POST administrativo exige ADMIN, recoloca evento da DLQ como pendente e registra identidade de quem executou para auditoria.

Proposta: preservar snapshot e event_id no replay para deduplicação; confirmar reinício do contador, destino após edição/exclusão de configuração e comportamento de replay repetido. Não gerar novo ID silenciosamente.

Crash após envio e antes da confirmação pode duplicar entrega. Crash após marcar processando pode deixar linha presa: mecanismo de recuperação, timeout de claim e comportamento de shutdown não foram definidos. Revisar com Diego antes de liberar worker para produção; não declarar lease ou lock como decisão já tomada.

Ordering exige discussão adicional: ordenar só por created_at não impede ultrapassagem quando uma entrega aguarda retry, nem resolve empates. Bloquear eventos posteriores do mesmo pedido é proposta possível, mas aumenta atraso. Confirmar política, chave (order_id ou order_id + endpoint), desempate e impacto na latência antes de implementá-la.

## Contratos públicos
**FDD-CONTRATO-BASE** — Usar /api/v1 como prefixo real da aplicação; request e response de configuração seguem camelCase do código, enquanto o callback segue snake_case definido na reunião.

Os caminhos abaixo são relativos a `/api/v1`. Somente `/webhooks/:id/deliveries` e `/admin/webhooks/dead-letter/:id/replay` foram ditos literalmente; os demais paths, envelopes, status HTTP e nomes de campos constituem **proposta derivada para revisão**, com origem funcional indicada. Todas as rotas de gestão usam `Authorization: Bearer <JWT>`; bodies JSON usam `Content-Type: application/json`. GET e DELETE não têm request body.

IDs de exemplo: `customerId = 11111111-1111-4111-8111-111111111111`, `webhookId = 22222222-2222-4222-8222-222222222222`. Nos exemplos, `<id>` representa UUID válido; `<secret-gerada>` é placeholder.

### POST /webhooks

**FDD-CONTRATO-01** — Criar cadastro; customerId explícito, URL HTTPS, filtro e secret gerada pelo servidor.

Request:
```json
{"customerId":"11111111-1111-4111-8111-111111111111","url":"https://cliente.example/hooks/orders","statuses":["SHIPPED","DELIVERED"]}
```

Response `201`:
```json
{"id":"22222222-2222-4222-8222-222222222222","customerId":"11111111-1111-4111-8111-111111111111","url":"https://cliente.example/hooks/orders","statuses":["SHIPPED","DELIVERED"],"active":true,"secret":"<secret-gerada>"}
```

400 validação; 401 JWT; 404 customer ausente. active=true é default proposto, não definido na reunião.

### PATCH /webhooks/:id

**FDD-CONTRATO-02** — Editar URL, filtro e estado ativo; alteração de secret usa operação própria.

Request:
```json
{"statuses":["DELIVERED"],"active":false}
```

Response `200`:
```json
{"id":"22222222-2222-4222-8222-222222222222","url":"https://cliente.example/hooks/orders","statuses":["DELIVERED"],"active":false}
```

400 validação; 401 JWT; 404 cadastro. Semântica de alterações sobre pendências não fechada.

### DELETE /webhooks/:id

**FDD-CONTRATO-03** — Remover configuração de webhook.

Request:
Sem body; id no path.

Response `204`:
Sem corpo.

401 JWT; 404 cadastro. Exclusão física versus lógica e destino de pendências requerem revisão.

### GET /webhooks?customerId=<uuid>&page=1&pageSize=20

**FDD-CONTRATO-04** — Listar webhooks de um customer, sem expor secret na listagem proposta.

Request:
Sem body; filtro e paginação na query.

Response `200`:
```json
{"data":[{"id":"22222222-2222-4222-8222-222222222222","url":"https://cliente.example/hooks/orders","statuses":["SHIPPED"],"active":true}],"pagination":{"page":1,"pageSize":20,"total":1,"totalPages":1}}
```

400 query; 401 JWT. Paginação proposta reutiliza o envelope existente.

### POST /webhooks/:id/rotate-secret

**FDD-CONTRATO-05** — Gerar nova secret e manter anterior válida por 24h.

Request:
```json
{}
```

Response `200`:
```json
{"id":"22222222-2222-4222-8222-222222222222","secret":"<nova-secret>","previousSecretValidUntil":"2026-10-04T12:00:00.000Z"}
```

401 JWT; 404 cadastro. Rotação simultânea/repetida e estratégia de assinatura precisam de aprovação.

### GET /webhooks/:id/deliveries?page=1&pageSize=100

**FDD-CONTRATO-06** — Consultar últimos envios, resultado, payload, resposta e tempo de resposta.

Request:
Sem body; paginação na query.

Response `200`:
```json
{"data":[{"eventId":"33333333-3333-4333-8333-333333333333","success":true,"payload":{"event_type":"order.status_changed","to_status":"SHIPPED"},"response":{"statusCode":200,"body":"ok"},"responseTimeMs":120}],"pagination":{"page":1,"pageSize":100,"total":1,"totalPages":1}}
```

401 JWT; 404 cadastro; 400 query. Payload abreviado neste exemplo; callback completo abaixo. Ordem decrescente por tentativa é proposta para últimos envios.

### POST /admin/webhooks/dead-letter/:id/replay

**FDD-CONTRATO-07** — Recolocar item da DLQ como pendente; ADMIN obrigatório e auditoria.

Request:
```json
{}
```

Response `202`:
```json
{"eventId":"33333333-3333-4333-8333-333333333333","status":"pending"}
```

401 sem JWT; 403 OPERATOR; 404 DLQ ausente. 202 indica reenfileiramento, não entrega ao cliente.

### Callback outbound
**FDD-CALLBACK-01** — Enviar JSON com event_id, event_type, timestamp ISO 8601, order_id, order_number, from_status, to_status, customer_id e total_cents; não enviar items.

```json
{"event_id":"33333333-3333-4333-8333-333333333333","event_type":"order.status_changed","timestamp":"2026-10-03T12:00:00.000Z","order_id":"44444444-4444-4444-8444-444444444444","order_number":"ORD-000001","from_status":"PROCESSING","to_status":"SHIPPED","customer_id":"11111111-1111-4111-8111-111111111111","total_cents":12990}
```

Request: POST na URL cadastrada, com o corpo acima. Response ilustrativa do consumidor: `200`, corpo `ok`; consumo desse body serve ao histórico, não à alteração do pedido.

**FDD-CALLBACK-02** — Headers: Content-Type application/json, X-Event-Id UUID, X-Signature HMAC, X-Timestamp do envio e X-Webhook-Id do cadastro.

X-Webhook-Id foi adicionado em [09:44] Sofia e confirmado em [09:45] Diego. Proposta: HMAC sobre exatamente os bytes UTF-8 transmitidos, sem reserializar entre cálculo e envio. Encoding da assinatura (hex/base64), prefixo e exemplos verificáveis precisam de confirmação. X-Timestamp não está coberto pelo HMAC sobre apenas o corpo; não afirmar que esse header sozinho impede replay. Janela de validação pelo consumidor não foi definida. A sobreposição de secrets exige contrato que permita verificar mensagens durante a migração; não assumir dois headers ou duas assinaturas sem decisão.

## Matriz de erros previstos
**FDD-ERR-01** — Erros específicos do domínio usam AppError e prefixo WEBHOOK_; códigos adicionais abaixo operacionalizam falhas discutidas, sem renomear erros compartilhados de autenticação/Zod/Prisma.

| Código proposto | Condição | HTTP da API / ação do worker |
|---|---|---|
| WEBHOOK_NOT_FOUND | Cadastro ausente | 404 |
| WEBHOOK_INVALID_URL | URL não HTTPS ou inválida | 400; mapear validação de URL no adaptador do módulo |
| WEBHOOK_SECRET_REQUIRED | Secret necessária à assinatura ausente | Erro interno do worker; preservar diagnóstico; tratamento terminal a revisar |
| WEBHOOK_PAYLOAD_TOO_LARGE | Corpo supera 64KB | Proposta 422 na publicação; rollback da transição; não truncar |
| WEBHOOK_DELIVERY_TIMEOUT | Destino não respondeu em 10s | Registrar falha e reagendar |
| WEBHOOK_DELIVERY_FAILED | Falha HTTP/rede | Registrar falha; classificação de status pendente |
| WEBHOOK_DEAD_LETTER_NOT_FOUND | Item de replay inexistente | 404 |

Envelope ilustrativo: `{"error":{"code":"WEBHOOK_NOT_FOUND","message":"Webhook not found"}}`. Apenas WEBHOOK_NOT_FOUND, WEBHOOK_INVALID_URL e WEBHOOK_SECRET_REQUIRED foram exemplificados nominalmente em [09:28] Bruno; os demais são nomes propostos. Auth continua UNAUTHORIZED/FORBIDDEN; validação genérica continua VALIDATION_ERROR. Não afirmar que o middleware existente converte todos os códigos para WEBHOOK_.

## Estratégias de resiliência
**FDD-RES-01** — Timeout de 10s por envio; retries persistentes; DLQ e replay manual como recuperação. Email não é fallback desta fase.

Preservar snapshot e ID nas retentativas. Indisponibilidade do receptor não reverte pedido já confirmado. Falha do banco durante publicação reverte pedido; falha do banco ao registrar entrega pode levar a duplicação. Persistência de retry e recuperação de processing são itens para revisão, não garantias implementadas.

## Observabilidade
**FDD-OBS-01** — Usar Pino para logs estruturados do ciclo de entrega e auditoria de replay; correlacionar event_id, webhook_id, order_id e usuário do replay.

Métricas propostas derivadas dos requisitos: tempo entre evento e primeiro envio (meta de produto <10s em cenário saudável), duração de HTTP (timeout 10s), número de falhas/tentativas, pendências e DLQ. Nomes, exporter, percentil de SLO, alertas e backend não foram decididos; não introduzir stack de métricas como dependência aprovada.

Tracing: a reunião não escolheu biblioteca nem propagação de trace. Proposta de correlação por event_id entre publicação, worker, tentativa e replay com Pino; **isso não equivale a tracing distribuído implementado**. Instrumentação distribuída fica pendente de revisão. O logger atual não redige secret nem X-Signature: evitar incluí-los nos objetos de log e revisar proteção com Sofia.

## Integração com o sistema existente

**FDD-INT-01** — `src/modules/orders/order.service.ts`: Estender changeStatus antes do retorno da transação com publishWebhookEvent(tx, refreshed, from, to); preservar validação, estoque e auditoria.

**FDD-INT-02** — `src/modules/orders/order.status.ts`: Reutilizar máquina de estados e regras de débito/reposição; filtro recebe enum válido, sem criar transições.

**FDD-INT-03** — `prisma/schema.prisma`: Planejar modelos de configuração/outbox/DLQ/histórico e relações; manter UUID Char(36), MySQL e migrações futuras. Nenhuma migration é entregue aqui.

**FDD-INT-04** — `src/config/database.ts`: Worker usa cliente próprio por processo no mesmo DATABASE_URL. O singleton carregado em outro processo já é outra instância; evitar criar dois pools acidentalmente.

**FDD-INT-05** — `src/app.ts`: Construir dependências do módulo via buildControllers e montar rotas sob /api/v1; não iniciar worker em buildApp.

**FDD-INT-06** — `src/routes/index.ts`: Adicionar futuramente routers de webhooks e replay administrativo ao agregador.

**FDD-INT-07** — `src/middlewares/auth.middleware.ts`: Reusar authenticate e requireRole(ADMIN) no replay. AuthUser não possui customerId; receber customer explicitamente.

**FDD-INT-08** — `src/shared/errors/app-error.ts`: Erros de domínio estendem AppError com código/status/details; não usar NotFoundError genérico esperando prefixo WEBHOOK_.

**FDD-INT-09** — `src/middlewares/validate.middleware.ts`: Zod é convertido para VALIDATION_ERROR; mapear WEBHOOK_INVALID_URL no módulo se esse contrato for aprovado.

**FDD-INT-10** — `src/middlewares/error.middleware.ts`: Reusar envelope AppError e tratamento genérico; falhas do worker precisam de catch/log próprio, pois não são requisições Express.

**FDD-INT-11** — `src/shared/logger/index.ts`: Reusar Pino; redaction atual não cobre secrets de webhook. Evitar logging dessas credenciais.

**FDD-INT-12** — `src/shared/http/response.ts`: Reusar paginated na proposta de listagem/histórico.

**FDD-INT-13** — `src/server.ts`: Referência para nova entry point proposta src/worker.ts; novo arquivo ainda não existe.

**FDD-INT-14** — `tests/orders.test.ts`: Referência para futura validação da transação e regressão dos pedidos; não modificar testes nesta entrega.

**FDD-INT-15** — `package.json`: Preservar Node >=20, TypeScript ESM e dependências atuais; npm run worker é script futuro, ainda inexistente.

Arquivos novos **propostos**, não referências existentes: `src/worker.ts` e `src/modules/webhooks/` com controller, service, repository, routes, schemas e processor ([09:27–09:28] Bruno). O código não implementa vínculo usuário-customer; CRUD por qualquer role autenticada foi aceito nesta fase ([09:37] Sofia). Não inventar isolamento multi-tenant já existente: limites de acesso por customer devem ser revisados e explicitados.

## Dependências e compatibilidade
**FDD-DEP-01** — Mesmo MySQL e stack do projeto; PrismaClient separado por processo e DATABASE_URL comum.

Não exigir Redis. Consumidores precisam de HTTPS, validação HMAC e deduplicação por X-Event-Id. Segurança deve revisar HMAC/geração de secret antes do deploy.

## Critérios de aceite técnicos e estratégia de validação
**FDD-ACE-01** — Verificar commit conjunto de pedido/histórico/estoque/outbox, rollback por falha de inserção e ausência de outbox para filtro sem interesse.

**FDD-ACE-02** — Verificar polling, timeout, retry conforme contagem revisada, DLQ, replay ADMIN auditado e duplicação com event_id preservado.

**FDD-ACE-03** — Verificar HTTPS, limite de tamanho sem truncamento, HMAC sobre corpo exato, rotação de 24h e ausência de items no snapshot.

Plano futuro: integração com MySQL para atomicidade; receptor HTTP controlado para sucesso, timeout, falha e confirmação perdida; relógio controlado para retry/rotação; chamadas API com ADMIN/OPERATOR/sem JWT; alterar pedido após enqueue e confirmar snapshot original. Cenário com múltiplos endpoints valida filtro e entregas independentes. Ordering com falha exige contrato revisado. Não foram executados testes de feature: a entrega é documental.

## Riscos e mitigação
**FDD-RISK-01** — Single-worker e receptores lentos acumulam atraso; medir duração/backlog e validar latência, sem incluir múltiplos workers nesta fase.

**FDD-RISK-02** — Secret vazada exige rotação por endpoint; revisão de segurança e overlap de 24h reduzem interrupção, mas emissão durante overlap precisa de definição.

## Pendências para revisão antes de implementar
A contagem de retry, a recuperação de processing, a ordenação sob falha, a assinatura durante rotação, o encoding HMAC, a classificação de status HTTP, a unidade do limite de payload e a semântica de edição/exclusão/replay são lacunas nas decisões acima. Confirmar com Larissa/Diego/Sofia; não registrar soluções candidatas como decisões aceitas. SSRF, redirects, proteção de secret em repouso e acesso por customer também precisam da revisão de segurança, sem atribuir requisitos novos à transcrição.
