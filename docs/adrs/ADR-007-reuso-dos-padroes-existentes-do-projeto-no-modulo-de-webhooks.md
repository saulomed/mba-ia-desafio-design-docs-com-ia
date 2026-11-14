# ADR-007: Módulo de webhooks construído com os padrões já existentes no projeto, sem bibliotecas ou estruturas novas

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Bruno (Engenheiro Pleno, Pedidos), Diego (Engenheiro Sênior, Plataforma)

## Contexto e problema

A feature de webhooks introduz endpoints de configuração, consulta de entregas, replay administrativo e um worker. O OMS já tem convenções estabelecidas: os módulos de domínio (`customers`, `orders`, `products` e `users`) ficam em `src/modules/<nome>/` com routes, controller, service, repository e schemas Zod (o módulo `auth` não tem repository), e são montados em `src/app.ts` e `src/routes/index.ts`; erros derivam de `AppError` (`src/shared/errors/app-error.ts`) com subclasses de domínio e códigos como `INSUFFICIENT_STOCK` e `INVALID_STATUS_TRANSITION` (`src/shared/errors/http-errors.ts`); o middleware central `src/middlewares/error.middleware.ts` serializa `AppError`, erros Zod e Prisma; o logging é Pino (`src/shared/logger/index.ts`).

A questão é se a feature segue essas convenções ou ganha estrutura própria. A autorização por perfil também já existe: o middleware `requireRole` em `src/middlewares/auth.middleware.ts`.

## Fatores de decisão

- Consistência com a codebase e curva de aprendizado de quem já mantém o OMS
- Tratamento de erros e logs uniforme, sem mudar o comportamento do código compartilhado

## Alternativas consideradas

1. **Reuso máximo: módulo `src/modules/webhooks` no padrão existente, `AppError` com prefixo `WEBHOOK_`, Pino, error middleware, schemas Zod**
2. **Estrutura e bibliotecas próprias para a feature** (observação do autor: a alternativa não foi discutida. A frase "não vamos botar nada novo" é de Bruno sobre o logger, [09:29] Bruno, e a extensão dela à feature inteira se apoia na decisão de "reuso máximo do que já existe", [09:30] Larissa)

## Decisão

Adotaremos reuso máximo do que já existe: o módulo `src/modules/webhooks` segue a mesma estrutura de controller, service, repository, routes e schemas Zod; os erros estendem a hierarquia de `AppError` com códigos prefixados por `WEBHOOK_`; o log usa o Pino existente; e o error middleware central trata os novos erros sem mudança, porque isso mantém a feature consistente com a codebase sem mudar o comportamento do código compartilhado. No código compartilhado entram só acréscimos (subclasses de erro em `src/shared/errors/http-errors.ts` e `src/shared/errors/index.ts`, variáveis de ambiente em `src/config/env.ts` e o registro do módulo em `src/app.ts` e `src/routes/index.ts`) e uma única mudança de comportamento: a inclusão dos caminhos da secret no `redactPaths` do logger (proposta P-24 do FDD).

Bruno citou como exemplos de código de erro `WEBHOOK_NOT_FOUND`, `WEBHOOK_INVALID_URL` e `WEBHOOK_SECRET_REQUIRED` ([09:28] Bruno). No [FDD](../FDD.md#81-erros-retornados-pela-api), `WEBHOOK_INVALID_URL` sai como mensagem em `details` de um `400 VALIDATION_ERROR`, e não como `error.code`, porque a validação do `https` fica no schema Zod ([09:23] Sofia) e o `validate` existente (`src/middlewares/validate.middleware.ts`) converte toda falha de schema nesse código (proposta P-21; a alternativa é o service lançar `BadRequestError` com código próprio). `WEBHOOK_SECRET_REQUIRED` não é usado, porque a secret é gerada pela plataforma ([09:31] Marcos).

O worker segue a mesma linha: `src/worker.ts` como entry-point ao lado de `src/server.ts`, com a lógica dentro do módulo, e o replay administrativo usa o `requireRole` já existente em `src/middlewares/auth.middleware.ts`.

## Consequências

**Positivas**
- Quem conhece um módulo do OMS entende o de webhooks
- Erros de webhook chegam ao cliente HTTP no mesmo formato `{ error: { code, message, details? } }` sem tocar no middleware
- Nenhuma dependência nova a avaliar, aprovar ou manter

**Negativas e trade-offs aceitos**
- A feature herda as limitações dos padrões atuais; necessidades específicas de webhooks (por exemplo, métricas de entrega) precisam caber no que Pino e a estrutura existente oferecem (observação do autor)
- O worker, por não ser uma requisição HTTP, não passa pelo error middleware nem pelo `src/middlewares/request-logger.middleware.ts`, e precisa tratar e logar seus erros por conta própria (observação do autor)
- A regra de `https` fica no schema e sai como `VALIDATION_ERROR`, sem código `WEBHOOK_` próprio no `error.code`, o que o cliente precisa ler em `details` (proposta P-21)
- O código compartilhado não fica intocado: recebe os acréscimos descritos na Decisão e uma mudança de comportamento, os caminhos da secret no `redactPaths` de `src/shared/logger/index.ts`, que passa a valer para todos os logs da aplicação (proposta P-24)

**O que fica em aberto ou a revisitar**
- Nenhum ponto adiado na reunião sobre esta decisão; as propostas P-21 e P-24 aguardam validação na [seção 18.2 do FDD](../FDD.md#182-propostas-do-autor-a-validar-na-revisão)

## Prós e contras das alternativas

### Reuso máximo dos padrões existentes
- Bom, porque mantém consistência e só acrescenta ao código compartilhado, sem mudar o comportamento dele, com a exceção da redaction da secret (proposta P-24)
- Ruim, porque limita a feature ao que os padrões atuais suportam

### Estrutura e bibliotecas próprias
- Bom, porque permitiria escolhas otimizadas para entrega assíncrona (observação do autor)
- Ruim, porque cria um módulo diferente dos demais e acrescenta dependências a manter dentro de um prazo de três sprints (observação do autor)

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seções "Integração com o sistema existente" e matriz de erros `WEBHOOK_*`
- ADRs: [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md) (entry-point do worker), [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md) (reuso de `requireRole`)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Módulo `src/modules/webhooks` no padrão | TRANSCRICAO | [09:27] Bruno |
| Local do worker | TRANSCRICAO | [09:28] Bruno |
| Erros com `AppError` e códigos de exemplo | TRANSCRICAO | [09:28] Bruno |
| Prefixo `WEBHOOK_` | TRANSCRICAO | [09:29] Larissa |
| Pino e error middleware sem mudança | TRANSCRICAO | [09:29] Bruno |
| Decisão | TRANSCRICAO | [09:30] Larissa |
| Validação do `https` no schema Zod | TRANSCRICAO | [09:23] Sofia |
| Secret gerada pela plataforma | TRANSCRICAO | [09:31] Marcos |
| Reuso de `requireRole` no replay | TRANSCRICAO | [09:36] Larissa |
| Middleware de autorização existente | CODIGO | src/middlewares/auth.middleware.ts (`requireRole`) |
| Hierarquia de erros | CODIGO | src/shared/errors/app-error.ts, src/shared/errors/http-errors.ts, src/shared/errors/index.ts |
| Error middleware central | CODIGO | src/middlewares/error.middleware.ts |
| Conversão de falha de schema em `VALIDATION_ERROR` | CODIGO | src/middlewares/validate.middleware.ts |
| Log de requisição, fora do caminho do worker | CODIGO | src/middlewares/request-logger.middleware.ts |
| Logger Pino e `redactPaths` | CODIGO | src/shared/logger/index.ts |
| Validação de variáveis de ambiente | CODIGO | src/config/env.ts |
| Composição dos módulos e prefixo `/api/v1` | CODIGO | src/app.ts |
| Montagem dos routers dos módulos | CODIGO | src/routes/index.ts |
| Entry-point da API | CODIGO | src/server.ts |
