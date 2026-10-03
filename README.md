# Da reunião ao documento: webhooks de notificação de pedidos

## Sobre o desafio

O desafio consiste em transformar a transcrição de uma reunião técnica e o código de um Order Management System em um pacote de documentação para uma nova feature de webhooks. A entrega reúne requisitos de produto, proposta arquitetural, decisões e especificações de implementação, com rastreabilidade às fontes.

O trabalho é exclusivamente documental: a aplicação e a transcrição foram mantidas sem alterações. O [repositório base](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia) contém o enunciado e o contexto do desafio.

## Ferramentas de IA utilizadas

- **Codex:** análise da transcrição e do código, redação dos documentos e revisão de consistência.
- **Skill fullcycle-marketplace:** apoio à organização dos ADRs e à distinção entre decisões arquiteturais e detalhes de implementação, a partir dos [playbooks da Full Cycle](https://github.com/devfullcycle/claude-mkt-place).

## Workflow adotado

1. Leitura da transcrição para separar requisitos, decisões fechadas, alternativas descartadas e itens adiados.
2. Exploração do código para entender a transação de pedidos, a máquina de estados, a autenticação e os padrões de módulos, erros e logs.
3. Produção dos ADRs como base das decisões arquiteturais.
4. Consolidação do RFC, com a proposta técnica e as questões para revisão.
5. Elaboração do FDD, detalhando fluxos, contratos e integração com o sistema existente.
6. Produção do PRD, consolidando problema, público, escopo e critérios de aceitação.
7. Construção do tracker durante a redação e revisão das referências à transcrição e ao código.
8. Revisão do pacote contra o enunciado e registro do processo neste README.

## Prompts customizados

### Extração de requisitos e decisões

```text
Leia TRANSCRICAO.md e o código do OMS. Separe decisão fechada, requisito
explícito, alternativa descartada, item adiado e lacuna. Para cada item,
registre timestamp e falante ou arquivo real. Verifique o conteúdo do JWT
antes de definir como o customer_id será recebido pela API.
```

### Organização dos documentos

```text
Produza ADRs de decisão, RFC em nível de arquitetura e FDD de implementação,
sem duplicar os contratos detalhados no RFC. Identifique detalhes derivados
como propostas para revisão. Confronte as cinco tentativas com os cinco
intervalos de retry, ordering com retentativas e rotação HMAC com overlap.
Mantenha os pontos não definidos como questões em aberto e gere o tracker.
```

### Revisão de consistência

```text
Confronte os documentos com a transcrição e os arquivos reais do projeto.
Confira prefixo de API, envelope de erro, autenticação e pontos de integração.
Revise a origem de requisitos, decisões, restrições e exclusões. Remova
informações sem fundamento nas fontes e preserve o código da aplicação.
```

## Iterações e ajustes

O pacote passou por duas rodadas principais de redação: a produção inicial e uma revisão crítica contra o enunciado. Os principais ajustes foram:

- **Rastreabilidade:** registros que agrupavam assuntos distintos foram separados. Erros, métricas, alternativas e consequências dos ADRs ganharam referências específicas.
- **Riscos:** a classificação genérica de probabilidade “média” foi substituída por possibilidade reconhecida e frequência não medida, considerando os antecedentes mencionados na reunião.
- **Contratos e integração:** o prefixo `/api/v1`, o `requestId` e os erros compartilhados foram conferidos no código. O padrão de PATCH parcial foi relacionado ao schema de clientes; GET e DELETE foram documentados sem body.
- **Ambiguidades técnicas:** a diferença entre cinco tentativas e cinco intervalos, a ordenação durante retries e a assinatura durante a rotação de secrets ficaram explícitas para revisão, em vez de receber soluções sem origem na reunião.

## Como navegar a entrega

1. [PRD](docs/PRD.md): problema, público, escopo e requisitos da feature.
2. [RFC](docs/RFC.md): proposta arquitetural, alternativas e questões em aberto.
3. [ADRs](docs/adrs/README.md): decisões arquiteturais e seus trade-offs.
4. [FDD](docs/FDD.md): fluxos, contratos, erros e integração com o código.
5. [Tracker](docs/TRACKER.md): origem dos itens documentados.
6. [Transcrição](TRANSCRICAO.md): fonte primária da reunião.
