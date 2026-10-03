# ADR-006 — Reuso dos padrões existentes

## Status

Aceito.

## Contexto

**ADR-006-CTX** — A aplicação já organiza domínios por camadas e centraliza validação, erros e logs.

## Decisão

**ADR-006** — Criar módulo de webhooks no padrão controller/service/repository/routes/schemas; reutilizar AppError, Zod, Pino e middleware de erro, com códigos de domínio WEBHOOK_.

## Alternativas Consideradas

**ADR-006-ALT-01** — Stack paralela de logging e erros é alternativa plausível, não discutida como proposta concreta; aumenta inconsistência e manutenção frente ao reuso fechado na reunião.

## Consequências

**ADR-006-CONS-POS** — **Positivas:** Menor custo de integração e convenções conhecidas pelo time.

**ADR-006-CONS-NEG** — **Negativas e trade-off:** As abstrações atuais não dão automaticamente prefixo WEBHOOK_ a Zod ou autenticação; o adaptador do módulo precisa preservar o contrato compartilhado.

## Referências

[TRANSCRICAO.md](../../TRANSCRICAO.md), [09:30] Larissa. Rastreabilidade: [Tracker](../TRACKER.md). Detalhamento: [FDD](../FDD.md).
- **ADR-006-COD-1** — [src/shared/errors/app-error.ts](../../src/shared/errors/app-error.ts)
- **ADR-006-COD-2** — [src/shared/logger/index.ts](../../src/shared/logger/index.ts)
- **ADR-006-COD-3** — [src/middlewares/error.middleware.ts](../../src/middlewares/error.middleware.ts)
