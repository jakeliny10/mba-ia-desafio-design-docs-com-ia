# ADR-004 — At-least-once e identificação estável

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
Uma entrega pode chegar ao consumidor e sua confirmação se perder.

## Decisão
**ADR-004** — Assumir duplicatas e transmitir UUID estável em X-Event-Id para deduplicação pelo consumidor. A política limitada pode acabar em DLQ; at-least-once não promete entrega eventual a destinatário permanentemente indisponível.

## Alternativas Consideradas
Exactly-once foi descartado por exigir coordenação dos dois lados e maior complexidade ([09:25] Diego).

## Consequências
**Positivas:** Retentativas podem recuperar falhas sem exigir protocolo distribuído de confirmação.

**Negativas e trade-off:** Deduplicação é responsabilidade do consumidor; efeitos não idempotentes podem se repetir.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:26] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
