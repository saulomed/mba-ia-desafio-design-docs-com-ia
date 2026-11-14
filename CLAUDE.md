# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Natureza do repositório

Este é o desafio "Da Reunião ao Documento: Design Docs Gerados por IA" (MBA Full Cycle). O entregável é **puramente documental**: a partir de `TRANSCRICAO.md` (reunião técnica sobre um Sistema de Webhooks de Notificação de Pedidos) e do código existente de um Order Management System (OMS), produzir o pacote de design docs em `docs/`.

**Restrição absoluta:** não alterar `src/`, `prisma/`, `tests/`, `TRANSCRICAO.md` nem arquivos de configuração. O código serve apenas de contexto e referência para os documentos. O enunciado completo, com requisitos e critérios de aceite, está em `README.md` (que deve ser substituído pela documentação do processo ao final).

### Entregáveis e regras de conteúdo

| Arquivo | Papel | Altura |
| --- | --- | --- |
| `docs/PRD.md` | Problema, público, escopo, métricas | Produto / "por que e o quê" |
| `docs/RFC.md` | Proposta técnica, alternativas descartadas, questões em aberto, links para ADRs | Arquitetura, 2 a 4 páginas |
| `docs/adrs/ADR-NNN-titulo-em-kebab-case.md` | 5 a 8 decisões isoladas, formato MADR (Status, Contexto, Decisão, Alternativas Consideradas, Consequências) | Decisão pontual |
| `docs/FDD.md` | Fluxos, contratos HTTP, matriz de erros `WEBHOOK_*`, resiliência, observabilidade, seção obrigatória "Integração com o sistema existente" (>= 4 caminhos reais de arquivo) | Implementação |
| `docs/TRACKER.md` | Tabela `ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização` | Transversal |
| `README.md` | Processo de produção: ferramentas, workflow, prompts, iterações, navegação | Meta |

- **Toda informação deve ser rastreável** à transcrição (`[hh:mm] Nome`) ou a um caminho de arquivo real. Nada de requisitos, decisões ou restrições inventados. Se não dá para preencher "Localização" no tracker, o item provavelmente foi alucinado e deve sair.
- **Não duplicar conteúdo entre documentos**: cada um opera em altura diferente. O RFC não detalha implementação; o FDD não rediscute alternativas.
- **Respeitar o que a reunião descartou ou adiou**: esses itens vão para "Fora de escopo" (PRD) e "Questões em aberto" (RFC), nunca como requisito.
- Ordem sugerida no enunciado: ADRs → RFC → FDD → PRD → Tracker → README.
- As 6 decisões principais da reunião que os ADRs devem cobrir (mínimo 5): Outbox no MySQL; retry com backoff e DLQ; HMAC-SHA256 com secret por endpoint; at-least-once com `X-Event-Id`; worker em processo separado em polling; reuso dos padrões existentes do projeto.
- Documentos em Markdown, em português. Seguir a regra global de parágrafos em linha única (sem soft wraps).

## Comandos (aplicação de referência)

Úteis apenas para verificar o comportamento do código ao escrever documentação. Requer Node >= 20 e MySQL (via Docker).

```bash
docker compose up -d          # MySQL 8 em localhost:3306
cp .env.example .env
npm install
npm run db:migrate            # prisma migrate dev
npm run db:seed               # tsx prisma/seed.ts
npm run dev                   # tsx watch src/server.ts (porta 3000)
npm test                      # vitest run (todos os testes)
npx vitest run tests/orders.test.ts            # um arquivo
npx vitest run -t "creates an order"           # um teste pelo nome
npm run lint                  # eslint
npm run format                # prettier
```

Os testes são de integração contra o MySQL real (`tests/setup.ts` limpa todas as tabelas antes de cada teste; `vitest.config.ts` força `singleFork` e desativa paralelismo de arquivos). Precisam do banco de pé e de `.env` configurado.

## Arquitetura do OMS (contexto para a documentação)

Stack: TypeScript ESM (`"type": "module"`, imports com sufixo `.js`), Express 4, Prisma 5 com MySQL, Zod, JWT, pino.

**Composição de dependências** fica toda em `src/app.ts` (`buildControllers` instancia Repository → Service → Controller por módulo, com injeção via construtor, sem container de DI) e `src/routes/index.ts` monta os routers sob `/api/v1`. `src/server.ts` só faz bootstrap e graceful shutdown. Um novo módulo (ex.: webhooks) seguiria exatamente esse encaixe.

**Convenção por módulo** em `src/modules/<nome>/`: `*.routes.ts` (aplica `authenticate` e `validate`), `*.controller.ts` (handlers que delegam ao service e passam erros para `next`), `*.service.ts` (regra de negócio, transações via `prisma.$transaction`), `*.repository.ts` (acesso Prisma), `*.schemas.ts` (Zod, tipos inferidos).

**Padrões transversais em `src/shared/` e `src/middlewares/`:**
- Erros: hierarquia `AppError(message, statusCode, errorCode, details)` em `shared/errors/`, com subclasses por status HTTP e erros de domínio (`InvalidStatusTransitionError`, `InsufficientStockError`). `error.middleware.ts` serializa como `{ error: { code, message, details? } }` e trata `ZodError` e erros Prisma P2002/P2025. Novos códigos de erro seguem esse formato.
- Autenticação: `auth.middleware.ts` valida Bearer JWT e popula `req.user` (`id`, `email`, `role` ADMIN|OPERATOR); `requireRole` existe e hoje só é usado em `src/modules/users/user.routes.ts` (`requireRole('ADMIN')`).
- Validação: `validate({ body, query, params })` com schemas Zod.
- Logging: pino com redaction de segredos (`shared/logger/`), `request-logger.middleware.ts` gera/propaga `X-Request-Id`.
- Paginação: `shared/http/response.ts` (`{ data, pagination }`).
- Configuração: `src/config/env.ts` valida `process.env` com Zod e encerra o processo se inválido. Novas variáveis entram nesse schema.

**Ponto de integração central para a feature de webhooks:** `OrderService.changeStatus` em `src/modules/orders/order.service.ts`. Dentro de uma única transação, ele valida a transição via `order.status.ts` (máquina de estados PENDING → PAID → PROCESSING → SHIPPED → DELIVERED, com CANCELLED a partir de PENDING/PAID/PROCESSING), debita estoque em PENDING→PAID, repõe estoque em cancelamentos a partir de PAID/PROCESSING, atualiza `orders` e insere em `order_status_history`. É nessa transação que a reunião propõe inserir o evento na outbox.

**Modelo de dados** em `prisma/schema.prisma`: `User`, `Customer`, `Product`, `Order`, `OrderItem`, `OrderStatusHistory`, `OrderNumberSequence` (gera `ORD-000001`). Migração inicial em `prisma/migrations/20260519182739_init/`. Não existe nenhuma tabela ou módulo de webhooks no código: tudo relativo à feature vem apenas da transcrição.
