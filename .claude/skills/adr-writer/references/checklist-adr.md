# Checklist de Validação dos ADRs

Execute para cada ADR e depois para o conjunto. Corrija e repita até 3 vezes. Persistindo falhas, reporte ao usuário.

## Por ADR

### Estrutura

- [ ] Nome do arquivo segue o padrão da pasta e o número não repete nenhum existente.
- [ ] Há H1 com número e título afirmativo, linha de metadados com status, data e decisores.
- [ ] Seções presentes: Status, Contexto e problema, Fatores de decisão, Alternativas consideradas, Decisão, Consequências, Prós e contras das alternativas, Relacionados; Origem em Modo Fontes.
- [ ] Texto em português do Brasil; identificadores e caminhos no original.
- [ ] Cada parágrafo, item e célula em uma única linha na fonte.

### Conteúdo

- [ ] O título diz a decisão, não o tema.
- [ ] Trata de uma única decisão.
- [ ] Teste da decisão trocada: o contexto continua verdadeiro se a decisão fosse a alternativa.
- [ ] O contexto cita as restrições que moldaram a escolha (time, prazo, infraestrutura, RNFs) com números quando a fonte os deu.
- [ ] Há de 2 a 5 fatores de decisão, e a justificativa da decisão os cita.
- [ ] Há pelo menos uma alternativa real, com motivo de descarte em termos dos fatores.
- [ ] A decisão é uma frase direta no presente.
- [ ] Há pelo menos uma consequência negativa ou trade-off aceito, concreto.
- [ ] Prós e contras não usam adjetivos sem referência ("mais simples", "mais robusto") sem dizer para quê.
- [ ] Não há descrição de fluxo completo, contrato, tabela ou endpoint além do necessário para entender a decisão.
- [ ] Links em Relacionados são relativos e apontam para arquivos que existem.

### Fidelidade (Modo Fontes)

- [ ] Decisão, alternativas e consequências têm localização na seção Origem.
- [ ] Nenhum argumento foi atribuído à fonte sem estar nela; argumentos próprios estão marcados como observação do autor.
- [ ] Alternativas não discutidas na fonte estão marcadas como tal.
- [ ] A versão registrada é a final da fonte, não uma revertida depois.
- [ ] Todo caminho de arquivo citado existe no repositório.
- [ ] O status reflete o que a fonte diz: "Aceito" só com fechamento explícito.

## Do conjunto

- [ ] Numeração sequencial sem repetição; números de ADRs obsoletos não foram reaproveitados.
- [ ] Nenhuma decisão aparece em dois ADRs; nenhum ADR contém duas decisões.
- [ ] ADRs que dependem de outros os citam em Relacionados, e a ordem numérica respeita a dependência quando criados juntos.
- [ ] Substituições têm link nos dois sentidos e o antigo tem status "Substituído por".
- [ ] O índice `README.md` lista todos os ADRs com título, status e data, e não contém instruções conflitantes com o padrão adotado.
- [ ] Decisões que o usuário listou como principais estão cobertas, ou a ausência foi justificada.
- [ ] O conjunto não contradiz PRD, RFC ou FDD existentes; divergências foram apontadas ao usuário.
- [ ] Pelo menos um ADR cita arquivos, módulos ou padrões reais do código quando a decisão toca o sistema existente.
