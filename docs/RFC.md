# RFC — Webhooks de Notificação de Pedidos

## Metadados

| Campo | Valor |
|---|---|
| Autor | Jakeliny |
| Status | Em revisão |
| Data | 2026-10-03 |
| Revisores | Larissa (Tech Lead), Marcos (PM), Bruno (Pedidos), Diego (Plataforma), Sofia (Segurança) |

Rastreabilidade: [Tracker](TRACKER.md). Detalhes propostos e questões em aberto estão identificados nas respectivas seções.

## Resumo executivo (TL;DR)

**RFC-PROP-01** — Adicionar webhooks outbound de mudanças de status por customer, com outbox transacional no MySQL e worker separado; o pedido não aguarda o HTTP externo.

A proposta reaproveita a stack do OMS, autentica os callbacks por HMAC e admite duplicatas com identificação estável. Entregas malsucedidas têm retry limitado e DLQ com replay ADMIN. Contratos e fluxo de construção estão no [FDD](FDD.md); motivação e critérios de produto estão no [PRD](PRD.md).

## Contexto e problema

**RFC-CTX-01** — Atlas Comercial, MaxDistribuição e Nova Cargo consultam pedidos periodicamente; desejam notificações abaixo de dez segundos e a Atlas sinalizou risco de migração.

**RFC-CTX-02** — A expectativa de latência foi esclarecida em [09:02] Marcos. A transação já altera status, histórico e estoque. Acoplar a disponibilidade externa a essa transação comprometeria o serviço de pedidos.

## Proposta técnica

**RFC-PROP-02** — Produzir snapshot de evento para endpoints ativos interessados no novo status; filtrar antes de persistir, garantindo commit ou rollback junto ao pedido.

**RFC-PROP-04** — O snapshot fechado em [09:52] Larissa mantém o significado do evento mesmo após novas mudanças. O worker consulta pendências antigas em batches pequenos a cada 2s e envia o HTTP fora da transação. **RFC-PROP-05**, **RFC-PROP-06** — Uma instância é o limite desta fase; não há garantia de ordering global nem solução de escalabilidade paralela.

**RFC-PROP-03** — HMAC-SHA256 com secret por endpoint, HTTPS obrigatório, rotação com sobreposição de 24h e X-Event-Id para deduplicação do consumidor.

Retentativas seguem os intervalos declarados na reunião, com DLQ separada e replay auditado. A contagem precisa ser esclarecida antes de codificar o scheduler. CRUD usa JWT existente; customer é explícito na requisição, pois o token representa um usuário. Replay exige ADMIN. A implementação mantém módulos, Pino, Zod, AppError e a infraestrutura Prisma.

## Alternativas consideradas

**RFC-ALT-01** — HTTP síncrono descartado: seria simples de ligar ao service, mas um destino lento prolongaria a transação e falhas externas levantariam rollback indevido.

**RFC-ALT-02** — Redis Streams descartado: acrescentaria infraestrutura e operação para um time pequeno; MySQL já disponível atende a necessidade.

**RFC-ALT-03** — Trigger descartada: MySQL não fornece o listener externo necessário; adaptações seriam mais complexas que polling de 2s.

**RFC-ALT-04** — Exactly-once descartado: exige coordenação com os consumidores; at-least-once aceita duplicatas e delega deduplicação.

## Questões em aberto

**RFC-Q-01** — Rate limiting de saída: observar volume e decidir depois; não entra nesta fase.

**RFC-Q-02** — Escala para múltiplos workers e particionamento/locks por pedido: adiados; ordering nesse cenário permanece sem definição.

**RFC-Q-03** — Arquivamento após cerca de 30 dias: mencionado como futuro; retenção e execução não foram fechadas.

**RFC-Q-04** — Contagem de retry: cinco tentativas declaradas versus cinco esperas (1m/5m/30m/2h/12h). Confirmar a interpretação com Larissa e Diego.

**RFC-Q-05** — Como assinar durante as 24h de overlap da rotação, e como tratar eventos já pendentes, não foi definido; revisão com Sofia.

**RFC-Q-06** — Ordering por pedido foi afirmada para single-worker, mas uma falha seguida de retry pode permitir ultrapassagem; confirmar semântica e impacto no objetivo de latência.

## Impacto e riscos

**RFC-RISK-01** — Outbox aumenta escrita e retenção no MySQL; índices de status/created_at e leitura em batches pequenos limitam o custo, sem incluir arquivamento nesta fase.

**RFC-RISK-02** — Destino lento e backlog podem impedir a expectativa abaixo de 10s: polling não é garantia de latência fim a fim; timeout é 10s por chamada.

A atomicidade preserva consistência, mas torna a inserção de eventos parte do sucesso da mudança de status. Duplicatas precisam ser explicitadas aos consumidores. **RFC-DEP-01**, **RFC-DEP-02** — A revisão de segurança precede deploy, com dois dias úteis reservados para Sofia ([09:46] Sofia). O planejamento é de três sprints ([09:47] Larissa).

## Decisões relacionadas

- [ADR-001 — Outbox no MySQL](adrs/ADR-001-outbox-no-mysql.md)
- [ADR-002 — Retry limitado e DLQ persistida](adrs/ADR-002-retry-e-dead-letter.md)
- [ADR-003 — HMAC-SHA256 e secret por endpoint](adrs/ADR-003-hmac-por-endpoint.md)
- [ADR-004 — At-least-once e identificação estável](adrs/ADR-004-at-least-once-com-event-id.md)
- [ADR-005 — Worker separado com polling](adrs/ADR-005-worker-separado-em-polling.md)
- [ADR-006 — Reuso dos padrões existentes](adrs/ADR-006-reuso-dos-padroes.md)
- [ADR-007 — Snapshot do evento na inserção](adrs/ADR-007-snapshot-do-evento.md)
