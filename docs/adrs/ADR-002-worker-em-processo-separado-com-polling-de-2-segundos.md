# ADR-002: Worker em processo separado da API, lendo a outbox por polling a cada 2 segundos

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos) · **Consultados:** Marcos (Product Manager)

## Contexto e problema

Com a outbox definida no [ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md), algum processo precisa descobrir os eventos pendentes e disparar as chamadas HTTP. Os clientes consideram aceitável uma entrega abaixo de 10 segundos.

O banco é MySQL, que não oferece um mecanismo nativo de notificação a processos externos equivalente ao `NOTIFY/LISTEN` do Postgres; triggers existem, mas só executam SQL. Hoje a aplicação tem um único entry-point, `src/server.ts`, que sobe a API Express, e o client Prisma é criado em `src/config/database.ts`.

Há duas perguntas acopladas: como o leitor fica sabendo que há eventos novos, e onde ele roda em relação à API, considerando que reinícios da API não podem interromper as entregas.

## Fatores de decisão

- Latência de entrega abaixo de 10 segundos
- Entregas não podem parar quando a API reinicia
- Usar o que o MySQL e a stack atual oferecem, sem mecanismos improvisados
- Reaproveitar o mesmo banco e a mesma stack

## Alternativas consideradas

1. **Polling em loop a cada 2 segundos, em processo Node separado com entry-point próprio**
2. **Trigger no banco para tornar o worker reativo**
3. **Worker rodando dentro do mesmo processo da API** (citado como o que não pode acontecer)

## Decisão

Adotaremos um worker em processo separado (`src/worker.ts`, executado por `npm run worker`) que faz polling da outbox a cada 2 segundos, buscando em lotes pequenos os eventos pendentes mais antigos, porque 2 segundos atende com folga o limite de 10 segundos, o MySQL não tem notificação nativa para processos externos e um processo separado não é derrubado pelos reinícios da API.

A trigger foi descartada porque, sem `NOTIFY/LISTEN`, avisar o worker exigiria improvisos como escrever em arquivo ou chamar um endpoint. Rodar dentro da API foi descartado porque um reinício da API derrubaria o worker. O worker usa o mesmo banco e a mesma `DATABASE_URL`, mas cria seu próprio `PrismaClient`, já que o client é por processo. A lógica de processamento fica no módulo (`src/modules/webhooks/webhook.worker.ts` ou `webhook.processor.ts`), e `src/worker.ts` é apenas o entry-point.

## Consequências

**Positivas**
- Em operação normal, um evento novo espera até 2 s até ser lido, o que deixa folga para o limite de 10 segundos pedido pelos clientes
- Entregas continuam durante reinícios e deploys da API
- Implementação com a stack atual, sem dependência de recursos que o MySQL não tem

**Negativas e trade-offs aceitos**
- Até 2 segundos de espera até o evento ser lido, aceito explicitamente pela equipe
- Consultas periódicas ao banco mesmo sem eventos
- Mais um processo para implantar, monitorar e encerrar com segurança
- A ordem de entrega vale apenas por `order_id` e apenas enquanto houver um único worker; não há garantia de ordem global. É limitação conhecida e documentada
- Com um único worker e timeout de 10 segundos por chamada, um cliente lento pode atrasar os eventos de outros clientes na mesma leitura, e o limite de 10 segundos deixa de ser atendido nesses casos (observação do autor; não discutido na reunião)
- Com a retenção proposta no FDD para preservar a ordem (proposta P-06), um evento que está atrás de outro do mesmo pedido e webhook em retentativa espera até o anterior ser entregue ou ir para a DLQ, o que pode levar até o fim da janela de retentativas, cerca de 14 h 36 min na leitura de 6 envios da proposta P-01 (observação do autor)

**O que fica em aberto ou a revisitar**
- Escalar para múltiplos workers exige particionar por `order_id` ou usar lock pessimista, tratado como problema futuro
- Retentativas podem quebrar a ordem mesmo com um único worker: se o evento PAID falha e aguarda o primeiro intervalo de retry, o PROCESSING seguinte do mesmo pedido poderia ser entregue antes (observação do autor; a reunião não tratou desse caso). O [FDD](../FDD.md#62-processamento-pelo-worker) propõe reter o evento seguinte do mesmo pedido e webhook enquanto houver um anterior não entregue (proposta P-06), a validar por Diego

## Prós e contras das alternativas

### Polling de 2 s em processo separado
- Bom, porque atende o limite de 10 s sem recurso que o MySQL não oferece
- Bom, porque sobrevive a reinícios da API
- Ruim, porque consulta o banco continuamente e adiciona um processo a operar

### Trigger no banco
- Bom, porque seria mais reativo que o polling
- Ruim, porque trigger no MySQL só executa SQL e não notifica processo externo; exigiria improviso para avisar o worker

### Worker no processo da API
- Bom, porque evitaria um processo a mais para implantar (observação do autor)
- Ruim, porque um reinício da API derruba o worker, violando o fator de continuidade das entregas

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seções de fluxo do worker e de resiliência
- ADRs: [ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md) (depende de), [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md) (política aplicada pelo worker)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Polling a cada 2 s | TRANSCRICAO | [09:09] Diego |
| Pendentes mais antigos, em lotes pequenos | TRANSCRICAO | [09:08] Diego, [09:09] Diego |
| Descarte da trigger | TRANSCRICAO | [09:09] Bruno, [09:09] Diego |
| Decisão do polling e latência aceita | TRANSCRICAO | [09:10] Marcos, [09:10] Larissa |
| Processo separado da API | TRANSCRICAO | [09:11] Diego |
| `src/worker.ts` e `npm run worker` | TRANSCRICAO | [09:11] Larissa |
| Mesmo banco, PrismaClient próprio | TRANSCRICAO | [09:11] Diego, [09:30] Bruno |
| Local da lógica do worker | TRANSCRICAO | [09:28] Bruno |
| Ordem apenas por `order_id` com single-worker | TRANSCRICAO | [09:12] Diego, [09:13] Diego, [09:13] Larissa |
| Timeout de 10 s por chamada | TRANSCRICAO | [09:42] Diego |
| Janela de retentativas que a retenção pode alcançar | TRANSCRICAO | [09:17] Diego, [09:17] Larissa |
| Entry-point atual da API | CODIGO | src/server.ts |
| Criação do client Prisma | CODIGO | src/config/database.ts |
