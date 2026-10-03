# ADR-002 — Retry limitado e DLQ persistida

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
**ADR-002-CTX** — Clientes podem ficar indisponíveis durante manutenção; eventos não devem permanecer indefinidamente pendurados.

## Decisão
**ADR-002** — Registrar a decisão literal de **cinco tentativas**, com intervalos declarados de 1m/5m/30m/2h/12h e DLQ em tabela separada, com replay manual administrativo. A interpretação da contagem permanece pendente: cinco tentativas totais não comportam cinco esperas após uma primeira tentativa.

## Alternativas Consideradas
**ADR-002-ALT-01**, **ADR-002-ALT-02** — Três tentativas foram descartadas por cobrir mal manutenções de duas horas ([09:16] Diego); retry indefinido foi descartado por deixar eventos pendurados ([09:15] Diego). **ADR-002-DLQ** — Marcar failed na outbox foi discutido, mas a tabela separada foi escolhida para facilitar leitura e diagnóstico ([09:18] Diego).

## Consequências
**ADR-002-CONS-POS** — **Positivas:** Recuperação de indisponibilidade transitória e evidência para diagnóstico.

**ADR-002-CONS-NEG** — **Negativas e trade-off:** Entrega pode terminar em falha; exige operação de replay e resolução da ambiguidade de contagem antes da implementação.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:17] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
