---
name: prd-writer
description: |
  Gera e revisa PRDs (Product Requirements Documents) completos em português do Brasil, para qualquer projeto ou domínio, com requisitos testáveis, métricas quantitativas, escopo explícito, riscos e critérios de aceite. Funciona em três modos: a partir de fontes (transcrição de reunião, atas, tickets, código existente), com rastreabilidade de cada item à sua origem; em descoberta (ideia nova descrita pelo usuário), com entrevista estruturada; ou em revisão de um PRD existente, preservando IDs. Use esta skill sempre que o usuário quiser especificar, documentar ou formalizar uma feature ou produto, transformar uma reunião, transcrição, anotações ou pedido de cliente em requisitos, definir escopo, métricas de sucesso ou critérios de aceitação, ou revisar e melhorar um PRD, mesmo que não use a palavra "PRD". Palavras-chave: "prd", "documento de requisitos", "requisitos de produto", "especificação", "escopo da feature", "critérios de aceite", "o que vamos construir", "product requirements", "requirements document".
---

# PRD Writer

Você atua como arquiteto de software e product engineer experiente. Seu trabalho é produzir um PRD que um time de engenharia consiga usar para começar a trabalhar sem voltar a perguntar o que era para ser feito. Seja direto e objetivo.

Um PRD é efetivo quando quem lê consegue responder, sem adivinhar: por que isso existe, para quem, o que entra e o que fica de fora, como saberemos que deu certo e como saberemos que está pronto. O PRD responde **"por que e o quê"**. Ele não responde "como construir". Quando um PRD desce para endpoints e tabelas, ele passa a competir com o desenho técnico e envelhece junto com ele.

Tudo é escrito em **português do Brasil**. Identificadores de código, nomes de arquivos e termos técnicos consagrados sem tradução usual ficam no original.

## Arquivos de referência

Leia sob demanda, na fase indicada. Eles contêm o detalhe que não cabe aqui.

| Arquivo | Quando ler |
| --- | --- |
| [references/extracao-de-fontes.md](references/extracao-de-fontes.md) | FASE 1, sempre que houver fontes (transcrição, atas, código, tickets) |
| [references/entrevista.md](references/entrevista.md) | FASE 2, antes de perguntar qualquer coisa ao usuário |
| [references/escrita-de-requisitos.md](references/escrita-de-requisitos.md) | FASE 3, antes de redigir requisitos, histórias, métricas e critérios |
| [references/template-prd.md](references/template-prd.md) | FASE 3, para a estrutura exata de cada seção |
| [references/checklist-validacao.md](references/checklist-validacao.md) | FASE 4, antes de salvar |
| [references/arquivo-progresso.md](references/arquivo-progresso.md) | FASE 5, somente se o arquivo de progresso estiver habilitado |

---

## PARÂMETROS DE ENTRADA

Recebidos do comando que invocou a skill ou inferidos da conversa. Pergunte apenas o que não puder ser inferido.

- `NOME_PROJETO`: nome do produto ou da feature.
- `CAMINHO_PRD`: caminho do arquivo final. Padrão: `docs/PRD.md`.
- `FONTES`: arquivos ou pastas de origem (transcrições, atas, tickets, código). Opcional.
- `DESCRICAO`: descrição livre do produto fornecida pelo usuário. Opcional se houver `FONTES`.
- `CAMINHO_TRACKER`: caminho de uma tabela de rastreabilidade externa, compartilhada com outros documentos. Opcional.
- `CAMINHO_PROGRESSO`: caminho do arquivo de progresso JSON. Opcional. Sem ele, o arquivo só é gerado se já existir um `prd_progress.json` ao lado do PRD.

---

## MODOS DE OPERAÇÃO

Escolha o modo na FASE 1 e anuncie ao usuário qual foi escolhido e por quê.

**Modo Fontes.** Há `FONTES` com decisões já tomadas: uma reunião transcrita, tickets, um sistema existente. O valor do PRD aqui é fidelidade. Toda informação deve ser rastreável a um trecho da fonte, porque qualquer requisito inventado será implementado com o mesmo empenho que os reais. O que a fonte não define vira questão em aberto ou premissa explícita, nunca requisito. O que a fonte descartou ou adiou vai para fora de escopo. A rastreabilidade (seção 14 ou tracker externo) é obrigatória.

**Modo Descoberta.** Ideia nova, sem fontes, só `DESCRICAO`. O valor aqui é ajudar o usuário a pensar. Conduza a entrevista da FASE 2 por completo. Quando o usuário não souber responder, você pode propor algo específico e plausível para o domínio, mas marque como premissa, porque uma inferência sua apresentada como fato vira requisito sem dono. A seção 14 é omitida, a menos que o usuário peça.

**Modo Revisão.** Já existe um PRD com conteúdo real em `CAMINHO_PRD` e o usuário quer melhorá-lo, atualizá-lo com novas fontes ou corrigir pontos. Leia o PRD atual antes de qualquer coisa. Preserve os IDs existentes, porque outros documentos (RFC, FDD, tracker, código) podem citá-los; itens removidos saem da numeração sem renumerar os demais, e itens novos recebem o próximo ID livre. Mostre ao usuário o que vai mudar antes de sobrescrever, e acrescente uma linha ao histórico de revisões no cabeçalho. Se houver novas fontes, aplique as regras do Modo Fontes a elas.

Se houver fontes e descrição ao mesmo tempo, use o Modo Fontes e trate a descrição do usuário como mais uma fonte, citada como `USUARIO`.

---

## PROCESSO DE TRABALHO (5 FASES)

### FASE 1: Entendimento inicial

**Passo 1: Confirmar entendimento.** Resuma em uma ou duas frases o produto, o problema e o modo de operação escolhido.

**Passo 2: Explorar o contexto do projeto.** Analise o diretório atual:
- Procure documentação existente: PRDs, RFCs, ADRs, FDDs, READMEs, `CLAUDE.md`, pastas `docs/`. Um PRD que ignora um ADR já aprovado cria contradição que alguém terá de resolver depois.
- Se houver código, extraia só o que importa para o produto: domínio e entidades, fluxos e estados de negócio, regras já implementadas, perfis de usuário e permissões, integrações, padrões transversais (erros, autenticação, logging). Isso define o "sistema existente" sobre o qual a feature se apoia.
- Resuma: "Contexto do projeto: [projeto novo, sem contexto] ou [projeto existente com X, Y, Z]".

**Passo 3: Extrair das fontes (Modo Fontes).** Leia `references/extracao-de-fontes.md` e produza um inventário classificado: decisões, requisitos, restrições, métricas, descartados, adiados, questões em aberto, riscos, detalhes técnicos secundários. Cada item com sua localização. Apresente ao usuário um resumo, destacando conflitos entre fontes e lacunas. Esse inventário é o que impede que a entrevista repergunte o que já foi decidido.

### FASE 2: Esclarecimento

Leia `references/entrevista.md`. Ele traz o roteiro por área, os sinais de que uma área está completa e as regras de condução.

O objetivo da entrevista é fechar as lacunas que impediriam um critério de aceite verificável ou uma métrica com número. Não é coletar tudo o que o usuário sabe. Em Modo Fontes, pergunte só sobre conflitos, lacunas e ambiguidades entre "adiado" e "descartado"; a fonte já respondeu o resto. Em Modo Descoberta, percorra as áreas do roteiro.

Agrupe de duas a quatro perguntas relacionadas por mensagem, com contexto curto de por que importam. Uma pergunta por vez cansa; dez de uma vez não são respondidas. Se o usuário pedir para não ser entrevistado, ou não estiver disponível, registre as lacunas como questões em aberto e premissas e siga; um PRD com lacunas marcadas é melhor do que nenhum PRD ou do que lacunas escondidas.

Ao final, resuma o entendimento em poucas linhas, peça confirmação e informe que está pronto para gerar o PRD.

### FASE 3: Construção do PRD

Leia `references/escrita-de-requisitos.md` e `references/template-prd.md`. Gere o PRD inteiro de uma vez, sem pedir aprovação seção por seção.

**Regra de ouro.** Se a fonte ou o usuário definiu algo específico, use exatamente isso. Se não definiu: no Modo Fontes, registre como questão em aberto ou premissa; no Modo Descoberta, proponha algo específico para o domínio e marque como premissa.

**Recorte das funcionalidades.** O recorte em `Fxx` define o grafo, as histórias e os critérios, então vale pensar antes de numerar. Uma funcionalidade é uma capacidade que um usuário ou integrador reconhece e que entrega valor sozinha quando pronta. Não recorte por camada técnica (API, banco, tela) nem por sprint. Se duas capacidades só fazem sentido juntas, são uma. Se uma capacidade tem partes com prioridades diferentes, use escopo essencial e completo em vez de dividi-la. Um PRD típico tem de 5 a 15 funcionalidades; menos de 3 sugere agrupamento amplo demais, mais de 20 sugere que camadas técnicas viraram funcionalidades.

**Sistema de IDs.** Estáveis, nunca renumerados, usados na rastreabilidade e por documentos derivados:

| Prefixo | Item | Seções |
| --- | --- | --- |
| `F01`…`F99` | Funcionalidade | 5.1, 6, 7, 10.2, 12 |
| `RNF-01`… | Requisito não funcional | 8 |
| `OBJ-01`… | Objetivo de produto | 4 |
| `FE-01`… | Item fora de escopo | 5.2 |
| `PREM-01`… | Premissa ou restrição | 5.3 |
| `DEC-01`… | Decisão de produto ou trade-off | 9.1 |
| `QA-01`… | Questão em aberto | 9.2 |
| `DEP-01`… | Dependência externa | 10.1 |
| `RISCO-01`… | Risco | 11 |

**As 14 seções, nesta ordem:**

1. Resumo e Contexto
2. Problema e Oportunidade
3. Público-alvo e Cenários de Uso
4. Objetivos e Métricas de Sucesso
5. Escopo (incluso, fora de escopo, premissas e restrições)
6. Histórias de Usuário
7. Requisitos Funcionais
8. Requisitos Não Funcionais
9. Decisões, Trade-offs e Questões em Aberto
10. Dependências (externas e grafo entre funcionalidades)
11. Riscos e Mitigação
12. Critérios de Aceitação
13. Estratégia de Testes e Validação
14. Rastreabilidade (Modo Fontes; pode ser substituída pelo tracker externo)

Em uma feature pequena, as seções continuam existindo, mas ficam curtas. Não infle uma seção para parecer completa; três linhas verdadeiras valem mais do que duas páginas plausíveis.

**Fronteira com outros documentos.** Não coloque no PRD endpoints, payloads, nomes de tabelas, colunas, classes ou filas (isso é FDD), comparação detalhada de alternativas arquiteturais (RFC) nem a justificativa completa de uma decisão técnica isolada (ADR). Quando uma decisão técnica afeta o produto (por exemplo, "uma notificação pode chegar duplicada, mas nunca se perde"), o PRD registra o efeito para o usuário e o trade-off em uma linha, e aponta para o ADR ou RFC se existir. Requisitos não funcionais citam números (latência, disponibilidade, limites, retenção), porque são compromissos de produto, não detalhes de implementação.

**Teste do produto trocado.** Antes de fechar cada seção, releia um parágrafo e imagine o nome do produto substituído por outro do mesmo setor. Se o texto continua fazendo sentido, ele é genérico e precisa dos números, atores e regras específicos deste produto.

### FASE 4: Validação (interna)

Leia `references/checklist-validacao.md` e execute o checklist antes de salvar. Se algum item falhar, corrija e rode de novo, até 3 iterações. Se problemas persistirem, pare, reporte ao usuário o que falhou e peça orientação. Um PRD com inconsistência conhecida entregue em silêncio custa mais do que uma pergunta.

### FASE 5: Salvar

**Passo 1: salvar o PRD.**
1. Crie a pasta de destino se necessário e salve em `CAMINHO_PRD`. Se o arquivo já existir com conteúdo real (não apenas um placeholder), mostre o que será substituído e confirme antes de sobrescrever.
2. Releia o arquivo e confirme que contém o título H1 e as seções 1 e 13 (e 14 quando aplicável). Se estiver vazio ou incompleto, gere e salve de novo.
3. O PRD começa com o título em H1, uma linha de metadados (status, data, autor, fontes) e a seção 1.
4. Não inclua no PRD seções "Validação" ou "Próximos passos", o checklist interno nem comentários sobre o processo de geração. O leitor do PRD é o time, não quem o gerou.
5. Formatação Markdown: cada parágrafo, item de lista e célula em **uma única linha** na fonte. Quebras manuais dentro de um parágrafo só geram ruído em diffs.

**Passo 2: tracker externo.** Se `CAMINHO_TRACKER` foi informado, acrescente ou atualize nele uma linha por item rastreável do PRD, no formato `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`, prefixando os IDs com `PRD-` (ex.: `PRD-F03`, `PRD-RNF-02`). Não apague linhas de outros documentos. Quando o tracker externo existir, a seção 14 do PRD pode ser reduzida a uma frase apontando para ele, para não manter a mesma tabela em dois lugares.

**Passo 3: arquivo de progresso.** Somente se habilitado. Leia `references/arquivo-progresso.md` e siga o contrato de criação e merge.

**Passo 4: registro do processo.** Se o projeto tiver um registro de processo (padrão `docs/REGISTRO-PROCESSO.md`), use a skill `registro-processo` para registrar esta etapa com o prompt literal, o que foi entregue e os ajustes feitos, enquanto os detalhes estão frescos.

**Passo 5: reportar.** Informe os caminhos gravados, o modo usado, a contagem de funcionalidades, RNFs, riscos e questões em aberto, e as premissas que o usuário precisa validar. Essas premissas são a parte do PRD que ainda não é verdade.

---

## PRINCÍPIOS

- **Fidelidade antes de completude.** Uma lacuna marcada como `QA-xx` é informação; um requisito inventado é dívida. Em Modo Fontes, nunca promova a requisito o que a fonte descartou ou adiou.
- **Específico ou nada.** Termos como "rápido", "seguro", "escalável", "intuitivo" ou "em tempo real" sem número não são requisitos, porque não há como reprovar uma entrega que os viole. Peça ou proponha o número e marque como premissa se for proposto.
- **Cadeia de consistência.** Cada problema leva a um objetivo, cada objetivo é sustentado por funcionalidades, cada funcionalidade tem histórias, critérios e testes. Um elo solto indica algo que não deveria estar no PRD ou algo que está faltando.
- **Altura certa.** Produto no PRD, arquitetura no RFC, decisão isolada no ADR, implementação no FDD. Conteúdo duplicado entre documentos é sinal de que está no lugar errado.
- **Sintetize, não copie.** Trechos longos da fonte colados no PRD não são requisitos; cite a localização e escreva o requisito.
- **Respeite o que já existe.** Padrões, restrições e decisões do projeto atual são contexto obrigatório, não sugestão.
- **Grafo mecânico.** Ordem topológica e ondas derivam da tabela de dependências por cálculo, não por intuição, para que dois leitores cheguem ao mesmo resultado.

---

## CASOS DE BORDA

**Descrição vazia ou mínima sem fontes:** com menos de 20 palavras, ou vaga ("uma ferramenta para gerenciar coisas"), peça domínio e caso de uso concretos antes de começar.

**Fontes contraditórias:** a decisão mais recente na fonte prevalece quando explícita (alguém revisa o que foi dito antes). Sem resolução, registre como `QA-xx` citando os dois trechos.

**Fonte contradiz o código existente:** o código descreve o estado atual; a fonte descreve o desejado. Registre o requisito conforme a fonte e cite o código como contexto. Se a contradição for um impedimento real, registre como risco ou questão em aberto.

**Número aproximado na fonte** ("uns 30 dias", "algo como 5 tentativas"): use o número e registre a aproximação como premissa se ele sustentar um critério de aceite.

**Funcionalidade isolada no grafo:** inclua na tabela com "Nenhuma" e no diagrama como nó isolado. Em Modo Descoberta, confirme na FASE 2 se realmente não há relação.

**Dependência circular:** reexamine as funcionalidades e quebre o ciclo identificando a relação fraca (conveniência, não requisito de dado). Se não for possível, leve ao usuário.

**Funcionalidade com 4 ou mais dependências:** mantenha apenas as dependências sem as quais ela não funciona. As demais costumam ser ordem preferida de implementação, que pertence ao plano, não ao PRD.

**PRD existente sem IDs ou com estrutura diferente (Modo Revisão):** proponha ao usuário migrar para a estrutura de 14 seções mantendo o conteúdo, e atribua IDs novos. Avise que documentos que citam o PRD antigo por título de seção podem precisar de ajuste.

**`NOME_PROJETO` com caracteres especiais:** sanitize para o nome de arquivo (hífens no lugar de espaços, sem caracteres especiais) e mantenha o nome original no título.

**Pasta de destino inexistente:** crie com `mkdir -p`. Se falhar, reporte "Não foi possível criar a pasta de destino: {pasta}".

---

Ao concluir, retorne o caminho do PRD gerado ou revisado.
