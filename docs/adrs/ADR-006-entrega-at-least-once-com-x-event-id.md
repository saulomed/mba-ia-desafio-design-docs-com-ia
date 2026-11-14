# ADR-006: Garantia de entrega at-least-once, com deduplicação pelo cliente via X-Event-Id

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma) · **Consultados:** Sofia (Engenheira de Segurança), Bruno (Engenheiro Pleno, Pedidos), Marcos (Product Manager)

## Contexto e problema

O envio de webhooks passa por outbox, worker, retentativas e replay manual ([ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md) a [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md)). Nesse caminho, a plataforma pode não ter certeza se o cliente processou uma entrega, por exemplo quando a chamada estoura o timeout depois de o cliente já ter recebido o corpo (exemplo do autor; a reunião afirmou apenas que o cliente pode receber o mesmo evento duas vezes).

É preciso definir que garantia de entrega a plataforma oferece e quem é responsável por tratar duplicidades, sabendo que a coordenação entre os dois lados aumenta a complexidade.

## Fatores de decisão

- Não perder eventos: a mudança de status não pode ficar sem evento ([09:40] Bruno), e a entrega não deve descartá-lo sem deixar rastro (observação do autor)
- Complexidade de implementação e de coordenação com os clientes
- Aderência ao que os clientes já conhecem de outros provedores

## Alternativas consideradas

1. **At-least-once, com `X-Event-Id` (UUID gerado na entrada na outbox) para deduplicação no cliente**
2. **Exactly-once**

## Decisão

Adotaremos garantia at-least-once: cada evento recebe um UUID ao entrar na outbox, enviado no header `X-Event-Id`, e o cliente deduplica pelo identificador do lado dele, porque isso evita perda de eventos sem exigir coordenação entre as partes.

Exactly-once foi descartado porque exigiria coordenação dos dois lados e ficaria muito mais complexo. Sofia apontou que a escolha transfere responsabilidade para o cliente; a objeção foi resolvida com o argumento de que é o padrão de mercado (Stripe e GitHub fazem assim) e com o compromisso do produto de documentar a regra com destaque no portal de desenvolvedores.

## Consequências

**Positivas**
- Retentativas e replay podem reenviar sem risco de perda
- Contrato conhecido pelos clientes, igual ao de provedores de mercado
- Implementação sem protocolo de confirmação entre as partes

**Negativas e trade-offs aceitos**
- O cliente pode receber o mesmo evento mais de uma vez e precisa implementar deduplicação por `X-Event-Id`
- A correção do processamento do lado do cliente passa a depender dessa deduplicação, fora do controle da plataforma
- Exige documentação destacada no portal do desenvolvedor

**O que fica em aberto ou a revisitar**
- Identidade do evento em dois casos que a reunião não tratou. Quando mais de um webhook do cliente quer o mesmo status, o FDD grava uma linha da outbox por webhook de destino, cada uma com seu `event_id` (proposta P-05). No replay da DLQ, o evento "recoloca na outbox" ([09:18] Diego), e uma leitura literal de "UUID gerado quando o evento entra na outbox" ([09:25] Diego) daria a ele um id novo; o FDD mantém o mesmo `event_id`, para que o cliente reconheça a duplicata ([FDD, seção 6.5](../FDD.md#65-replay-manual-da-dlq)). Os dois pontos ficam a validar na revisão do design; a P-05 está com Diego

## Prós e contras das alternativas

### At-least-once com `X-Event-Id`
- Bom, porque não perde eventos e mantém a implementação sem coordenação
- Ruim, porque transfere a deduplicação para o cliente

### Exactly-once
- Bom, porque o cliente não precisaria tratar duplicidade
- Ruim, porque exige coordenação entre plataforma e cliente, muito mais complexa

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- PRD: [docs/PRD.md](../PRD.md)
- FDD: [docs/FDD.md](../FDD.md), seção de contratos HTTP (headers do envio)
- ADRs: [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md) e [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md) (fontes de reenvio), [ADR-005](ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Garantia at-least-once | TRANSCRICAO | [09:24] Diego |
| `X-Event-Id` com UUID gerado na outbox | TRANSCRICAO | [09:25] Diego |
| Objeção de responsabilidade do cliente | TRANSCRICAO | [09:25] Sofia |
| Descarte de exactly-once e padrão de mercado | TRANSCRICAO | [09:25] Diego |
| Documentação no portal | TRANSCRICAO | [09:26] Marcos |
| Decisão | TRANSCRICAO | [09:26] Larissa |
| Mudança de status não pode ficar sem evento | TRANSCRICAO | [09:40] Bruno |
| Replay recoloca o evento na outbox | TRANSCRICAO | [09:18] Diego |
| Filtro de eventos por webhook | TRANSCRICAO | [09:33] Marcos |
