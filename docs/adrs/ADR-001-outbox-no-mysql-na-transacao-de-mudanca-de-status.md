# ADR-001: Outbox no MySQL existente, gravada na mesma transação da mudança de status do pedido

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos) · **Consultados:** Sofia (Segurança), Marcos (Product Manager)

## Contexto e problema

Três clientes B2B (Atlas Comercial, MaxDistribuição e Nova Cargo) pediram para ser notificados quando o status dos seus pedidos muda; hoje fazem polling em `GET /orders`, o que deixa a integração lenta e cara. Para eles, "tempo real" é qualquer coisa abaixo de 10 segundos. O fluxo é apenas de saída (outbound webhook).

A mudança de status acontece em `OrderService.changeStatus` (`src/modules/orders/order.service.ts`), dentro de um único `prisma.$transaction` que valida a transição (`src/modules/orders/order.status.ts`), debita ou repõe estoque, atualiza `orders` e insere em `order_status_history`. É uma transação já considerada pesada pelo time de Pedidos.

O problema é como acoplar a geração da notificação a essa mudança de forma que nenhuma mudança de status que um webhook ativo do cliente escolheu receber fique sem evento e nenhum evento seja emitido para uma mudança que sofreu rollback, sem deixar a transação dependente da disponibilidade dos endpoints dos clientes. Mudanças de status que nenhum webhook do cliente quer receber não geram evento, porque o filtro é aplicado na inserção. O time é pequeno e a infraestrutura em operação é o MySQL usado pela aplicação (`prisma/schema.prisma`).

## Fatores de decisão

- Consistência entre a mudança de status e o registro do evento (nenhuma mudança que um webhook ativo escolheu receber fica sem evento, e nenhum evento sobra de mudança revertida)
- Isolamento da transação de pedidos em relação à latência e à disponibilidade dos clientes
- Infraestrutura que um time pequeno consegue operar
- Latência de entrega abaixo de 10 segundos

## Alternativas consideradas

1. **Tabela de outbox no MySQL existente, gravada na mesma transação de `changeStatus` e lida por um worker**
2. **Chamada HTTP síncrona dentro de `changeStatus`**
3. **Redis Streams (ou Redis Cluster) como fila de eventos**

## Decisão

Adotaremos o padrão outbox no MySQL existente: a inserção do evento em uma tabela `webhook_outbox` ocorre dentro da mesma transação que atualiza `orders` e `order_status_history`, e uma falha nessa inserção provoca rollback da mudança de status, porque isso garante consistência sem acoplar a transação aos clientes e sem infraestrutura nova.

A chamada síncrona foi descartada por violar o isolamento: um cliente lento travaria a mudança de status de outros pedidos, e não há resposta aceitável para cliente fora do ar, já que dar rollback na mudança de status não é opção. Redis Streams foi descartado pelo fator de infraestrutura: exigiria subir e operar mais um componente, considerado overengineering para um time pequeno. A outbox no MySQL atende a consistência por construção, pois o evento commita ou some junto com a transação principal.

## Consequências

**Positivas**
- Nenhuma mudança de status que um webhook ativo escolheu receber fica sem evento registrado, e nenhum evento sobra de mudança revertida
- A transação de pedidos não faz I/O de rede; a disponibilidade dos clientes não afeta a operação de pedidos
- Nenhuma peça nova de infraestrutura para operar

**Negativas e trade-offs aceitos**
- A transação de `changeStatus`, já pesada, ganha mais uma escrita e passa a falhar também quando a inserção na outbox falha
- A entrega é assíncrona e depende de um processo leitor (ver [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md))
- A tabela cresce com o volume; depende de índices em status e `created_at`, e o arquivamento das linhas entregues (cerca de 30 dias) ficou fora do escopo da feature
- `OrderService` passa a depender do módulo de webhooks para enfileirar o evento dentro da transação; a forma dessa chamada e o filtro de status por webhook estão no FDD

**O que fica em aberto ou a revisitar**
- Política de arquivamento das linhas entregues, explicitamente fora do escopo desta feature

## Prós e contras das alternativas

### Outbox no MySQL existente
- Bom, porque o evento é atômico com a mudança de status
- Bom, porque reutiliza o banco e o Prisma já em operação
- Ruim, porque adiciona escrita na transação e exige um worker e limpeza periódica da tabela

### Chamada HTTP síncrona em `changeStatus`
- Bom, porque a notificação sairia no mesmo instante da mudança (observação do autor)
- Ruim, porque um cliente lento bloqueia mudanças de status de outros pedidos, violando o isolamento
- Ruim, porque cliente fora do ar não tem tratamento aceitável sem desfazer a mudança de status

### Redis Streams
- Bom, porque é um mecanismo de fila dedicado e mais reativo que a leitura periódica de uma tabela (observação do autor)
- Ruim, porque exige subir e operar infraestrutura nova, o que o time pequeno considerou overengineering
- Ruim, porque, por estar fora do MySQL, não participa da transação de `changeStatus` (observação do autor)

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seção "Integração com o sistema existente"
- ADRs: [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md) (consome a outbox), [ADR-008](ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md) (conteúdo da linha da outbox)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Pedido dos clientes e limite de 10 s | TRANSCRICAO | [09:00] Marcos, [09:02] Marcos |
| Fluxo apenas de saída (outbound) | TRANSCRICAO | [09:02] Marcos, [09:03] Sofia |
| Descarte do síncrono | TRANSCRICAO | [09:04] Bruno, [09:06] Diego |
| Proposta da outbox e atomicidade | TRANSCRICAO | [09:06] Diego |
| Descarte de Redis Streams | TRANSCRICAO | [09:07] Larissa, [09:07] Diego |
| Índices e arquivamento fora de escopo | TRANSCRICAO | [09:08] Diego |
| Decisão | TRANSCRICAO | [09:08] Larissa |
| Filtro de status na inserção da outbox | TRANSCRICAO | [09:33] Marcos, [09:34] Bruno |
| Inserção na mesma transação com rollback | TRANSCRICAO | [09:40] Bruno, [09:41] Diego |
| Dependência de `OrderService` com o módulo de webhooks | TRANSCRICAO | [09:41] Bruno, [09:41] Diego |
| Transação de mudança de status | CODIGO | src/modules/orders/order.service.ts (`changeStatus`) |
| Máquina de estados | CODIGO | src/modules/orders/order.status.ts |
| MySQL como banco da aplicação | CODIGO | prisma/schema.prisma |
