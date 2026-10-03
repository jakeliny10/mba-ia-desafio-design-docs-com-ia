# Da reunião ao documento: webhooks do OMS

## Sobre o desafio
Esta entrega transforma a reunião sobre notificações de pedidos e a leitura de um OMS Node.js/TypeScript em um pacote de design docs rastreável. O objetivo é permitir que engenharia revise a proposta e inicie implementação sem atribuir à reunião decisões que ela não tomou.

A entrega é exclusivamente documental. A transcrição, src/, prisma/, tests/ e configurações foram preservados. Enunciado preservado: [docs/ENUNCIADO.md](docs/ENUNCIADO.md). Código base: [repositório do desafio](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia). Fork: [jakeliny10/mba-ia-desafio-design-docs-com-ia](https://github.com/jakeliny10/mba-ia-desafio-design-docs-com-ia).

## Ferramentas de IA utilizadas
- Codex: leitura da transcrição e do código, identificação de decisões e lacunas, redação e revisão dos documentos.
- Skill [fullcycle-marketplace](https://github.com/devfullcycle/claude-mkt-place), disponível localmente como playbook: apoio à separação entre decisão arquitetural e implementação, estrutura MADR e referências. Adaptado ao formato exigido pelo desafio; comandos Claude não foram executados literalmente.
- Python e Git como ferramentas auxiliares, sem IA: gravação Markdown, auditoria de IDs/fontes/links e verificação de alterações.

## Workflow adotado
1. Clone do fork como projeto irmão dentro de mba/; inventário dos arquivos e leitura integral da transcrição.
2. Mapeamento focado em pedidos, Prisma, JWT, composição de rotas, erros, validação e logs. Identificação das seis decisões principais e do snapshot adicional.
3. ADRs primeiro, RFC conciso em seguida, FDD com fluxos/contratos e integração, PRD consolidando produto. Tracker montado junto aos IDs de cada documento.
4. Revisão de ambiguidades e da diferença entre decisão fechada, proposta derivada e pendência; README por último.
5. Auditoria documental de seções, IDs, fontes, timestamps, paths e links, e comparação Git para preservar arquivos protegidos. Não executar testes da feature sem implementação.

## Prompts customizados
Os prompts abaixo são instruções customizadas formuladas/adaptadas para orientar esta produção e sua revisão. Registram o método de trabalho; não são logs integrais de conversas nem alegações de feedback dos participantes da reunião.

```text
Leia TRANSCRICAO.md e o código do OMS. Separe decisão fechada, requisito
explícito, alternativa descartada, item adiado e lacuna. Para cada item,
registre timestamp e falante ou arquivo real. Não derive customer_id do JWT
sem verificar AuthUser. Identifique contradições antes de gerar documentos.
```

```text
Produza ADRs de decisão, RFC em nível de arquitetura e FDD de implementação,
sem duplicar contratos no RFC. Marque detalhes não decididos como propostas
para revisão. Confronte cinco tentativas com cinco intervalos de retry,
ordering com retries e a rotação HMAC com overlap. Não resolva silenciosamente
nenhuma dessas lacunas. Gere tracker por ID e preserve todos os arquivos de código.
```

```text
Revise cada caminho existente, prefixo de API e envelope de erro contra o código.
Confira cobertura do tracker e timestamps. Remova garantias não sustentadas:
polling não garante sozinho latência fim a fim, logs correlacionados não são
tracing distribuído e prefixo WEBHOOK_ não é aplicado automaticamente a Zod.
```

## Iterações e ajustes
Houve duas rodadas principais de redação: produção inicial e revisão crítica solicitada pelo aluno. Dentro delas, o trabalho percorreu quatro etapas: contextualização, redação, checagem estrutural e revisão semântica. Não se afirma que houve três a cinco gerações independentes nem aprovação da equipe.

1. Produção inicial: os documentos registraram a ambiguidade de cinco tentativas versus cinco intervalos, os limites de ordering sob retry e a ausência de customer no JWT. Esses pontos foram confrontados com a transcrição e o código, preservando decisões fechadas sem preencher lacunas silenciosamente.
2. Ajuste concreto do tracker: a primeira versão tinha 115 registros, mas grupos como erros, métricas e alternativas dos ADRs eram amplos demais. Na revisão crítica, foram desdobrados em itens com fontes específicas; cada erro e métrica passou a ter ID próprio. A auditoria inicial verificava presença de IDs e arquivos, mas não demonstrava por si só cobertura semântica de 80%.
3. Ajuste concreto de riscos e fontes: a primeira versão dizia probabilidade “média” sem evidência para essa classificação. A revisão substituiu-a por possibilidade reconhecida e frequência não medida, com antecedentes da reunião. Separou também a fonte do prazo de novembro da estimativa de três sprints, e o source de PATCH parcial da paginação.
4. Ajuste concreto de integração e processo: o tracing passou a relacionar o requestId real da API aos IDs de evento, sem inventar stack distribuída; GET/DELETE ganharam exemplos HTTP sem bodies fictícios. O README deixou de apresentar etapas internas como ciclos independentes. O enunciado original foi preservado e os 31 critérios receberam evidência na checklist.

Os ajustes foram produzidos pelo Codex sob a orientação do aluno, incluindo o pedido explícito de revisão criteriosa. Não há alegação de revisão de conteúdo pelos participantes. O RFC está disponível para revisão no fork; não foi enviado diretamente aos revisores nem aprovado por eles.

## Como navegar a entrega
Ordem sugerida para um leitor novo:

1. [PRD](docs/PRD.md): problema, público, escopo e critérios de produto.
2. [RFC](docs/RFC.md): visão arquitetural, alternativas e questões em aberto.
3. [ADRs](docs/adrs/README.md): sete decisões isoladas com trade-offs.
4. [FDD](docs/FDD.md): fluxos, contratos propostos, erros e integração com arquivos reais.
5. [Tracker](docs/TRACKER.md): origem de cada ID.
6. [Transcrição preservada](TRANSCRICAO.md): fonte primária da reunião.
7. [Revisão final](docs/REVISAO.md): critérios, evidências e limites.
8. [Enunciado original](docs/ENUNCIADO.md): cópia exata do README da base.

As pendências do FDD devem ser resolvidas na revisão técnica antes dos respectivos componentes serem codificados. Esta entrega não altera nem implementa a aplicação.

## Validação da entrega
A revisão confirmou sete ADRs cobrindo as seis decisões principais, doze requisitos funcionais, sete endpoints de gestão (quatro com exemplos JSON de request/response) e sete códigos WEBHOOK_* na matriz, além do erro compartilhado de customer ausente. O tracker possui **222 registros**, dos quais **187 (84,2%)** usam TRANSCRICAO e **35** usam CODIGO.

Foram conferidos seções obrigatórias, timestamps/falantes, origem semântica das decisões, arquivos reais, links/âncoras locais, tabelas, JSON dos exemplos e diferença Git contra o commit base `e7f6311`. As verificações de preservação abrangem todos os arquivos originais fora de README/docs, incluindo configurações, src/, prisma/, tests/ e TRANSCRICAO.md.

A [checklist](docs/REVISAO.md) documenta os 31 critérios de aceite e os limites da revisão. O inventário explícito registra todos os IDs; isso não transforma inferências técnicas em decisões aprovadas. Detalhes não fechados continuam marcados como propostas ou pendências. O material do curso com modelos próprios não foi fornecido; foram seguidas as seções do enunciado. Não foram executados testes da aplicação ou da feature nesta entrega documental.
