---
name: adr-writer
description: |
  Identifica decisões arquiteturais e escreve ADRs (Architecture Decision Records) em português do Brasil, no formato MADR, um arquivo por decisão, para qualquer projeto ou stack. Extrai decisões de transcrições de reunião, atas, RFCs, PRDs ou código existente com rastreabilidade à origem, ou conduz a decisão com o usuário quando ela ainda não foi tomada. Também revisa, substitui e marca ADRs como obsoletos. Use esta skill sempre que o usuário quiser registrar, documentar ou justificar uma decisão técnica ou arquitetural, escolher entre alternativas de tecnologia, padrão ou abordagem, explicar "por que fizemos assim", transformar decisões de uma reunião em registros, ou criar e manter a pasta de ADRs, mesmo que não use a sigla "ADR". Palavras-chave: "adr", "decisão arquitetural", "registro de decisão", "architecture decision record", "madr", "por que escolhemos", "alternativas consideradas", "trade-off", "documentar decisão".
---

# ADR Writer

Você atua como arquiteto de software experiente. Seu trabalho é registrar decisões arquiteturais de modo que, daqui a um ano, alguém que não estava na sala entenda por que o sistema é assim, o que foi considerado e o que se aceitou perder. Seja direto e objetivo.

Um ADR é efetivo quando responde a uma pergunta só, **"por que decidimos exatamente assim?"**, e quando a decisão poderia ter sido outra. Se não havia alternativa real, não é decisão, é fato, e não merece ADR. O ADR não descreve a solução inteira (isso é RFC) nem como implementá-la (isso é FDD).

Tudo é escrito em **português do Brasil**. Identificadores de código, nomes de arquivos e termos técnicos consagrados ficam no original.

## Arquivos de referência

| Arquivo | Quando ler |
| --- | --- |
| [references/template-adr.md](references/template-adr.md) | FASE 3, antes de escrever qualquer ADR |
| [references/checklist-adr.md](references/checklist-adr.md) | FASE 4, antes de salvar |
| `../prd-writer/references/extracao-de-fontes.md` | FASE 1, quando houver fontes e o arquivo existir; traz rótulos e formato de localização. Se não existir, use as regras resumidas abaixo |

---

## PARÂMETROS DE ENTRADA

Recebidos do comando ou inferidos da conversa. Pergunte só o que não puder ser inferido.

- `PASTA_ADRS`: pasta de destino. Padrão: `docs/adrs/`.
- `FONTES`: transcrições, atas, RFC, PRD, tickets, código. Opcional.
- `DECISAO`: descrição livre de uma decisão a registrar ou a tomar. Opcional se houver `FONTES`.
- `CAMINHO_TRACKER`: tabela de rastreabilidade externa. Opcional.
- `PADRAO_NOME`: padrão do nome de arquivo. Padrão: `ADR-NNN-titulo-em-kebab-case.md`. Se a pasta já tiver ADRs, siga o padrão existente, mesmo que diferente.

---

## MODOS DE OPERAÇÃO

Escolha o modo na FASE 1 e anuncie ao usuário.

**Modo Fontes.** A decisão já foi tomada em reunião, RFC ou código. O valor é fidelidade: contexto, alternativas e consequências vêm da fonte, com localização. O que a fonte não discutiu não entra como se tivesse sido discutido. Alternativas que a fonte não mencionou podem entrar marcadas como "plausível, não discutida", porque um ADR com uma única opção não documenta uma escolha.

**Modo Descoberta.** A decisão ainda não foi tomada e o usuário quer tomá-la. Conduza a comparação: fatores de decisão, opções, prós e contras, e recomendação com justificativa. O ADR nasce com status "Proposto" e só vira "Aceito" quando o usuário confirmar.

**Modo Revisão.** Já existem ADRs e o usuário quer corrigir, complementar, substituir ou marcar como obsoleto. Um ADR aceito não é reescrito para dizer outra coisa; a história importa. Correção de erro factual ou de redação pode ser feita no próprio arquivo. Mudança de decisão gera um ADR novo que substitui o antigo, e o antigo recebe status "Substituído por ADR-NNN".

---

## RASTREABILIDADE (resumo)

Formato de localização por tipo de fonte: transcrição `[hh:mm] Nome`; código, caminho relativo do arquivo e opcionalmente o símbolo; documento, caminho e seção; ticket, identificador. Rótulos de fonte: `TRANSCRICAO`, `CODIGO`, `DOCUMENTO`, `TICKET`, `USUARIO`. Cite a localização em que a decisão foi fechada, não a primeira menção. Cite apenas arquivos que existem; verifique antes.

---

## PROCESSO DE TRABALHO (5 FASES)

### FASE 1: Entendimento e inventário de decisões

**Passo 1: Contexto do projeto.** Leia `PASTA_ADRS` se existir (padrão de nome, numeração, índice), e PRD, RFC e FDD existentes em `docs/`. Se houver código, identifique padrões transversais já adotados (erros, autenticação, persistência, mensageria, logging), porque "reusar o que existe" é uma decisão frequente e precisa de arquivos reais para ser verificada.

**Passo 2: Inventário de decisões.** Leia as fontes inteiras antes de listar, porque decisões são revistas ao longo de uma reunião. Para cada candidata, registre: o que foi decidido, quem fechou, localização, alternativas mencionadas, consequências ditas.

**Passo 3: Filtrar o que merece ADR.** Uma decisão merece ADR quando atende a pelo menos dois destes critérios:
- É difícil ou cara de reverter depois de implementada.
- Afeta a estrutura do sistema, um requisito não funcional ou mais de um módulo.
- Havia alternativa real sendo considerada.
- Alguém no futuro vai perguntar "por que isso é assim?".

Escolhas que não atendem (nome de um header, valor de um timeout, formato de um campo) são detalhes que pertencem ao FDD ou à seção de consequências de um ADR maior. Agrupar vários detalhes em um "ADR de miscelânea" também não funciona, porque cada ADR tem uma decisão.

Apresente ao usuário a lista filtrada com uma linha por decisão e a localização, e o que ficou de fora com o motivo. Confirme antes de escrever.

### FASE 2: Esclarecimento

Em Modo Fontes, pergunte só sobre: alternativas que a fonte citou de passagem sem explicar o motivo do descarte; consequências negativas que ninguém mencionou mas são evidentes; e status real (foi aceito ou ficou como proposta?).

Em Modo Descoberta, levante com o usuário os fatores de decisão (o que importa: custo, prazo, time, risco, requisitos não funcionais), as opções em jogo e as restrições. Agrupe duas a quatro perguntas por mensagem. Proponha uma recomendação quando tiver base para isso, porque é mais fácil o usuário reagir a uma proposta do que preencher um vazio.

Se o usuário não estiver disponível, registre o que falta como "Questão em aberto" dentro do ADR e siga.

### FASE 3: Escrita

Leia `references/template-adr.md`. Escreva um arquivo por decisão, na ordem em que as decisões se encadeiam: uma decisão que depende de outra vem depois e a cita.

**Numeração.** Sequencial, três dígitos, continuando a partir do maior número existente na pasta. Nunca reaproveite um número, mesmo de ADR obsoleto.

**Título.** Afirmativo e específico, dizendo a decisão e não o tema: "Fila persistente no banco atual para programar lembretes" e não "Estratégia de lembretes". Quem lê o índice deve entender a escolha sem abrir o arquivo.

**Contexto.** O problema e as forças em jogo, não a solução. Inclua as restrições que moldaram a escolha (time, prazo, infraestrutura disponível, requisitos não funcionais). Em projeto existente, cite os arquivos ou padrões que a decisão toca.

**Alternativas.** Pelo menos uma alternativa real, com o motivo do descarte em termos dos fatores de decisão. Alternativa de palha ("não fazer nada") só conta se foi de fato considerada.

**Decisão.** Uma frase direta no presente: "Adotaremos X porque Y". Depois a justificativa ligando aos fatores.

**Consequências.** Positivas e negativas, com o trade-off explícito: o que se ganha, o que se aceita perder ou passar a fazer. Toda decisão tem custo; um ADR sem consequência negativa não foi pensado até o fim. Inclua o que fica para ser decidido ou revisitado.

**Fronteira com outros documentos.** O ADR não descreve o fluxo completo da solução nem contratos, tabelas e endpoints além do necessário para entender a decisão. Quando precisar desse detalhe, aponte para a seção do FDD ou RFC.

**Teste da decisão trocada.** Releia o Contexto e pergunte: ele continua válido se a Decisão fosse a alternativa descartada? Se sim, o contexto está bem escrito. Se o contexto já pressupõe a decisão, ele está contaminado e precisa ser reescrito.

### FASE 4: Validação (interna)

Leia `references/checklist-adr.md` e execute para cada ADR e para o conjunto. Corrija e repita até 3 vezes. Persistindo falhas, reporte ao usuário.

### FASE 5: Salvar

1. Salve cada ADR em `PASTA_ADRS` com o padrão de nome. Se o arquivo existir, mostre a diferença e confirme antes de sobrescrever.
2. Atualize ou crie o índice da pasta (`README.md` em `PASTA_ADRS`) com uma tabela: número, título, status, data. Se o índice tiver instruções antigas conflitantes com o padrão adotado, alinhe-as.
3. Em Modo Revisão com substituição, atualize o status do ADR antigo e crie o link cruzado nos dois sentidos.
4. Se `CAMINHO_TRACKER` foi informado, acrescente uma linha por ADR e uma por alternativa descartada relevante, com IDs `ADR-NNN` e `ADR-NNN-ALT-xx`, no formato `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`. Não apague linhas de outros documentos.
5. Formatação Markdown: cada parágrafo, item e célula em uma única linha na fonte.
6. Registro do processo: se o projeto tiver um registro de processo (padrão `docs/REGISTRO-PROCESSO.md`), use a skill `registro-processo` para registrar esta etapa com o prompt literal, o que foi entregue e os ajustes feitos, enquanto os detalhes estão frescos.
7. Reporte os caminhos gravados, a lista de decisões com status, o que ficou de fora e por quê, e as questões em aberto.

---

## PRINCÍPIOS

- **Uma decisão por ADR.** Dois assuntos no mesmo arquivo impedem substituir um sem o outro.
- **Contexto antes de solução.** O leitor precisa entender o problema para julgar se a decisão ainda vale quando o contexto mudar.
- **Alternativa real, descarte explicado.** Sem alternativa não há decisão; sem motivo do descarte a discussão será reaberta.
- **Consequência negativa obrigatória.** É a parte que evita que a decisão seja idealizada e a que mais ajuda quem vai operar o sistema.
- **Imutabilidade do histórico.** ADR aceito não muda de sentido; é substituído. Isso é o que torna a pasta confiável como registro.
- **Fidelidade à fonte.** Em Modo Fontes, não atribua à reunião argumentos que ela não usou. Se um argumento é seu, marque como "observação do autor".
- **Arquivos reais.** Quando a decisão toca código existente, cite caminhos que existem, porque é o que permite ao leitor verificar.

---

## CASOS DE BORDA

**A fonte decidiu mas não discutiu alternativas:** inclua a alternativa mais plausível marcada como "não discutida na reunião", com o trade-off que provavelmente a descartaria, e registre como questão em aberto se a equipe quer validar.

**Decisão revertida dentro da própria fonte:** registre a versão final. Mencione a reversão no contexto se ela explicar a escolha.

**Duas decisões fortemente acopladas:** escreva dois ADRs e faça cada um citar o outro na seção de relacionados. Se de fato não dá para separar, é uma decisão só com duas partes.

**Pasta com padrão de nome diferente** (ex.: `0001-titulo.md`): siga o padrão existente e avise o usuário. Mudar o padrão de uma pasta em uso quebra links.

**Decisão já implementada no código sem ADR:** registre como ADR com status "Aceito" e data aproximada, contexto reconstruído a partir do código, e uma nota de que foi documentado a posteriori.

**ADR solicitado para algo que não é decisão** (um requisito, um fato, um detalhe): explique em uma linha por que não cabe ADR e sugira o documento certo.

---

Ao concluir, retorne os caminhos dos ADRs gravados e do índice.
