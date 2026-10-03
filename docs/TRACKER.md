# Tracker de rastreabilidade

Cada ID identifica um grupo semântico; seu registro cobre explicações e exemplos derivados naquele grupo. `TRANSCRICAO` aponta evidência literal ou ponto que motivou proposta/lacuna; não significa que detalhes propostos foram aceitos. `CODIGO` identifica arquivo real da base. Probabilidades de riscos, envelopes, nomes novos e estratégias de validação são inferências explicitamente rotuladas nos documentos, sem criar requisitos aceitos. Metadados e relato do processo não constituem requisitos da feature.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
|---|---|---|---|---|---|
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Outbox no MySQL | TRANSCRICAO | [09:06] Diego |
| ADR-001-COD-1 | docs/adrs/ADR-001-outbox-no-mysql.md | Integração | Padrão existente em src/modules/orders/order.service.ts | CODIGO | src/modules/orders/order.service.ts |
| ADR-001-COD-2 | docs/adrs/ADR-001-outbox-no-mysql.md | Integração | Padrão existente em prisma/schema.prisma | CODIGO | prisma/schema.prisma |
| ADR-002 | docs/adrs/ADR-002-retry-e-dead-letter.md | Decisão | Retry limitado e DLQ persistida | TRANSCRICAO | [09:17] Larissa |
| ADR-003 | docs/adrs/ADR-003-hmac-por-endpoint.md | Decisão | HMAC-SHA256 e secret por endpoint | TRANSCRICAO | [09:22] Sofia |
| ADR-004 | docs/adrs/ADR-004-at-least-once-com-event-id.md | Decisão | At-least-once e identificação estável | TRANSCRICAO | [09:26] Larissa |
| ADR-005 | docs/adrs/ADR-005-worker-separado-em-polling.md | Decisão | Worker separado com polling | TRANSCRICAO | [09:10] Larissa |
| ADR-006 | docs/adrs/ADR-006-reuso-dos-padroes.md | Decisão | Reuso dos padrões existentes | TRANSCRICAO | [09:30] Larissa |
| ADR-006-COD-1 | docs/adrs/ADR-006-reuso-dos-padroes.md | Integração | Padrão existente em src/shared/errors/app-error.ts | CODIGO | src/shared/errors/app-error.ts |
| ADR-006-COD-2 | docs/adrs/ADR-006-reuso-dos-padroes.md | Integração | Padrão existente em src/shared/logger/index.ts | CODIGO | src/shared/logger/index.ts |
| ADR-006-COD-3 | docs/adrs/ADR-006-reuso-dos-padroes.md | Integração | Padrão existente em src/middlewares/error.middleware.ts | CODIGO | src/middlewares/error.middleware.ts |
| ADR-007 | docs/adrs/ADR-007-snapshot-do-evento.md | Decisão | Snapshot do evento na inserção | TRANSCRICAO | [09:52] Larissa |
| ADR-007-COD-1 | docs/adrs/ADR-007-snapshot-do-evento.md | Integração | Padrão existente em prisma/schema.prisma | CODIGO | prisma/schema.prisma |
| RFC-PROP-01 | docs/RFC.md | Proposta técnica | Adicionar webhooks outbound de mudanças de status por customer, com outbox transacional no MySQL e worker separado; o pedido não aguarda o HTTP externo. | TRANSCRICAO | [09:48] Larissa |
| RFC-CTX-01 | docs/RFC.md | Contexto | Atlas Comercial, MaxDistribuição e Nova Cargo consultam pedidos periodicamente; desejam notificações abaixo de dez segundos e a Atlas sinalizou risco de migração. | TRANSCRICAO | [09:00] Marcos |
| RFC-PROP-02 | docs/RFC.md | Decisão | Produzir snapshot de evento para endpoints ativos interessados no novo status; filtrar antes de persistir, garantindo commit ou rollback junto ao pedido. | TRANSCRICAO | [09:34] Bruno |
| RFC-PROP-03 | docs/RFC.md | Decisão | HMAC-SHA256 com secret por endpoint, HTTPS obrigatório, rotação com sobreposição de 24h e X-Event-Id para deduplicação do consumidor. | TRANSCRICAO | [09:48] Larissa |
| RFC-ALT-01 | docs/RFC.md | Alternativa descartada | HTTP síncrono descartado: seria simples de ligar ao service, mas um destino lento prolongaria a transação e falhas externas levantariam rollback indevido. | TRANSCRICAO | [09:04] Bruno |
| RFC-ALT-02 | docs/RFC.md | Alternativa descartada | Redis Streams descartado: acrescentaria infraestrutura e operação para um time pequeno; MySQL já disponível atende a necessidade. | TRANSCRICAO | [09:07] Diego |
| RFC-ALT-03 | docs/RFC.md | Alternativa descartada | Trigger descartada: MySQL não fornece o listener externo necessário; adaptações seriam mais complexas que polling de 2s. | TRANSCRICAO | [09:09] Diego |
| RFC-ALT-04 | docs/RFC.md | Alternativa descartada | Exactly-once descartado: exige coordenação com os consumidores; at-least-once aceita duplicatas e delega deduplicação. | TRANSCRICAO | [09:25] Diego |
| RFC-Q-01 | docs/RFC.md | Pendência | Rate limiting de saída: observar volume e decidir depois; não entra nesta fase. | TRANSCRICAO | [09:39] Larissa |
| RFC-Q-02 | docs/RFC.md | Pendência | Escala para múltiplos workers e particionamento/locks por pedido: adiados; não prometer ordering nesse cenário. | TRANSCRICAO | [09:13] Diego |
| RFC-Q-03 | docs/RFC.md | Pendência | Arquivamento após cerca de 30 dias: mencionado como futuro; retenção e execução não foram fechadas. | TRANSCRICAO | [09:08] Diego |
| RFC-Q-04 | docs/RFC.md | Pendência | Contagem de retry: cinco tentativas declaradas versus cinco esperas (1m/5m/30m/2h/12h). Confirmar com Larissa e Diego; não escolher seis envios silenciosamente. | TRANSCRICAO | [09:17] Larissa |
| RFC-Q-05 | docs/RFC.md | Pendência | Como assinar durante as 24h de overlap da rotação, e como tratar eventos já pendentes, não foi definido; revisão com Sofia. | TRANSCRICAO | [09:22] Sofia |
| RFC-Q-06 | docs/RFC.md | Pendência | Ordering por pedido foi afirmada para single-worker, mas uma falha seguida de retry pode permitir ultrapassagem; confirmar semântica e impacto no objetivo de latência. | TRANSCRICAO | [09:12] Diego |
| RFC-RISK-01 | docs/RFC.md | Risco | Outbox aumenta escrita e retenção no MySQL; índices de status/created_at e leitura em batches pequenos limitam o custo, sem incluir arquivamento nesta fase. | TRANSCRICAO | [09:08] Diego |
| RFC-RISK-02 | docs/RFC.md | Risco | Destino lento e backlog podem impedir a expectativa abaixo de 10s: polling não é garantia de latência fim a fim; timeout é 10s por chamada. | TRANSCRICAO | [09:42] Diego |
| FDD-OBJ-01 | docs/FDD.md | Objetivo | Persistir notificações atomicamente com a transição e executar HTTP somente após commit, isolado da API. | TRANSCRICAO | [09:40] Bruno |
| FDD-ESC-01 | docs/FDD.md | Escopo | Implementar configuração por customer, filtro por status, histórico de entregas, assinatura, retry, DLQ e replay ADMIN; sem email, dashboard ou rate limiting nesta fase. | TRANSCRICAO | [09:48] Larissa |
| FDD-MOD-01 | docs/FDD.md | Proposta derivada | Configuração guarda id UUID, customer_id, url, secret, estado ativo e lista de status; rotação exige referência à secret anterior e expiração. Nomes exatos dos campos novos são proposta para revisão. | TRANSCRICAO | [09:21] Bruno |
| FDD-MOD-02 | docs/FDD.md | Proposta derivada | Outbox guarda UUID, vínculo ao endpoint e pedido, snapshot, estado, created_at e controle persistente de tentativas/próxima execução. Estados lógicos: pendente, processando, entregue, falhou. Índices de status e created_at apoiam polling. | TRANSCRICAO | [09:08] Diego |
| FDD-MOD-03 | docs/FDD.md | Proposta derivada | DLQ separada preserva payload, motivo e timestamp; histórico de entregas precisa preservar sucesso/falha, payload, resposta e duração para consulta. Estrutura física do histórico fica para revisão de modelagem. | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | Dentro de changeStatus, após validar transição e alterar estoque/status/histórico, chamar publishWebhookEvent(tx, order, fromStatus, toStatus) antes do retorno; usar exclusivamente o tx recebido. | TRANSCRICAO | [09:41] Bruno |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | Processo separado consulta pendências antigas em batch pequeno a cada 2s; marca processamento e realiza chamada com timeout de 10s. | TRANSCRICAO | [09:09] Diego |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Timeout ou falha de entrega leva a retry persistente; usar a sequência declarada 1m/5m/30m/2h/12h e, após esgotamento confirmado, mover para webhook_dead_letter. | TRANSCRICAO | [09:48] Larissa |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | POST administrativo exige ADMIN, recoloca evento da DLQ como pendente e registra identidade de quem executou para auditoria. | TRANSCRICAO | [09:36] Sofia |
| FDD-CONTRATO-BASE | docs/FDD.md | Proposta derivada | Usar /api/v1 como prefixo real da aplicação; request e response de configuração seguem camelCase do código, enquanto o callback segue snake_case definido na reunião. | CODIGO | src/app.ts |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato proposto | Criar cadastro; customerId explícito, URL HTTPS, filtro e secret gerada pelo servidor. | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato proposto | Editar URL, filtro e estado ativo; alteração de secret usa operação própria. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato proposto | Remover configuração de webhook. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato proposto | Listar webhooks de um customer, sem expor secret na listagem proposta. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato proposto | Gerar nova secret e manter anterior válida por 24h. | TRANSCRICAO | [09:22] Sofia |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato proposto | Consultar últimos envios, resultado, payload, resposta e tempo de resposta. | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato proposto | Recolocar item da DLQ como pendente; ADMIN obrigatório e auditoria. | TRANSCRICAO | [09:35] Diego |
| FDD-CALLBACK-01 | docs/FDD.md | Contrato | Enviar JSON com event_id, event_type, timestamp ISO 8601, order_id, order_number, from_status, to_status, customer_id e total_cents; não enviar items. | TRANSCRICAO | [09:43] Diego |
| FDD-CALLBACK-02 | docs/FDD.md | Contrato | Headers: Content-Type application/json, X-Event-Id UUID, X-Signature HMAC, X-Timestamp do envio e X-Webhook-Id do cadastro. | TRANSCRICAO | [09:44] Diego |
| FDD-ERR-01 | docs/FDD.md | Proposta derivada | Erros específicos do domínio usam AppError e prefixo WEBHOOK_; códigos adicionais abaixo operacionalizam falhas discutidas, sem renomear erros compartilhados de autenticação/Zod/Prisma. | TRANSCRICAO | [09:29] Larissa |
| FDD-RES-01 | docs/FDD.md | Restrição | Timeout de 10s por envio; retries persistentes; DLQ e replay manual como recuperação. Email não é fallback desta fase. | TRANSCRICAO | [09:42] Diego |
| FDD-OBS-01 | docs/FDD.md | Proposta derivada | Usar Pino para logs estruturados do ciclo de entrega e auditoria de replay; correlacionar event_id, webhook_id, order_id e usuário do replay. | TRANSCRICAO | [09:29] Bruno |
| FDD-INT-01 | docs/FDD.md | Integração | src/modules/orders/order.service.ts: Estender changeStatus antes do retorno da transação com publishWebhookEvent(tx, refreshed, from, to); preservar validação, estoque e auditoria. | CODIGO | src/modules/orders/order.service.ts |
| FDD-INT-02 | docs/FDD.md | Integração | src/modules/orders/order.status.ts: Reutilizar máquina de estados e regras de débito/reposição; filtro recebe enum válido, sem criar transições. | CODIGO | src/modules/orders/order.status.ts |
| FDD-INT-03 | docs/FDD.md | Integração | prisma/schema.prisma: Planejar modelos de configuração/outbox/DLQ/histórico e relações; manter UUID Char(36), MySQL e migrações futuras. Nenhuma migration é entregue aqui. | CODIGO | prisma/schema.prisma |
| FDD-INT-04 | docs/FDD.md | Integração | src/config/database.ts: Worker usa cliente próprio por processo no mesmo DATABASE_URL. O singleton carregado em outro processo já é outra instância; evitar criar dois pools acidentalmente. | CODIGO | src/config/database.ts |
| FDD-INT-05 | docs/FDD.md | Integração | src/app.ts: Construir dependências do módulo via buildControllers e montar rotas sob /api/v1; não iniciar worker em buildApp. | CODIGO | src/app.ts |
| FDD-INT-06 | docs/FDD.md | Integração | src/routes/index.ts: Adicionar futuramente routers de webhooks e replay administrativo ao agregador. | CODIGO | src/routes/index.ts |
| FDD-INT-07 | docs/FDD.md | Integração | src/middlewares/auth.middleware.ts: Reusar authenticate e requireRole(ADMIN) no replay. AuthUser não possui customerId; receber customer explicitamente. | CODIGO | src/middlewares/auth.middleware.ts |
| FDD-INT-08 | docs/FDD.md | Integração | src/shared/errors/app-error.ts: Erros de domínio estendem AppError com código/status/details; não usar NotFoundError genérico esperando prefixo WEBHOOK_. | CODIGO | src/shared/errors/app-error.ts |
| FDD-INT-09 | docs/FDD.md | Integração | src/middlewares/validate.middleware.ts: Zod é convertido para VALIDATION_ERROR; mapear WEBHOOK_INVALID_URL no módulo se esse contrato for aprovado. | CODIGO | src/middlewares/validate.middleware.ts |
| FDD-INT-10 | docs/FDD.md | Integração | src/middlewares/error.middleware.ts: Reusar envelope AppError e tratamento genérico; falhas do worker precisam de catch/log próprio, pois não são requisições Express. | CODIGO | src/middlewares/error.middleware.ts |
| FDD-INT-11 | docs/FDD.md | Integração | src/shared/logger/index.ts: Reusar Pino; redaction atual não cobre secrets de webhook. Evitar logging dessas credenciais. | CODIGO | src/shared/logger/index.ts |
| FDD-INT-12 | docs/FDD.md | Integração | src/shared/http/response.ts: Reusar paginated na proposta de listagem/histórico. | CODIGO | src/shared/http/response.ts |
| FDD-INT-13 | docs/FDD.md | Integração | src/server.ts: Referência para nova entry point proposta src/worker.ts; novo arquivo ainda não existe. | CODIGO | src/server.ts |
| FDD-INT-14 | docs/FDD.md | Integração | tests/orders.test.ts: Referência para futura validação da transação e regressão dos pedidos; não modificar testes nesta entrega. | CODIGO | tests/orders.test.ts |
| FDD-INT-15 | docs/FDD.md | Integração | package.json: Preservar Node >=20, TypeScript ESM e dependências atuais; npm run worker é script futuro, ainda inexistente. | CODIGO | package.json |
| FDD-DEP-01 | docs/FDD.md | Dependência | Mesmo MySQL e stack do projeto; PrismaClient separado por processo e DATABASE_URL comum. | TRANSCRICAO | [09:30] Bruno |
| FDD-ACE-01 | docs/FDD.md | Critério derivado | Verificar commit conjunto de pedido/histórico/estoque/outbox, rollback por falha de inserção e ausência de outbox para filtro sem interesse. | TRANSCRICAO | [09:40] Bruno |
| FDD-ACE-02 | docs/FDD.md | Critério derivado | Verificar polling, timeout, retry conforme contagem revisada, DLQ, replay ADMIN auditado e duplicação com event_id preservado. | TRANSCRICAO | [09:48] Larissa |
| FDD-ACE-03 | docs/FDD.md | Critério derivado | Verificar HTTPS, limite de tamanho sem truncamento, HMAC sobre corpo exato, rotação de 24h e ausência de items no snapshot. | TRANSCRICAO | [09:22] Sofia |
| FDD-RISK-01 | docs/FDD.md | Risco | Single-worker e receptores lentos acumulam atraso; medir duração/backlog e validar latência, sem incluir múltiplos workers nesta fase. | TRANSCRICAO | [09:12] Diego |
| FDD-RISK-02 | docs/FDD.md | Risco | Secret vazada exige rotação por endpoint; revisão de segurança e overlap de 24h reduzem interrupção, mas emissão durante overlap precisa de definição. | TRANSCRICAO | [09:22] Sofia |
| PRD-CTX-01 | docs/PRD.md | Contexto | OMS passa a oferecer notificações outbound sobre mudança de status, permitindo integrações B2B orientadas por notificações. | TRANSCRICAO | [09:02] Marcos |
| PRD-PROB-01 | docs/PRD.md | Problema | Atlas Comercial, MaxDistribuição e Nova Cargo fazem polling de pedidos, com integração lenta e cara; Atlas sinalizou possível migração sem entrega até o prazo solicitado. | TRANSCRICAO | [09:00] Marcos |
| PRD-PUB-01 | docs/PRD.md | Público-alvo | Clientes B2B integrando seus sistemas ao OMS; usuários autenticados representam esses clientes, sem customer implícito no JWT. | TRANSCRICAO | [09:32] Marcos |
| PRD-USO-01 | docs/PRD.md | Cenário | Consumidor escolhe receber apenas SHIPPED e DELIVERED, e consulta detalhes do pedido pela API quando necessário. | TRANSCRICAO | [09:33] Marcos |
| PRD-MET-01 | docs/PRD.md | Objetivo quantitativo | Notificação em menos de 10 segundos para destinatário disponível; medir intervalo entre mudança confirmada e recepção no cenário saudável. | TRANSCRICAO | [09:02] Marcos |
| PRD-MET-02 | docs/PRD.md | Planejamento | Planejamento estimado de três sprints, incluindo revisão de segurança; Atlas pede fim de novembro, sem ano especificado na transcrição. | TRANSCRICAO | [09:47] Larissa |
| PRD-FORA-01 | docs/PRD.md | Exclusão | Email de aviso/fallback adiado para uma possível próxima fase. | TRANSCRICAO | [09:37] Larissa |
| PRD-FORA-02 | docs/PRD.md | Exclusão | Dashboard visual excluído desta entrega; projeto separado de frontend. | TRANSCRICAO | [09:40] Larissa |
| PRD-FORA-03 | docs/PRD.md | Exclusão | Rate limiting de saída adiado para observação e decisão posterior. | TRANSCRICAO | [09:39] Larissa |
| PRD-FORA-04 | docs/PRD.md | Exclusão | Múltiplos workers/particionamento e ordering global não são compromisso desta fase. | TRANSCRICAO | [09:13] Larissa |
| PRD-FORA-05 | docs/PRD.md | Exclusão | Arquivamento de eventos entregues mencionado como futuro, fora desta feature. | TRANSCRICAO | [09:08] Diego |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | Cadastrar webhook por customer com URL, secret gerada pelo servidor e lista de status desejados. | TRANSCRICAO | [09:31] Marcos |
| PRD-FR-02 | docs/PRD.md | Requisito Funcional | Receber customer explicitamente no body ou path; não derivar do JWT. | TRANSCRICAO | [09:32] Larissa |
| PRD-FR-03 | docs/PRD.md | Requisito Funcional | Editar configuração de webhook. | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-04 | docs/PRD.md | Requisito Funcional | Remover configuração de webhook. | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-05 | docs/PRD.md | Requisito Funcional | Listar webhooks de um customer. | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-06 | docs/PRD.md | Requisito Funcional | Filtrar pelo status na inserção; não criar evento se nenhum cadastro quiser recebê-lo. | TRANSCRICAO | [09:34] Bruno |
| PRD-FR-07 | docs/PRD.md | Requisito Funcional | Exibir histórico com sucesso/falha, payload, resposta e tempo de resposta, incluindo consulta aos últimos 100 envios. | TRANSCRICAO | [09:34] Marcos |
| PRD-FR-08 | docs/PRD.md | Requisito Funcional | Rotacionar secret pela API, mantendo a antiga válida por 24 horas em paralelo. | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-09 | docs/PRD.md | Requisito Funcional | Persistir falhas esgotadas em DLQ e permitir replay manual por endpoint administrativo. | TRANSCRICAO | [09:18] Diego |
| PRD-FR-10 | docs/PRD.md | Requisito Funcional | Exigir ADMIN para replay e registrar quem executou a operação. | TRANSCRICAO | [09:36] Sofia |
| PRD-FR-11 | docs/PRD.md | Requisito Funcional | Permitir CRUD de configuração a qualquer role autenticada nesta fase. | TRANSCRICAO | [09:37] Sofia |
| PRD-FR-12 | docs/PRD.md | Requisito Funcional | Informar event_id estável ao consumidor para deduplicar notificações repetidas. | TRANSCRICAO | [09:25] Diego |
| PRD-NFR-01 | docs/PRD.md | Requisito Não Funcional | Chamadas externas não bloqueiam a transação de pedidos; evento e mudança de status persistem juntos. | TRANSCRICAO | [09:06] Diego |
| PRD-NFR-02 | docs/PRD.md | Requisito Não Funcional | HTTPS obrigatório, recusando cadastro de URL HTTP. | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-03 | docs/PRD.md | Requisito Não Funcional | Autenticar origem/integridade por HMAC-SHA256 com secret única por endpoint. | TRANSCRICAO | [09:22] Sofia |
| PRD-NFR-04 | docs/PRD.md | Requisito Não Funcional | Limitar payload a 64KB e retornar erro em vez de truncar; unidade exata a confirmar. | TRANSCRICAO | [09:24] Larissa |
| PRD-NFR-05 | docs/PRD.md | Requisito Não Funcional | Admitir duplicatas na garantia at-least-once; consumidor deduplica por X-Event-Id. | TRANSCRICAO | [09:26] Larissa |
| PRD-NFR-06 | docs/PRD.md | Requisito Não Funcional | Timeout de 10 segundos por chamada do worker; cliente lento é tratado como falha. | TRANSCRICAO | [09:42] Diego |
| PRD-NFR-07 | docs/PRD.md | Requisito Não Funcional | Usar worker separado com polling de 2 segundos e ordenação limitada ao pedido no cenário single-worker. | TRANSCRICAO | [09:13] Larissa |
| PRD-DEC-01 | docs/PRD.md | Trade-off | MySQL/outbox reduz infraestrutura e preserva atomicidade; polling adiciona espera e operação single-worker limita capacidade. | TRANSCRICAO | [09:07] Diego |
| PRD-DEC-02 | docs/PRD.md | Trade-off | Retry limitado evita eventos pendurados para sempre, mas indisponibilidade longa pode terminar em DLQ e intervenção manual. | TRANSCRICAO | [09:15] Diego |
| PRD-DEP-01 | docs/PRD.md | Dependência | Clientes implementam validação de assinatura e deduplicação; integração e documentação para consumidores são necessárias. | TRANSCRICAO | [09:26] Marcos |
| PRD-DEP-02 | docs/PRD.md | Dependência | Reservar ao menos dois dias úteis para revisão de HMAC e geração de secret por Sofia antes do deploy. | TRANSCRICAO | [09:46] Sofia |
| PRD-RISK-01 | docs/PRD.md | Risco derivado | Indisponibilidade: probabilidade média estimada, impacto em entrega; mitigar com timeout/retry/DLQ. | TRANSCRICAO | [09:15] Diego |
| PRD-RISK-02 | docs/PRD.md | Risco derivado | Vazamento: probabilidade média estimada, impacto em autenticidade; secret individual/rotação/revisão. | TRANSCRICAO | [09:22] Sofia |
| PRD-RISK-03 | docs/PRD.md | Risco derivado | Duplicatas: probabilidade média estimada, efeitos repetidos; deduplicação pelo consumidor. | TRANSCRICAO | [09:24] Diego |
| PRD-ACE-01 | docs/PRD.md | Critério de aceitação derivado | Usuário autenticado cria, edita, remove e lista cadastros; cliente e filtro são explícitos. | TRANSCRICAO | [09:33] Bruno |
| PRD-ACE-02 | docs/PRD.md | Critério de aceitação derivado | Mudança elegível produz notificação em <10s em cenário saudável e sem bloquear pedido por HTTP. | TRANSCRICAO | [09:02] Marcos |
| PRD-ACE-03 | docs/PRD.md | Critério de aceitação derivado | Histórico permite ver resultado, payload, resposta e duração dos últimos envios. | TRANSCRICAO | [09:34] Marcos |
| PRD-ACE-04 | docs/PRD.md | Critério de aceitação derivado | Replay rejeita OPERATOR e aceita ADMIN com identidade registrada. | TRANSCRICAO | [09:36] Sofia |
| PRD-ACE-05 | docs/PRD.md | Critério de aceitação derivado | URL HTTP é recusada; callback assinado; rotação mantém transição de 24h após definir contrato técnico. | TRANSCRICAO | [09:22] Sofia |
| PRD-TEST-01 | docs/PRD.md | Validação derivada | Validar integração ponta a ponta entre mudança de status, filtro e receptor HTTP, cobrindo sucesso, indisponibilidade e replay; incluir revisão de segurança no fechamento. | TRANSCRICAO | [09:46] Larissa |
