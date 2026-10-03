# Tracker de rastreabilidade

A tabela relaciona os itens documentados às falas da reunião ou aos arquivos do código. `TRANSCRICAO` identifica timestamp e participante; `CODIGO` identifica o caminho do arquivo. Propostas derivadas e questões em aberto são indicadas na coluna Tipo.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
|---|---|---|---|---|---|
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Outbox no MySQL | TRANSCRICAO | [09:06] Diego |
| ADR-001-COD-1 | docs/adrs/ADR-001-outbox-no-mysql.md | Integração | Padrão existente em src/modules/orders/order.service.ts | CODIGO | src/modules/orders/order.service.ts |
| ADR-001-COD-2 | docs/adrs/ADR-001-outbox-no-mysql.md | Integração | Padrão existente em prisma/schema.prisma | CODIGO | prisma/schema.prisma |
| ADR-002 | docs/adrs/ADR-002-retry-e-dead-letter.md | Decisão | Cinco tentativas declaradas, intervalos 1m/5m/30m/2h/12h; interpretação de contagem precisa de revisão. | TRANSCRICAO | [09:17] Larissa |
| ADR-003 | docs/adrs/ADR-003-hmac-por-endpoint.md | Decisão | HMAC-SHA256 e secret por endpoint | TRANSCRICAO | [09:22] Sofia |
| ADR-004 | docs/adrs/ADR-004-at-least-once-com-event-id.md | Decisão | Garantia at-least-once com X-Event-Id e deduplicação no consumidor. | TRANSCRICAO | [09:26] Larissa |
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
| FDD-MOD-03 | docs/FDD.md | Proposta derivada | DLQ separada preserva payload, motivo e timestamp; estrutura física nova permanece proposta. | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | Dentro de changeStatus, após validar transição e alterar estoque/status/histórico, chamar publishWebhookEvent(tx, order, fromStatus, toStatus) antes do retorno; usar exclusivamente o tx recebido. | TRANSCRICAO | [09:41] Bruno |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | Polling de 2s seleciona pendências antigas; marca processamento. | TRANSCRICAO | [09:09] Diego |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Timeout ou falha de entrega leva a retry persistente; usar a sequência declarada 1m/5m/30m/2h/12h e, após esgotamento confirmado, mover para webhook_dead_letter. | TRANSCRICAO | [09:48] Larissa |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | POST administrativo exige ADMIN, recoloca evento da DLQ como pendente e registra identidade de quem executou para auditoria. | TRANSCRICAO | [09:36] Sofia |
| FDD-CONTRATO-BASE | docs/FDD.md | Proposta derivada | Prefixo /api/v1 existente; contratos de configuração propostos para revisão. | CODIGO | src/app.ts |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato proposto | Criar cadastro; customerId explícito, URL HTTPS, filtro e secret gerada pelo servidor. | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato proposto | Editar URL, filtro e estado ativo; alteração de secret usa operação própria. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato proposto | Remover configuração de webhook. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato proposto | Listar webhooks de um customer, sem expor secret na listagem proposta. | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato proposto | Gerar nova secret e manter anterior válida por 24h. | TRANSCRICAO | [09:22] Sofia |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato proposto | Consultar últimos envios, resultado, payload, resposta e tempo de resposta. | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato proposto | Recolocar item da DLQ como pendente; ADMIN obrigatório e auditoria. | TRANSCRICAO | [09:35] Diego |
| FDD-CALLBACK-01 | docs/FDD.md | Contrato | Enviar JSON com event_id, event_type, timestamp ISO 8601, order_id, order_number, from_status, to_status, customer_id e total_cents; não enviar items. | TRANSCRICAO | [09:43] Diego |
| FDD-CALLBACK-02 | docs/FDD.md | Contrato | Headers Content-Type, X-Event-Id, X-Signature e X-Timestamp conforme reunião. | TRANSCRICAO | [09:44] Diego |
| FDD-ERR-01 | docs/FDD.md | Proposta derivada | Erros específicos do domínio usam AppError e prefixo WEBHOOK_; códigos adicionais abaixo operacionalizam falhas discutidas, sem renomear erros compartilhados de autenticação/Zod/Prisma. | TRANSCRICAO | [09:29] Larissa |
| FDD-RES-01 | docs/FDD.md | Restrição | Timeout de 10s por envio; retries persistentes; DLQ e replay manual como recuperação. Email não é fallback desta fase. | TRANSCRICAO | [09:42] Diego |
| FDD-OBS-01 | docs/FDD.md | Proposta derivada | Usar Pino para logs estruturados do ciclo de entrega e auditoria de replay; correlacionar event_id, webhook_id, order_id e usuário do replay. | TRANSCRICAO | [09:29] Bruno |
| FDD-INT-01 | docs/FDD.md | Integração | src/modules/orders/order.service.ts: Estender changeStatus antes do retorno da transação com publishWebhookEvent(tx, refreshed, from, to); preservar validação, estoque e auditoria. | CODIGO | src/modules/orders/order.service.ts |
| FDD-INT-02 | docs/FDD.md | Integração | src/modules/orders/order.status.ts: Reutilizar máquina de estados e regras de débito/reposição; filtro recebe enum válido, sem criar transições. | CODIGO | src/modules/orders/order.status.ts |
| FDD-INT-03 | docs/FDD.md | Integração | prisma/schema.prisma: Planejar modelos de configuração/outbox/DLQ/histórico e relações; manter UUID Char(36), MySQL e migrações futuras. | CODIGO | prisma/schema.prisma |
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
| FDD-INT-14 | docs/FDD.md | Integração | tests/orders.test.ts: Referência para validação da transação e regressão dos pedidos. | CODIGO | tests/orders.test.ts |
| FDD-INT-15 | docs/FDD.md | Integração | package.json: Preservar Node >=20, TypeScript ESM e dependências atuais; npm run worker é script futuro, ainda inexistente. | CODIGO | package.json |
| FDD-DEP-01 | docs/FDD.md | Dependência | Mesmo MySQL e stack do projeto; PrismaClient separado por processo e DATABASE_URL comum. | TRANSCRICAO | [09:30] Bruno |
| FDD-ACE-01 | docs/FDD.md | Critério derivado | Verificar commit conjunto de pedido/histórico/estoque/outbox, rollback por falha de inserção e ausência de outbox para filtro sem interesse. | TRANSCRICAO | [09:40] Bruno |
| FDD-ACE-02 | docs/FDD.md | Critério derivado | Verificar polling, timeout, retry conforme contagem revisada, DLQ, replay ADMIN auditado e duplicação com event_id preservado. | TRANSCRICAO | [09:48] Larissa |
| FDD-ACE-03 | docs/FDD.md | Critério derivado | Validar HMAC sobre corpo e rotação de 24h; demais checks têm origens próprias. | TRANSCRICAO | [09:22] Sofia |
| FDD-RISK-01 | docs/FDD.md | Risco | Single-worker e receptores lentos acumulam atraso; medir duração/backlog e validar latência, sem incluir múltiplos workers nesta fase. | TRANSCRICAO | [09:12] Diego |
| FDD-RISK-02 | docs/FDD.md | Risco | Secret vazada exige rotação por endpoint; revisão de segurança e overlap de 24h reduzem interrupção, mas emissão durante overlap precisa de definição. | TRANSCRICAO | [09:22] Sofia |
| PRD-CTX-01 | docs/PRD.md | Contexto | OMS passa a oferecer notificações outbound sobre mudança de status, permitindo integrações B2B orientadas por notificações. | TRANSCRICAO | [09:02] Marcos |
| PRD-PROB-01 | docs/PRD.md | Problema | Atlas Comercial, MaxDistribuição e Nova Cargo fazem polling de pedidos, com integração lenta e cara; Atlas sinalizou possível migração sem entrega até o prazo solicitado. | TRANSCRICAO | [09:00] Marcos |
| PRD-PUB-01 | docs/PRD.md | Público-alvo | Usuários autenticados representam clientes; customer explícito, conforme correção da reunião. | TRANSCRICAO | [09:32] Marcos |
| PRD-USO-01 | docs/PRD.md | Cenário | Consumidor escolhe receber apenas SHIPPED e DELIVERED, e consulta detalhes do pedido pela API quando necessário. | TRANSCRICAO | [09:33] Marcos |
| PRD-MET-01 | docs/PRD.md | Objetivo quantitativo | Notificação em menos de 10 segundos para destinatário disponível; medir intervalo entre mudança confirmada e recepção no cenário saudável. | TRANSCRICAO | [09:02] Marcos |
| PRD-MET-02 | docs/PRD.md | Planejamento | Planejamento estimado de três sprints, incluindo revisão de segurança. | TRANSCRICAO | [09:47] Larissa |
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
| PRD-RISK-01 | docs/PRD.md | Risco derivado | Indisponibilidade possível com antecedente de manutenção; frequência não medida; impacto em entrega e retry/DLQ. | TRANSCRICAO | [09:16] Diego |
| PRD-RISK-02 | docs/PRD.md | Risco derivado | Vazamento possível com antecedente em logs; frequência não medida; impacto na autenticidade e rotação por endpoint. | TRANSCRICAO | [09:22] Diego |
| PRD-RISK-03 | docs/PRD.md | Risco derivado | Duplicata possível na garantia declarada; frequência não medida; impacto no consumidor e deduplicação. | TRANSCRICAO | [09:24] Diego |
| PRD-ACE-01 | docs/PRD.md | Critério de aceitação derivado | Usuário autenticado cria, edita, remove e lista cadastros; cliente e filtro são explícitos. | TRANSCRICAO | [09:33] Bruno |
| PRD-ACE-02 | docs/PRD.md | Critério de aceitação derivado | Mudança elegível produz notificação em <10s em cenário saudável e sem bloquear pedido por HTTP. | TRANSCRICAO | [09:02] Marcos |
| PRD-ACE-03 | docs/PRD.md | Critério de aceitação derivado | Histórico permite ver resultado, payload, resposta e duração dos últimos envios. | TRANSCRICAO | [09:34] Marcos |
| PRD-ACE-04 | docs/PRD.md | Critério de aceitação derivado | Replay rejeita OPERATOR e aceita ADMIN com identidade registrada. | TRANSCRICAO | [09:36] Sofia |
| PRD-ACE-05 | docs/PRD.md | Critério de aceitação derivado | URL HTTP é recusada; callback assinado; rotação mantém transição de 24h após definir contrato técnico. | TRANSCRICAO | [09:22] Sofia |
| PRD-TEST-01 | docs/PRD.md | Validação derivada | Validar integração ponta a ponta entre mudança de status, filtro e receptor HTTP, cobrindo sucesso, indisponibilidade e replay; incluir revisão de segurança no fechamento. | TRANSCRICAO | [09:46] Larissa |
| PRD-PRAZO-01 | docs/PRD.md | Prazo solicitado | Atlas solicita fim de novembro; o ano não consta na reunião. | TRANSCRICAO | [09:45] Marcos |
| PRD-PUB-02 | docs/PRD.md | Correção de requisito | customer_id explícito no body/path; usuários representam clientes. | TRANSCRICAO | [09:32] Larissa |
| PRD-RISK-01-MIT | docs/PRD.md | Mitigação | Retry limitado e DLQ mitigam indisponibilidade. | TRANSCRICAO | [09:17] Larissa |
| PRD-RISK-02-MIT | docs/PRD.md | Mitigação | Secret por endpoint e rotação com 24h mitigam vazamento. | TRANSCRICAO | [09:22] Sofia |
| PRD-RISK-03-MIT | docs/PRD.md | Mitigação | X-Event-Id permite deduplicação pelo consumidor. | TRANSCRICAO | [09:25] Diego |
| RFC-CTX-02 | docs/RFC.md | Decisão / dependência | Latência aceita abaixo de dez segundos. | TRANSCRICAO | [09:02] Marcos |
| RFC-PROP-04 | docs/RFC.md | Decisão / dependência | Snapshot fechado na inserção. | TRANSCRICAO | [09:52] Larissa |
| RFC-PROP-05 | docs/RFC.md | Decisão / dependência | Worker separado da API. | TRANSCRICAO | [09:11] Diego |
| RFC-PROP-06 | docs/RFC.md | Decisão / dependência | PrismaClient por processo, mesmo DATABASE_URL. | TRANSCRICAO | [09:30] Bruno |
| RFC-DEP-01 | docs/RFC.md | Decisão / dependência | Revisão de segurança de pelo menos dois dias úteis antes do deploy. | TRANSCRICAO | [09:46] Sofia |
| RFC-DEP-02 | docs/RFC.md | Decisão / dependência | Estimativa de três sprints inclui revisão. | TRANSCRICAO | [09:47] Larissa |
| FDD-SEC-01 | docs/FDD.md | Segurança / pendência | Secret gerada no servidor; algoritmo, tamanho e armazenamento não definidos. | TRANSCRICAO | [09:31] Marcos |
| FDD-MOD-04 | docs/FDD.md | Proposta derivada | Uma linha por evento/destino, derivada da identificação por webhook; cardinalidade e event_id entre destinos não decididos. | TRANSCRICAO | [09:44] Sofia |
| FDD-MOD-05 | docs/FDD.md | Proposta derivada | Histórico por tentativa com payload/resposta/duração; estrutura, retenção e limites de resposta não fechados. | TRANSCRICAO | [09:34] Marcos |
| FDD-ESC-02 | docs/FDD.md | Limite de evidência | Evento de criação não foi fechado; integração explícita da reunião é changeStatus. | TRANSCRICAO | [09:40] Bruno |
| FDD-FLUXO-05 | docs/FDD.md | Proposta derivada | Selecionar por elegibilidade, processar fora de transação longa e persistir resultado; batch exato não definido. | TRANSCRICAO | [09:08] Diego |
| FDD-HTTP-01 | docs/FDD.md | Proposta derivada / pendência | 2xx como sucesso é proposta; classificação de status e redirects não definida. | TRANSCRICAO | [09:34] Marcos |
| FDD-RETRY-01 | docs/FDD.md | Ambiguidade | Cinco tentativas versus cinco intervalos: não há definição inequívoca do total de envios. | TRANSCRICAO | [09:17] Larissa |
| FDD-RETRY-02 | docs/FDD.md | Proposta derivada | Agendamento persistente e transferência atômica para DLQ operacionalizam retry/DLQ, sem alterar pedido. | TRANSCRICAO | [09:18] Diego |
| FDD-REPLAY-01 | docs/FDD.md | Proposta derivada / pendência | Preservar snapshot/ID; contador e replay repetido não definidos. | TRANSCRICAO | [09:18] Diego |
| FDD-REC-01 | docs/FDD.md | Risco derivado / pendência | Reprocessamento pode duplicar; recuperação de processing e shutdown não definidos. | TRANSCRICAO | [09:24] Diego |
| FDD-ORDER-01 | docs/FDD.md | Lacuna derivada | Ordenação por created_at não resolve retries nem empates; bloqueio por pedido é candidato, não decisão. | TRANSCRICAO | [09:12] Diego |
| FDD-CONTRATO-REGRA | docs/FDD.md | Convenção proposta | Diferenciar paths literais dos derivados e requests sem body nas rotas GET/DELETE. | TRANSCRICAO | [09:33] Bruno |
| FDD-EXEMPLO-01 | docs/FDD.md | Exemplo ilustrativo | UUIDs fictícios usados nos contratos; formato segue identificadores UUID decididos. | TRANSCRICAO | [09:51] Larissa |
| FDD-HMAC-01 | docs/FDD.md | Decisão / proposta derivada | HMAC sobre corpo; bytes exatos no envio; encoding e overlap permanecem sem definição. | TRANSCRICAO | [09:22] Sofia |
| FDD-ERR-ENVELOPE | docs/FDD.md | Compatibilidade | Envelope AppError usa error.code/message/details; erros genéricos não recebem prefixo WEBHOOK_. | CODIGO | src/middlewares/error.middleware.ts |
| FDD-RES-02 | docs/FDD.md | Resiliência derivada | Snapshot e ID estáveis nos retries; falha externa não reverte pedido já commitado. | TRANSCRICAO | [09:25] Diego |
| FDD-NOVO-01 | docs/FDD.md | Integração futura | Módulo webhooks e entry point worker são arquivos novos propostos, não arquivos existentes citados como fonte. | TRANSCRICAO | [09:28] Bruno |
| FDD-DEP-02 | docs/FDD.md | Dependência | MySQL escolhido sem Redis; consumidor valida assinatura/deduplica, com revisão de segurança antes do deploy. | TRANSCRICAO | [09:07] Diego |
| FDD-TEST-01 | docs/FDD.md | Validação derivada | Plano de testes ponta a ponta e revisão de segurança. | TRANSCRICAO | [09:46] Larissa |
| FDD-PEND-01 | docs/FDD.md | Pendência | Síntese das lacunas derivadas do processamento/retry; não são requisitos novos aprovados. | TRANSCRICAO | [09:48] Larissa |
| FDD-SCHEMA-01 | docs/FDD.md | Proposta derivada | UUID e enum OrderStatus reutilizados nos schemas; restrições não fechadas permanecem abertas. | CODIGO | src/modules/orders/order.schemas.ts |
| FDD-SCHEMA-02 | docs/FDD.md | Proposta derivada | Mapeamento dos campos reais Order para snapshot; estados da transição são explícitos. | CODIGO | prisma/schema.prisma |
| FDD-SCHEMA-03 | docs/FDD.md | Proposta derivada | Paginação 1/20 e pageSize até 100 reutilizam schema real; PATCH parcial usa referência própria. | CODIGO | src/modules/orders/order.schemas.ts |
| FDD-SCHEMA-02-EVENTO | docs/FDD.md | Contrato | Timestamp do evento ISO 8601 vem do payload definido na reunião. | TRANSCRICAO | [09:43] Diego |
| FDD-SCHEMA-02-ENVIO | docs/FDD.md | Contrato | X-Timestamp indica envio, distinto do timestamp do snapshot. | TRANSCRICAO | [09:44] Diego |
| FDD-HTTP-02 | docs/FDD.md | Convenção HTTP proposta | GET/DELETE sem body, rotas protegidas por Bearer, respostas JSON/204. | CODIGO | src/modules/orders/order.controller.ts |
| FDD-HTTP-03 | docs/FDD.md | Compatibilidade | API já retorna X-Request-Id pelo request logger. | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-ERRO-01 | docs/FDD.md | Erro / proposta derivada | Cadastro ausente: código exemplificado; HTTP 404 proposto por reuso. | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-02 | docs/FDD.md | Erro / proposta derivada | URL HTTPS obrigatória; WEBHOOK_INVALID_URL citado em 09:28, HTTP 400 por validação. | TRANSCRICAO | [09:23] Sofia |
| FDD-ERRO-03 | docs/FDD.md | Erro / proposta derivada | Código exemplificado para secret ausente; falha interna de assinatura a revisar. | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-04 | docs/FDD.md | Erro / proposta derivada | Erro sem truncar acima de 64KB; nome/422 e rollback são proposta derivada. | TRANSCRICAO | [09:24] Larissa |
| FDD-ERRO-05 | docs/FDD.md | Erro / proposta derivada | Timeout 10s é falha para retry; nome interno proposto. | TRANSCRICAO | [09:42] Diego |
| FDD-ERRO-06 | docs/FDD.md | Erro / proposta derivada | Falha de entrega gera retry; código interno proposto, classificação HTTP aberta. | TRANSCRICAO | [09:15] Diego |
| FDD-ERRO-07 | docs/FDD.md | Erro / proposta derivada | Replay requer item DLQ; ausência 404 e código são proposta derivada. | TRANSCRICAO | [09:18] Diego |
| FDD-ERRO-08 | docs/FDD.md | Compatibilidade | Customer ausente usa NotFoundError/NOT_FOUND do service de pedidos existente. | CODIGO | src/modules/orders/order.service.ts |
| FDD-METRICA-01 | docs/FDD.md | Métrica derivada | Latência commit à recepção, meta <10s saudável. | TRANSCRICAO | [09:02] Marcos |
| FDD-METRICA-02 | docs/FDD.md | Métrica derivada | Espera snapshot-início de tentativa, sem confundir com recepção. | TRANSCRICAO | [09:09] Diego |
| FDD-METRICA-03 | docs/FDD.md | Métrica derivada | Duração de HTTP, timeout de 10s. | TRANSCRICAO | [09:42] Diego |
| FDD-METRICA-04 | docs/FDD.md | Métrica derivada | Tentativas/falhas observam política de retry. | TRANSCRICAO | [09:17] Larissa |
| FDD-METRICA-05 | docs/FDD.md | Métrica derivada | Pendências/DLQ observam acúmulo e replay. | TRANSCRICAO | [09:18] Diego |
| FDD-TRACE-01 | docs/FDD.md | Tracing / proposta derivada | requestId já existe; correlação com event_id é derivada, spans e propagação não foram decididos. | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-LOG-02 | docs/FDD.md | Compatibilidade / risco | Redaction atual não cobre secret/X-Signature; logger HTTP identifica userId, auditoria de replay exige relação com evento. | CODIGO | src/shared/logger/index.ts |
| FDD-LOG-02-AUD | docs/FDD.md | Auditoria | Replay deve logar quem fez a ação. | TRANSCRICAO | [09:36] Sofia |
| FDD-MOD-01-ROT | docs/FDD.md | Decisão | Secret anterior tem validade paralela de 24h. | TRANSCRICAO | [09:21] Sofia |
| FDD-MOD-02-ID | docs/FDD.md | Decisão | UUID na outbox segue padrão do projeto. | TRANSCRICAO | [09:51] Larissa |
| FDD-MOD-02-RETRY | docs/FDD.md | Proposta derivada | Próxima execução e contador derivados do retry persistente. | TRANSCRICAO | [09:17] Diego |
| FDD-FLUXO-02-TIMEOUT | docs/FDD.md | Restrição | Timeout por chamada é 10s. | TRANSCRICAO | [09:42] Diego |
| FDD-CONTRATO-01-CUSTOMER | docs/FDD.md | Correção | customer explícito, não no token. | TRANSCRICAO | [09:32] Larissa |
| FDD-CONTRATO-01-HTTPS | docs/FDD.md | Restrição | Cadastro de URL HTTP recusado. | TRANSCRICAO | [09:23] Sofia |
| FDD-CONTRATO-07-AUTH | docs/FDD.md | Decisão | Replay é exclusivo de ADMIN e auditado. | TRANSCRICAO | [09:36] Sofia |
| FDD-CALLBACK-02-WEBHOOK | docs/FDD.md | Contrato | X-Webhook-Id identifica cadastro receptor. | TRANSCRICAO | [09:44] Sofia |
| FDD-HMAC-02 | docs/FDD.md | Lacuna derivada | X-Timestamp não assinado quando HMAC cobre só corpo; não garantir antirreplay pelo header isolado. | TRANSCRICAO | [09:44] Diego |
| FDD-RES-01-FALLBACK | docs/FDD.md | Exclusão | Email como fallback não entra nesta fase. | TRANSCRICAO | [09:37] Larissa |
| FDD-ACE-03-HTTPS | docs/FDD.md | Critério derivado | Verificar URL HTTPS e rejeição de HTTP. | TRANSCRICAO | [09:23] Sofia |
| FDD-ACE-03-PAYLOAD | docs/FDD.md | Critério derivado | Validar teto de 64KB e erro sem truncamento. | TRANSCRICAO | [09:24] Larissa |
| FDD-ACE-03-SNAPSHOT | docs/FDD.md | Critério derivado | Snapshot não envia items. | TRANSCRICAO | [09:43] Diego |
| FDD-TEST-02 | docs/FDD.md | Validação derivada | Mudar pedido após enqueue não altera snapshot original. | TRANSCRICAO | [09:52] Larissa |
| FDD-TEST-03 | docs/FDD.md | Validação derivada | Testar assinatura/rotação com revisão de segurança. | TRANSCRICAO | [09:46] Sofia |
| ADR-001-CTX | docs/adrs/ADR-001-outbox-no-mysql.md | Contexto | A chamada externa não pode bloquear a transação nem desaparecer depois do commit. | TRANSCRICAO | [09:06] Diego |
| ADR-001-ALT-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa descartada | Envio síncrono foi descartado por bloquear pedidos e vincular rollback à disponibilidade externa ([09:04] Bruno). Redis Streams foi descartado pela infraestrutura adicional ([09:07] Larissa e Diego). | TRANSCRICAO | [09:04] Bruno |
| ADR-001-ALT-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa descartada | Segunda alternativa real discutida; trade-off descrito na seção. | TRANSCRICAO | [09:07] Diego |
| ADR-001-CONS-POS | docs/adrs/ADR-001-outbox-no-mysql.md | Consequência derivada | Atomicidade entre pedido e evento; nenhuma dependência de broker adicional. | TRANSCRICAO | [09:06] Diego |
| ADR-001-CONS-NEG | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off derivado | Mais escrita e crescimento do banco; falha na inserção da outbox implica rollback da mudança de status. | TRANSCRICAO | [09:06] Diego |
| ADR-002-CTX | docs/adrs/ADR-002-retry-e-dead-letter.md | Contexto | Clientes podem ficar indisponíveis durante manutenção; eventos não devem permanecer indefinidamente pendurados. | TRANSCRICAO | [09:17] Larissa |
| ADR-002-ALT-01 | docs/adrs/ADR-002-retry-e-dead-letter.md | Alternativa descartada | Três tentativas foram descartadas por cobrir mal manutenções de duas horas ([09:16] Diego); retry indefinido foi descartado por deixar eventos pendurados ([09:15] Diego). Marcar failed na outbox foi discutido, mas a tabela separada foi escolhida para facilitar leitura e diagnóstico ([09:18] Diego). | TRANSCRICAO | [09:16] Diego |
| ADR-002-ALT-02 | docs/adrs/ADR-002-retry-e-dead-letter.md | Alternativa descartada | Segunda alternativa real discutida; trade-off descrito na seção. | TRANSCRICAO | [09:15] Diego |
| ADR-002-CONS-POS | docs/adrs/ADR-002-retry-e-dead-letter.md | Consequência derivada | Recuperação de indisponibilidade transitória e evidência para diagnóstico. | TRANSCRICAO | [09:18] Diego |
| ADR-002-CONS-NEG | docs/adrs/ADR-002-retry-e-dead-letter.md | Trade-off derivado | Entrega pode terminar em falha; exige operação de replay e resolução da ambiguidade de contagem antes da implementação. | TRANSCRICAO | [09:18] Diego |
| ADR-003-CTX | docs/adrs/ADR-003-hmac-por-endpoint.md | Contexto | Consumidores precisam verificar origem e integridade; vazamento de uma credencial não deve comprometer todas as integrações. | TRANSCRICAO | [09:22] Sofia |
| ADR-003-ALT-01 | docs/adrs/ADR-003-hmac-por-endpoint.md | Alternativa descartada | Secret global foi explicitamente rejeitada pelo alcance de um vazamento ([09:21] Sofia). | TRANSCRICAO | [09:21] Sofia |
| ADR-003-CONS-POS | docs/adrs/ADR-003-hmac-por-endpoint.md | Consequência derivada | Validação de integridade e isolamento de credenciais entre endpoints. | TRANSCRICAO | [09:22] Sofia |
| ADR-003-CONS-NEG | docs/adrs/ADR-003-hmac-por-endpoint.md | Trade-off derivado | Clientes precisam implementar verificação e rotação; estratégia de assinatura durante sobreposição ainda requer revisão. | TRANSCRICAO | [09:22] Sofia |
| ADR-004-CTX | docs/adrs/ADR-004-at-least-once-com-event-id.md | Contexto | Uma entrega pode chegar ao consumidor e sua confirmação se perder. | TRANSCRICAO | [09:26] Larissa |
| ADR-004-ALT-01 | docs/adrs/ADR-004-at-least-once-com-event-id.md | Alternativa descartada | Exactly-once foi descartado por exigir coordenação dos dois lados e maior complexidade ([09:25] Diego). | TRANSCRICAO | [09:25] Diego |
| ADR-004-CONS-POS | docs/adrs/ADR-004-at-least-once-com-event-id.md | Consequência derivada | Retentativas podem recuperar falhas sem exigir protocolo distribuído de confirmação. | TRANSCRICAO | [09:25] Diego |
| ADR-004-CONS-NEG | docs/adrs/ADR-004-at-least-once-com-event-id.md | Trade-off derivado | Deduplicação é responsabilidade do consumidor; efeitos não idempotentes podem se repetir. | TRANSCRICAO | [09:25] Diego |
| ADR-005-CTX | docs/adrs/ADR-005-worker-separado-em-polling.md | Contexto | A API não deve depender de chamadas externas nem abrigar o ciclo de entrega. | TRANSCRICAO | [09:10] Larissa |
| ADR-005-ALT-01 | docs/adrs/ADR-005-worker-separado-em-polling.md | Alternativa descartada | Triggers foram descartadas por não notificarem processo externo no MySQL ([09:09] Diego). Rodar dentro da API foi rejeitado pelo acoplamento ao seu ciclo de reinício ([09:11] Diego). | TRANSCRICAO | [09:09] Diego |
| ADR-005-ALT-02 | docs/adrs/ADR-005-worker-separado-em-polling.md | Alternativa descartada | Segunda alternativa real discutida; trade-off descrito na seção. | TRANSCRICAO | [09:11] Diego |
| ADR-005-CONS-POS | docs/adrs/ADR-005-worker-separado-em-polling.md | Consequência derivada | Isolamento do ciclo de entrega; polling atende a expectativa de baixa latência em condições normais. | TRANSCRICAO | [09:13] Larissa |
| ADR-005-CONS-NEG | docs/adrs/ADR-005-worker-separado-em-polling.md | Trade-off derivado | Polling consome banco e adiciona espera; single-worker limita capacidade. Ordering em presença de retry e recuperação de processing precisam de definição no FDD. | TRANSCRICAO | [09:13] Larissa |
| ADR-006-CTX | docs/adrs/ADR-006-reuso-dos-padroes.md | Contexto | A aplicação já organiza domínios por camadas e centraliza validação, erros e logs. | TRANSCRICAO | [09:30] Larissa |
| ADR-006-ALT-01 | docs/adrs/ADR-006-reuso-dos-padroes.md | Alternativa plausível | Stack paralela de logging e erros é alternativa plausível, não discutida como proposta concreta; aumenta inconsistência e manutenção frente ao reuso fechado na reunião. | TRANSCRICAO | [09:29] Bruno |
| ADR-006-CONS-POS | docs/adrs/ADR-006-reuso-dos-padroes.md | Consequência derivada | Menor custo de integração e convenções conhecidas pelo time. | TRANSCRICAO | [09:30] Larissa |
| ADR-006-CONS-NEG | docs/adrs/ADR-006-reuso-dos-padroes.md | Trade-off derivado | As abstrações atuais não dão automaticamente prefixo WEBHOOK_ a Zod ou autenticação; o adaptador do módulo precisa preservar o contrato compartilhado. | TRANSCRICAO | [09:30] Larissa |
| ADR-007-CTX | docs/adrs/ADR-007-snapshot-do-evento.md | Contexto | O pedido pode mudar novamente antes da entrega do evento anterior. | TRANSCRICAO | [09:52] Larissa |
| ADR-007-ALT-01 | docs/adrs/ADR-007-snapshot-do-evento.md | Alternativa descartada | Guardar somente order_id e renderizar no envio foi descartado porque poderia refletir estado posterior ([09:51] Bruno; [09:52] Larissa). | TRANSCRICAO | [09:51] Bruno |
| ADR-007-CONS-POS | docs/adrs/ADR-007-snapshot-do-evento.md | Consequência derivada | Evento preserva o estado da transição original, inclusive em retry. | TRANSCRICAO | [09:52] Larissa |
| ADR-007-CONS-NEG | docs/adrs/ADR-007-snapshot-do-evento.md | Trade-off derivado | Dados ficam duplicados e ocupam armazenamento; o payload deve permanecer enxuto e respeitar o teto discutido. | TRANSCRICAO | [09:52] Larissa |
| ADR-002-DLQ | docs/adrs/ADR-002-retry-e-dead-letter.md | Decisão | DLQ separada escolhida em vez de failed na outbox. | TRANSCRICAO | [09:18] Diego |
| ADR-007-ID | docs/adrs/ADR-007-snapshot-do-evento.md | Decisão | UUID segue padrão do projeto. | TRANSCRICAO | [09:51] Larissa |
| FDD-SCHEMA-03-PATCH | docs/FDD.md | Compatibilidade | PATCH parcial segue updateCustomerSchema.partial(). | CODIGO | src/modules/customers/customer.schemas.ts |
| FDD-LOG-02-HTTP | docs/FDD.md | Compatibilidade | Log HTTP inclui userId e requestId; vincular evento no replay é derivação de auditoria. | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-ESC-02-STOCK | docs/FDD.md | Restrição existente | Débito PENDING→PAID; reposição no cancelamento de PAID/PROCESSING. | CODIGO | src/modules/orders/order.status.ts |
| FDD-CTX-01 | docs/FDD.md | Contexto existente | changeStatus não publica eventos; novo módulo integra o caminho crítico. | CODIGO | src/modules/orders/order.service.ts |
| FDD-NOVO-01-AUTH | docs/FDD.md | Decisão | CRUD pode usar qualquer role autenticada nesta fase. | TRANSCRICAO | [09:37] Sofia |
