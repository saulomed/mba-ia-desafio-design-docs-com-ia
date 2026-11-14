# Checklist de Validação do FDD

Execute antes de salvar. Corrija e repita até 3 vezes. Persistindo falhas, reporte ao usuário.

## Estrutura

- [ ] H1 e linha de metadados com status, data, autor e documentos relacionados.
- [ ] Seções 1 a 19 presentes na ordem do template; seção 20 em Modo Fontes.
- [ ] Texto em português do Brasil; identificadores, caminhos, endpoints e campos no original.
- [ ] Cada parágrafo, item e célula em uma única linha na fonte.

## Código real

- [ ] Todo caminho de arquivo citado como existente foi confirmado por listagem real do sistema de arquivos nesta sessão.
- [ ] Arquivos novos propostos estão marcados como "(novo)".
- [ ] A seção de integração tem pelo menos quatro arquivos reais em projeto existente, cada um com "hoje", "mudança ou uso" e "cuidado".
- [ ] O ponto de encaixe da feature nomeia o arquivo, a função e a transação em que entra.
- [ ] Erros, validação, autenticação, logging, resposta, configuração e transações reusam as abstrações do projeto, nomeadas com classe e arquivo; padrões novos têm justificativa e referência a ADR.
- [ ] Nomes de tabelas, colunas, arquivos e códigos seguem as convenções observadas no projeto.

## Acionabilidade

- [ ] Teste do desenvolvedor: nenhuma seção deixa um valor, nome ou comportamento para o implementador escolher.
- [ ] Cada fluxo tem gatilho, passos numerados com componente responsável, limites de transação explícitos, pós-condições e o comportamento de falha em cada passo.
- [ ] Há diagrama de sequência para o fluxo principal e para o fluxo de falha mais relevante; diagrama de estados quando há máquina de estados.
- [ ] Cada contrato tem método, caminho, autenticação, cabeçalhos, exemplo de requisição e de resposta, tabela de campos com regras e tabela de status codes com condição.
- [ ] Há pelo menos quatro contratos quando a feature expõe API; chamadas de saída têm exemplo completo do que o destinatário recebe.
- [ ] A matriz de erros usa o prefixo definido em todos os códigos da feature, e cada erro tem HTTP, condição, mensagem, ação do cliente e classe reusada ou nova.
- [ ] Resiliência define tempo limite, tentativas, intervalo com fórmula e teto, condição de falha definitiva, destino do registro definitivo, reprocessamento manual e idempotência.
- [ ] Observabilidade nomeia métricas com tipo e rótulos, eventos de log com campos, propagação de correlação e alertas com limiar.
- [ ] Configuração lista cada variável nova com tipo, padrão, obrigatoriedade e onde é validada.
- [ ] Estratégia de testes segue o framework e os helpers do projeto e lista cenários ligados a fluxos e erros.
- [ ] Critérios de aceite cobrem cada fluxo, contrato, erro crítico e RNF.

## Consistência com outros documentos

- [ ] Latências, tentativas, intervalos, limites e retenção têm o mesmo valor que no PRD, RFC e ADRs.
- [ ] Requisitos são referenciados por ID do PRD, não reescritos.
- [ ] Decisões são referenciadas por ADR, sem justificativa repetida; não há "porque é melhor que".
- [ ] Alternativas não aparecem; quando necessário, há link para o RFC.
- [ ] Itens fora de escopo ou adiados no PRD ou RFC não receberam desenho.
- [ ] Nomes de componentes batem com o RFC.

## Fidelidade (Modo Fontes)

- [ ] Cada fluxo, contrato, número e decisão tem localização na seção Origem ou no tracker.
- [ ] Todo valor sem fonte está marcado como `AUTOR` e, se crítico, listado em questões em aberto com proposta provisória.
- [ ] Onde o código contradiz a fonte, o FDD segue o código, cita ambos e abre questão.
- [ ] Nenhum requisito ou comportamento foi inventado sem marcação.
