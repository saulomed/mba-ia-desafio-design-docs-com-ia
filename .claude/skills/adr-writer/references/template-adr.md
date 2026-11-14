# Template do ADR

Formato MADR adaptado, em português do Brasil. Todas as seções abaixo são obrigatórias, exceto as marcadas como opcionais. Cada parágrafo, item e célula em uma única linha na fonte.

## Nome do arquivo

`ADR-NNN-titulo-em-kebab-case.md`, com três dígitos e título curto derivado do título do documento. Exemplo: `ADR-003-fila-persistente-para-lembretes.md`. Se a pasta já usar outro padrão, siga o existente.

## Estrutura

```markdown
# ADR-NNN: <Título afirmativo que diz a decisão>

## Status

Proposto | Aceito | Rejeitado | Substituído por [ADR-MMM](ADR-MMM-....md) | Obsoleto · **Data:** AAAA-MM-DD · **Decisores:** <nomes e papéis> · **Consultados:** <opcional> · **Informados:** <opcional>

## Contexto e problema

<2 a 4 parágrafos. O problema que exige decisão, as forças em jogo e as restrições: prazo, time, infraestrutura disponível, requisitos não funcionais, decisões anteriores. Em projeto existente, os arquivos ou padrões que a decisão toca, com caminhos reais. Nada aqui pressupõe a decisão.>

## Fatores de decisão

- <fator 1, ex.: consistência entre a mudança de estado e o evento publicado>
- <fator 2, ex.: tamanho do time e infraestrutura que ele consegue operar>
- <fator 3, ex.: prazo comercial>

## Alternativas consideradas

1. **<Opção escolhida>**
2. **<Alternativa A>**
3. **<Alternativa B>** <(marcar "plausível, não discutida" quando a fonte não a mencionou)>

## Decisão

<Uma frase no presente: "Adotaremos <opção> porque <fatores decisivos>.">

<Justificativa em 1 a 3 parágrafos ligando a opção aos fatores de decisão. Se houve divergência entre decisores, registre como foi resolvida.>

## Consequências

**Positivas**
- <o que se ganha, em termos do problema e dos fatores>

**Negativas e trade-offs aceitos**
- <o que se perde ou passa a ser necessário fazer: custo operacional, complexidade, limite conhecido>

**O que fica em aberto ou a revisitar**
- <condição que levaria a rever a decisão, ou ponto adiado>

## Prós e contras das alternativas

### <Opção escolhida>
- Bom, porque <...>
- Ruim, porque <...>

### <Alternativa A>
- Bom, porque <...>
- Ruim, porque <...> <(motivo do descarte)>

### <Alternativa B>
- Bom, porque <...>
- Ruim, porque <...>

## Validação (opcional)

<Como saberemos que a decisão está funcionando: métrica, revisão em data, teste.>

## Relacionados

- PRD: [docs/PRD.md](../PRD.md) <seção ou ID, se houver>
- RFC: [docs/RFC.md](../RFC.md) <seção, se houver>
- FDD: [docs/FDD.md](../FDD.md) <seção, se houver>
- ADRs: [ADR-MMM](ADR-MMM-....md) <relação: depende de, complementa, substitui>

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Decisão | TRANSCRICAO | [hh:mm] Nome |
| Alternativa A | TRANSCRICAO | [hh:mm] Nome |
| Restrição de infraestrutura | CODIGO | caminho/do/arquivo |
```

A seção **Origem** é obrigatória em Modo Fontes e omitida em Modo Descoberta, a menos que o usuário peça. Quando houver tracker externo, ela pode ser reduzida a uma linha apontando para ele.

## Orientações por seção

**Título.** Diz a escolha, não o tema. "Fila persistente no banco atual para lembretes" é título; "Mecanismo de lembretes" não é. Evite "Decisão sobre..." e "Estratégia de...".

**Status.** "Proposto" até confirmação explícita de quem decide. Em Modo Fontes, uma decisão fechada na reunião nasce "Aceito" com a data da reunião. "Substituído por" sempre com link para o ADR novo. "Obsoleto" quando o contexto desapareceu sem substituição.

**Contexto.** Teste da decisão trocada: o contexto continua verdadeiro se a decisão fosse a alternativa? Se não, ele está contaminado pela solução. Números e restrições pertencem aqui ("time de 4 pessoas", "latência percebida como aceitável abaixo de 10 s").

**Fatores de decisão.** De 2 a 5. São o critério de julgamento. Cada alternativa é avaliada contra eles, e a justificativa da decisão os cita.

**Opções.** A escolhida primeiro. Cada opção com nome que a distinga em uma linha. Pelo menos uma alternativa real.

**Decisão.** Frase única e direta. Evite "optou-se por", prefira "adotaremos", "usaremos", "manteremos".

**Consequências negativas.** Toda decisão tem custo. Exemplos de custo típico: operação adicional, acoplamento, limite de escala, duplicidade tolerada, latência adicional, dívida a pagar depois. Se não encontrar nenhum, a decisão não foi analisada até o fim.

**Prós e contras.** Em termos dos fatores de decisão, não em generalidades ("mais simples", "mais robusto" sem dizer para quê).

**Relacionados.** Links relativos que funcionem a partir da pasta de ADRs. Cite o ID ou seção específica quando houver.

## Exemplo resumido

Ilustrativo, domínio fictício. Use apenas a forma.

```markdown
# ADR-002: Fila persistente no banco atual para programar lembretes

## Status

Aceito · **Data:** 2026-03-05 · **Decisores:** Ana (tech lead), Paulo (eng. sênior) · **Consultados:** Júlia (segurança)

## Contexto e problema

A confirmação de uma consulta precisa gerar um lembrete programado para 24 h antes. Hoje a confirmação é uma transação que atualiza `src/appointments/appointment.service.ts` (`confirm`) e grava histórico. Enviar o lembrete na própria requisição deixaria a confirmação dependente do provedor de SMS, e um provedor lento travaria a recepção. O time tem 3 pessoas e opera apenas o banco relacional existente; não há mensageria em produção.

## Fatores de decisão

- Nenhuma consulta confirmada pode ficar sem lembrete programado
- Confirmação não pode ficar mais lenta do que hoje (p95 de 300 ms)
- O time precisa conseguir operar a solução sem infraestrutura nova

## Alternativas consideradas

1. **Tabela de lembretes pendentes no banco atual, processada por um worker**
2. **Envio síncrono na requisição de confirmação**
3. **Broker de mensagens dedicado** (plausível, não discutida)

## Decisão

Adotaremos uma tabela de lembretes pendentes gravada na mesma transação da confirmação, processada por um worker separado, porque garante que confirmação e lembrete são registrados juntos sem adicionar infraestrutura que o time não opera.

## Consequências

**Positivas**
- Confirmação e lembrete programado são atômicos; não há consulta confirmada sem lembrete
- Nenhuma peça nova de infraestrutura para operar

**Negativas e trade-offs aceitos**
- O worker introduz um processo a mais para implantar e monitorar
- A tabela cresce com o volume; precisa de limpeza periódica, ainda não definida

**O que fica em aberto ou a revisitar**
- Se o volume passar de 20.000 lembretes por dia, reavaliar um broker dedicado

## Prós e contras das alternativas

### Tabela de pendentes no banco atual
- Bom, porque é atômico com a confirmação
- Ruim, porque exige worker e limpeza

### Envio síncrono
- Bom, porque é o mais simples de implementar
- Ruim, porque acopla a confirmação ao provedor de SMS e viola o fator de latência

### Broker dedicado
- Bom, porque escala e desacopla
- Ruim, porque adiciona infraestrutura que o time de 3 pessoas não opera hoje

## Relacionados

- PRD: [docs/PRD.md](../PRD.md), RNF-01
- FDD: [docs/FDD.md](../FDD.md), seção "Fluxos detalhados"

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Decisão | TRANSCRICAO | [14:12] Ana |
| Descarte do envio síncrono | TRANSCRICAO | [14:05] Paulo |
| Transação de confirmação existente | CODIGO | src/appointments/appointment.service.ts |
```

## Índice da pasta

`README.md` em `PASTA_ADRS`:

```markdown
# Architecture Decision Records

Decisões arquiteturais do projeto, uma por arquivo, no formato MADR. Nome: `ADR-NNN-titulo-em-kebab-case.md`. ADRs aceitos não mudam de sentido; mudanças geram um ADR novo que substitui o anterior.

| ADR | Título | Status | Data |
| --- | --- | --- | --- |
| [ADR-001](ADR-001-....md) | ... | Aceito | AAAA-MM-DD |
```
