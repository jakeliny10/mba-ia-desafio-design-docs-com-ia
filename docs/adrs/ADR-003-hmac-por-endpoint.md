# ADR-003 — HMAC-SHA256 e secret por endpoint

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
Consumidores precisam verificar origem e integridade; vazamento de uma credencial não deve comprometer todas as integrações.

## Decisão
**ADR-003** — Assinar o corpo com HMAC-SHA256, usar secret única por endpoint e suportar rotação com validade paralela da antiga por 24 horas.

## Alternativas Consideradas
Secret global foi explicitamente rejeitada pelo alcance de um vazamento ([09:21] Sofia).

## Consequências
**Positivas:** Validação de integridade e isolamento de credenciais entre endpoints.

**Negativas e trade-off:** Clientes precisam implementar verificação e rotação; estratégia de assinatura durante sobreposição ainda requer revisão.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:22] Sofia. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
