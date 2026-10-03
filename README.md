# Da reunião ao documento: webhooks do OMS

## Sobre o desafio
Esta entrega transforma a reunião sobre notificações de pedidos e a leitura de um OMS Node.js/TypeScript em um pacote de design docs rastreável. O objetivo é permitir que engenharia revise a proposta e inicie implementação sem atribuir à reunião decisões que ela não tomou.

A entrega é exclusivamente documental. A transcrição, src/, prisma/, tests/ e configurações foram preservados. Enunciado original e código base: [repositório do desafio](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia). Fork: [jakeliny10/mba-ia-desafio-design-docs-com-ia](https://github.com/jakeliny10/mba-ia-desafio-design-docs-com-ia).

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
Os prompts abaixo registram as instruções dirigidas usadas na composição e revisão desta entrega; não representam uma ferramenta adicional nem resultados de uma reunião com o time.

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
Foram três ciclos principais de análise/redação/revisão, sem simular feedback humano ou aprovação da equipe:

1. Contextualização: a formulação inicial de “mesmo Prisma client” foi refinada para instância própria por processo após confronto de [09:30] Bruno com src/config/database.ts. O JWT foi verificado: customer não vem do token.
2. Redação crítica: a promessa genérica de retry/ordering foi desdobrada em lacunas concretas. Cinco intervalos não cabem em cinco tentativas totais; created_at não impede ultrapassagem durante retry. Os documentos preservam decisões e pedem revisão dos detalhes.
3. Revisão de contratos: erros Zod e auth foram preservados como compartilhados; WEBHOOK_* é contrato de domínio. Endpoints não ditos literalmente e status HTTP foram rotulados como propostas. Tracing foi descrito como pendência, e não como infraestrutura existente. IDs e caminhos foram auditados.

Esses ajustes foram conduzidos pelo Codex durante a produção; não há alegação de que o aluno ou participantes tenham revisado cada documento. RFC permanece para revisão; não foi enviado aos participantes.

## Como navegar a entrega
Ordem sugerida para um leitor novo:

1. [PRD](docs/PRD.md): problema, público, escopo e critérios de produto.
2. [RFC](docs/RFC.md): visão arquitetural, alternativas e questões em aberto.
3. [ADRs](docs/adrs/README.md): sete decisões isoladas com trade-offs.
4. [FDD](docs/FDD.md): fluxos, contratos propostos, erros e integração com arquivos reais.
5. [Tracker](docs/TRACKER.md): origem de cada ID.
6. [Transcrição preservada](TRANSCRICAO.md): fonte primária da reunião.

As pendências do FDD devem ser resolvidas na revisão técnica antes dos respectivos componentes serem codificados. Esta entrega não altera nem implementa a aplicação.

## Validação da entrega
A auditoria documental confirmou sete ADRs, doze requisitos funcionais, sete endpoints de gestão com exemplos, 115 IDs únicos no tracker, 80,9% de fontes TRANSCRICAO e 22 registros CODIGO. Foram conferidos timestamps/falantes contra a transcrição, existência dos arquivos citados pelo tracker, links locais, blocos de código Markdown e preservação dos arquivos protegidos por comparação Git. A verificação é estrutural e de referência; não substitui a revisão técnica das propostas e lacunas.

A cobertura é organizada por grupos semânticos identificados: explicações e exemplos pertencem ao ID da seção. Não foi calculada uma porcentagem automática sobre cada frase; detalhes não decididos continuam rotulados para revisão.
