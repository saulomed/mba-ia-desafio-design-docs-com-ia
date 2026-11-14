# ADR-008: Payload do evento renderizado como snapshot no momento da inserção na outbox

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos)

## Contexto e problema

Cada linha da outbox ([ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md)) pode ser entregue segundos ou horas depois de criada, por causa do polling e das retentativas ([ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md), [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md)): até quase 15 horas na leitura de 6 envios adotada pelo FDD (proposta P-01). Nesse intervalo o pedido pode mudar de novo.

O payload contém dados do pedido no momento da mudança de status: `order_id`, `order_number`, `from_status`, `to_status`, `customer_id` e "os campos básicos da order tipo total_cents" ([09:43] Diego). Desses campos básicos, o FDD fixa só `total_cents`, o único nomeado (proposta P-08). É preciso decidir se a outbox guarda o payload pronto ou só a referência ao pedido, com o corpo montado na hora do envio.

## Fatores de decisão

- O evento deve refletir o estado do pedido no instante da mudança de status
- Previsibilidade do que o cliente recebe em retentativas e replays (observação do autor)

## Alternativas consideradas

1. **Payload renderizado e gravado na inserção (snapshot)**
2. **Guardar apenas `order_id` e renderizar na hora do envio**

## Decisão

Adotaremos o snapshot: o payload é renderizado e gravado na outbox no momento da inserção, porque assim o evento reflete o estado do pedido de quando o status mudou, mesmo que o pedido seja alterado depois.

Renderizar no envio foi descartado porque, se o pedido mudasse entre a inserção e a entrega, o evento poderia carregar dados que não correspondem à transição notificada.

## Consequências

**Positivas**
- O evento é fiel à transição que o originou
- Retentativas e replays enviam exatamente o mesmo corpo, que inclui o `event_id`, o que mantém a deduplicação coerente; a assinatura, porém, muda se a secret for rotacionada entre um envio e outro ([FDD, seção 6.6](../FDD.md#66-rotação-de-secret), proposta P-02) (observação do autor)

**Negativas e trade-offs aceitos**
- A outbox armazena o corpo completo de cada evento, ocupando mais espaço que uma referência
- A montagem do payload acontece dentro da transação de `changeStatus`, que já é pesada (observação do autor)
- Um evento entregue tarde mostra o estado do pedido no momento da mudança, que pode já estar desatualizado; o estado atual exige consultar `GET /orders/:id` (observação do autor). A reunião citou essa consulta só para os detalhes que ficam fora do payload, como os itens ([09:43] Diego)

**O que fica em aberto ou a revisitar**
- Nenhum ponto adiado na reunião sobre esta decisão

## Prós e contras das alternativas

### Snapshot na inserção
- Bom, porque o evento corresponde exatamente à transição notificada
- Ruim, porque aumenta o tamanho da linha da outbox e o trabalho dentro da transação

### Renderizar no envio
- Bom, porque a outbox guardaria só a referência
- Ruim, porque o conteúdo poderia divergir do estado de quando o status mudou

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seção de formato do payload
- ADRs: [ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md) (depende de)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Pergunta: payload pronto ou só `order_id` | TRANSCRICAO | [09:51] Bruno |
| Preferência pelo snapshot e motivo | TRANSCRICAO | [09:52] Larissa |
| Concordância | TRANSCRICAO | [09:52] Diego |
| Decisão | TRANSCRICAO | [09:52] Bruno |
| Campos do payload, sem itens, e detalhes por `GET /orders/:id` | TRANSCRICAO | [09:43] Diego |
| Transação de `changeStatus` já pesada | TRANSCRICAO | [09:04] Bruno |
| Rotação de secret com 24 h de convivência | TRANSCRICAO | [09:21] Sofia |
