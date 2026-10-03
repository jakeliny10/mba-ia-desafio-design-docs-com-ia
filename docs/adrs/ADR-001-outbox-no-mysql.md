# ADR-001 — Outbox no MySQL

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
A chamada externa não pode bloquear a transação nem desaparecer depois do commit.

## Decisão
**ADR-001** — Registrar o evento na mesma transação SQL de status, histórico e estoque, usando o MySQL existente.

## Alternativas Consideradas
Envio síncrono foi descartado por bloquear pedidos e vincular rollback à disponibilidade externa ([09:04] Bruno). Redis Streams foi descartado pela infraestrutura adicional ([09:07] Larissa e Diego).

## Consequências
**Positivas:** Atomicidade entre pedido e evento; nenhuma dependência de broker adicional.

**Negativas e trade-off:** Mais escrita e crescimento do banco; falha na inserção da outbox implica rollback da mudança de status.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:06] Diego. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
- [src/modules/orders/order.service.ts](../../src/modules/orders/order.service.ts)
- [prisma/schema.prisma](../../prisma/schema.prisma)
