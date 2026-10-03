# ADR-004 — At-least-once e identificação estável

## Status

Aceito.

## Contexto

**ADR-004-CTX** — Uma entrega pode chegar ao consumidor e sua confirmação se perder.

## Decisão

**ADR-004** — Adotar a garantia **at-least-once**, assumindo duplicatas, e transmitir UUID estável em X-Event-Id para deduplicação pelo consumidor. A política limitada pode acabar em DLQ; at-least-once não promete entrega eventual a destinatário permanentemente indisponível.

## Alternativas Consideradas

**ADR-004-ALT-01** — Exactly-once foi descartado por exigir coordenação dos dois lados e maior complexidade ([09:25] Diego).

## Consequências

**ADR-004-CONS-POS** — **Positivas:** Retentativas podem recuperar falhas sem exigir protocolo distribuído de confirmação.

**ADR-004-CONS-NEG** — **Negativas e trade-off:** Deduplicação é responsabilidade do consumidor; efeitos não idempotentes podem se repetir.

## Referências

[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:26] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
