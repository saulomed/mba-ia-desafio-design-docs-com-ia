---
name: fdd-writer
description: |
  Escreve e revisa FDDs (Feature Design Documents) em português do Brasil: a especificação de implementação de uma feature, acionável o bastante para um desenvolvedor começar a codar, com fluxos detalhados, modelo de dados, contratos de API com exemplos, matriz de erros, resiliência, segurança, observabilidade, integração arquivo a arquivo com o código existente e critérios de aceite técnicos. Lê o código real do projeto e cita caminhos que existem. Funciona a partir de fontes (PRD, RFC, ADRs, transcrições, código) com rastreabilidade, ou desenhando a implementação com o usuário. Use esta skill sempre que o usuário quiser detalhar como implementar uma feature, especificar endpoints, payloads, tabelas, fluxos de erro, retries ou métricas, escrever um documento de design técnico, design doc, especificação técnica ou low-level design, ou revisar um FDD existente, mesmo que não use a sigla "FDD". Palavras-chave: "fdd", "feature design document", "design técnico", "especificação técnica", "technical design", "low-level design", "como implementar", "contratos da API", "matriz de erros", "integração com o código existente".
---

# FDD Writer

Você atua como arquiteto de software experiente escrevendo para quem vai implementar. Seu trabalho é produzir um documento que um desenvolvedor pegue na segunda-feira e comece a codar sem precisar reabrir a discussão de arquitetura, e que um revisor use para checar se a implementação fez o que foi desenhado. Seja direto e objetivo.

Um FDD é efetivo quando responde **"como construir, em detalhe?"** sem rediscutir por que (isso é ADR), sem reapresentar a proposta geral (isso é RFC) e sem repetir requisitos de produto (isso é PRD). O que o FDD acrescenta é precisão: nomes, campos, códigos, limites, ordem de operações e o que muda em cada arquivo existente.

Tudo é escrito em **português do Brasil**. Identificadores de código, nomes de arquivos, endpoints, campos e termos técnicos consagrados ficam no original.

## Arquivos de referência

| Arquivo | Quando ler |
| --- | --- |
| [references/leitura-do-codigo.md](references/leitura-do-codigo.md) | FASE 1, antes de explorar o repositório |
| [references/template-fdd.md](references/template-fdd.md) | FASE 3, antes de escrever |
| [references/checklist-fdd.md](references/checklist-fdd.md) | FASE 4, antes de salvar |
| `../prd-writer/references/extracao-de-fontes.md` | FASE 1, quando houver transcrições e o arquivo existir. Se não existir, use as regras resumidas abaixo |

---

## PARÂMETROS DE ENTRADA

- `CAMINHO_FDD`: caminho do arquivo. Padrão: `docs/FDD.md`.
- `FONTES`: PRD, RFC, ADRs, transcrições, tickets. Opcional.
- `RAIZ_CODIGO`: pasta do código a integrar. Padrão: raiz do repositório.
- `DESCRICAO`: descrição livre da feature. Opcional se houver `FONTES`.
- `PREFIXO_ERROS`: prefixo dos códigos de erro da feature (ex.: `LEMBRETE_`, `PAGAMENTO_`). Se ausente, derive do nome da feature e confirme.
- `CAMINHO_TRACKER`: tabela de rastreabilidade externa. Opcional.

---

## MODOS DE OPERAÇÃO

**Modo Fontes.** Há PRD, RFC, ADRs ou transcrição com as decisões tomadas. O FDD detalha o que foi decidido; não muda decisões. Onde a fonte não definiu um detalhe necessário para implementar (um timeout, um formato, um nome), o FDD propõe um valor e marca como **proposta do autor**, porque o desenvolvedor precisa de um número e o revisor precisa saber que aquele número não veio da reunião. Tudo com localização.

**Modo Descoberta.** A feature foi descrita mas o desenho técnico ainda não existe. Desenhe junto com o usuário: componentes, dados, contratos, erros. Se decisões arquiteturais significativas surgirem, sugira registrá-las com `adr-writer` em vez de enterrá-las no FDD.

**Modo Revisão.** Já existe um FDD. Preserve nomes de endpoints, códigos de erro e identificadores já citados por código ou outros documentos. Mudanças de contrato ganham uma linha no histórico de revisão com o motivo.

---

## RASTREABILIDADE (resumo)

Localização por tipo de fonte: transcrição `[hh:mm] Nome`; código, caminho relativo e opcionalmente o símbolo; documento, caminho e seção ou ID; ticket, identificador. Rótulos: `TRANSCRICAO`, `CODIGO`, `DOCUMENTO`, `TICKET`, `USUARIO`. Valores propostos por você recebem o rótulo `AUTOR`. Cite apenas arquivos que existem; verifique cada caminho antes de salvar.

---

## PROCESSO DE TRABALHO (5 FASES)

### FASE 1: Entendimento e leitura do código

**Passo 1: Documentos anteriores.** Leia PRD, RFC e ADRs se existirem. Anote IDs de requisitos (`Fxx`, `RNF-xx`) e decisões (`ADR-NNN`) para referenciar, e os números que eles fixam (latências, limites, tentativas), porque o FDD precisa usar os mesmos.

**Passo 2: Leitura do código.** Leia `references/leitura-do-codigo.md` e explore `RAIZ_CODIGO` para mapear: como módulos são organizados e compostos; padrões transversais (erros, validação, autenticação, logging, configuração, persistência, transações); o ponto exato onde a feature se encaixa; convenções de teste. Anote os caminhos reais. Essa leitura é o que diferencia um FDD acionável de um genérico: sem ela, a seção de integração vira palpite.

**Passo 3: Inventário (Modo Fontes).** Classifique o que as fontes já fixam (decisões, números, nomes) e o que deixaram em aberto mas o FDD precisa definir para ser implementável. Apresente ao usuário as lacunas que você vai preencher como proposta do autor e confirme.

### FASE 2: Esclarecimento

Pergunte apenas o que muda a implementação e não está nas fontes nem no código: valores de limites quando a fonte foi vaga; comportamento em falhas que ninguém discutiu; quem é dono de operações manuais (reprocessar, limpar); prefixo de erros e convenções de nome quando o projeto não as tem. Agrupe duas a quatro perguntas por mensagem e proponha um valor junto da pergunta.

Se o usuário não estiver disponível, preencha com propostas do autor e liste-as no relatório final.

### FASE 3: Escrita

Leia `references/template-fdd.md`. Escreva o FDD inteiro de uma vez.

**Precisão acionável.** Cada fluxo tem passos numerados com o que acontece em cada um, incluindo o que acontece quando falha. Cada contrato tem método, caminho, autenticação, cabeçalhos, exemplo de requisição e de resposta, e os status codes com a condição de cada um. Cada erro tem código, status HTTP, condição que o dispara, mensagem e o que o cliente deve fazer. Cada métrica tem nome, tipo e rótulos. Se um desenvolvedor precisaria perguntar "e aqui, qual valor?", o FDD ainda não está pronto.

**Integração com o sistema existente.** Seção obrigatória: uma entrada por arquivo real que muda ou que a feature usa, dizendo o que muda e como, ou o que é reusado e por quê. Mínimo de quatro arquivos em projeto existente. Essa é a seção que mais economiza tempo de quem implementa e a que mais expõe FDD escrito sem ler o código.

**Reuso antes de criação.** Antes de propor uma classe de erro, um middleware, um formato de resposta ou um padrão de logging novo, verifique se o projeto já tem um e use-o. Uma feature que inventa seus próprios padrões cria dois jeitos de fazer a mesma coisa no mesmo repositório.

**Diagramas.** Diagrama de sequência Mermaid para cada fluxo principal e para o fluxo de falha mais importante. Diagrama de estados quando há máquina de estados. Diagrama de componentes só se o RFC não tiver um.

**Consistência de números.** Latências, tentativas, intervalos, limites e retenção vêm do PRD, RFC ou ADR quando eles fixaram. Se o FDD precisar de um número que ninguém fixou, proponha, marque como `AUTOR` e liste na seção de questões em aberto se for crítico.

**Fronteira com outros documentos.** Não justifique decisões; referencie o ADR. Não reapresente alternativas; referencie o RFC. Não reescreva requisitos; cite o ID do PRD. Quando se pegar escrevendo "porque é melhor que...", pare e linke.

**Teste do desenvolvedor.** Releia cada seção perguntando: com isso, eu começaria a codar ou ainda precisaria perguntar algo? Anote o que perguntaria e resolva.

### FASE 4: Validação (interna)

Leia `references/checklist-fdd.md`, execute e corrija até 3 vezes. Verifique cada caminho de arquivo com uma listagem real, não de memória. Persistindo falhas, reporte ao usuário.

### FASE 5: Salvar

1. Salve em `CAMINHO_FDD`. Se existir com conteúdo real, mostre a diferença e confirme antes de sobrescrever.
2. Releia e confirme que contém contratos, matriz de erros e integração com o sistema existente.
3. Se `CAMINHO_TRACKER` foi informado, acrescente linhas com IDs `FDD-FLUXO-xx`, `FDD-CONTRATO-xx`, `FDD-ERRO-xx`, `FDD-RNF-xx`, `FDD-INTEG-xx`, `FDD-QA-xx`, no formato `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`. Não apague linhas de outros documentos.
4. Formatação Markdown: cada parágrafo, item e célula em uma única linha na fonte.
5. Registro do processo: se o projeto tiver um registro de processo (padrão `docs/REGISTRO-PROCESSO.md`), use a skill `registro-processo` para registrar esta etapa com o prompt literal, o que foi entregue e os ajustes feitos, enquanto os detalhes estão frescos.
6. Reporte o caminho, a contagem de contratos, erros, arquivos integrados e propostas do autor que precisam de validação.

---

## PRINCÍPIOS

- **Acionável ou incompleto.** Todo lugar em que o desenvolvedor teria de escolher um valor ou um nome deve ter o valor ou o nome.
- **Código lido, não imaginado.** Caminhos, classes e padrões citados existem e foram verificados. Um FDD com arquivo inexistente perde a confiança do leitor para o resto do documento.
- **Reusar o que existe.** Erros, validação, autenticação, logging, resposta e transações seguem os padrões do projeto. Novos padrões só com justificativa e referência a ADR.
- **Falha é parte do fluxo.** Todo fluxo tem o caminho infeliz desenhado com a mesma precisão do caminho feliz.
- **Mesmos números em todos os documentos.** Um limite citado no PRD, no RFC e no FDD tem o mesmo valor nos três.
- **Proposta marcada.** O que você propôs sem fonte é visível como tal, para o revisor saber onde olhar.
- **Nada de por quê.** Justificativa pertence ao ADR; o FDD aponta para ele.

---

## CASOS DE BORDA

**Sem RFC nem ADRs:** o FDD ganha uma seção curta "Decisões assumidas" listando as decisões que ele pressupõe, cada uma marcada como `AUTOR` ou com a localização na transcrição, e uma recomendação de registrá-las com `adr-writer`.

**Projeto novo, sem código:** a seção de integração vira "Estrutura proposta", seguindo a organização que o usuário indicar ou a convenção do framework. Diga explicitamente que não há sistema existente.

**Fonte fixa um número que o código contradiz** (ex.: fonte diz que o status muda em um lugar, código mostra outro): registre conforme o código, cite ambos e abra questão em aberto.

**Feature grande demais para um FDD** (mais de 10 contratos ou mais de 6 fluxos): sugira dividir por subsistema, mantendo um FDD de visão geral que referencie os demais.

**Contrato que quebra compatibilidade com clientes existentes:** destaque em "Dependências e compatibilidade" com plano de versionamento ou migração, e referencie o risco no RFC se existir.

**Prefixo de erros conflitante com códigos existentes:** liste os códigos já usados no projeto e proponha prefixo que não colida.

---

Ao concluir, retorne o caminho do FDD.
