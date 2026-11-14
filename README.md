# Da Reunião ao Documento: design docs do Sistema de Webhooks

Pacote de design docs da feature de webhooks de notificação de pedidos do OMS, produzido com IA a partir da transcrição da reunião técnica e do código existente. Este README conta como o pacote foi produzido. O enunciado original do desafio continua no fim deste arquivo, em [Enunciado original do desafio](#enunciado-original-do-desafio).

## Sobre o desafio

Uma reunião de cerca de 55 minutos entre tech lead, PM, engenheiros e segurança decidiu como o OMS vai avisar clientes B2B quando o status de um pedido muda: webhooks de saída, com outbox no MySQL, worker separado, retentativas, DLQ e assinatura HMAC. Nada foi registrado além da transcrição (`TRANSCRICAO.md`). A tarefa é transformar essa conversa e o código do OMS (Node.js, TypeScript, Express, Prisma e MySQL) num pacote de documentação acionável, com PRD, RFC, ADRs, FDD e um tracker que liga cada item à reunião ou ao código, sem alterar a aplicação.

O difícil não é escrever, é escrever só o que tem origem. A reunião descarta e adia ideias, deixa números ambíguos (as "5 tentativas" podem ser 5 ou 6 envios) e não fixa vários detalhes de que a implementação precisa. Usei a IA como ferramenta principal de produção e fiquei no papel de revisor: PRD, RFC, ADRs e FDD foram gerados cada um com uma skill própria, o tracker saiu de um script alimentado por uma lista escrita pela IA, e tudo foi revisado criticamente, corrigido e validado por script contra a transcrição, o código e os critérios de aceite do enunciado.

## Ferramentas de IA utilizadas

| Ferramenta | Papel no processo |
| --- | --- |
| Claude Code (app desktop, aba Code) | Ferramenta principal: leu o repositório e a transcrição e escreveu as skills, os documentos e os scripts de validação. Usei o Claude Opus 5.5 na maior parte do trabalho e o Claude Fable 5.1 na preparação (ETAPA-01 e 02), na revisão da `prd-writer` e na criação das skills de ADR, RFC e FDD (ETAPA-04 e 05), alternando com `/model` |
| Skills do projeto, em `.claude/skills/` | Uma por documento (`prd-writer`, `adr-writer`, `rfc-writer` e `fdd-writer`), mais a `registro-processo`, que manteve o diário de bordo e consolidou este README. Foram escritas com a IA antes dos documentos, em pt-BR e sem depender deste projeto |
| Skill `anthropic-skills:skill-creator` | Critérios de boas práticas para revisar a `prd-writer` antes de usá-la |
| Skill `doc-review` | Revisão crítica dos ADRs e do FDD em busca de contradições, ambiguidades, inconsistências e lacunas |
| Subagentes do Claude Code | Revisão final (ETAPA-14): sete agentes em paralelo, um por documento e um para a consistência do pacote, e depois dois agentes para aplicar as correções nos ADRs e no PRD |
| MCP Context7 | Documentação atualizada de Prisma, Zod, Pino, MySQL, OpenAPI, Redocly CLI e Swagger UI, para confirmar restrições técnicas usadas no desenho |

Ferramentas de apoio, sem modelo de IA: Redocly CLI 2.76.0 para o lint da spec OpenAPI e o HTML do Redoc, Swagger UI via Docker para conferir a spec, o navegador embutido do app com Mermaid 11.4.1 para renderizar os diagramas, o Chrome em modo headless para medir em PDF as páginas do RFC, e scripts Python que a IA escreveu para conferir citações, caminhos, links, âncoras, exemplos e a cobertura do tracker.

## Workflow adotado

Trabalhei em quatro fases e registrei as etapas em [`docs/REGISTRO-PROCESSO.md`](docs/REGISTRO-PROCESSO.md): a partir da ETAPA-06, no momento em que aconteciam; as cinco primeiras foram reconstruídas depois, a partir do histórico da sessão.

| Fase | Etapas | O que foi produzido |
| --- | --- | --- |
| Preparação | ETAPA-01 | `CLAUDE.md`, gerado com `/init`, com as restrições do desafio: não alterar o código, rastrear tudo e respeitar a altura de cada documento |
| Ferramental | ETAPA-02 a 06 | Uma tentativa de importar configurações de outras ferramentas com `/import` não foi aplicada. Antes de qualquer documento, adaptei para pt-BR o modelo de skill de PRD que estava em `.claude/skills/prd-writer/` e o tornei independente do projeto. Depois revisei a skill com a `skill-creator`, criei skills irmãs para ADR, RFC e FDD e uma skill para registrar o processo |
| Documentos | ETAPA-07 a 13 | ADRs, RFC, FDD, spec OpenAPI e diagramas, PRD, tracker e este README, na ordem sugerida pelo enunciado |
| Revisão final | ETAPA-14 | Validação de cada documento contra o checklist do enunciado, com um agente por documento, e correção de tudo o que foi apontado |

A ordem dos documentos fez cada um consolidar os anteriores. Os ADRs fixaram as decisões. O RFC as reuniu em uma proposta e listou as alternativas. O FDD detalhou a implementação com o código em mãos. O PRD subiu para a altura de produto citando os três, e o tracker varreu os quatro. A spec OpenAPI e os diagramas vieram depois do FDD, a pedido meu, para tirar o detalhe dos contratos do texto.

Na interação com a IA, mantive cinco práticas:

- **Uma skill por documento, em Modo Fontes.** PRD, RFC, ADRs e FDD saíram cada um da sua skill, e o tracker, de um script. Todo item precisa de localização `[hh:mm] Nome` ou de um caminho real de código. O que a reunião não fixou entra como proposta marcada (**(AUTOR, P-xx)** no FDD) ou como premissa (`PREM-xx` no PRD), nunca como requisito.
- **Inventário antes de escrever, nos ADRs.** A IA mostrou o que ia entrar e o que ficava de fora, com a localização, e eu decidi. Foi assim que escolhi 8 ADRs em vez de 6. Nos outros documentos, a IA seguiu sem entrevista e marcou o que faltava como proposta ou premissa.
- **Revisão crítica separada da escrita.** Nos ADRs e no FDD, uma rodada separada com a `doc-review` e com a skill em Modo Revisão; no RFC, a minha revisão; no PRD e no tracker, autoverificação e script. No fim, uma revisão de todo o pacote com um agente independente por documento, cruzando o texto com as fontes e com os critérios de aceite do enunciado.
- **Validação por script.** Citações conferidas contra a transcrição, caminhos de código, links e âncoras, parágrafos sem quebra de linha, exemplos do FDD comparados com a spec, grafo de dependências do PRD e cobertura do tracker. Os scripts rodaram numa pasta temporária da sessão e não fazem parte da entrega.
- **Registro na hora, a partir da ETAPA-06.** Cada etapa entrou no registro com o prompt literal, a entrega, os problemas e os ajustes. Este README foi consolidado a partir dele.

## Prompts customizados

Os prompts estão literais, com os erros de digitação originais. Os prompts de cada etapa estão no [registro](docs/REGISTRO-PROCESSO.md).

**Adaptação da skill de PRD (ETAPA-03).** Definiu o papel da IA e pediu que as lições do desafio entrassem na skill. O resultado separou o Modo Fontes, em que inventar é proibido, do Modo Descoberta, e criou IDs estáveis para alimentar o tracker.

```text
quero que você atue atue como um arquiteto de software experiente e vamos adaptar o modelo da skill de prd wiriter. o modelo está em .claude/skills/prd-writer/SKILL.md. Primeiro vamos transformar tudo sempre para pt-br e vamos criar uma skill agnostica de projeto. Aproveite e use algumas referencias do nosso desafio para criar esta skill da melhor forma possivel
```

**Skill de registro do processo (ETAPA-06).** Criou a skill que registra cada etapa durante o trabalho e reconstruiu as etapas anteriores a partir do histórico da sessão.

```text
Uma dos itens do desafio é criar um registro dos passos realizados para a criação dos documentos. Então seria interessante criarmos uma skill dedicada a isso e já incluir os passos que executamos de criação das outras skills um documento de registro ou algo parecido. Para ao final de todo o processo podermos recuperar deste documento de registro e criar a sessão especifica no readme do projeto, assim como pede o desafio
```

**Extração dos ADRs (ETAPA-07).** A restrição "não invente nada" levou a IA a apresentar as 8 decisões com a localização de cada uma e a lista do que ficou de fora, com o motivo, antes de escrever.

```text
Vamos iniciar o desafio deste projeto. Quero que você avaliar o documento @TRANSCRICAO.md para identificar os ADRs que foram definidos na reunião. Não invente nada, crie somente os adrs que você possa relacionar com um trecho da reunião.
```

**Correção do RFC (ETAPA-08, iteração 2).** Escrito depois de ler a primeira versão, longa demais e com questões em aberto que não vinham da reunião.

```text
Vamos revisar o RFC e fazer algumas alterações para enxugar o documento e não ficar tão extenso. A origem pode ser uma referencia ao tracker. As alternativas podem ser mais concisas para assim ficar com menos texto. As questões em aberto devem vir da transcrição da reunião, e não do que inferimos
```

**Escrita do FDD (ETAPA-09).** Amarrou o documento mais técnico aos critérios de aceite do enunciado.

```text
Agora vamos construir as fdds necessárias para o nosso projeto. Não esquecer de garantir todo os pontos de criterio de aceite para o fdds presentes na descrição do desafio
```

**Diagramas e spec OpenAPI (ETAPA-10).** Levou os contratos para uma spec navegável por ferramenta e acrescentou seis diagramas ao FDD.

```text
Acredito que o FDD teve pouco uso de diagramas mermaid para exemplificar fluxos e modelos ER. Fora isto, seria interessante já cria uma spec open api, no lugar de colocar a spec toda dentro do arquivo FDD. No FDD poderia estra resumido e a com a spec em anexo, podemo acessar com alguma ferramenta especifica e o entendimento fica bem facil. Caso julgue necessario, poderia cria um manual especifico para a spec.
```

**Revisão final (ETAPA-14).** Separou a validação por documento, com um agente independente para cada um, e pediu um resultado consolidado.

```text
faça uma revisão final para garantir todos os itens do checklist. Faça uma separação de validação por documento, onde você pode lançar um agente para cada um deles e ao final termos um resultado consolidado
```

**Trecho de skill: o Modo Fontes da `fdd-writer` (ETAPA-05).** Os prompts mais trabalhados ficaram nas skills, que a IA segue a cada documento. Este trecho é o que faz o FDD marcar como proposta do autor todo valor que a reunião não fixou.

```text
**Modo Fontes.** Há PRD, RFC, ADRs ou transcrição com as decisões tomadas. O FDD detalha o que foi decidido; não muda decisões. Onde a fonte não definiu um detalhe necessário para implementar (um timeout, um formato, um nome), o FDD propõe um valor e marca como **proposta do autor**, porque o desenvolvedor precisa de um número e o revisor precisa saber que aquele número não veio da reunião. Tudo com localização.
```

## Iterações e ajustes

O registro tem 14 etapas e 23 iterações no total. Contando só os documentos entregues, foram 14 iterações principais até o resultado final:

| Documento | Iterações | O que mudou |
| --- | --- | --- |
| ADRs | 2 | Revisão com `doc-review`: 2 contradições, 4 ambiguidades, 3 inconsistências e 4 lacunas corrigidas; ambiguidades da reunião viraram pontos em aberto |
| RFC | 2 | Enxugado de cerca de 3.070 para 2.235 palavras na iteração 2; questões em aberto reduzidas de 8 para 4, só com o que a reunião adiou |
| FDD e spec OpenAPI | 6 | Escrita, revisão com `doc-review`, revisão contra os critérios de aceite, diagramas e spec, replay concorrente redesenhado e descrição das colunas no diagrama ER |
| PRD | 1 | Escrito com os outros documentos já revisados; os ajustes vieram da autoverificação na mesma iteração |
| Tracker | 1 | Gerado e validado por script; as lacunas encontradas foram corrigidas na mesma iteração |
| README | 1 | Consolidado a partir do registro |
| Revisão final do pacote | 1 | Sete agentes, um por frente; correções em todos os documentos, propostas P-21 a P-27 no FDD e o RFC reduzido a 4 páginas |

Os momentos em que algo precisou ser corrigido:

- **Um ADR contradizia a fonte.** O ADR-004 dizia que a outbox "só contém eventos ainda em ciclo de entrega", mas em [09:08] Diego lista os status "falhou" e "entregue" na própria outbox. A validação por script não pegaria isso, porque a citação existia. A revisão com `doc-review` achou essa e mais 12 falhas, que corrigi com a resposta "Aplicar todas".
- **O RFC passou do tamanho e trouxe questões inferidas.** A primeira versão passava das cerca de 2.000 palavras que a skill usa como equivalente às 2 a 4 páginas do enunciado, e 5 das 8 questões em aberto vinham da revisão dos ADRs, não da reunião. Com o prompt de correção acima, a origem virou uma referência ao tracker, a tabela de alternativas perdeu duas colunas e ficaram só as 4 questões que a reunião adiou. Mesmo assim, renderizado em PDF, o RFC ainda ocupava 7 páginas; só na revisão final ele caiu para 4, com as alternativas de implementação deixadas para o FDD.
- **Uma orientação de segurança errada sobreviveu a duas revisões do FDD.** A seção de assinatura orientava o cliente a detectar replay por uma janela de tempo sobre o `timestamp` do corpo. Só que o corpo se repete nas retentativas por até 14 h 36 min, e o cliente recusaria entregas legítimas. A terceira revisão, feita contra os critérios do enunciado, trocou a orientação para deduplicar pelo `event_id`.
- **Detalhes técnicos conferidos na documentação, não na memória.** O Context7 mostrou que o caminho `*.secret` no redaction do Pino não cobre a chave `secret` no topo do objeto de log, e que um `refine` do Zod com `new URL()` pode lançar exceção e virar erro 500 numa URL malformada. As duas correções entraram no FDD.
- **Um pedido meu esbarrou num critério de aceite.** Pedi para tirar a spec do FDD, mas o enunciado exige no próprio FDD exemplos de requisição e resposta de pelo menos 4 endpoints. A IA manteve um exemplo por endpoint e levou o resto para a spec OpenAPI 3.1. Também trocou o exemplo de assinatura por um HMAC real, que virou vetor de teste conferido por script.
- **Um ponto não verificado virou mudança de desenho.** O 409 do replay concorrente dependia do tratamento de P2002 no error middleware, que chama `.join` sobre um campo que o Prisma pode devolver como texto no MySQL. Sem banco para testar, a IA propôs uma reserva condicional com `updateMany`, confirmada na documentação do MySQL, e eu aprovei com "Sim, ajustar".
- **Termos do desafio vazaram para exemplos genéricos.** Mesmo com a convenção de domínio fictício das skills, os templates saíram com o prefixo `WEBHOOK_` e um caminho real do projeto. Uma busca por termos do desafio depois de gerar pegou e trocou os exemplos.
- **Os nomes das seções foram conferidos contra o enunciado.** A skill nomeava "Problema e Oportunidade" a seção que o enunciado chama de "Problema e motivação", como já tinha acontecido com "Resiliência" no FDD. As duas foram renomeadas. No PRD, as metas que a reunião não deu viraram premissas a validar, em vez de números inventados.
- **O tracker revelou lacunas que a leitura deixou passar.** O script de cobertura achou o código `WEBHOOK_SECRET_REQUIRED`, citado no FDD para explicar por que não é usado, e duas referências de código do ADR-007 sem linha. As três entraram antes da entrega.
- **A revisão final achou contradições entre documentos que as revisões de cada um não viram.** Os sete agentes confirmaram os 31 critérios de aceite, mas apontaram o ADR-005 dizendo que o `X-Timestamp` ajudava a detectar replay enquanto o FDD dizia que ele não protege, o PRD tratando como decidida a leitura de 6 envios, que é proposta do autor, o RFC prometendo a ordem dos eventos sem a ressalva das retentativas e os ADRs com "Opções consideradas" onde o critério pede "Alternativas Consideradas". Também acharam um erro no `CLAUDE.md` da ETAPA-01, que dizia que `requireRole` não era usado em nenhuma rota. Respondi "Corrigir tudo", e as correções viraram as propostas P-21 a P-27 do FDD, um tracker com 513 linhas e o RFC em 4 páginas.

Também houve tropeço de ferramenta: comandos do chat do Claude Code digitados no terminal, e o Redoc e o Swagger UI que não renderizaram a spec no navegador embutido, resolvido subindo o Swagger UI via Docker com o comando documentado no manual da spec.

## Como navegar a entrega

Ordem sugerida de leitura, do "por quê" ao "como":

| Ordem | Arquivo | O que é |
| --- | --- | --- |
| 1 | [`docs/PRD.md`](docs/PRD.md) | Problema, público, objetivos, escopo, requisitos e critérios de aceitação |
| 2 | [`docs/RFC.md`](docs/RFC.md) | Proposta de arquitetura, alternativas descartadas, riscos e questões em aberto |
| 3 | [`docs/adrs/`](docs/adrs/README.md) | Índice e os 8 ADRs, um por decisão da reunião |
| 4 | [`docs/FDD.md`](docs/FDD.md) | Como construir: modelo de dados, fluxos, contratos, erros, resiliência, observabilidade e integração com o código |
| 5 | [`docs/openapi/webhooks.openapi.yaml`](docs/openapi/webhooks.openapi.yaml) e [`docs/openapi/README.md`](docs/openapi/README.md) | Spec OpenAPI 3.1 dos contratos e manual para visualizá-la e validá-la |
| 6 | [`docs/TRACKER.md`](docs/TRACKER.md) | Origem de cada item dos documentos, na reunião ou no código |

Material de apoio: [`TRANSCRICAO.md`](TRANSCRICAO.md) é a fonte; [`docs/REGISTRO-PROCESSO.md`](docs/REGISTRO-PROCESSO.md) é o diário de bordo com os prompts e as iterações de cada etapa; [`CLAUDE.md`](CLAUDE.md) e [`.claude/skills/`](.claude/skills/) são as instruções e as skills usadas para produzir os documentos.

```
.
├── README.md                     (este arquivo)
├── CLAUDE.md                     (contexto e restrições para a IA)
├── TRANSCRICAO.md                (fonte, não alterada)
├── .claude/skills/               (prd-writer, adr-writer, rfc-writer, fdd-writer, registro-processo)
└── docs/
    ├── PRD.md
    ├── RFC.md
    ├── FDD.md
    ├── TRACKER.md
    ├── REGISTRO-PROCESSO.md
    ├── adrs/                     (README.md e ADR-001 a ADR-008)
    └── openapi/                  (webhooks.openapi.yaml e README.md)
```

---

## Enunciado original do desafio

Mantido como referência, como o próprio enunciado permite. O texto está como no repositório base, com os títulos rebaixados dois níveis para ficarem abaixo desta seção.

### Da Reunião ao Documento: Design Docs Gerados por IA

#### Descrição

Neste desafio você vai transformar a transcrição de uma reunião técnica em um pacote completo de design docs, usando IA como ferramenta principal de produção.

**Cenário:** uma empresa que opera um Order Management System (OMS) em produção vai construir uma nova feature, um Sistema de Webhooks de Notificação de Pedidos. A decisão técnica já foi tomada em uma reunião entre tech lead, PM, engenheiros e segurança, mas nada foi registrado além da transcrição da call (`TRANSCRICAO.md`).

**Sua tarefa:** produzir, a partir da transcrição e do código existente, a documentação técnica da feature, em nível acionável o suficiente para o time de engenharia iniciar a implementação.

#### Sobre o uso de IA

A IA é sua ferramenta principal de produção neste desafio. Você vai usá-la para ler o código, analisar a transcrição, estruturar os documentos e gerar o conteúdo final. O que se espera de você é o papel de maestro: definir o que precisa ser feito, formular bons prompts, revisar criticamente o que a IA entrega, corrigir e refinar até o resultado ficar consistente.

#### Estrutura do desafio

O desafio consiste em produzir um **pacote de design docs**: PRD, RFC, FDD, ADRs, Tracker e o README do processo a partir da transcrição e do código.

#### Objetivo

Entregar, em um repositório público no GitHub (fork do repositório base), o seguinte pacote de documentação:

- PRD (Product Requirement Document) da feature
- RFC (Request for Comments) com a proposta técnica da solução, submetida à equipe para revisão
- FDD (Feature Design Document) da feature
- Entre 5 e 8 ADRs (Architecture Decision Records) das decisões discutidas
- Tracker de rastreabilidade ligando cada item à origem na transcrição ou no código
- README atualizado documentando o processo de produção

Toda informação registrada nos documentos deve ser rastreável à transcrição ou ao código fonte da aplicação. Não é permitido inventar requisitos, decisões ou restrições sem origem identificável.

##### O pacote de documentos e o papel de cada um

Os documentos não se repetem: cada um opera em uma **altura** diferente. Antes de produzir, entenda a fronteira entre eles: conteúdo duplicado entre documentos é sinal de que algo está no lugar errado.

| Documento | Papel | Altura | Pergunta que responde |
| --- | --- | --- | --- |
| **PRD** | Problema, público, escopo e métricas de sucesso | Produto / negócio | *Por que e o quê?* |
| **RFC** | Proposta técnica da solução para revisão: abordagem geral, alternativas e questões em aberto | Arquitetura | *Como pretendemos resolver, e o que ainda está em aberto?* |
| **ADRs** | Cada decisão arquitetural isolada, com contexto e consequências | Decisão pontual | *Por que decidimos exatamente assim?* |
| **FDD** | Especificação de implementação: fluxos, contratos, erros, integração com o código | Implementação | *Como construir, em detalhe?* |
| **Tracker** | Rastreabilidade de cada item ao código ou à transcrição | Transversal | *De onde veio cada coisa?* |

Em uma frase: o **RFC propõe e abre para revisão**, os **ADRs registram cada decisão fechada** e o **FDD detalha como construir**. O RFC é conciso (2 a 4 páginas) e fala em decisão; o FDD é profundo e fala em implementação. Não repita no RFC o nível de detalhe do FDD.

#### Contexto

##### A aplicação existente

O repositório base contém uma aplicação Node.js + TypeScript funcional: um Order Management System com módulos de autenticação, usuários, clientes, produtos e pedidos. Banco MySQL via Prisma. O ciclo de vida do pedido tem máquina de estados controlada, controle transacional de estoque e auditoria de mudanças de status.

A aplicação não tem nenhum mecanismo de notificação externa, eventos, filas ou webhooks. Esse vácuo é proposital. É exatamente o que a feature discutida na reunião pretende preencher.

Seus documentos vão precisar referenciar componentes do código existente, como a estrutura modular, a máquina de estados, a transação do `changeStatus`, as classes de erro, o padrão de códigos de erro, o middleware `requireRole`, o error middleware centralizado e o logger Pino. Use a IA para mapear esses pontos a partir do código.

##### A transcrição

O arquivo `TRANSCRICAO.md` contém a gravação literal da reunião técnica. Cinco participantes discutem por aproximadamente 55 minutos no formato `[hh:mm] Nome: fala`.

A transcrição inclui decisões fechadas, requisitos funcionais explícitos, restrições, ganchos com o código existente, pontos descartados ou adiados para fases futuras e detalhes técnicos secundários. Nem tudo que foi mencionado vira requisito. Algumas coisas foram explicitamente descartadas, outras foram adiadas. Identificar o que NÃO entra é tão importante quanto identificar o que entra. Use a IA com prompts dirigidos para fazer essa filtragem, não pedidos genéricos.

#### Tecnologias e ferramentas

Liberdade total na escolha de ferramentas de IA. Você pode usar qualquer combinação de Claude, ChatGPT, Cursor, Copilot Chat, Gemini, agentes, prompts customizados, skills ou plugins. Aproveite os prompts e plugins disponibilizados pelo professor durante o curso como ponto de partida.

Os documentos devem ser entregues em formato Markdown.

A entrega é puramente documental: você não deve mexer no código da aplicação (`src/`, `prisma/`, `tests/`, configurações). O código serve de contexto e referência.

#### Requisitos

##### 1. PRD da feature

Produza o arquivo `docs/PRD.md` cobrindo a feature de Sistema de Webhooks de Notificação de Pedidos. O PRD deve seguir o formato apresentado no curso e incluir, no mínimo, as seguintes seções:

- Resumo e contexto da feature
- Problema e motivação
- Público-alvo e cenários de uso
- Objetivos e métricas de sucesso
- Escopo (incluso e fora de escopo)
- Requisitos funcionais
- Requisitos não funcionais
- Decisões e trade-offs principais
- Dependências
- Riscos e mitigação
- Critérios de aceitação
- Estratégia de testes e validação

A seção "Fora de escopo" deve listar explicitamente pelo menos 2 itens descartados ou adiados durante a reunião.

##### 2. RFC da feature

Produza o arquivo `docs/RFC.md` com a proposta técnica da solução, no formato de um documento submetido à equipe para revisão. O RFC opera em nível de arquitetura: apresenta a abordagem escolhida, as alternativas que foram colocadas na mesa e as questões deixadas em aberto. É um documento conciso (2 a 4 páginas); o detalhamento de implementação fica no FDD. Deve seguir o formato apresentado no curso e incluir, no mínimo:

- Metadados (autor, status, data, revisores); use os participantes da reunião como revisores
- Resumo executivo (TL;DR) da proposta
- Contexto e problema
- Proposta técnica (visão geral da solução, sem descer ao detalhe de implementação do FDD)
- Alternativas consideradas (pelo menos 2 alternativas reais discutidas e descartadas na reunião, cada uma com o trade-off que levou ao descarte)
- Questões em aberto (pelo menos 2 pontos levantados na reunião e não decididos ou adiados)
- Impacto e riscos
- Decisões relacionadas (links para os ADRs correspondentes)

O RFC não deve duplicar o detalhamento do FDD. Ele responde "o que propomos e por quê"; o "como construir" em detalhe fica no FDD.

##### 3. FDD da feature

Produza o arquivo `docs/FDD.md` detalhando o "como implementar" da feature. O FDD é o documento mais técnico e precisa estar acionável o suficiente para um desenvolvedor pegar e começar a codar. Deve seguir o formato apresentado no curso e incluir, no mínimo:

- Contexto e motivação técnica
- Objetivos técnicos
- Escopo e exclusões
- Fluxos detalhados (criação do evento na outbox, processamento pelo worker, retry, DLQ)
- Contratos públicos (endpoints HTTP com payloads de exemplo, headers, status codes, semântica)
- Matriz de erros previstos com códigos no padrão `WEBHOOK_*`
- Estratégias de resiliência (timeouts, retries, backoff, fallback)
- Observabilidade (métricas, logs, tracing)
- Dependências e compatibilidade
- Critérios de aceite técnicos
- Riscos e mitigação

Seção obrigatória adicional, específica deste desafio: **"Integração com o sistema existente"**. Esta seção deve nomear pelo menos 4 caminhos de arquivo reais do código base e descrever como o módulo de webhooks vai se integrar com cada um (por exemplo, como o método `changeStatus` será estendido, como as classes de erro existentes serão reutilizadas).

##### 4. ADRs

Produza entre 5 e 8 ADRs em arquivos separados dentro de `docs/adrs/`, nomeados no formato `ADR-NNN-titulo-em-kebab-case.md` (ex: `ADR-001-outbox-no-mysql.md`).

Cada ADR deve seguir o formato MADR (ou variante padrão) com no mínimo as seções: Status, Contexto, Decisão, Alternativas Consideradas (pelo menos 1 alternativa real discutida ou plausível), Consequências (positivas e negativas, com trade-off explícito).

Pelo menos 1 ADR deve referenciar explicitamente arquivos, módulos ou padrões do código existente.

O conjunto de ADRs deve cobrir, no mínimo, 5 das 6 decisões principais discutidas na reunião:

- Padrão Outbox no MySQL
- Política de retry com backoff e DLQ
- Autenticação HMAC-SHA256 com secret por endpoint
- Garantia at-least-once com `X-Event-Id`
- Worker em processo separado em polling
- Reuso dos padrões existentes do projeto

Decisões técnicas secundárias (formato de payload, timeouts, headers, entre outras) podem virar ADRs adicionais ou ficar apenas no FDD, conforme você considerar mais adequado.

##### 5. Tracker de Rastreabilidade

Produza o arquivo `docs/TRACKER.md`, uma tabela markdown que mapeia cada item registrado nos seus documentos à origem na transcrição ou no código. O tracker funciona como uma referência cruzada: permite que qualquer leitor entenda de onde veio cada decisão, requisito ou restrição, e garante que a documentação está alinhada com o que foi efetivamente discutido e com o que existe no código.

O tracker não é um conceito padrão do mercado nem é um documento abordado diretamente no curso. É uma exigência específica deste desafio que ajuda a manter a integridade da documentação contra alucinações da IA.

Formato obrigatório da tabela:

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
|  |  |  |  |  |  |

Onde:

- **ID**: identificador único do item (ex: PRD-FR-01, RFC-ALT-02, FDD-CONTRATO-03, ADR-002)
- **Documento**: arquivo onde o item aparece (`docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/adrs/ADR-002-...md`)
- **Tipo**: Requisito Funcional, Requisito Não Funcional, Decisão, Restrição, Trade-off, entre outros
- **Conteúdo (resumo)**: descrição de uma linha do item
- **Fonte**: `TRANSCRICAO` ou `CODIGO`
- **Localização**: para `TRANSCRICAO`, timestamp + nome do falante (ex: `[09:17] Diego`). Para `CODIGO`, caminho do arquivo (ex: `src/modules/orders/order.service.ts`).

Cobertura mínima: pelo menos 80% dos itens identificáveis nos seus documentos devem ter linha correspondente no tracker.

##### 6. README com o processo

O `README.md` na raiz do repositório base contém este enunciado. Substitua o conteúdo dele pela documentação do seu processo de produção. Você pode manter um link ou seção fazendo referência ao enunciado original se quiser, mas o foco do novo conteúdo é descrever sua jornada.

Estrutura obrigatória do novo README:

- **Sobre o desafio**: 1 a 2 parágrafos descrevendo a tarefa em suas palavras
- **Ferramentas de IA utilizadas**: lista das ferramentas que você usou, com breve nota sobre o papel de cada uma
- **Workflow adotado**: como você organizou o trabalho. Em que ordem produziu os documentos, como organizou a interação com a IA
- **Prompts customizados**: pelo menos 2 prompts relevantes que você escreveu ou adaptou, mostrados em blocos de código
- **Iterações e ajustes**: descreva os principais momentos em que a IA gerou algo errado ou superficial e você teve que corrigir. Quantas iterações principais até chegar ao resultado final
- **Como navegar a entrega**: caminho dos arquivos entregues e ordem sugerida de leitura

---

#### Critérios de Aceite

A entrega é avaliada contra os critérios abaixo. Todos são obrigatórios.

##### PRD (`docs/PRD.md`)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 1
- ☐ Identifica no mínimo 8 requisitos funcionais discutidos na reunião
- ☐ Inclui pelo menos 1 objetivo com métrica e meta quantitativa
- ☐ Seção "Fora de escopo" lista pelo menos 2 itens explicitamente descartados ou adiados na reunião
- ☐ Seção "Riscos" inclui pelo menos 2 riscos com probabilidade, impacto e mitigação

##### RFC (`docs/RFC.md`)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 2
- ☐ Seção "Alternativas consideradas" lista pelo menos 2 alternativas descartadas na reunião, cada uma com o trade-off que motivou o descarte
- ☐ Seção "Questões em aberto" lista pelo menos 2 pontos adiados ou não decididos na reunião
- ☐ Referencia, com link, pelo menos 2 ADRs do pacote

##### FDD (`docs/FDD.md`)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 3
- ☐ Seção "Contratos públicos" inclui pelo menos 4 endpoints HTTP com payload de exemplo (request e response) e status codes
- ☐ Matriz de erros usa códigos com prefixo `WEBHOOK_`
- ☐ Seção "Integração com o sistema existente" referencia pelo menos 4 caminhos de arquivo reais do código base
- ☐ Seção "Observabilidade" cita métricas, logs e tracing

##### ADRs (`docs/adrs/ADR-NNN-*.md`)

- ☐ Pasta `docs/adrs/` contém entre 5 e 8 arquivos no formato `ADR-NNN-titulo-em-kebab-case.md`
- ☐ Cada ADR contém as seções Status, Contexto, Decisão, Alternativas Consideradas, Consequências
- ☐ O conjunto cobre pelo menos 5 das 6 decisões principais listadas no requisito 4
- ☐ Pelo menos 1 ADR referencia explicitamente arquivos, módulos ou classes do código base

##### Tracker (`docs/TRACKER.md`)

- ☐ Arquivo existe e segue o formato de tabela definido no requisito 5
- ☐ Pelo menos 80% dos itens identificáveis dos documentos têm linha correspondente
- ☐ Pelo menos 70% das linhas têm Fonte = `TRANSCRICAO` com timestamp válido no formato `[hh:mm] Nome`
- ☐ Pelo menos 5 linhas têm Fonte = `CODIGO` com caminho de arquivo real

##### README (`README.md`)

- ☐ Contém todas as seções obrigatórias listadas no requisito 6
- ☐ Lista pelo menos 1 ferramenta de IA utilizada
- ☐ Mostra pelo menos 2 prompts customizados em blocos de código
- ☐ Descreve pelo menos 2 iterações ou ajustes concretos feitos durante a produção

##### Consistência geral

- ☐ Nenhum requisito, decisão ou restrição registrada nos documentos contradiz a transcrição ou o código
- ☐ Nenhum arquivo de código mencionado nos documentos é inexistente no repositório

---

#### Estrutura obrigatória do entregável

```
.
├── README.md                              (substituído pelo aluno)
├── TRANSCRICAO.md                         (não alterar)
├── docs/
│   ├── PRD.md                             (preenchido pelo aluno)
│   ├── RFC.md                             (preenchido pelo aluno)
│   ├── FDD.md                             (preenchido pelo aluno)
│   ├── TRACKER.md                         (preenchido pelo aluno)
│   └── adrs/
│       ├── ADR-001-titulo-curto.md
│       ├── ADR-002-titulo-curto.md
│       ├── ADR-003-titulo-curto.md
│       ├── ADR-004-titulo-curto.md
│       ├── ADR-005-titulo-curto.md
│       └── ... (até 8 ADRs)
├── src/                                   (não alterar)
├── prisma/                                (não alterar)
├── tests/                                 (não alterar)
└── ... (demais arquivos do boilerplate)
```

A entrega deve ser feita como repositório público no GitHub, a partir de fork do repositório base do desafio.

#### Repositório base

O repositório base do desafio contém a aplicação completa, a transcrição e a estrutura de pastas pra você preencher:

https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia

#### Ordem de execução sugerida

1. **Fork e setup**: faça o fork do repositório base e clone localmente.
2. **Contextualização com IA**: forneça à IA acesso ao código (via Claude Code, Cursor lendo o repo, ou colando trechos relevantes) e à transcrição. Peça uma exploração inicial para entender estrutura, padrões e o que a feature precisa endereçar.
3. **ADRs primeiro**: identifique e produza as decisões principais antes dos demais documentos. As decisões formam o esqueleto do "como implementar".
4. **RFC**: consolide a proposta técnica em cima das decisões. As alternativas descartadas e as questões em aberto da reunião têm lugar natural aqui. Referencie os ADRs já escritos.
5. **FDD**: com as decisões formalizadas e a proposta consolidada, o desenho técnico se constrói em cima delas. Lembre da seção obrigatória "Integração com o sistema existente".
6. **PRD**: produza o PRD por último entre os grandes documentos. Como ele é mais alto nível, com RFC, FDD e ADRs em mãos vira praticamente uma consolidação.
7. **Tracker**: monte em paralelo com os outros documentos ou no fim, varrendo os documentos prontos.
8. **README do processo**: deixe por último, quando o processo já está completo e você pode documentá-lo com clareza.
9. **Revisão final**: passe pela checklist de critérios de aceite item por item antes do push final.
10. **Itere**: é esperado que o processo demande 3 a 5 ciclos de geração, revisão crítica, ajuste de prompt e nova geração. Se você gerou tudo de primeira sem ajustes, os documentos provavelmente estão genéricos demais.

#### Dicas Finais

A qualidade do prompt determina a qualidade do documento. Prompts vagos do tipo "gere um PRD a partir dessa transcrição" produzem documentos vazios e genéricos. Aproveite os prompts disponibilizados pelo professor no curso como base e adapte-os ao contexto deste desafio.

O tracker é seu melhor aliado contra alucinações da IA. Se você não consegue preencher a coluna "Localização" para uma linha do PRD ou do FDD, é sinal de que aquela informação não tem origem identificável e provavelmente foi inventada pela IA. Ajuste ou remova.

Cuidado com o que NÃO entra na documentação. A reunião descarta explicitamente algumas ideias. Se essas coisas aparecerem como requisito nos seus documentos, é sinal de que a IA não está sendo cuidadosa com o que você pediu.

A restrição de não alterar o código da aplicação é absoluta: o código serve de contexto e referência, e o entregável é puramente documental.

Itere bastante. Os primeiros documentos que a IA gerar provavelmente serão superficiais ou redundantes. Volte com correções, peça refinamento de pontos específicos, peça para remover trechos vagos, peça exemplos concretos. O resultado final deve parecer escrito por alguém que pensou no problema com a IA ao lado, não por alguém que copiou e colou da transcrição.
