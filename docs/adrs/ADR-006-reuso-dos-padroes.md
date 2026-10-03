# ADR-006 — Reuso dos padrões existentes

## Status
Aceito na reunião; registro documental elaborado em 2026-10-03. As pendências não constituem decisões adicionais aceitas.

## Contexto
A aplicação já organiza domínios por camadas e centraliza validação, erros e logs.

## Decisão
**ADR-006** — Criar módulo de webhooks no padrão controller/service/repository/routes/schemas; reutilizar AppError, Zod, Pino e middleware de erro, com códigos de domínio WEBHOOK_.

## Alternativas Consideradas
Stack paralela de logging e erros é alternativa plausível, não discutida como proposta concreta; aumenta inconsistência e manutenção frente ao reuso fechado na reunião.

## Consequências
**Positivas:** Menor custo de integração e convenções conhecidas pelo time.

**Negativas e trade-off:** As abstrações atuais não dão automaticamente prefixo WEBHOOK_ a Zod ou autenticação; o adaptador do módulo precisa preservar o contrato compartilhado.

## Referências
[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:30] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
- [src/shared/errors/app-error.ts](../../src/shared/errors/app-error.ts)
- [src/shared/logger/index.ts](../../src/shared/logger/index.ts)
- [src/middlewares/error.middleware.ts](../../src/middlewares/error.middleware.ts)
