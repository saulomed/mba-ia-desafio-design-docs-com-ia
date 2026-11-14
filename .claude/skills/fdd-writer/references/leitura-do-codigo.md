# Leitura do Código

Roteiro para explorar um repositório antes de escrever o FDD. O objetivo é mapear onde a feature se encaixa e quais padrões ela deve seguir, com caminhos reais. Leia o suficiente para responder às perguntas abaixo; não leia o repositório inteiro.

## Ordem de leitura

1. **Documentação de entrada.** `README.md`, `CLAUDE.md`, `CONTRIBUTING.md`, `docs/`. Muitas vezes já descrevem arquitetura, convenções e comandos.
2. **Manifesto e configuração.** `package.json`, `pom.xml`, `build.gradle`, `pyproject.toml`, `go.mod` ou equivalente: stack, versões, scripts de build e teste. Arquivos de ambiente de exemplo (`.env.example`): variáveis existentes e onde novas devem entrar.
3. **Ponto de entrada e composição.** Onde a aplicação sobe e como as dependências são montadas (container de DI, composição manual, módulos do framework). É onde um módulo novo será registrado.
4. **Um módulo existente completo.** Escolha o módulo mais parecido com a feature e leia todas as camadas: rota ou controller, serviço, repositório ou acesso a dados, schemas ou DTOs, testes. Esse módulo é o molde.
5. **Padrões transversais.** Erros e seu formato de resposta; validação de entrada; autenticação e autorização; logging e correlação de requisições; configuração; acesso a banco e transações; mensageria, filas ou jobs se houver.
6. **Modelo de dados.** Schema do ORM ou migrações: entidades, enums, índices, convenções de nome de tabela e coluna, estratégia de IDs.
7. **Ponto de encaixe da feature.** O arquivo e a função onde a feature se conecta ao fluxo existente (um método de serviço, um evento, um hook). Leia a função inteira, incluindo a transação em que roda, porque é aí que a integração acontece.
8. **Testes.** Como são organizados, que helpers e fábricas existem, como o banco de teste é preparado. O FDD deve dizer como a feature será testada no mesmo estilo.

## Perguntas a responder

Ao final da leitura, você deve conseguir preencher:

| Pergunta | Onde a resposta vai no FDD |
| --- | --- |
| Como um módulo novo é criado e registrado? | Integração com o sistema existente |
| Qual a hierarquia de erros e o formato de resposta de erro? | Matriz de erros, Integração |
| Como entrada é validada e com que biblioteca? | Contratos, Integração |
| Como rotas são autenticadas e autorizadas? | Contratos, Segurança |
| Como se loga e como requisições são correlacionadas? | Observabilidade |
| Como variáveis de ambiente são lidas e validadas? | Configuração |
| Como transações são abertas e qual o cliente de banco? | Fluxos, Modelo de dados |
| Em que função exata a feature se encaixa, e dentro de qual transação? | Fluxos, Integração |
| Quais entidades e enums existem que a feature vai referenciar? | Modelo de dados |
| Como os testes preparam dados e banco? | Estratégia de testes |
| Que convenções de nome o projeto usa (arquivos, tabelas, colunas, códigos de erro)? | Todo o documento |

## O que anotar

Para cada arquivo relevante, uma linha: caminho, o que ele faz, e o que a feature faz com ele (muda, estende, reusa, registra). Essa lista vira a seção de integração.

Para cada padrão transversal, o nome da abstração e o arquivo onde está definida, para que o FDD diga "usar `NomeDaClasse` de `caminho/arquivo`" em vez de "usar o tratamento de erros existente".

## Sinais de alerta

- **Nenhum módulo parecido existe.** A feature introduz um tipo novo de componente (ex.: primeiro worker em um projeto só de API). O FDD precisa dizer como esse componente sobe, é configurado e é testado, e isso provavelmente merece ADR.
- **Padrão transversal ausente** (ex.: não há logging estruturado). O FDD não deve inventar um só para a feature; aponte a ausência como questão em aberto ou proposta marcada como `AUTOR`.
- **Código contradiz os documentos anteriores.** Registre conforme o código, cite ambos e abra questão.
- **Função de encaixe grande ou com efeitos colaterais.** Descreva no fluxo exatamente em que ponto da função a feature entra e o que acontece com a transação se a feature falhar.

## Verificação final

Antes de salvar o FDD, liste os caminhos citados e confirme a existência de cada um com uma listagem real do sistema de arquivos. Caminhos de arquivos novos propostos ficam marcados como "(novo)" para que não sejam confundidos com existentes.
