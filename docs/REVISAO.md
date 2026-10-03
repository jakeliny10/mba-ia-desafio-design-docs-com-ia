# Revisão crítica contra o enunciado

Revisão elaborada em 2026-10-03 a pedido do aluno. Referência: [enunciado original](ENUNCIADO.md), copiado sem alteração do README no commit base `e7f6311`. Os critérios abaixo transcrevem os 31 itens da checklist original; a evidência identifica onde verificar cada um.

## Checklist de critérios de aceite

“Atendido documentalmente” significa conteúdo presente e confrontado com as fontes disponíveis; não é certificação do professor, aprovação dos revisores ou garantia de comportamento de código ainda não implementado.

| ID | Grupo | Critério do enunciado | Resultado | Evidência |
|---|---|---|---|---|
| C01 | PRD | Arquivo existe e está em Markdown | Atendido documentalmente | [PRD.md](PRD.md) existe e é Markdown. |
| C02 | PRD | Contém todas as seções obrigatórias listadas no requisito 1 | Atendido documentalmente | As 12 seções do requisito 1 estão presentes, incluindo as duas subseções de escopo. |
| C03 | PRD | Identifica no mínimo 8 requisitos funcionais discutidos na reunião | Atendido documentalmente | PRD-FR-01 a PRD-FR-12: 12 RFs; confrontados com 09:18, 09:21, 09:25 e 09:31–09:37. |
| C04 | PRD | Inclui pelo menos 1 objetivo com métrica e meta quantitativa | Atendido documentalmente | PRD-MET-01: <10s; origem 09:02 Marcos. Medição fim a fim em cenário saudável; sem percentil inventado. |
| C05 | PRD | Seção "Fora de escopo" lista pelo menos 2 itens explicitamente descartados ou adiados na reunião | Atendido documentalmente | PRD-FORA-01 a 05: email, dashboard, rate limiting, escala/ordering global e arquivamento. |
| C06 | PRD | Seção "Riscos" inclui pelo menos 2 riscos com probabilidade, impacto e mitigação | Atendido documentalmente | PRD-RISK-01 a 03: probabilidade descrita como possível/frequência não medida, impacto e mitigação. Sem escala arbitrária de probabilidade. |
| C07 | RFC | Arquivo existe e está em Markdown | Atendido documentalmente | [RFC.md](RFC.md) existe e é Markdown. |
| C08 | RFC | Contém todas as seções obrigatórias listadas no requisito 2 | Atendido documentalmente | Metadados, TL;DR, contexto, proposta, alternativas, questões, impacto/riscos e links de decisões presentes. |
| C09 | RFC | Seção "Alternativas consideradas" lista pelo menos 2 alternativas descartadas na reunião, cada uma com o trade-off que motivou o descarte | Atendido documentalmente | RFC-ALT-01 a 04: síncrono, Redis Streams, triggers e exactly-once; trade-offs explicitados. |
| C10 | RFC | Seção "Questões em aberto" lista pelo menos 2 pontos adiados ou não decididos na reunião | Atendido documentalmente | RFC-Q-01 a 03 são pontos da reunião adiados: rate limiting, escala e arquivamento. Q-04 a 06 identificam ambiguidades derivadas; não se confundem com adiamentos explícitos. |
| C11 | RFC | Referencia, com link, pelo menos 2 ADRs do pacote | Atendido documentalmente | Sete links locais para ADRs; arquivos e âncoras verificados. |
| C12 | FDD | Arquivo existe e está em Markdown | Atendido documentalmente | [FDD.md](FDD.md) existe e é Markdown. |
| C13 | FDD | Contém todas as seções obrigatórias listadas no requisito 3 | Atendido documentalmente | As seções do requisito 3, integração e critérios de aceite estão presentes; modelagem, validações e plano de testes dão suporte à implementação. |
| C14 | FDD | Seção "Contratos públicos" inclui pelo menos 4 endpoints HTTP com payload de exemplo (request e response) e status codes | Atendido documentalmente | Sete rotas de gestão; POST cadastro, PATCH, rotação e replay têm JSON de request/response e status. GET/DELETE têm exemplos sem body, conforme semântica HTTP. |
| C15 | FDD | Matriz de erros usa códigos com prefixo `WEBHOOK_` | Atendido documentalmente | FDD-ERRO-01 a 07 identificam sete códigos WEBHOOK_*; erro compartilhado NOT_FOUND para customer ausente é documentado separadamente, sem fingir prefixo automático. |
| C16 | FDD | Seção "Integração com o sistema existente" referencia pelo menos 4 caminhos de arquivo reais do código base | Atendido documentalmente | FDD-INT-01 a 15 apontam 15 arquivos reais; origem conferida no Git. Arquivos futuros são nomeados como propostos, não existentes. |
| C17 | FDD | Seção "Observabilidade" cita métricas, logs e tracing | Atendido documentalmente | FDD-METRICA-01 a 05; FDD-OBS-01/LOG-02; FDD-TRACE-01. Tracing/correlação tem base no requestId existente; stack de spans não foi decidida. |
| C18 | ADRs | Pasta `docs/adrs/` contém entre 5 e 8 arquivos no formato `ADR-NNN-titulo-em-kebab-case.md` | Atendido documentalmente | Sete arquivos ADR-001 a 007, todos no padrão ADR-NNN-kebab-case.md. |
| C19 | ADRs | Cada ADR contém as seções Status, Contexto, Decisão, Alternativas Consideradas, Consequências | Atendido documentalmente | Todos contêm Status, Contexto, Decisão, Alternativas Consideradas e Consequências positivas/negativas. |
| C20 | ADRs | O conjunto cobre pelo menos 5 das 6 decisões principais listadas no requisito 4 | Atendido documentalmente | ADRs 001–006 cobrem as seis decisões principais; ADR-007 registra snapshot. ADR-002 preserva literalmente cinco tentativas, sem escolher a contagem ambígua. |
| C21 | ADRs | Pelo menos 1 ADR referencia explicitamente arquivos, módulos ou classes do código base | Atendido documentalmente | ADRs 001, 006 e 007 referenciam arquivos reais do projeto, com registros CODIGO no tracker. |
| C22 | Tracker | Arquivo existe e segue o formato de tabela definido no requisito 5 | Atendido documentalmente | [TRACKER.md](TRACKER.md): tabela com as seis colunas exatas exigidas. |
| C23 | Tracker | Pelo menos 80% dos itens identificáveis dos documentos têm linha correspondente | Atendido documentalmente | 222 itens do inventário explícito com 222 linhas correspondentes (100% dos IDs). Revisão semântica desdobrou erros, métricas, alternativas, consequências e fontes compostas; leitura complementar descrita abaixo. |
| C24 | Tracker | Pelo menos 70% das linhas têm Fonte = `TRANSCRICAO` com timestamp válido no formato `[hh:mm] Nome` | Atendido documentalmente | 187/222 = 84,2% de linhas TRANSCRICAO; todos os timestamps/falantes conferidos literalmente na fonte. |
| C25 | Tracker | Pelo menos 5 linhas têm Fonte = `CODIGO` com caminho de arquivo real | Atendido documentalmente | 35 linhas CODIGO; todas apontam arquivos existentes. O enunciado exige pelo menos cinco. |
| C26 | README | Contém todas as seções obrigatórias listadas no requisito 6 | Atendido documentalmente | As seis seções obrigatórias estão presentes, com dois parágrafos em Sobre o desafio. |
| C27 | README | Lista pelo menos 1 ferramenta de IA utilizada | Atendido documentalmente | Codex listado com seu papel; playbook e ferramentas auxiliares são identificados sem tratá-los como modelos adicionais. |
| C28 | README | Mostra pelo menos 2 prompts customizados em blocos de código | Atendido documentalmente | Três prompts customizados em blocos de código, rotulados como instruções formuladas/adaptadas. |
| C29 | README | Descreve pelo menos 2 iterações ou ajustes concretos feitos durante a produção | Atendido documentalmente | Correções concretas documentadas: tracker amplo, probabilidades sem base, fontes compostas, tracing e relato dos ciclos. Duas rodadas de redação e quatro etapas internas descritas com transparência. |
| C30 | Consistência geral | Nenhum requisito, decisão ou restrição registrada nos documentos contradiz a transcrição ou o código | Atendido documentalmente | Decisões foram confrontadas com a transcrição e pontos de integração com o código; inferências/pendências são rotuladas e não substituem decisões fechadas. |
| C31 | Consistência geral | Nenhum arquivo de código mencionado nos documentos é inexistente no repositório | Atendido documentalmente | Caminhos existentes conferidos; src/worker.ts e src/modules/webhooks/ aparecem explicitamente como novos/propostos, conforme a própria reunião. |

## Requisitos adicionais e limites de verificação

| Exigência | Resultado verificável |
|---|---|
| Fork público do repositório base | API GitHub confirmou isFork=true, isPrivate=false e parent=devfullcycle/mba-ia-desafio-design-docs-com-ia. |
| Entrega apenas documental | Comparação integral contra e7f6311 mostra mudanças exclusivamente em README.md e docs/. Todos os arquivos originais fora desse conjunto são preservados byte a byte. |
| Transcrição não alterada | Git e comparação de bytes confirmam identidade com a base. |
| RFC com 2 a 4 páginas | RFC é conciso, sem contratos/algoritmos de implementação. Markdown não define paginação física; o comprimento em palavras é informado pela auditoria. Sem fonte/margens/template de exportação, não se declara contagem física exata. |
| Formato apresentado no curso | Todas as seções mínimas fornecidas no enunciado foram seguidas. Templates adicionais do curso não foram disponibilizados; equivalência a eles não pode ser certificada. |
| RFC submetido à equipe para revisão | Documento em revisão, com cinco participantes como revisores indicados, disponível no fork. Não há registro de envio, comentário ou aprovação desses participantes; não se simula tal atividade. |
| Implementação acionável | FDD apresenta fluxo transacional, schema lógico, mapeamento de campos, contratos propostos, validações, erros, integração e plano de testes. Engenharia pode iniciar o módulo e integração; os detalhes inconsistentes/ausentes da reunião precisam de revisão antes dos componentes afetados. |
| Não inventar requisitos/decisões/restrições | Detalhes de implementação derivados são identificados como propostas, exemplos fictícios não são requisitos e lacunas ficam explícitas. Não foram adicionados email, dashboard, broker, multi-worker ou rate limiting ao escopo aprovado. |

## Cobertura e auditoria semântica
O inventário do tracker possui **222 itens**, **187 TRANSCRICAO (84,2%)** e **35 CODIGO**. Cada ID aparece no documento indicado; cada item inventariado tem linha própria. IDs agrupados no mesmo parágrafo separam fontes complementares. Campos de um único payload pertencem ao contrato, com mapeamento de origem; não se contam UUIDs fictícios como novos itens.

A contagem de 100% é relativa ao inventário explícito, não uma prova matemática de que nenhuma frase poderia ser decomposta de outra forma. Para o requisito de 80%, a revisão também percorreu os parágrafos técnicos, linhas de erros/métricas/riscos e seções de cada ADR, adicionando origens antes ausentes. Metadados, índices, checklist, enunciado preservado e relato do processo não são requisitos novos da feature.

Foram corrigidos os pontos seguintes:

- A primeira checagem validava arquivos/IDs, mas não sustentava sozinha a cobertura semântica. O tracker agora inclui unidades separadas de alternativas e consequências dos ADRs, erros e métricas.
- A classificação de probabilidade “média” sem base foi substituída por probabilidade possível e frequência não medida, com evidência da reunião.
- O prazo de novembro foi separado dos três sprints; timeout, headers e revisão de segurança ganharam fontes complementares.
- O prefixo real /api/v1 e o requestId existente foram distinguidos de detalhes propostos de callback e tracing; o padrão de PATCH parcial foi vinculado ao schema de clientes, que efetivamente usa partial().
- O README passou a relatar duas rodadas de redação e quatro etapas internas, sem alegar ciclos independentes ou feedback humano inexistente.

## Pendências legítimas da fonte

1. **Retry:** a reunião declara cinco tentativas e cinco intervalos. Preservar ambos e pedir interpretação evita inventar cinco retries adicionais ou ignorar 12h. A soma de cinco esperas é 14h36m.
2. **Ordering:** single-worker/created_at foi fechado, mas retries podem permitir ultrapassagem. Não substituir a decisão por multi-worker ou promessa de ordering global; revisar comportamento sob falha.
3. **Rotação:** secret anterior válida por 24h está fechada; representação da assinatura e emissão durante overlap não foram especificadas.
4. **Operação:** recuperação de processing, classificação de respostas HTTP, destino de pendências após CRUD e replay repetido não foram definidos. São lacunas do desenho, não funcionalidades extras já aprovadas.
5. **Observabilidade:** Pino e auditoria foram decididos; nomes de métricas e correlação assíncrona são derivações para validar a feature. Não há stack de métricas/tracing escolhida na reunião.

Nenhuma dessas lacunas justifica alterar TRANSCRICAO.md ou fabricar aprovação. Testes de implementação não foram executados porque a entrega não contém implementação.

## Resultado da verificação final

- 31 critérios de aceite inventariados e vinculados a evidências.
- 222 IDs únicos, presentes nos documentos indicados e com linha correspondente no tracker.
- 187 fontes TRANSCRICAO (84,2%), com timestamp/falante existentes; 35 fontes CODIGO, com caminhos reais.
- 11 blocos de exemplos JSON analisados sem erro de sintaxe.
- Links relativos e âncoras de seções conferidos; tabelas e blocos Markdown conferidos.
- 59 arquivos originais protegidos comparados byte a byte contra e7f6311, todos idênticos.
- Enunciado preservado como cópia exata do README da base.
- Fork público e origem no repositório devfullcycle confirmados pela API GitHub.
- RFC com 780 palavras, sem duplicar os contratos detalhados do FDD; paginação física depende da exportação.

A verificação automatizada foi executada com Python/Git e a revisão semântica pelo Codex, a pedido do aluno. Checagens estruturais passaram; os limites e pendências descritos acima permanecem explícitos.
