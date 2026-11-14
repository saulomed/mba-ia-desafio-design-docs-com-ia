# Template do Registro do Processo

Estrutura em português do Brasil. Cada parágrafo, item e célula em uma única linha na fonte. Prompts sempre em bloco de código, literais.

## Estrutura do arquivo

````markdown
# Registro do Processo — <nome do projeto ou entrega>

Diário de bordo da produção com IA. Fonte para a seção de processo do README. Horários em <fuso, ex.: UTC-3>.

## Ferramentas utilizadas

| Ferramenta | Modelo ou versão | Papel no processo | Primeira etapa |
| --- | --- | --- | --- |
| <Claude Code> | <modelo> | <para que foi usada> | ETAPA-01 |

## Índice de etapas

| Etapa | Data e hora | Fase | Objetivo | Iterações | Resultado |
| --- | --- | --- | --- | --- | --- |
| ETAPA-01 | AAAA-MM-DD hh:mm | <fase> | <uma linha> | <n> | <arquivo ou "sem arquivo"> |

---

## Etapas

<blocos de etapa, do mais antigo para o mais recente>

---

## Correções do registro

| Data | Etapa | O que mudou |
| --- | --- | --- |
````

## Fases

Use uma destas, para o README conseguir agrupar o workflow:

| Fase | O que entra |
| --- | --- |
| Preparação | Contextualização da IA com o repositório, criação de arquivos de instrução (ex.: `CLAUDE.md`), leitura inicial |
| Ferramental | Configuração de ferramentas, troca de modelo, criação ou revisão de skills, prompts e plugins |
| Geração | Produção da primeira versão de um entregável |
| Revisão | Revisão crítica e nova geração de um entregável já existente |
| Consolidação | Tracker, README, revisão final contra os critérios de aceite |

## Bloco de etapa

````markdown
### ETAPA-NN — <título curto e afirmativo>

**Data e hora:** AAAA-MM-DD hh:mm · **Fase:** <fase> · **Ferramenta:** <ferramenta> (<modelo>) · **Iterações:** <n> · **Registro:** no momento | retroativo | relato do usuário

**Objetivo:** <o que se queria obter, em uma ou duas frases>.

**Prompt** <acrescente "· Prompt de destaque" quando valer ir para o README>:

```text
<texto literal digitado pelo usuário>
```

**O que a IA entregou:** <resumo factual do resultado, com números quando houver: arquivos, seções, linhas>.

**Revisão crítica e ajustes:**

- <Iteração 1> <problema concreto encontrado> → percebido por <usuário | autoverificação da IA> → <ajuste feito>.
- <Iteração 2> ...

**Arquivos:** `<caminho>` (criado | alterado), `<caminho>` (...).

**Lições:** <o que esta etapa ensinou sobre usar IA neste processo, quando houver; omita se não houver>.
````

Regras de preenchimento:

- **Prompt.** Se a etapa teve mais de um prompt relevante, use um bloco por prompt, na ordem. Se foi um comando, registre o comando com argumentos em bloco `bash` ou `text`.
- **Revisão crítica.** Use "Sem ajustes" só quando de fato não houve; nesse caso diga se a entrega foi revisada e por quem. Uma entrega aceita sem revisão é um dado, não um elogio.
- **Iterações.** Conte ciclos de geração, revisão e nova geração dentro da mesma etapa. A primeira geração conta como 1.
- **Campo desconhecido.** Escreva "não registrado". Não preencha por suposição.

## Exemplo resumido

Domínio fictício, apenas para ilustrar o nível de detalhe.

````markdown
### ETAPA-04 — Primeira versão do PRD de lembretes de consulta

**Data e hora:** 2026-03-10 15:20 · **Fase:** Geração · **Ferramenta:** Claude Code (modelo X) · **Iterações:** 2 · **Registro:** no momento

**Objetivo:** gerar o PRD a partir da transcrição da reunião de 03/03 e dos ADRs já escritos.

**Prompt** · Prompt de destaque:

```text
Use a skill prd-writer em Modo Fontes. Fonte: docs/reuniao-0303.md. Não inclua nada que a reunião descartou; o que foi adiado vai para fora de escopo com o horário da fala.
```

**O que a IA entregou:** PRD com 14 seções, 5 funcionalidades e 9 linhas no tracker.

**Revisão crítica e ajustes:**

- Iteração 1: lembrete por WhatsApp apareceu como requisito F05, mas foi adiado em [14:32] Paulo → percebido pelo usuário → F05 removida e item movido para "Fora de escopo" como adiado.
- Iteração 2: métrica "melhorar a taxa de comparecimento" sem número → percebido pela autoverificação da IA → reescrita com linha de base de 78% e meta de 88% em 3 meses, conforme [14:40] Ana.

**Arquivos:** `docs/PRD.md` (criado), `docs/TRACKER.md` (alterado).

**Lições:** pedir explicitamente o tratamento do que foi adiado evitou parte do problema, mas não todo; o checklist de fidelidade pegou o resto.
````
