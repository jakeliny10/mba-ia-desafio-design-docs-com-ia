# ADR-005 — Worker separado com polling

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
**ADR-005-CTX** — A API não deve depender de chamadas externas nem abrigar o ciclo de entrega.

## Decisão
**ADR-005** — Executar um único worker em processo Node separado, consultando pendências a cada dois segundos; usar o mesmo banco, com PrismaClient próprio por processo. Ordenação é limitada ao pedido e ao cenário single-worker.

## Alternativas Consideradas
**ADR-005-ALT-01**, **ADR-005-ALT-02** — Triggers foram descartadas por não notificarem processo externo no MySQL ([09:09] Diego). Rodar dentro da API foi rejeitado pelo acoplamento ao seu ciclo de reinício ([09:11] Diego).

## Consequências
**ADR-005-CONS-POS** — **Positivas:** Isolamento do ciclo de entrega; polling atende a expectativa de baixa latência em condições normais.

**ADR-005-CONS-NEG** — **Negativas e trade-off:** Polling consome banco e adiciona espera; single-worker limita capacidade. Ordering em presença de retry e recuperação de processing precisam de definição no FDD.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:10] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
