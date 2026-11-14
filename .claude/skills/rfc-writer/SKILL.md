---
name: rfc-writer
description: |
  Escreve e revisa RFCs (Request for Comments) técnicos em português do Brasil: a proposta de solução em nível de arquitetura, submetida à equipe para revisão, com abordagem escolhida, alternativas descartadas e seus trade-offs, impacto, riscos e questões em aberto, em 2 a 4 páginas. Funciona a partir de fontes (transcrição de reunião, PRD, ADRs, código existente) com rastreabilidade, ou conduzindo a proposta com o usuário quando ela ainda está sendo desenhada. Use esta skill sempre que o usuário quiser propor uma solução técnica para a equipe avaliar, escrever um documento de design para revisão, comparar abordagens arquiteturais, consolidar decisões de uma reunião em uma proposta, pedir comentários sobre um desenho técnico ou revisar um RFC existente, mesmo que não use a sigla "RFC". Palavras-chave: "rfc", "request for comments", "proposta técnica", "design proposal", "proposta de arquitetura", "documento de design", "submeter para revisão", "alternativas", "questões em aberto", "tech proposal".
---

# RFC Writer

Você atua como arquiteto de software experiente escrevendo para pares. Seu trabalho é apresentar uma proposta técnica clara o bastante para que revisores consigam concordar, discordar ou apontar o que falta, sem precisar perguntar o que está sendo proposto. Seja direto e objetivo.

Um RFC é efetivo quando responde **"como pretendemos resolver, o que descartamos e o que ainda está em aberto?"** em 2 a 4 páginas. Ele não justifica cada decisão isolada em profundidade (isso é ADR), não detalha contratos e implementação (isso é FDD) e não rediscute o problema de produto (isso é PRD). Um RFC que tenta ser os três fica longo demais para ser revisado.

Tudo é escrito em **português do Brasil**. Identificadores de código, nomes de arquivos e termos técnicos consagrados ficam no original.

## Arquivos de referência

| Arquivo | Quando ler |
| --- | --- |
| [references/template-rfc.md](references/template-rfc.md) | FASE 3, antes de escrever |
| [references/checklist-rfc.md](references/checklist-rfc.md) | FASE 4, antes de salvar |
| `../prd-writer/references/extracao-de-fontes.md` | FASE 1, quando houver fontes e o arquivo existir. Se não existir, use as regras resumidas abaixo |

---

## PARÂMETROS DE ENTRADA

- `CAMINHO_RFC`: caminho do arquivo. Padrão: `docs/RFC.md`. Em projetos com vários RFCs, `docs/rfcs/RFC-NNN-titulo.md`.
- `FONTES`: transcrições, atas, PRD, ADRs, tickets, código. Opcional.
- `PROPOSTA`: descrição livre da solução pretendida. Opcional se houver `FONTES`.
- `REVISORES`: nomes e papéis. Em Modo Fontes, os participantes da reunião quando não houver outra indicação.
- `CAMINHO_TRACKER`: tabela de rastreabilidade externa. Opcional.

---

## MODOS DE OPERAÇÃO

**Modo Fontes.** A solução foi discutida e decidida em reunião, ou já há ADRs. O RFC consolida: abordagem, alternativas que estiveram na mesa com o trade-off que as descartou, e o que ficou sem decisão. Tudo com localização. Alternativas não discutidas podem entrar marcadas como "não discutida", porque um RFC sem alternativas não convida revisão.

**Modo Descoberta.** A solução ainda está sendo desenhada. Ajude o usuário a estruturar a proposta: objetivos e não objetivos, abordagem, alternativas e por que não, riscos. O RFC nasce com status "Rascunho".

**Modo Revisão.** Já existe um RFC. Comentários de revisores viram ajustes no texto ou entradas em questões em aberto; mudanças de abordagem ficam registradas no histórico de revisão para que o leitor entenda o que mudou e por quê. Preserve a numeração de alternativas e questões já citadas por outros.

---

## RASTREABILIDADE (resumo)

Localização por tipo de fonte: transcrição `[hh:mm] Nome`; código, caminho relativo; documento, caminho e seção; ticket, identificador. Rótulos: `TRANSCRICAO`, `CODIGO`, `DOCUMENTO`, `TICKET`, `USUARIO`. Cite o trecho em que algo foi fechado ou descartado, não a primeira menção. Cite apenas arquivos que existem.

---

## PROCESSO DE TRABALHO (5 FASES)

### FASE 1: Entendimento

**Passo 1: Contexto.** Leia PRD, ADRs e FDD existentes. Se há ADRs, o RFC os referencia em vez de repetir a argumentação. Se há PRD, o RFC resume o problema em um parágrafo e aponta para ele. Se há código, identifique os componentes que a proposta toca e os padrões que ela deve respeitar.

**Passo 2: Inventário (Modo Fontes).** Leia as fontes inteiras e classifique: abordagem escolhida e seus componentes; alternativas consideradas com o motivo de descarte; pontos adiados ou não decididos; riscos levantados; impactos mencionados em sistemas, times e operação. Apresente ao usuário em lista curta com localização e confirme.

**Passo 3: Ordem dos documentos.** Se ADRs ainda não existem e as fontes contêm decisões claras, sugira produzi-los antes (skill `adr-writer`), porque o RFC fica mais curto e mais forte referenciando decisões já registradas. Se o usuário preferir seguir, escreva o RFC e registre em "Decisões relacionadas" quais ADRs deveriam existir.

### FASE 2: Esclarecimento

Em Modo Fontes, pergunte apenas sobre: alternativas citadas sem motivo de descarte; pontos ambíguos entre "decidido" e "adiado"; revisores e prazo de revisão; sistemas afetados que a fonte não mencionou mas o código revela.

Em Modo Descoberta, levante objetivos e não objetivos, restrições, abordagem pretendida, alternativas que o usuário já descartou e por quê, e as dúvidas que ele quer que os revisores respondam. Agrupe duas a quatro perguntas por mensagem.

Se o usuário não estiver disponível, registre lacunas como questões em aberto e siga.

### FASE 3: Escrita

Leia `references/template-rfc.md`. Escreva o RFC inteiro de uma vez.

**Tamanho.** De 2 a 4 páginas, algo entre 800 e 2.000 palavras fora dos diagramas. Se passar disso, o conteúdo excedente provavelmente é detalhe de FDD ou argumentação de ADR; mova e referencie.

**TL;DR.** De 3 a 5 frases que um revisor apressado lê e entende: problema, abordagem, trade-off principal, o que precisa de decisão.

**Proposta técnica.** Visão geral da solução: componentes, responsabilidades, fluxo principal, e um diagrama Mermaid de alto nível quando houver mais de dois componentes interagindo. Nomeie componentes pelo papel ("worker de entrega", "tabela de eventos pendentes") e não por nomes de classe. Nada de payloads, DDL ou assinaturas de função.

**Alternativas.** Pelo menos duas reais, cada uma com: o que era, o que teria de bom, e o trade-off que levou ao descarte, em termos dos fatores que importam neste projeto (time, prazo, infraestrutura, RNFs). Referencie o ADR correspondente quando existir.

**Questões em aberto.** Pelo menos duas, cada uma com a pergunta, por que importa para a revisão, quem decide e até quando, se souber. É aqui que os revisores são convidados a contribuir; um RFC sem questões parece um comunicado, não um pedido de comentários.

**Impacto e riscos.** Sistemas, módulos e times afetados; mudanças de operação; segurança; dados; compatibilidade; custo. Riscos com mitigação. Em projeto existente, cite os módulos ou arquivos afetados.

**Fronteira com outros documentos.** Uma decisão pontual tem no RFC um parágrafo e um link para o ADR. Um contrato tem no RFC o nome e o propósito, e o detalhe no FDD. Um requisito de produto tem no RFC uma linha e a referência ao PRD. Quando se pegar escrevendo uma tabela de campos ou uma lista de status codes, pare e referencie.

**Teste do revisor.** Releia como alguém que vai gastar 20 minutos revisando: em cada seção, há algo com que ele possa concordar ou discordar? Se uma seção só informa, encurte.

### FASE 4: Validação (interna)

Leia `references/checklist-rfc.md`, execute e corrija até 3 vezes. Persistindo falhas, reporte ao usuário.

### FASE 5: Salvar

1. Salve em `CAMINHO_RFC`. Se existir com conteúdo real, mostre a diferença e confirme antes de sobrescrever.
2. Releia e confirme que contém metadados, TL;DR, alternativas e questões em aberto.
3. Se `CAMINHO_TRACKER` foi informado, acrescente linhas com IDs `RFC-PROP-xx` (componentes da proposta), `RFC-ALT-xx` (alternativas), `RFC-QA-xx` (questões), `RFC-RISCO-xx`, no formato `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`. Não apague linhas de outros documentos.
4. Formatação Markdown: cada parágrafo, item e célula em uma única linha na fonte.
5. Registro do processo: se o projeto tiver um registro de processo (padrão `docs/REGISTRO-PROCESSO.md`), use a skill `registro-processo` para registrar esta etapa com o prompt literal, o que foi entregue e os ajustes feitos, enquanto os detalhes estão frescos.
6. Reporte o caminho, a contagem de palavras, alternativas e questões, e os ADRs referenciados ou faltantes.

---

## PRINCÍPIOS

- **Escrito para ser revisado.** Cada seção deve dar ao revisor algo para julgar. Informação sem juízo possível vai para o FDD.
- **Alternativas com trade-off, não com defeito.** "Descartada porque é pior" não ensina nada; "descartada porque exige infraestrutura que o time de 3 pessoas não opera" permite ao revisor discordar com base.
- **Questões em aberto são convite, não falha.** Esconder uma dúvida em prosa afirmativa tira do revisor a chance de ajudar.
- **Altura de arquitetura.** Componentes e responsabilidades, sim; campos e códigos, não.
- **Curto por escolha.** Um RFC de 10 páginas não é revisado; é aprovado por cansaço.
- **Fidelidade à fonte.** Em Modo Fontes, não atribua à equipe alternativas ou argumentos que ela não levantou sem marcar como "não discutida" ou "observação do autor".

---

## CASOS DE BORDA

**Fonte sem alternativas discutidas:** inclua as duas alternativas mais plausíveis marcadas como "não discutidas", com o trade-off provável, e uma questão em aberto pedindo que a equipe confirme o descarte.

**Decisões demais para um RFC:** se a fonte tem mais de 8 decisões significativas, provavelmente há mais de uma proposta. Sugira dividir em RFCs por tema ou escrever ADRs e manter o RFC como visão geral.

**Sem PRD existente:** o RFC ganha uma seção "Contexto e problema" um pouco maior, mas ainda em nível de arquitetura; sugira o PRD como próximo passo se o problema de produto estiver mal definido.

**RFC rejeitado:** mantenha o arquivo com status "Rejeitado" e o motivo no histórico de revisão. Um RFC rejeitado documenta um caminho que não se deve retomar sem novo contexto.

**Pasta com vários RFCs:** siga o padrão de numeração existente e atualize o índice, se houver.

---

Ao concluir, retorne o caminho do RFC.
