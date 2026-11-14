# Checklist de Validação do PRD

Execute antes de salvar. Se algum item falhar, corrija e execute de novo, até 3 iterações. Persistindo falhas, reporte ao usuário e peça orientação.

## Estrutura e idioma

- [ ] O PRD começa com H1 e linha de metadados, seguido da seção 1. Em Modo Revisão, o histórico de revisões tem uma linha nova.
- [ ] Em Modo Revisão, nenhum ID existente foi renumerado ou reaproveitado para outro item.
- [ ] Há exatamente as seções 1 a 13, mais a 14 no Modo Fontes, nesta ordem e sem seções extras.
- [ ] Todo o texto está em português do Brasil. Apenas identificadores de código, nomes de arquivo e termos técnicos consagrados ficam no original.
- [ ] Nenhum parágrafo, item de lista ou célula está quebrado em várias linhas na fonte.
- [ ] Não há "Próximos passos", "Validação" nem comentários sobre o processo de geração.

## Fidelidade à fonte (Modo Fontes)

- [ ] Todo requisito, decisão, restrição, número e meta tem origem identificável. Item sem origem foi removido ou convertido em `PREM-xx` ou `QA-xx`.
- [ ] Nenhum item descartado ou adiado na fonte aparece como requisito, capacidade ou Escopo completo.
- [ ] Toda localização `[hh:mm] Nome` corresponde a um trecho que diz o que o item afirma.
- [ ] Todo caminho de arquivo citado existe no repositório.
- [ ] Decisões revistas mais adiante na fonte estão registradas na versão final, não na inicial.
- [ ] Conflitos não resolvidos entre fontes estão em `QA-xx`.
- [ ] A seção 14 cobre pelo menos 80% dos itens com ID.

## Altura do documento

- [ ] Não há endpoints, payloads, nomes de tabelas, colunas, classes, filas ou cabeçalhos HTTP, exceto quando forem visíveis e relevantes para o usuário ou integrador.
- [ ] Decisões técnicas aparecem pelo efeito no produto, em uma linha, com referência ao ADR ou RFC, sem repetir a argumentação.
- [ ] Nenhum trecho longo da fonte foi copiado literalmente.

## Qualidade da escrita

- [ ] Teste do produto trocado: nenhum parágrafo de seções 1, 2 e 7 continuaria fazendo sentido com o nome de outro produto do mesmo setor.
- [ ] Cada requisito funcional pode ser reprovado por uma entrega concreta e trata de um só assunto.
- [ ] Requisitos dizem o quê, não o como; nenhuma solução técnica está disfarçada de requisito.
- [ ] Histórias têm ator específico, ação concreta no produto e benefício; nenhuma repete as Capacidades.
- [ ] Critérios de aceitação usam os mesmos números dos requisitos e RNFs que verificam.
- [ ] Métricas têm indicador, linha de base (ou "a medir"), meta, prazo e fonte do dado; há pelo menos uma métrica de resultado, não só de saída.

## Conteúdo mínimo

- [ ] Pelo menos um objetivo tem métrica com meta quantitativa, unidade e prazo ou condição.
- [ ] Todo objetivo tem métrica e forma de medir.
- [ ] Fora de escopo tem pelo menos 2 itens, cada um com tipo (Descartado ou Adiado), motivo e origem.
- [ ] Todo RNF tem critério mensurável com número.
- [ ] Há pelo menos 2 riscos, cada um com probabilidade, impacto e mitigação concreta.
- [ ] Questões em aberto estão explícitas e não foram resolvidas por suposição silenciosa.
- [ ] A estratégia de testes cobre os cenários de falha das funcionalidades críticas e a validação dos RNFs críticos.
- [ ] Nenhum requisito ou critério usa termo vago sem número ("rápido", "seguro", "escalável", "intuitivo").

## Consistência entre seções

- [ ] Cada problema da seção 2 tem solução correspondente na seção 7.
- [ ] Cada objetivo da seção 4 é sustentado por pelo menos uma funcionalidade.
- [ ] Cada funcionalidade da seção 7 tem histórias na seção 6 e critérios na seção 12.
- [ ] Cada funcionalidade da seção 7 aparece exatamente uma vez na tabela da seção 10.2, e vice-versa.
- [ ] Não há contradição entre a seção 7 e Fora de escopo.
- [ ] Os IDs de cada prefixo são sequenciais, com dois dígitos e sem lacunas.
- [ ] Funcionalidades críticas (autenticação, financeiro, perda de dados, segurança, operação irreversível) têm Tratamento de erros.

## Integridade do grafo de dependências

- [ ] Nenhuma dependência órfã: todo ID na coluna Dependências existe na coluna #.
- [ ] Sem ciclos: o grafo é acíclico.
- [ ] Ordem topológica: nenhuma linha cita dependência que aparece abaixo dela.
- [ ] Desempate topológico pelo ID menor.
- [ ] As arestas do Mermaid correspondem exatamente à coluna Dependências.
- [ ] Toda funcionalidade citada em um bloco Consome aparece como dependência na tabela.

## Ondas de execução

- [ ] Toda funcionalidade da tabela aparece em exatamente uma onda.
- [ ] A onda de cada funcionalidade é `max(onda das dependências) + 1`; a Onda 1 contém exatamente as que não têm dependências.
- [ ] Dentro de cada onda, ordem por prioridade crescente e desempate pelo ID menor.

## Funcionalidades de fundação (somente se a Parte 2 existir)

- [ ] Toda funcionalidade de fundação existe na tabela.
- [ ] Estão na mesma ordem topológica da tabela.
- [ ] Cada uma descreve o que contribui para a infraestrutura compartilhada.
- [ ] A nota sobre serialização da fundação está presente nas ondas.
- [ ] Se a feature é construída sobre um sistema existente, a presença de fundação está justificada.

## Contratos de integração

- [ ] Toda declaração Consome tem pelo menos um critério no bloco Integração entre funcionalidades.
- [ ] Toda referência "(usado por Fxx)" em Fornece tem a entrada Consome correspondente.
- [ ] Todo dado citado em Consome está coberto explicitamente pelo Fornece correspondente, pelo mesmo nome ou por um termo claramente mais amplo. Na dúvida, nomeie o dado no Fornece.

## Escopo essencial e completo

- [ ] Escopo completo só aparece em funcionalidades que têm Escopo essencial.
- [ ] Escopo essencial só é usado quando há capacidades de prioridades diferentes.
- [ ] A prioridade na tabela reflete o Escopo essencial.
- [ ] Escopo completo não contém nada adiado para fase futura.

## Arquivo de progresso (somente se habilitado)

- [ ] Todo ID da seção 7 será chave em `features`.
- [ ] `dependencies`, `priority` e `wave` de cada funcionalidade batem com a seção 10.2.
