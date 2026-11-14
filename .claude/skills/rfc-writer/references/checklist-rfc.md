# Checklist de Validação do RFC

Execute antes de salvar. Corrija e repita até 3 vezes. Persistindo falhas, reporte ao usuário.

## Estrutura

- [ ] H1 com título que diz a proposta, seguido da tabela de metadados com autor, status, data e revisores.
- [ ] Seções presentes na ordem: TL;DR, Contexto e problema, Objetivos e não objetivos, Proposta técnica, Alternativas consideradas, Impacto e riscos, Questões em aberto, Decisões relacionadas, Histórico de revisão; Origem em Modo Fontes.
- [ ] Entre 800 e 2.000 palavras fora dos diagramas e tabelas de origem.
- [ ] Texto em português do Brasil; identificadores e caminhos no original.
- [ ] Cada parágrafo, item e célula em uma única linha na fonte.

## Altura do documento

- [ ] Não há payloads, DDL, assinaturas de função, lista de status codes nem tabela de campos. Onde isso seria necessário, há referência ao FDD.
- [ ] Nenhuma decisão é argumentada em mais de um parágrafo; o detalhe está no ADR referenciado.
- [ ] Requisitos de produto aparecem resumidos com referência ao PRD, não copiados.
- [ ] Componentes são nomeados pelo papel, não por nome de classe.

## Conteúdo

- [ ] TL;DR tem de 3 a 5 frases e cobre problema, abordagem, trade-off principal e o que precisa de decisão.
- [ ] Há objetivos verificáveis e pelo menos um não objetivo com motivo.
- [ ] A proposta descreve componentes, responsabilidades e fluxo principal, com diagrama quando há mais de dois componentes interagindo.
- [ ] Garantias e limites têm números quando a fonte os deu.
- [ ] Há pelo menos duas alternativas reais, cada uma com o que era, pontos a favor e trade-off do descarte em termos dos fatores do projeto.
- [ ] Há pelo menos duas questões em aberto, concretas, com por que importa e quem decide.
- [ ] Impacto cita módulos ou arquivos reais quando há sistema existente.
- [ ] Há riscos com probabilidade, impacto e mitigação concreta.
- [ ] Decisões relacionadas referenciam pelo menos dois ADRs com link, ou dizem quais deveriam existir.
- [ ] Teste do revisor: cada seção dá ao leitor algo para concordar ou discordar.

## Fidelidade (Modo Fontes)

- [ ] Abordagem, alternativas, descartes e questões têm localização na seção Origem.
- [ ] Alternativas não discutidas na fonte estão marcadas como tal.
- [ ] Itens que a fonte adiou estão em não objetivos ou questões em aberto, nunca na proposta.
- [ ] Nenhum argumento foi atribuído à equipe sem estar na fonte; argumentos próprios estão marcados como observação do autor.
- [ ] Todo caminho de arquivo citado existe no repositório.
- [ ] Todo link para ADR, PRD ou FDD aponta para arquivo existente.

## Consistência com outros documentos

- [ ] Números (latências, volumes, limites) batem com PRD e ADRs.
- [ ] Nomes de componentes batem com os usados no FDD, quando existir.
- [ ] Nenhuma alternativa descartada aqui aparece como adotada em outro documento.
