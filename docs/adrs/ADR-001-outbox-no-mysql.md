# ADR-001 — Outbox no MySQL

## Status

Aceito.

## Contexto

**ADR-001-CTX** — A chamada externa não pode bloquear a transação nem desaparecer depois do commit.

## Decisão

**ADR-001** — Registrar o evento na mesma transação SQL de status, histórico e estoque, usando o MySQL existente.

## Alternativas Consideradas

**ADR-001-ALT-01**, **ADR-001-ALT-02** — Envio síncrono foi descartado por bloquear pedidos e vincular rollback à disponibilidade externa ([09:04] Bruno). Redis Streams foi descartado pela infraestrutura adicional ([09:07] Larissa e Diego).

## Consequências

**ADR-001-CONS-POS** — **Positivas:** Atomicidade entre pedido e evento; nenhuma dependência de broker adicional.

**ADR-001-CONS-NEG** — **Negativas e trade-off:** Mais escrita e crescimento do banco; falha na inserção da outbox implica rollback da mudança de status.

## Referências

[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:06] Diego. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
- **ADR-001-COD-1** — [src/modules/orders/order.service.ts](../../src/modules/orders/order.service.ts)
- **ADR-001-COD-2** — [prisma/schema.prisma](../../prisma/schema.prisma)
