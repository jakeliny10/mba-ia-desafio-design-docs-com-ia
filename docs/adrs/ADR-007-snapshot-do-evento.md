# ADR-007 — Snapshot do evento na inserção

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
**ADR-007-CTX** — O pedido pode mudar novamente antes da entrega do evento anterior.

## Decisão
**ADR-007** — Persistir payload já renderizado no momento da inserção; **ADR-007-ID** — usar UUID conforme o padrão existente confirmado na reunião.

## Alternativas Consideradas
**ADR-007-ALT-01** — Guardar somente order_id e renderizar no envio foi descartado porque poderia refletir estado posterior ([09:51] Bruno; [09:52] Larissa).

## Consequências
**ADR-007-CONS-POS** — **Positivas:** Evento preserva o estado da transição original, inclusive em retry.

**ADR-007-CONS-NEG** — **Negativas e trade-off:** Dados ficam duplicados e ocupam armazenamento; o payload deve permanecer enxuto e respeitar o teto discutido.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:52] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
- **ADR-007-COD-1** — [prisma/schema.prisma](../../prisma/schema.prisma)
