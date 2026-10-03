# PRD — Webhooks de Notificação de Pedidos

Este documento distingue **decisão fechada** na reunião, **proposta derivada** para revisão e **pendência** sem definição. Exemplos são ilustrativos, não dados de produção. IDs remetem ao [Tracker](TRACKER.md). A reunião não informa data completa; a data dos documentos é a de elaboração, não a da decisão.

## Resumo e contexto da feature
**PRD-CTX-01** — OMS passa a oferecer notificações outbound sobre mudança de status, permitindo integrações B2B orientadas por notificações.

## Problema e motivação
**PRD-PROB-01** — Atlas Comercial, MaxDistribuição e Nova Cargo fazem polling de pedidos, com integração lenta e cara; Atlas sinalizou possível migração sem entrega até o prazo solicitado.

## Público-alvo e cenários de uso
**PRD-PUB-01** — Clientes B2B integrando seus sistemas ao OMS; usuários autenticados representam esses clientes, sem customer implícito no JWT.

**PRD-USO-01** — Consumidor escolhe receber apenas SHIPPED e DELIVERED, e consulta detalhes do pedido pela API quando necessário.

O cenário de consulta posterior deriva de [09:43] Diego. Operação administrativa recupera notificações que esgotaram retentativas, e o cliente consulta entregas para diagnosticar falhas.

## Objetivos e métricas de sucesso
**PRD-MET-01** — Notificação em menos de 10 segundos para destinatário disponível; medir intervalo entre mudança confirmada e recepção no cenário saudável.

Meta quantitativa de produto, não SLA incondicional. Percentil, carga e janela de avaliação não foram definidos. Medir com receptor controlado e depois observar operação; atraso de retries não representa o cenário saudável.

**PRD-MET-02** — Planejamento estimado de três sprints, incluindo revisão de segurança; Atlas pede fim de novembro, sem ano especificado na transcrição.

## Escopo
### Incluso
CRUD por cliente, filtro de status, entrega assinada e assíncrona, histórico, rotação de secret, retries e replay administrativo. Detalhes arquiteturais ficam no RFC e especificações no FDD.

### Fora de escopo

**PRD-FORA-01** — Email de aviso/fallback adiado para uma possível próxima fase.

**PRD-FORA-02** — Dashboard visual excluído desta entrega; projeto separado de frontend.

**PRD-FORA-03** — Rate limiting de saída adiado para observação e decisão posterior.

**PRD-FORA-04** — Múltiplos workers/particionamento e ordering global não são compromisso desta fase.

**PRD-FORA-05** — Arquivamento de eventos entregues mencionado como futuro, fora desta feature.

## Requisitos funcionais

**PRD-FR-01** — Cadastrar webhook por customer com URL, secret gerada pelo servidor e lista de status desejados.

**PRD-FR-02** — Receber customer explicitamente no body ou path; não derivar do JWT.

**PRD-FR-03** — Editar configuração de webhook.

**PRD-FR-04** — Remover configuração de webhook.

**PRD-FR-05** — Listar webhooks de um customer.

**PRD-FR-06** — Filtrar pelo status na inserção; não criar evento se nenhum cadastro quiser recebê-lo.

**PRD-FR-07** — Exibir histórico com sucesso/falha, payload, resposta e tempo de resposta, incluindo consulta aos últimos 100 envios.

**PRD-FR-08** — Rotacionar secret pela API, mantendo a antiga válida por 24 horas em paralelo.

**PRD-FR-09** — Persistir falhas esgotadas em DLQ e permitir replay manual por endpoint administrativo.

**PRD-FR-10** — Exigir ADMIN para replay e registrar quem executou a operação.

**PRD-FR-11** — Permitir CRUD de configuração a qualquer role autenticada nesta fase.

**PRD-FR-12** — Informar event_id estável ao consumidor para deduplicar notificações repetidas.

## Requisitos não funcionais

**PRD-NFR-01** — Chamadas externas não bloqueiam a transação de pedidos; evento e mudança de status persistem juntos.

**PRD-NFR-02** — HTTPS obrigatório, recusando cadastro de URL HTTP.

**PRD-NFR-03** — Autenticar origem/integridade por HMAC-SHA256 com secret única por endpoint.

**PRD-NFR-04** — Limitar payload a 64KB e retornar erro em vez de truncar; unidade exata a confirmar.

**PRD-NFR-05** — Admitir duplicatas na garantia at-least-once; consumidor deduplica por X-Event-Id.

**PRD-NFR-06** — Timeout de 10 segundos por chamada do worker; cliente lento é tratado como falha.

**PRD-NFR-07** — Usar worker separado com polling de 2 segundos e ordenação limitada ao pedido no cenário single-worker.

## Decisões e trade-offs principais
**PRD-DEC-01** — MySQL/outbox reduz infraestrutura e preserva atomicidade; polling adiciona espera e operação single-worker limita capacidade.

**PRD-DEC-02** — Retry limitado evita eventos pendurados para sempre, mas indisponibilidade longa pode terminar em DLQ e intervenção manual.

Decisões detalhadas: [ADRs](RFC.md#decisões-relacionadas). A contagem dos retries e ordering sob falha estão abertas no RFC; não se promete exactly-once nem entrega eventual ilimitada.

## Dependências
**PRD-DEP-01** — Clientes implementam validação de assinatura e deduplicação; integração e documentação para consumidores são necessárias.

**PRD-DEP-02** — Reservar ao menos dois dias úteis para revisão de HMAC e geração de secret por Sofia antes do deploy.

## Riscos e mitigação
Probabilidades abaixo são avaliações qualitativas derivadas para planejamento, **não estimativas declaradas na reunião**. Não há dados para quantificar frequência.

| ID | Risco | Probabilidade estimada | Impacto | Mitigação derivada | Origem |
|---|---|---|---|---|---|
| PRD-RISK-01 | Cliente indisponível ou lento | Média | Atraso/falha de notificação | Timeout, retries e DLQ com replay | [09:15] Diego |
| PRD-RISK-02 | Vazamento de secret | Média | Falsificação de notificações daquele endpoint | Secret individual, rotação e revisão de segurança | [09:22] Sofia |
| PRD-RISK-03 | Duplicação de entrega | Média | Efeito duplicado no consumidor | event_id e orientação de deduplicação | [09:24] Diego |


## Critérios de aceitação

**PRD-ACE-01** — Usuário autenticado cria, edita, remove e lista cadastros; cliente e filtro são explícitos.

**PRD-ACE-02** — Mudança elegível produz notificação em <10s em cenário saudável e sem bloquear pedido por HTTP.

**PRD-ACE-03** — Histórico permite ver resultado, payload, resposta e duração dos últimos envios.

**PRD-ACE-04** — Replay rejeita OPERATOR e aceita ADMIN com identidade registrada.

**PRD-ACE-05** — URL HTTP é recusada; callback assinado; rotação mantém transição de 24h após definir contrato técnico.

## Estratégia de testes e validação
**PRD-TEST-01** — Validar integração ponta a ponta entre mudança de status, filtro e receptor HTTP, cobrindo sucesso, indisponibilidade e replay; incluir revisão de segurança no fechamento.

Usar receptor controlado para medir latência e comparar cadastro/filtro/histórico. Exercitar duplicatas e orientação ao consumidor. O plano técnico de atomicidade, timeout, HMAC e rotação está no [FDD](FDD.md#critérios-de-aceite-técnicos-e-estratégia-de-validação). Nenhum teste da feature foi executado, pois não há implementação nesta entrega.
