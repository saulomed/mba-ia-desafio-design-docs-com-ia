# Consolidação do Registro no README

Como transformar o registro do processo na seção de processo do README. O registro é longo e cronológico; o README é curto e organizado por tema. Sintetize, não copie.

## Passo 1: Descobrir a estrutura exigida

Leia o README atual e qualquer enunciado, guia de contribuição ou critério de aceite do projeto que diga o que a seção de processo deve conter. Se houver estrutura obrigatória, siga-a na ordem e com os nomes pedidos, porque é contra ela que a entrega será avaliada. Se não houver, use a estrutura padrão abaixo.

Se o README atual for o enunciado de um desafio que pede para ser substituído, preserve um link para o enunciado original quando o próprio enunciado permitir, e substitua o resto.

## Passo 2: Mapear registro para seções

| Seção do README | De onde vem no registro | Como sintetizar |
| --- | --- | --- |
| Ferramentas de IA utilizadas | Tabela "Ferramentas utilizadas" | Uma linha por ferramenta com o papel; modelos quando a troca de modelo foi deliberada |
| Workflow adotado | Índice de etapas, agrupado por fase | Ordem em que os entregáveis foram produzidos e por quê; como a interação com a IA foi organizada (skills, modos, revisão a cada etapa) |
| Prompts customizados | Etapas marcadas com "Prompt de destaque" | Os mais representativos, em blocos de código, cada um com uma linha de contexto: em que etapa e o que produziu |
| Iterações e ajustes | Campos "Revisão crítica e ajustes" | Os momentos mais significativos com problema concreto e correção; total de iterações principais |
| Como navegar a entrega | Campo "Arquivos" das etapas e estrutura real de pastas | Caminhos dos entregáveis e ordem sugerida de leitura, verificados com listagem real |

## Passo 3: Escolher o que entra

- **Prompts:** prefira os que mostram intenção (papel, restrições, formato, critério de qualidade). Inclua no mínimo a quantidade exigida pelo projeto. Mantenha o texto literal; se o prompt tiver erros de digitação, mantenha-os, porque o README documenta o que foi feito.
- **Iterações:** escolha as que mostram a IA errando de forma instrutiva (inventou requisito, vazou contexto, ficou genérica, citou arquivo inexistente) e a correção concreta. Inclua no mínimo a quantidade exigida pelo projeto. Diga o total de iterações principais contando a partir do índice.
- **Workflow:** conte a ordem real, incluindo desvios da ordem sugerida e o motivo.

## Passo 4: Escrever

- Texto em português do Brasil, direto, em primeira pessoa do autor da entrega quando o README for dele.
- Cada parágrafo, item e célula em uma única linha na fonte.
- Cite etapas do registro só quando ajudar a navegação (ex.: "detalhes em `docs/REGISTRO-PROCESSO.md`, ETAPA-05"). Não reproduza blocos inteiros.
- Inclua o registro na seção "Como navegar a entrega", como material de apoio.

## Estrutura padrão (quando o projeto não define uma)

```markdown
# <Nome da entrega>

<Uma ou duas frases: o que é este repositório e o que foi entregue.>

## Ferramentas de IA utilizadas

## Workflow adotado

## Prompts customizados

## Iterações e ajustes

## Como navegar a entrega
```

## Checklist antes de salvar

- [ ] Todas as seções obrigatórias do projeto estão presentes, na ordem e com os nomes pedidos.
- [ ] Quantidade mínima de ferramentas, prompts e iterações exigida pelo projeto foi atingida, com conteúdo vindo do registro.
- [ ] Cada prompt está em bloco de código e é literal ao registro.
- [ ] Cada iteração descreve problema concreto e correção concreta.
- [ ] Todo caminho citado em "Como navegar a entrega" existe.
- [ ] Nada no README contradiz o registro; nada no README deixa de ter origem no registro ou no repositório.
- [ ] Se o README substituído era um enunciado, o link para o original foi preservado quando permitido.
