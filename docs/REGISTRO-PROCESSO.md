# Registro do Processo — Design Docs do Sistema de Webhooks

Diário de bordo da produção com IA. Fonte para a seção de processo do `README.md`. Horários em UTC-3. Mantido pela skill `registro-processo`.

## Ferramentas utilizadas

| Ferramenta | Modelo ou versão | Papel no processo | Primeira etapa |
| --- | --- | --- | --- |
| Claude Code (app desktop, aba Code) | Claude Opus 5.5 e Claude Fable 5.1, alternados por `/model` | Leitura do repositório, criação do `CLAUDE.md`, criação e revisão das skills de documentação, geração dos documentos | ETAPA-01 |
| Subagentes do Claude Code | Claude Opus 5.5 | Revisão final em paralelo, um agente por documento, e correção dos ADRs e do PRD | ETAPA-14 |
| Skill `anthropic-skills:skill-creator` | não se aplica | Guia de boas práticas para escrever e revisar as skills do projeto | ETAPA-04 |
| Skills do projeto em `.claude/skills/` | não se aplica | `prd-writer`, `adr-writer`, `rfc-writer`, `fdd-writer` e `registro-processo`, cada uma para um tipo de documento | ETAPA-03 |
| Skill `doc-review` (global do usuário) | não se aplica | Revisão crítica de documentos: contradições, ambiguidades, inconsistências e lacunas | ETAPA-07 |
| MCP Context7 | não se aplica | Consulta à documentação atualizada de bibliotecas e especificações (Prisma, Zod, Pino, MySQL, OpenAPI, Redocly CLI e Swagger UI) para confirmar restrições usadas no desenho | ETAPA-09 |
| Redocly CLI, via `npx` | 2.76.0 | Lint da spec OpenAPI e geração da documentação em HTML (Redoc) | ETAPA-10 |
| Swagger UI, via Docker | imagem `swaggerapi/swagger-ui:latest` | Conferência da renderização da spec, com o comando documentado no manual | ETAPA-10 |
| Navegador embutido do app, com Mermaid | Mermaid 11.4.1 | Renderização dos diagramas do FDD para conferir sintaxe e legibilidade | ETAPA-10 |
| Google Chrome em modo headless | não registrada | Impressão do RFC em PDF para medir o número de páginas | ETAPA-14 |

## Índice de etapas

| Etapa | Data e hora | Fase | Objetivo | Iterações | Resultado |
| --- | --- | --- | --- | --- | --- |
| ETAPA-01 | 2026-11-08 12:40 | Preparação | Contextualizar a IA com o repositório e criar o `CLAUDE.md` | 1 | `CLAUDE.md` |
| ETAPA-02 | 2026-11-08 12:43 | Ferramental | Importar configurações do Gemini CLI e do Cursor | 3 | sem arquivo, importação não aplicada |
| ETAPA-03 | 2026-11-08 12:57 | Ferramental | Adaptar a skill `prd-writer` para pt-BR e torná-la agnóstica de projeto | 1 | `.claude/skills/prd-writer/` |
| ETAPA-04 | 2026-11-08 13:12 | Ferramental | Revisar a `prd-writer` para produzir PRDs efetivos | 1 | `.claude/skills/prd-writer/` |
| ETAPA-05 | 2026-11-08 13:23 | Ferramental | Criar skills separadas para ADR, RFC e FDD | 1 | `.claude/skills/adr-writer/`, `rfc-writer/`, `fdd-writer/` |
| ETAPA-06 | 2026-11-08 14:46 | Ferramental | Criar a skill de registro do processo e este registro | 1 | `.claude/skills/registro-processo/`, `docs/REGISTRO-PROCESSO.md` |
| ETAPA-07 | 2026-11-08 15:06 | Documentos | Extrair da transcrição os ADRs decididos na reunião | 2 | `docs/adrs/` (8 ADRs e índice) |
| ETAPA-08 | 2026-11-08 15:29 | Documentos | Consolidar o RFC a partir dos ADRs, com todas as alternativas da reunião | 2 | `docs/RFC.md` |
| ETAPA-09 | 2026-11-08 16:23 | Documentos | Escrever o FDD cobrindo os critérios de aceite do desafio | 3 | `docs/FDD.md`, links nos ADR-003, ADR-004 e ADR-005 |
| ETAPA-10 | 2026-11-08 17:25 | Documentos | Ampliar os diagramas Mermaid do FDD e extrair os contratos para uma spec OpenAPI com manual | 3 | `docs/FDD.md`, `docs/openapi/webhooks.openapi.yaml`, `docs/openapi/README.md`, link no `docs/RFC.md` |
| ETAPA-11 | 2026-11-14 16:30 | Documentos | Escrever o PRD a partir da transcrição, do código e dos documentos já produzidos, validado contra os critérios de aceite do desafio | 1 | `docs/PRD.md` |
| ETAPA-12 | 2026-11-14 16:47 | Documentos | Montar o tracker de rastreabilidade de PRD, RFC, ADRs e FDD no formato obrigatório | 1 | `docs/TRACKER.md`, seção de origem do `docs/FDD.md` e do `docs/RFC.md` |
| ETAPA-13 | 2026-11-14 17:02 | Documentos | Consolidar o README do processo a partir do registro, mantendo o enunciado original | 1 | `README.md` |
| ETAPA-14 | 2026-11-14 17:06 | Revisão final | Validar cada documento contra o checklist do enunciado, com um agente por documento, e corrigir o que foi apontado | 2 | Todos os documentos, `CLAUDE.md` e o template da skill `adr-writer` |

---

## Etapas

### ETAPA-01 — Contextualização do repositório e criação do CLAUDE.md

**Data e hora:** 2026-11-08 12:40 · **Fase:** Preparação · **Ferramenta:** Claude Code (Claude Fable 5.1, confirmado no histórico da sessão) · **Iterações:** 1 · **Registro:** retroativo

**Objetivo:** dar à IA um mapa do repositório antes de qualquer documento: natureza do desafio, restrições, entregáveis, comandos e arquitetura do OMS.

**Prompt:**

```text
/init
```

**O que a IA entregou:** `CLAUDE.md` na raiz com a natureza puramente documental do desafio, a restrição de não alterar `src/`, `prisma/`, `tests/` e `TRANSCRICAO.md`, a tabela de entregáveis com a altura de cada documento, as regras de rastreabilidade, as seis decisões que os ADRs devem cobrir, os comandos de desenvolvimento e a arquitetura do OMS, com destaque para a transação de `OrderService.changeStatus` em `src/modules/orders/order.service.ts` como ponto de integração da feature. Registrou também que não existe código de webhooks no repositório.

**Revisão crítica e ajustes:** sem ajustes. A entrega foi aceita pelo usuário sem pedido de correção.

**Arquivos:** `CLAUDE.md` (criado).

**Lições:** começar pelo `CLAUDE.md` fixa as restrições do desafio para todas as etapas seguintes, sem precisar repeti-las em cada prompt.

### ETAPA-02 — Tentativa de importar configurações do Gemini CLI e do Cursor

**Data e hora:** 2026-11-08 12:43 · **Fase:** Ferramental · **Ferramenta:** Claude Code (Claude Fable 5.1, confirmado no histórico da sessão) · **Iterações:** 3 · **Registro:** retroativo

**Objetivo:** trazer para o Claude Code configurações já existentes em outras ferramentas de IA, sugerido pela IA ao detectar `~/.gemini/settings.json` na ETAPA-01.

**Prompts:**

```text
/import
```

```text
/import --yes=81cc26dc429efb0f26eae8d4e4062687
```

```text
/import --yes=81cc26dc429efb0f26eae8d4e4062687 --dry-run
```

**O que a IA entregou:** varredura com quatro itens importáveis (servidor MCP `context7` duas vezes, instruções `GEMINI.md` e a skill `handoff`, retida por exigir revisão manual) e cinco sem mapeamento automático.

**Revisão crítica e ajustes:**

- Iteração 1: o segundo comando foi digitado no painel de terminal, e o zsh respondeu "no such file or directory: /import" → percebido pela IA → explicou que comandos com barra são do chat do Claude Code, não do shell.
- Iteração 2: o comando com `--dry-run` também foi para o terminal, com o mesmo erro → percebido pela IA → confirmou que o CLI `claude` está instalado e indicou `claude import` como alternativa pelo terminal.

**Arquivos:** nenhum. A importação não foi aplicada.

**Lições:** a ferramenta tem dois lugares de entrada, chat e terminal, e comandos de um não funcionam no outro. A importação não era necessária para os documentos, porque o Context7 já estava configurado no Claude Code.

### ETAPA-03 — Adaptação da skill prd-writer para pt-BR e uso em qualquer projeto

**Data e hora:** 2026-11-08 12:57 · **Fase:** Ferramental · **Ferramenta:** Claude Code (Claude Opus 5.5) · **Iterações:** 1 · **Registro:** retroativo

**Objetivo:** partir do modelo de skill de PRD já presente em `.claude/skills/prd-writer/SKILL.md`, que não estava em português nem servia a qualquer projeto, e transformá-lo em uma skill agnóstica, aproveitando as exigências do desafio.

**Prompts:**

```text
/model claude-opus-5-5
```

Prompt de destaque:

```text
quero que você atue atue como um arquiteto de software experiente e vamos adaptar o modelo da skill de prd wiriter. o modelo está em .claude/skills/prd-writer/SKILL.md. Primeiro vamos transformar tudo sempre para pt-br e vamos criar uma skill agnostica de projeto. Aproveite e use algumas referencias do nosso desafio para criar esta skill da melhor forma possivel
```

**O que a IA entregou:** `SKILL.md` reescrito em pt-BR só com o processo, e quatro referências carregadas por fase: extração de fontes, template do PRD, checklist de validação e arquivo de progresso. Mudanças principais: Modo Fontes, em que inventar é proibido, separado do Modo Descoberta, em que inferências viram premissas; seções ampliadas de 9 para 14; "Fora de escopo" separando descartado de adiado; fronteira de altura entre PRD, RFC e ADR; IDs estáveis para alimentar o tracker no formato de seis colunas do desafio; arquivo de progresso JSON tornado opcional.

**Revisão crítica e ajustes:** a revisão foi feita sobre o modelo original, não sobre a entrega. A IA apontou que o modelo sempre inferia requisitos, o principal risco de alucinação em um desafio que exige rastreabilidade, e que o arquivo de progresso dependia de skills inexistentes no projeto. Ambos foram corrigidos na própria geração. Os exemplos foram escritos em um domínio fictício de lembretes de consulta para não vazarem para os documentos reais.

**Arquivos:** `.claude/skills/prd-writer/SKILL.md` (alterado), `.claude/skills/prd-writer/references/extracao-de-fontes.md`, `template-prd.md`, `checklist-validacao.md`, `arquivo-progresso.md` (criados).

**Lições:** pedir que a IA "atue como arquiteto experiente" e "use referências do desafio" fez as lições do desafio entrarem como regras, não como exemplos copiados.

### ETAPA-04 — Revisão da prd-writer com a skill de criação de skills

**Data e hora:** 2026-11-08 13:12 · **Fase:** Ferramental · **Ferramenta:** Claude Code (Claude Fable 5.1) com a skill `anthropic-skills:skill-creator` · **Iterações:** 1 · **Registro:** retroativo

**Objetivo:** levar a skill de PRD ao melhor nível possível antes de usá-la nos documentos reais.

**Prompts:**

```text
/model claude-fable-5-1
```

Prompt de destaque:

```text
revise a skill do prd-writer que criamos. Vamos deixar a skill o melhor possivel para criarmos PRDs efetivos
```

**O que a IA entregou:** revisão guiada pelas práticas da `skill-creator` e por seis fraquezas encontradas na releitura. Entraram o Modo Revisão, que preserva IDs e registra histórico, atendendo aos ciclos de iteração esperados pelo desafio; uma heurística para recortar funcionalidades; entrevista em grupos de duas a quatro perguntas; o "teste do produto trocado" contra texto genérico; duas referências novas, `entrevista.md` e `escrita-de-requisitos.md`; sumário e histórico de revisões no template.

**Revisão crítica e ajustes:**

- Iteração 1: a versão da ETAPA-03 tinha onze regras SEMPRE/NUNCA sem justificativa → percebido pela autoverificação da IA à luz da `skill-creator` → substituídas por princípios que explicam o motivo de cada regra.
- Tropeço operacional: a chamada `skill-creator` foi recusada por ambiguidade entre skills de mesmo nome → percebido pela IA → refeita com o nome qualificado `anthropic-skills:skill-creator`.

**Arquivos:** `.claude/skills/prd-writer/SKILL.md`, `references/template-prd.md`, `references/checklist-validacao.md` (alterados), `references/entrevista.md`, `references/escrita-de-requisitos.md` (criados).

**Lições:** usar uma skill de boas práticas como revisora trouxe critérios externos para a revisão, em vez de a IA avaliar o próprio texto pelos mesmos critérios com que o escreveu.

### ETAPA-05 — Criação das skills adr-writer, rfc-writer e fdd-writer

**Data e hora:** 2026-11-08 13:23 · **Fase:** Ferramental · **Ferramenta:** Claude Code (Claude Fable 5.1) · **Iterações:** 1 · **Registro:** retroativo

**Objetivo:** ter uma skill dedicada para cada documento do pacote, acionada automaticamente, com as mesmas convenções da `prd-writer`.

**Prompt** · Prompt de destaque:

```text
assim como fizemos uma skill para prd, crie uma skill para ADR, FDD e RFC. Crie skills separada para cada um deles e que seja acionada automaticamente
```

**O que a IA entregou:** três skills, cada uma com `SKILL.md`, template e checklist, compartilhando pt-BR, os três modos, a fronteira de altura e a integração com o tracker por prefixos próprios de ID. A `adr-writer` traz o filtro do que merece ADR, formato MADR e o "teste da decisão trocada". A `rfc-writer` limita o documento a 2 a 4 páginas e exige alternativas e questões em aberto. A `fdd-writer` ganhou a referência `leitura-do-codigo.md` e exige a seção de integração com pelo menos quatro arquivos reais. Descrições redigidas para acionar mesmo sem a sigla.

**Revisão crítica e ajustes:**

- Iteração 1: a autoverificação encontrou três ecos do domínio do desafio nos exemplos genéricos: um título de ADR de exemplo próximo da decisão de outbox, o caminho `src/shared/errors/http-errors.ts` no template do FDD e o prefixo `WEBHOOK_` como exemplo de prefixo de erro → percebido pela autoverificação da IA → trocados por "Fila persistente no banco atual para programar lembretes", `src/shared/errors.ts` (caminho fictício de exemplo, que não existe neste repositório) e `PAGAMENTO_`.
- A verificação de referências cruzadas acusou um arquivo faltando → percebido pela IA como falso positivo do próprio script de verificação, porque o caminho `../prd-writer/references/extracao-de-fontes.md` existe.

**Arquivos:** `.claude/skills/adr-writer/` (`SKILL.md`, `references/template-adr.md`, `references/checklist-adr.md`), `.claude/skills/rfc-writer/` (`SKILL.md`, `references/template-rfc.md`, `references/checklist-rfc.md`), `.claude/skills/fdd-writer/` (`SKILL.md`, `references/leitura-do-codigo.md`, `references/template-fdd.md`, `references/checklist-fdd.md`) (criados).

**Lições:** mesmo com instrução explícita de usar domínio fictício, termos do contexto da sessão vazaram para exemplos. Uma busca por termos do desafio depois de gerar é necessária, não opcional.

### ETAPA-06 — Skill de registro do processo e criação deste registro

**Data e hora:** 2026-11-08 14:46 · **Fase:** Ferramental · **Ferramenta:** Claude Code (Claude Opus 5.5) · **Iterações:** 1 · **Registro:** no momento

**Objetivo:** atender ao item do desafio que pede o README com o processo, registrando os passos durante o trabalho em vez de reconstruí-los no fim.

**Prompts:**

```text
/model claude-opus-5-5
```

Prompt de destaque:

```text
Uma dos itens do desafio é criar um registro dos passos realizados para a criação dos documentos. Então seria interessante criarmos uma skill dedicada a isso e já incluir os passos que executamos de criação das outras skills um documento de registro ou algo parecido. Para ao final de todo o processo podermos recuperar deste documento de registro e criar a sessão especifica no readme do projeto, assim como pede o desafio
```

**O que a IA entregou:** a skill `registro-processo`, com os modos Registrar, Consolidar e Corrigir, um template de etapa com prompt literal e revisão crítica por iteração, e um guia de consolidação que mapeia o registro para as cinco seções obrigatórias do README do desafio. As skills de documento passaram a registrar a etapa ao concluir quando este arquivo existir. Este registro foi criado com as etapas anteriores reconstruídas do histórico persistido da sessão, com horários convertidos de UTC para UTC-3.

**Revisão crítica e ajustes:** às 14:56, o usuário perguntou como a skill seria acionada e pediu que ela valesse para todos os prompts, em qualquer sessão (prompt abaixo, recuperado do histórico da sessão na ETAPA-14). A resposta da IA não foi registrada na época.

```text
explique como será o funcionamento da skill de registros. Ela deve ser acionada em todos os prompts que forem enviado, independente da sessão que esteja sendo executada
```

**Arquivos:** `.claude/skills/registro-processo/SKILL.md`, `references/template-registro.md`, `references/consolidacao-readme.md` (criados), `docs/REGISTRO-PROCESSO.md` (criado), `SKILL.md` de `prd-writer`, `adr-writer`, `rfc-writer` e `fdd-writer` (alterados).

**Lições:** o histórico persistido da sessão permitiu recuperar prompts literais e horários depois de uma compactação de contexto, o que não seria possível de memória.

### ETAPA-07 — Extração dos ADRs a partir da transcrição

**Data e hora:** 2026-11-08 15:06 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) com as skills `adr-writer` em Modo Fontes e `doc-review` · **Iterações:** 2 · **Registro:** no momento

**Objetivo:** identificar na reunião apenas as decisões arquiteturais que podem ser ligadas a um trecho da transcrição e registrá-las como ADRs no formato MADR.

**Prompt** · Prompt de destaque:

```text
Vamos iniciar o desafio deste projeto. Quero que você avaliar o documento @TRANSCRICAO.md para identificar os ADRs que foram definidos na reunião. Não invente nada, crie somente os adrs que você possa relacionar com um trecho da reunião.
```

**Interação:** antes de escrever, a IA apresentou o inventário de 8 decisões com a localização de cada uma e a lista do que ficou de fora com o motivo: TLS obrigatório e limite de 64KB (a própria Larissa disse em [09:24] que não são decisões arquiteturais), timeout de 10 s, UUID como id, filtro de eventos na inserção, headers e formato do payload. Ofereceu duas opções: escrever os 8 ou reduzir a 6, fundindo retry e DLQ e retirando o snapshot do payload. O usuário escolheu "Escrever os 8".

**O que a IA entregou:** oito ADRs com status Aceito: ADR-001 outbox no MySQL na transação de `changeStatus`; ADR-002 worker em processo separado com polling de 2 s; ADR-003 retry com 5 tentativas em 1m/5m/30m/2h/12h; ADR-004 DLQ em tabela separada com replay restrito a ADMIN; ADR-005 HMAC-SHA256 com secret por endpoint e rotação com 24 h de convivência; ADR-006 at-least-once com `X-Event-Id`; ADR-007 reuso dos padrões existentes; ADR-008 payload como snapshot na inserção. Cada um tem seção Origem com `[hh:mm] Nome` e caminhos reais de código. O índice `docs/adrs/README.md` foi reescrito.

**Revisão crítica e ajustes:**

- O índice original de `docs/adrs/README.md` indicava o padrão `0001-titulo-da-decisao.md`, conflitante com o `ADR-NNN-titulo-em-kebab-case.md` do enunciado → percebido pela IA → índice alinhado ao padrão do enunciado.
- A transcrição registra só "quinta-feira, 09:00", sem data → percebido pela IA → os ADRs trazem "não registrada (reunião de quinta-feira)" em vez de uma data inventada.
- Argumentos que não foram ditos na reunião (por exemplo, que Redis Streams não participaria da transação SQL, ou que o worker não passa pelo error middleware) → marcados como "observação do autor". A alternativa "estrutura própria" do ADR-007 não foi discutida na reunião → marcada como "plausível, não discutida".
- Validação automática por script: todos os links relativos resolvem, todas as citações `[hh:mm] Nome` existem na transcrição, e todos os caminhos de código existem, exceto `src/worker.ts` e `src/modules/webhooks/webhook.worker.ts`, que são arquivos propostos na reunião e aparecem como proposta.
- O `CLAUDE.md` afirma que `requireRole` não é usado em nenhuma rota, mas a leitura do código mostrou uso em `src/modules/users/user.routes.ts` → os ADRs citam apenas a existência do middleware; o `CLAUDE.md` não foi alterado.
- Iteração 2 (2026-11-08 15:21): o usuário escolheu "Revisar ADRs antes" e a IA rodou a skill `doc-review` sobre os 8 ADRs, achando 2 contradições, 4 ambiguidades, 3 inconsistências e 4 lacunas, sem citação atribuída à pessoa errada → percebido pela revisão da IA sobre o próprio texto → o usuário respondeu "Aplicar todas". Exemplos concretos: o ADR-004 dizia que a outbox "só contém eventos ainda em ciclo de entrega", contradito por [09:08] Diego (status "falhou" e "entregue" ficam na outbox) e reescrito com a frase de [09:18]; o ADR-001 trazia o filtro de status e `publishWebhookEvent` na Decisão, embora a IA tivesse dito ao usuário que esses itens iriam para o FDD, e foram retirados; o ADR-007 usava o prazo de três sprints ([09:47]) como argumento de uma decisão fechada antes ([09:30]), e o argumento foi removido; o ADR-006 tinha um exemplo de timeout próprio sem marcação, e passou a ser marcado como exemplo do autor; o reuso de `requireRole` foi concentrado no ADR-007. Entraram como questões em aberto: contagem das 5 tentativas (5 ou 6 envios, ADR-003), assinatura durante a convivência de 24 h (ADR-005), destino da linha da outbox ao ir para a DLQ (ADR-004) e quebra de ordem por retentativa (ADR-002). O ADR-002 ganhou o risco de um cliente lento com timeout de 10 s atrasar os outros no worker único. Links e citações foram revalidados por script.

**Arquivos:** `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md`, `ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md`, `ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md`, `ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md`, `ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md`, `ADR-006-entrega-at-least-once-com-x-event-id.md`, `ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md`, `ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` (criados), `docs/adrs/README.md` (alterado).

**Lições:** apresentar o inventário e o que ficou de fora antes de escrever transformou a escolha do número de ADRs em decisão do usuário. Verificar as citações por script garante que o trecho existe, mas não que o ADR diz o mesmo que o trecho: as contradições da iteração 2 só apareceram em uma leitura crítica separada da escrita.

### ETAPA-08 — Consolidação do RFC a partir dos ADRs

**Data e hora:** 2026-11-08 15:29 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) com a skill `rfc-writer` em Modo Fontes · **Iterações:** 2 · **Registro:** no momento

**Objetivo:** escrever o RFC em altura de arquitetura, referenciando os 8 ADRs em vez de repetir a argumentação, e listando todas as alternativas levantadas na reunião.

**Prompt** · Prompt de destaque:

```text
Consolide o RFC com base em todas as adrs criadas a partir da reunião. Citar todas as alternativas propostas
```

**O que a IA entregou:** `docs/RFC.md` com status "Em revisão" e revisores Bruno, Diego e Sofia, porque a Larissa anunciou em [09:50] uma sessão de revisão do design antes de codar. Contém TL;DR, objetivos e não objetivos (e-mail, dashboard, ordem global, exactly-once, arquivamento, webhooks de entrada), diagrama Mermaid, tabela de componentes pelo papel, fluxo principal sem payloads, garantias com os números da reunião, 17 alternativas (ALT-01 a ALT-10 vindas dos ADRs, ALT-11 marcada como não discutida, ALT-12 a ALT-17 escolhas pontuais da reunião sem ADR próprio), impacto com arquivos reais, 6 riscos, plano de três sprints, 8 questões em aberto (QA-01 a QA-05 vindas da revisão dos ADRs, QA-06 a QA-08 itens adiados na reunião), links para os 8 ADRs e seção Origem.

**Revisão crítica e ajustes:**

- A primeira versão tinha 2.871 palavras, acima do limite de 2.000 da skill, com 845 só na seção de alternativas em formato de subseção por alternativa → percebido pela validação da IA → alternativas condensadas em uma única tabela e links repetidos para ADRs retirados das garantias. Ficou com cerca de 1.080 palavras de prosa e 2.660 contando as tabelas. O excesso foi mantido porque vem das tabelas de alternativas e de questões em aberto, e o pedido foi citar todas as alternativas.
- Probabilidade e impacto dos riscos não foram discutidos na reunião → marcados como avaliação do autor. QA-04 e QA-05, que vieram da revisão dos ADRs e não da reunião, estão marcadas como observação do autor.
- Validação por script: todos os links resolvem e todas as citações `[hh:mm] Nome` existem na transcrição. Os caminhos `src/worker.ts` e `src/modules/webhooks` não existem porque são propostos.
- Iteração 2 (2026-11-08 15:41): o usuário perguntou de onde vinha o limite de 2.000 palavras (primeiro prompt abaixo), e a IA explicou que o enunciado pede 2 a 4 páginas (`README.md`, linhas 44 e 93) e que a faixa de palavras é a conversão feita pela skill. Na mesma resposta, a IA apontou que QA-04 e QA-05 tinham vindo da revisão dos ADRs e não da reunião. O usuário pediu o enxugamento (segundo prompt abaixo). Ajustes: a seção Origem (cerca de 400 palavras) virou uma linha apontando para `docs/TRACKER.md`. A tabela de alternativas perdeu as colunas "O que teria de bom" e "Origem" e caiu de cerca de 650 para 374 palavras, mantendo as 17 alternativas. As questões em aberto passaram de 8 para 4, só com o que a reunião adiou: rate limiting ([09:39]), escala para mais de um worker ([09:13]), permissões do CRUD ([09:37]) e e-mail na próxima fase ([09:37]). As três ambiguidades vindas dos ADRs ficaram registradas como pontos a tratar no FDD. Também saíram dois riscos que eram só observação do autor. O documento caiu de cerca de 3.070 para 2.235 palavras no total, com 1.136 de prosa.

**Prompts da iteração 2** (horários do histórico da sessão: 15:34 e 15:40):

```text
antes de continuarmos, quero entender por existe um limite de 2000 palavras na RFC?
```

Prompt de destaque:

```text
Vamos revisar o RFC e fazer algumas alterações para enxugar o documento e não ficar tão extenso. A origem pode ser uma referencia ao tracker. As alternativas podem ser mais concisas para assim ficar com menos texto. As questões em aberto devem vir da transcrição da reunião, e não do que inferimos
```

**Arquivos:** `docs/RFC.md` (reescrito a partir do placeholder e enxugado na iteração 2).

**Lições:** pedir "todas as alternativas" entra em tensão com o limite de páginas do RFC. A tabela resolveu sem perder nenhuma, e as justificativas longas continuam nos ADRs.

### ETAPA-09 — Escrita do FDD

**Data e hora:** 2026-11-08 16:23 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) com a skill `fdd-writer` em Modo Fontes e consulta ao MCP Context7; revisões com a skill `doc-review` (iteração 2) e com a `fdd-writer` em Modo Revisão (iteração 3) · **Iterações:** 3 · **Registro:** no momento

**Objetivo:** escrever o FDD acionável a partir da transcrição, dos 8 ADRs, do RFC e do código, cobrindo todos os critérios de aceite do FDD no `README.md` e fechando as três ambiguidades que o RFC encaminhou.

**Prompt** · Prompt de destaque:

```text
Agora vamos construir as fdds necessárias para o nosso projeto. Não esquecer de garantir todo os pontos de criterio de aceite para o fdds presentes na descrição do desafio
```

Prompts da iteração 3, o segundo enviado durante a revisão:

```text
Faça uma nova revisão do @docs/FDD.md
```

```text
Precisamos garantir os criterios de aceite do desafio
```

```text
Aplicar todas
```

**O que a IA entregou:** `docs/FDD.md` com 20 seções. Contém modelo Prisma de 4 tabelas e 1 enum; 7 fluxos (criação na outbox, worker, retry, DLQ, replay, rotação e efeitos do CRUD), com 2 diagramas de sequência e 1 de estados; 7 endpoints HTTP com requisição e resposta de exemplo e tabela de status codes, mais o contrato do POST de saída com headers, corpo e exemplo de verificação; matriz com 6 erros de API e 6 internos `WEBHOOK_*`; resiliência com tabela de envios; métricas, logs e tracing; 5 variáveis de ambiente; 16 entradas de integração cobrindo cerca de 22 arquivos reais; 18 cenários de teste; 20 critérios de aceite; 8 riscos. As três ambiguidades dos ADRs foram fechadas como propostas do autor: 6 envios (o inicial e 5 retentativas, a única leitura que soma "quase 15 horas"), duas assinaturas em `X-Signature` durante a rotação e linha movida da outbox para a DLQ.

**Revisão crítica e ajustes:**

- O prompt fala em "fdds" no plural, mas o enunciado pede um único `docs/FDD.md` → a IA tratou a feature em um FDD só, sem dividir por subsistema.
- A leitura do código mudou o desenho em pontos que a transcrição não previa: `validate.middleware.ts` converte toda falha Zod em `VALIDATION_ERROR`, então `WEBHOOK_INVALID_URL` sai em `details[].message`, sem alterar o middleware (ADR-007); `NotFoundError` tem código fixo, então os 404 `WEBHOOK_*` viram subclasses de `AppError`; `tests/setup.ts` apaga cada tabela em ordem a cada teste, então a limpeza das tabelas novas entrou na integração; `OrderService.delete` seria bloqueado por chave estrangeira para `orders`, então a outbox não tem essa chave.
- Context7 confirmou que o Prisma só suporta listas escalares no PostgreSQL → o filtro de status virou coluna `Json`. O payload foi para `MEDIUMTEXT` porque o MySQL reordena chaves de colunas `JSON`, o que mudaria os bytes assinados entre envios.
- `WEBHOOK_SECRET_REQUIRED`, listado por Bruno em [09:28], não foi adotado, porque Marcos definiu em [09:31] que a secret é gerada pela plataforma.
- Seguindo o ajuste pedido no RFC (ETAPA-08, iteração 2), as questões em aberto foram separadas: 18.1 só aponta os itens que a reunião adiou (QA-01 a QA-04 do RFC), e 18.2 lista as 15 propostas do autor como pontos a validar, não como questões da reunião.
- Validação por script: as 46 citações `[hh:mm] Nome` existem na transcrição; os 9 caminhos inexistentes são todos arquivos novos e estão marcados assim; 3 entradas da integração estavam sem o campo "Cuidado" exigido pelo checklist da skill e foram completadas.
- Tamanho: cerca de 10.700 palavras, com blocos de código e tabelas. Ficou longo por escolha, já que o enunciado pede o FDD como o documento mais profundo.
- Iteração 2 (2026-11-08 16:46): o usuário escolheu "Revisar o FDD antes", e a IA rodou a skill `doc-review` cruzando o FDD com a transcrição, os ADRs, o RFC e o código. Achados: 1 contradição, 6 ambiguidades, 11 inconsistências (7 delas de atribuição às fontes) e 4 lacunas. Os principais: o "Cuidado" de `tests/setup.ts` dizia que a suíte quebraria sem a limpeza das tabelas novas, mas a cascata definida no próprio FDD já as apaga (a afirmação também estava nesta etapa e foi corrigida, ver "Correções do registro"); um `refine` com `new URL()` lançaria `TypeError` em URL malformada e viraria 500, porque o Zod continua executando refinamentos depois de erros não fatais (conferido no Context7); o gauge de pendentes contava eventos segurados pela regra de ordem e dispararia o alerta de latência sem problema real; sete trechos atribuíam à reunião escolhas do autor, como o envio em sequência, o momento do `timestamp` do corpo e o exemplo de verificação para o portal. O usuário respondeu "Aplicar todas". Ajustes: todas as correções aplicadas, com 4 novas propostas na seção 18.2 (P-16 a P-19), 404 para cliente inexistente na listagem e links dos ADR-003, ADR-004 e ADR-005 para as propostas P-01, P-03 e P-02 do FDD. A revalidação manteve as 46 citações válidas e todos os links e âncoras resolvendo; o FDD passou a ter cerca de 11.400 palavras.
- Iteração 3 (2026-11-08 16:47): o usuário pediu uma nova revisão e, durante o trabalho, pediu para garantir os critérios de aceite do desafio. A IA rodou a `fdd-writer` em Modo Revisão, cruzando o FDD com os critérios de aceite do enunciado (FDD e "Consistência geral"), a transcrição, os ADRs, o RFC e o código. Todos os critérios do FDD passavam, mas havia risco em "Consistência geral" e erros que as iterações anteriores não pegaram. Risco aos critérios: 13 caminhos de arquivos propostos (`src/worker.ts`, arquivos de `src/modules/webhooks/`, testes novos, migração, `dist/worker.js`) citados sem marcação uniforme de "(novo)", contra o critério "nenhum arquivo de código mencionado é inexistente"; o resumo "total 5 tentativas" de [09:48] Larissa não era citado, embora o FDD fixe 6 envios; as 19 propostas do autor não tinham localização, e o tracker só aceita fonte `TRANSCRICAO` ou `CODIGO`. Correções técnicas: a seção 10.3 orientava o cliente a detectar replay pelo `timestamp` do corpo, que se repete nas retentativas por até 14 h 36 min e faria o cliente recusar entregas legítimas; o heartbeat não tinha lugar no fluxo do worker, e um lote de 10 envios com timeout de 10 s (cerca de 100 s) passaria do alerta crítico de 90 s; `*.secret` no `redactPaths` do Pino não cobre `secret` no topo do objeto de log (conferido no Context7, no código de redaction do Pino); não estava definido se um 2xx com corpo lento é sucesso ou timeout. Inconsistências menores: CA-19 dizia que nada em `tests/` muda, mas a seção 13 altera `tests/setup.ts`; OBJ-06 prometia não alterar o logger; o log `worker_bootstrap_failed` faltava na tabela de logs; OBJ-07 não citava [09:21] Sofia; a analogia com `InsufficientStockError` era imprecisa; o título "Resiliência" diferia do critério "Estratégias de resiliência"; não havia critério de aceite para o CRUD, os órfãos e o webhook inativo. O usuário respondeu "Aplicar todas". Ajustes: a convenção do topo lista os arquivos novos; a seção 9 cita [09:48] Larissa e lê as 5 tentativas como 5 retentativas; a tabela 18.2 ganhou a coluna "Origem da lacuna"; a orientação de replay passou a ser deduplicar pelo `event_id`; o heartbeat é emitido pelo próprio loop, no máximo a cada 40 s; `secret` e `previousSecret` entraram no `redactPaths`; um 2xx recebido no prazo é sucesso; entraram CA-21, CA-22 e T-19; a seção 9 foi renomeada e a âncora do link no ADR-003 atualizada. Revalidação por script: 49 citações válidas, links e âncoras resolvendo, os 13 caminhos inexistentes listados como novos, 22 CAs e 19 testes em sequência; o FDD passou a ter cerca de 12.400 palavras. A revisão também constatou que `docs/PRD.md` e `docs/TRACKER.md` ainda são placeholders, embora a seção 20 do FDD já aponte para os IDs `FDD-*` no tracker.

**Arquivos:** `docs/FDD.md` (reescrito a partir do placeholder e revisado nas iterações 2 e 3), `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md`, `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` e `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` (questões em aberto apontando para as propostas do FDD; no ADR-003, âncora do link atualizada na iteração 3), `docs/REGISTRO-PROCESSO.md` (Context7 e `doc-review` incluídos na tabela de ferramentas).

**Lições:** ler as camadas transversais do código antes de escrever a matriz de erros evitou propor códigos que o middleware existente não conseguiria devolver. A validação por script da iteração 1 conferiu citações e caminhos, mas não pegou a contradição entre duas seções do próprio documento nem as atribuições exageradas; isso só apareceu na revisão crítica cruzada. Na iteração 3, revisar contra a lista de critérios de aceite do enunciado, e não só contra as fontes, revelou riscos de avaliação (caminhos propostos sem marcação, propostas sem localização) que as revisões focadas em fidelidade não procuravam. Uma orientação de segurança errada sobreviveu a duas revisões porque nenhuma a confrontou com o comportamento de retentativa descrito no próprio documento.

### ETAPA-10 — Diagramas no FDD e spec OpenAPI dos contratos

**Data e hora:** 2026-11-08 17:25 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) seguindo a skill `fdd-writer` em Modo Revisão, com consulta ao MCP Context7, Redocly CLI 2.76.0, Swagger UI via Docker e o navegador embutido do app; na iteração 2, consulta à documentação do MySQL no MCP Context7 · **Iterações:** 3 · **Registro:** no momento

**Objetivo:** usar mais diagramas Mermaid no FDD para fluxos e modelo de dados, e tirar do FDD o detalhe dos contratos, levando-o para uma spec OpenAPI navegável por ferramenta, com um manual se necessário.

**Prompt** · Prompt de destaque:

```text
Acredito que o FDD teve pouco uso de diagramas mermaid para exemplificar fluxos e modelos ER. Fora isto, seria interessante já cria uma spec open api, no lugar de colocar a spec toda dentro do arquivo FDD. No FDD poderia estra resumido e a com a spec em anexo, podemo acessar com alguma ferramenta especifica e o entendimento fica bem facil. Caso julgue necessario, poderia cria um manual especifico para a spec.
```

Prompt da iteração 2, resposta à pergunta da IA "Ajusto o replay concorrente para responder 409 sem depender do error middleware?":

```text
Sim, ajustar
```

Prompt da iteração 3:

```text
No diagrama ER incluir os comentarios de colunas com descrição
```

**O que a IA entregou:**
- `docs/openapi/webhooks.openapi.yaml` em OpenAPI 3.1, com os 7 endpoints em `paths` e o POST enviado ao cliente na seção `webhooks` (`orderStatusChanged`). A spec traz 18 schemas, 45 exemplos de requisição, resposta e erro, e a mesma convenção de rastreabilidade do FDD, com as propostas do autor marcadas como **(AUTOR, P-xx)**.
- `docs/openapi/README.md`, o manual da spec: o que há em cada seção, o mapa de `operationId` para as seções do FDD, como visualizar (Redoc estático, Swagger UI via Docker ou extensão do VS Code), como validar com lint, convenções e regras de manutenção.
- Na seção 7 do FDD: tabela de endpoints com o `operationId`, matriz única de status codes e um exemplo de requisição e de resposta por endpoint, iguais aos da spec.
- Seis diagramas novos no FDD: ER (seção 5), ciclo do worker (6.2), falha e retentativa (6.3), rotação de secret (6.6), replay com os caminhos de erro (6.5) e mapa de integração (13). O diagrama da seção 6.1 foi refeito, e o FDD passou de 3 para 8 diagramas.

**Revisão crítica e ajustes:**

- O pedido de tirar a spec do FDD conflita com o critério de aceite do desafio que exige, na seção "Contratos públicos" do próprio FDD, pelo menos 4 endpoints com exemplo de requisição e resposta e os status codes. A IA manteve no FDD um exemplo por endpoint e uma matriz de status codes, e levou para a spec as tabelas de campos, a representação do recurso, os exemplos de erro e o código de verificação da assinatura. A seção 7 encolheu de 323 para cerca de 250 linhas: o resumo é limitado pelos exemplos que o critério obriga a manter.
- A skill `fdd-writer` só pede diagrama de componentes quando o RFC não tem um, e o RFC já tem o de arquitetura. Por isso, em vez de repeti-lo, o FDD ganhou um mapa de integração por arquivo, colorido entre novo, alterado e reusado.
- Os dois diagramas de sequência originais misturavam fluxos. O da seção 6.1 juntava a criação do evento com o loop do worker, e o da 6.5 juntava falha, DLQ e replay. Cada um foi separado no seu fluxo, com os ramos `alt` do caso sem webhook, do rollback e das respostas 403, 404 e 409.
- A primeira versão do mapa de integração (16 nós, 4 subgrafos) era válida em sintaxe, mas saiu com as arestas cruzadas quando renderizada no navegador. Foi refeita com 14 nós e menos ligações.
- O exemplo de `X-Signature` do FDD era um texto genérico. Passou a ser o HMAC-SHA256 real do corpo de exemplo (326 bytes) com a secret do exemplo de criação, e a spec traz também o par de assinaturas da rotação. O exemplo virou vetor de teste do T-02, e a conferência passou a ser automática.
- No Swagger UI, a tag "Evento de saída" aparecia como um grupo vazio, porque o webhook é listado numa seção própria. A tag foi removida.
- Tropeço de ferramenta: o painel de navegador do app abre arquivos locais como `data:` URL. Nesse modo, o Redoc 3 falhou por falta de `localStorage`, e o Swagger UI não resolveu os `$ref` ("Invalid URL"). A conferência foi feita subindo o Swagger UI via Docker em `localhost:8080`, com o mesmo comando documentado no manual, o que também testou o manual. A pasta temporária `dist/` foi removida ao final.
- Validação:
  - lint do Redocly válido, com 2 avisos aceitos e documentados no manual (`info-license` e `no-server-example.com`);
  - os 45 exemplos validados contra os schemas com `jsonschema` (Draft 2020-12), sem erro;
  - os 9 corpos JSON e o `X-Signature` da seção 7 do FDD idênticos aos da spec;
  - os 8 diagramas renderizados no Mermaid 11.4.1;
  - 53 citações válidas entre FDD, RFC, spec e manual, e links e âncoras resolvendo.
- Ponto não verificado: o 409 `CONFLICT` do replay concorrente depende do tratamento de P2002 no `error.middleware.ts`, que chama `.join` sobre `meta.target`. No MySQL, o Prisma pode devolver esse campo como texto, e não como array. Não havia banco no ar para conferir, então a spec usa a mensagem genérica do middleware.
- Iteração 2 (2026-11-14 16:09): o usuário respondeu "Sim, ajustar" à pergunta sobre o ponto não verificado. Em vez de capturar o P2002, a IA propôs reservar a entrada da DLQ com `updateMany` condicionado a `replayedAt` nulo, o mesmo padrão da reserva do worker. Antes de escrever, confirmou na documentação do MySQL, via Context7, que um `UPDATE` em `REPEATABLE READ` espera o lock da linha e não usa o snapshot. Assim, o segundo replay simultâneo recebe `count = 0` e responde `409 WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED`, sem passar pelo middleware. Ajustes: passos e diagrama da seção 6.5, matriz de status da seção 7, matriz de erros, entrada do `error.middleware.ts` na seção 13 (com o "Cuidado" sobre o `.join` em `meta.target`), T-10, CA-10, nova proposta P-20 e linha no histórico do FDD; na spec, descrições do replay e do 409 ajustadas e exemplo `CONFLICT` removido. Os scripts de validação, apagados do scratchpad entre as sessões, foram recriados, e a validação passou de novo: lint válido com os 2 avisos conhecidos, 44 exemplos sem erro, P-01 a P-20 em sequência e os 8 diagramas renderizados.
- Iteração 3 (2026-11-14 16:26): o diagrama ER tinha comentário em só 17 das 49 colunas, e as descrições eram fragmentos como "sem FK" ou "1 a 6". A IA escreveu uma descrição para cada coluna das quatro tabelas novas e das três existentes, tirada das seções 5 e 6 do próprio FDD, sem fato novo. Como o `erDiagram` do Mermaid só marca PK, FK e UK, e não nulidade, a descrição das colunas opcionais diz quando elas ficam nulas, e o parágrafo antes do diagrama explica isso. O schema Prisma não mudou. O diagrama foi renderizado de novo no Mermaid 11.4.1 pelo navegador embutido, com uma página temporária em `dist/`, removida depois; os 8 diagramas passaram no parse, e o script de verificação manteve as 53 citações válidas, os 9 corpos iguais aos da spec e P-01 a P-20 em sequência. O FDD ganhou uma linha no histórico de revisão.

**Arquivos:** `docs/FDD.md` (seção 7 resumida, seis diagramas novos e o da 6.1 refeito, histórico de revisão), `docs/openapi/webhooks.openapi.yaml` (novo), `docs/openapi/README.md` (novo), `docs/RFC.md` (link para a spec na seção de proposta), `docs/REGISTRO-PROCESSO.md` (ferramentas novas). Na iteração 2, `docs/FDD.md` e `docs/openapi/webhooks.openapi.yaml` de novo. Na iteração 3, só `docs/FDD.md` (diagrama ER e histórico).

**Lições:** um pedido do usuário pode esbarrar num critério de aceite, e o critério precisa ser conferido antes de mover conteúdo entre documentos. Renderizar os diagramas num motor Mermaid real pegou um problema de legibilidade que a checagem de sintaxe não pegaria. Trocar o exemplo de assinatura por um valor real permitiu conferir FDD e spec por script e deu ao portal um vetor de teste.

### ETAPA-11 — Escrita do PRD

**Data e hora:** 2026-11-14 16:30 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) com a skill `prd-writer` em Modo Fontes · **Iterações:** 1 · **Registro:** no momento

**Objetivo:** escrever o PRD por último entre os grandes documentos, como sugere o enunciado, consolidando o "por que e o quê" a partir da transcrição, do código, dos ADRs, do RFC, do FDD e da spec, e validá-lo contra os critérios de aceite do PRD e de "Consistência geral" do `README.md`.

**Prompt** · Prompt de destaque:

```text
Agora vamos criar o PRD com base em todos os documentos ja criados e validem com os criterios de aceite do nosso desafio
```

**O que a IA entregou:** `docs/PRD.md` com as 14 seções da skill. Traz 9 funcionalidades (F01 a F09: cadastro, consulta e manutenção, notificação, assinatura, rotação, reenvio, guarda das falhas permanentes, reprocessamento e histórico), cada uma com histórias, capacidades, comportamento observável, tratamento de erros e linha de origem. Completam o documento: 17 RNFs com critério numérico; 5 objetivos com linha de base, meta e forma de medir; 12 itens fora de escopo (7 descartados e 5 adiados na reunião); 9 premissas e restrições; 8 decisões com ganho e custo para o produto, ligadas aos ADRs; 6 questões em aberto; 6 dependências externas; grafo de dependências com 5 ondas e diagrama Mermaid; 6 riscos com probabilidade, impacto e mitigação; critérios de aceitação por funcionalidade, de integração e não funcionais; e estratégia de testes. A seção 14 aponta para o tracker, como no RFC e no FDD.

**Revisão crítica e ajustes:**

- O título da seção 2 da skill ("Problema e Oportunidade") diferia do nome exigido pelo enunciado ("Problema e motivação") → a IA renomeou para "Problema e Motivação", o mesmo cuidado que levou a renomear a seção de resiliência do FDD na ETAPA-09.
- A skill pede entrevista no Modo Fontes para fechar lacunas de métricas. As lacunas eram metas de produto que a reunião não deu: o percentual de primeiros envios abaixo de 10 s, a medida de retenção dos três clientes e a queda esperada no polling. Uma resposta do usuário não seria fonte válida para o tracker, que só aceita `TRANSCRICAO` ou `CODIGO` → a IA não perguntou e registrou as três metas como premissas a validar com o Marcos (PREM-07 a PREM-09), marcadas nos objetivos.
- O PRD não repete o que o FDD propôs sem fonte: cita a proposta (P-01, P-02, P-04 e outras 12) e mostra só o efeito para o cliente. As questões em aberto do RFC voltam apenas com o impacto no produto e link para o RFC. Duas propostas do FDD que mudam o que o portal promete aos clientes, a contagem de 6 envios e a dupla assinatura na rotação, viraram questões em aberto (QA-04 e QA-05) e condição de lançamento.
- Na releitura, a IA achou três problemas no próprio texto. "O webhook nasce ativo" citava [09:21] Bruno, que só pergunta se a tabela guarda o "estado ativo", e passou a citar o FDD para o valor padrão. Uma história usava o ator "responsável pela segurança", que não está entre os perfis da seção 3, e foi reescrita para o administrador. O critério do RNF-02 comparava a mudança de status com a de um cliente sem webhook, o que reprovaria sem defeito real, porque a transação ganha uma consulta; passou a comparar o endpoint fora do ar com o endpoint no ar.
- Validação por script (novo `check_prd.py` no scratchpad, e o `check_docs.py` passou a incluir o PRD): 14 seções em ordem e todas as exigidas pelo enunciado presentes; IDs de todos os prefixos em sequência; tabela do grafo em ordem topológica, coerente com os blocos Consome e Fornece, com as arestas do Mermaid e com as ondas calculadas; um critério de integração para cada dependência; 71 citações distintas válidas; 13 caminhos de código existentes; as 15 propostas do FDD citadas existem; links, âncoras e parágrafos em linha única conferidos.
- Critérios de aceite do PRD no enunciado: arquivo em Markdown com todas as seções obrigatórias; 9 requisitos funcionais da reunião (mínimo 8); objetivos com meta quantitativa vinda da reunião (10 s, fim de novembro, zero mudanças sem notificação); 12 itens fora de escopo descartados ou adiados na reunião (mínimo 2); 6 riscos com probabilidade, impacto e mitigação (mínimo 2). Na consistência geral, nenhum caminho inexistente e nenhuma citação que não esteja na transcrição.
- Tamanho: cerca de 9.300 palavras, contando as 267 citações. O excesso vem do formato da skill, com bloco de capacidades e de erros por funcionalidade e critérios de sucesso e de falha para cada uma.

**Arquivos:** `docs/PRD.md` (escrito a partir do placeholder).

**Lições:** escrever o PRD por último, com RFC, ADRs e FDD prontos, transformou a escrita em consolidação, e o trabalho passou a ser decidir a altura de cada coisa: o efeito para o cliente fica no PRD e a decisão técnica fica no documento de origem, com link. Conferir os títulos das seções contra o enunciado, e não só contra o template da skill, pegou a mesma divergência de nome que já tinha aparecido no FDD. Métricas sem número na fonte não devem ser completadas por resposta do usuário quando o tracker exige fonte da reunião ou do código; ficam como premissas explícitas.

### ETAPA-12 — Tracker de rastreabilidade

**Data e hora:** 2026-11-14 16:47 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5), com scripts Python no scratchpad para gerar e validar a tabela · **Iterações:** 1 · **Registro:** no momento

**Objetivo:** montar o `docs/TRACKER.md` no formato obrigatório do enunciado, ligando cada item de PRD, RFC, ADRs e FDD a um trecho da transcrição ou a um arquivo do código, com pelo menos 80% dos itens cobertos, 70% das linhas com fonte `TRANSCRICAO` e 5 linhas com fonte `CODIGO`.

**Prompt** (resposta à pergunta da IA "Monto o docs/TRACKER.md agora?"):

```text
Sim, montar o tracker
```

**O que a IA entregou:** `docs/TRACKER.md` com 370 linhas na tabela `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`, precedida das convenções de ID e de uma tabela de cobertura por documento. São 78 linhas do PRD, 58 do RFC, 53 dos ADRs e 181 do FDD; 320 (86%) têm fonte `TRANSCRICAO` e 50 têm fonte `CODIGO`, com caminho real. Os IDs do PRD e do RFC repetem os do documento com prefixo (`PRD-F03`, `RFC-ALT-02`). Os itens sem ID ganharam prefixo por seção (`RFC-GAR`, `FDD-FLUXO`, `FDD-INTEG` e outros), e os ADRs usam sufixos para alternativa, consequência, ponto em aberto e referência ao código. A tabela é gerada por `gen_tracker.py` a partir de uma lista de linhas, o que mantém o formato e calcula a cobertura. O conteúdo de cada linha foi escrito pela IA lendo os documentos.

**Revisão crítica e ajustes:**

- O enunciado só aceita `TRANSCRICAO` ou `CODIGO` como fonte, mas os documentos têm propostas do autor (as P-xx do FDD) e premissas (as PREM do PRD). A IA usou como localização o trecho ou o arquivo que deixou a lacuna, a mesma coluna "Origem da lacuna" criada no FDD na ETAPA-09, e o resumo da linha diz que o valor é proposta ou premissa. Nenhuma linha usa fonte inventada.
- O enunciado não define o que é "item identificável". O tracker explicita quais categorias contam em cada documento e o que fica de fora: históricos de revisão, diagramas, exemplos, seções narrativas do PRD e os critérios e testes do PRD. A cobertura declarada vale para essa definição.
- O script de validação (`check_tracker.py`) conferiu o formato de 6 colunas, a fonte, cada timestamp contra a transcrição, cada caminho de código e a cobertura contra os IDs e as tabelas dos documentos. Ele achou duas lacunas na primeira geração. O código `WEBHOOK_SECRET_REQUIRED`, que o FDD explica por que não é usado, não tinha linha e ganhou a FDD-ERRO-14. O ADR-007 cita 7 arquivos no Origem, mas só 5 tinham linha, e entraram as ADR-007-COD-06 e COD-07.
- A seção 20 do FDD citava só 6 prefixos (`FDD-FLUXO`, `FDD-CONTRATO` e outros), e a seção 11 do RFC citava só 4 tipos de item. As duas foram alinhadas ao que o tracker usa, com uma linha no histórico de cada documento.
- Limite da validação: o script confere que o timestamp existe, não que o trecho diga o que a linha afirma. Essa conferência foi feita na escrita, lendo a transcrição, e não por uma revisão independente.

**Arquivos:** `docs/TRACKER.md` (escrito a partir do placeholder), `docs/FDD.md` (seção 20 e histórico), `docs/RFC.md` (seção 11 e histórico).

**Lições:** gerar a tabela a partir de uma lista e validá-la contra os próprios documentos transformou a cobertura em algo mensurável, e o script achou itens que a leitura deixou passar. A coluna "Origem da lacuna", criada no FDD duas etapas antes por causa do tracker, resolveu aqui o rastreio das propostas do autor sem criar fonte fora das duas permitidas.

### ETAPA-13 — README do processo

**Data e hora:** 2026-11-14 17:02 · **Fase:** Documentos · **Ferramenta:** Claude Code (Claude Opus 5.5) com a skill `registro-processo` em Modo Consolidar · **Iterações:** 1 · **Registro:** no momento

**Objetivo:** escrever no `README.md` a documentação do processo, com as seis seções obrigatórias do enunciado, sintetizada a partir deste registro, sem apagar o enunciado original que estava no arquivo.

**Prompt** · Prompt de destaque:

```text
Pode criar o readme do projeto, mas não apague o que já existe. acredito que isso era uma opção do desafio.
```

**O que a IA entregou:** `README.md` com o processo no topo: "Sobre o desafio" em dois parágrafos; tabela de ferramentas de IA, com as ferramentas de apoio sem IA à parte; workflow em três fases (preparação, ferramental e documentos) e as cinco práticas de interação com a IA; 6 prompts literais (ETAPA-03, 06, 07, 08 na iteração 2, 09 e 10), cada um com uma linha de contexto; tabela de iterações por documento, com 13 iterações principais e 21 no registro inteiro, mais 9 momentos de erro e correção com exemplo concreto; e a ordem de leitura com os caminhos da entrega. O enunciado original ficou no fim, na seção "Enunciado original do desafio".

**Revisão crítica e ajustes:**

- O guia de consolidação da skill manda preservar um link para o enunciado e substituir o resto, mas o usuário pediu para não apagar nada. O enunciado também permite manter uma seção de referência ao original. A IA manteve o texto inteiro numa seção no fim e rebaixou os títulos dele dois níveis, para não competirem com as seções novas. Um script comparou o original com a versão do README sem os marcadores de título e confirmou que o texto é idêntico.
- O registro traz dois números para o tamanho do RFC: 2.871 palavras na primeira versão e "cerca de 3.070" como ponto de partida da iteração 2, provavelmente medidos de formas diferentes. O README usa só a comparação da iteração 2 (de cerca de 3.070 para 2.235 palavras) e descreve a primeira versão pelo limite da skill, sem número.
- Os scripts de validação usados nas etapas rodaram numa pasta temporária da sessão e não estão no repositório. O README diz isso explicitamente, para que ninguém procure por eles.
- O enunciado pede o total de iterações principais. O README separa as 21 iterações do registro, que incluem a preparação e as skills, das 13 dos documentos entregues, e diz que PRD e tracker tiveram uma iteração cada, com os ajustes feitos por autoverificação.
- Validação por script: as seis seções na ordem e com os nomes exigidos; os 6 prompts idênticos aos do registro; 11 links locais existentes; a única citação `[hh:mm] Nome` válida; nenhum parágrafo quebrado no README inteiro, incluindo o enunciado.

**Arquivos:** `README.md` (processo acrescentado no topo; enunciado original mantido no fim).

**Lições:** escrever o README a partir de um registro feito na hora deixou a seção de iterações com exemplos concretos e números conferíveis, em vez de lembranças vagas. A instrução do usuário de não apagar o enunciado prevaleceu sobre o guia da skill, e a solução de rebaixar os títulos manteve o texto intacto sem quebrar a hierarquia do README.

### ETAPA-14 — Revisão final contra o checklist, com um agente por documento

**Data e hora:** 2026-11-14 17:06 · **Fase:** Revisão final · **Ferramenta:** Claude Code (Claude Opus 5.5) com subagentes em paralelo; Chrome em modo headless para medir as páginas do RFC · **Iterações:** 2 · **Registro:** no momento

**Objetivo:** passar o pacote inteiro pelo checklist de critérios de aceite do enunciado, como pede o passo 9 da ordem sugerida, com uma validação independente por documento e um resultado consolidado.

**Prompts:**

Prompt de destaque:

```text
faça uma revisão final para garantir todos os itens do checklist. Faça uma separação de validação por documento, onde você pode lançar um agente para cada um deles e ao final termos um resultado consolidado
```

Depois do relatório consolidado, a resposta ao botão "Aplico as correções apontadas na revisão?":

```text
Corrigir tudo
```

**O que a IA entregou:**

- Iteração 1: sete agentes em paralelo, um para PRD, RFC, FDD com a spec, ADRs, tracker, README e consistência geral, cada um com os critérios literais do enunciado, instrução de ser adversarial e de não editar nada. Resultado: os 31 critérios de aceite atendidos e nenhum bloqueante. A IA conferiu os achados principais nos arquivos antes de consolidar.
- Iteração 2: dois agentes corrigiram os ADRs e o PRD e escreveram as linhas novas do tracker; a IA corrigiu o FDD, a spec, o RFC, o tracker, o README, o `CLAUDE.md` e este registro, e rodou de novo todas as checagens.

**Revisão crítica e ajustes:**

- Riscos de leitura literal dos critérios: os 8 ADRs usavam "Opções consideradas" e Status em linha → percebido pelo agente dos ADRs → seções `## Status` e `## Alternativas consideradas` nos ADRs e no template da skill `adr-writer`. O FDD tinha só 2 endpoints com corpo nos dois lados → frase explicando que GET, DELETE, `rotate-secret` e replay não têm corpo de requisição. A cobertura do tracker caía para 75% numa contagem estrita → linhas para os critérios e testes do PRD e para todas as consequências dos ADRs; o tracker passou de 370 para 513 linhas, com 87% de linhas `TRANSCRICAO`.
- O RFC renderizava em 6 a 7 páginas, contra as 2 a 4 do enunciado → percebido pelo agente do RFC, que mediu em PDF → componentes e fluxo condensados na visão geral, alternativas de implementação (ALT-11 a ALT-17) deixadas para o FDD, histórico e seção de origem retirados (o histórico fica neste registro). Foram várias rodadas de corte, cada uma medida no Chrome, até caber em 4 páginas em 11pt e em 10pt, com 1.552 palavras.
- Contradições entre documentos: o ADR-005 dizia que o `X-Timestamp` apoiava a detecção de replay, e o FDD e a spec diziam o contrário; o PRD tratava a leitura de 6 envios (P-01) como decidida; o RFC prometia ordem sem a ressalva das retentativas e dava como "confirmado" um prazo que o Marcos só prometeu confirmar em [09:47]; o ADR-007 e o OBJ-06 do FDD diziam que o código compartilhado não mudava → todos alinhados, com as propostas novas P-21 a P-27 no FDD.
- O `CLAUDE.md` da ETAPA-01 dizia que `requireRole` não era usado em nenhuma rota; ele é usado em `src/modules/users/user.routes.ts` → percebido por três agentes → frase corrigida.
- O README afirmava "registro na hora" para todas as etapas, `doc-review` depois de cada documento e o Fable na revisão das skills → percebido pelo agente do README, que conferiu no histórico da sessão → afirmações restringidas ao que este registro mostra, e o modelo das ETAPA-01 e 02 confirmado como Fable 5.1 (ver Correções do registro).
- Os scripts de checagem dos agentes acusaram falsos positivos, como caminhos de documentos lidos como diretórios e os arquivos do módulo de webhooks, que são propostos → descartados na conferência.

**Arquivos:** `docs/adrs/ADR-001` a `ADR-008` e `docs/adrs/README.md`, `docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/openapi/webhooks.openapi.yaml` (versão 0.2.0), `docs/TRACKER.md`, `README.md`, `CLAUDE.md`, `.claude/skills/adr-writer/references/template-adr.md` e `checklist-adr.md`, `docs/REGISTRO-PROCESSO.md`.

**Lições:** revisões feitas documento a documento não pegam contradições entre eles; agentes independentes, cada um com o critério literal e a instrução de procurar o que reprovaria a entrega, acharam pontos que sobreviveram a todas as rodadas anteriores. Medir o tamanho do RFC renderizado, e não por contagem de palavras, mostrou que ele estava quase no dobro do limite.

---

## Correções do registro

| Data | Etapa | O que mudou |
| --- | --- | --- |
| 2026-11-08 | ETAPA-09 | Removida a afirmação de que a chave estrangeira de `webhooks` quebraria a suíte de testes: com `onDelete: Cascade`, apagar clientes já remove as tabelas novas. Erro apontado na revisão do FDD (iteração 2) |
| 2026-11-14 | ETAPA-01 e 02 | Modelo trocado de "não registrado" para Claude Fable 5.1, conferido no histórico da sessão durante a revisão final (ETAPA-14) |
| 2026-11-14 | ETAPA-05 | O caminho `src/shared/errors.ts` do exemplo trocado no template ganhou a nota de que é fictício, para não ser lido como arquivo do repositório |
| 2026-11-14 | ETAPA-06 | Acrescentado o prompt literal das 14:56 sobre o acionamento da skill, recuperado do histórico da sessão |
| 2026-11-14 | ETAPA-08 | Os dois prompts da iteração 2 passaram de citação no texto para blocos de código, com o texto literal do histórico da sessão |
| 2026-11-14 | Ferramentas | MySQL incluído entre as consultas ao Context7, como já constava na ETAPA-10 |
