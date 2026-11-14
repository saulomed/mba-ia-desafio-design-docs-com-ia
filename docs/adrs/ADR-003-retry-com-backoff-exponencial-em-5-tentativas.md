# ADR-003: Retry com backoff exponencial em 5 tentativas (1m, 5m, 30m, 2h, 12h)

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos) · **Consultados:** Marcos (Product Manager)

## Contexto e problema

O worker do [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md) entrega eventos a endpoints de clientes que podem estar fora do ar, lentos ou em manutenção. A equipe já teve cliente com indisponibilidade de duas horas em manutenção planejada.

É preciso definir quantas vezes e com que espaçamento reenviar um evento que falhou, e quando desistir. Tentativas demais deixam eventos pendurados indefinidamente se o cliente desapareceu; tentativas de menos descartam eventos durante indisponibilidades comuns.

## Fatores de decisão

- Cobrir indisponibilidades reais dos clientes, incluindo manutenções de horas
- Evitar eventos pendurados para sempre quando o cliente some
- Ter um ponto claro em que a falha passa a ser permanente

## Alternativas consideradas

1. **5 tentativas com backoff exponencial de 1m, 5m, 30m, 2h e 12h, depois falha permanente**
2. **Retry indefinido com backoff**
3. **3 tentativas**

## Decisão

Adotaremos 5 tentativas com backoff exponencial nos intervalos de 1 minuto, 5 minutos, 30 minutos, 2 horas e 12 horas, e após a última o evento é considerado falha permanente e vai para a DLQ, porque essa janela cobre manutenções planejadas sem deixar eventos pendentes indefinidamente. A janela é de "quase 15 horas entre primeira falha e última tentativa" ([09:17] Diego) se as 5 tentativas forem retentativas depois do envio inicial, leitura adotada pelo FDD (proposta P-01; ver "O que fica em aberto").

O retry indefinido foi descartado porque deixa o evento pendurado para sempre se o cliente sumiu. Três tentativas foram descartadas por serem pouco: retentando três vezes em cerca de 30 minutos, uma indisponibilidade de manhã ou a manutenção de duas horas já vista em cliente mataria o evento. O Product Manager considerou aceitável que um cliente fora do ar por mais de 15 horas perca a entrega automática.

## Consequências

**Positivas**
- Indisponibilidades de até quase 15 horas não causam perda da entrega automática, se valer a leitura de 6 envios (14 h 36 min, proposta P-01); com 5 envios no total, a janela seria de 2 h 36 min
- Todo evento tem destino final definido: entregue ou movido para a DLQ

**Negativas e trade-offs aceitos**
- Um evento pode chegar com até quase 15 horas de atraso em relação à mudança de status, na leitura de 6 envios (proposta P-01)
- Clientes fora do ar por mais tempo que a janela dependem de replay manual pela DLQ (ver [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md))
- Retentativas reenviam o mesmo evento, o que reforça a necessidade de deduplicação pelo cliente (ver [ADR-006](ADR-006-entrega-at-least-once-com-x-event-id.md))

**O que fica em aberto ou a revisitar**
- Aviso ao cliente sobre falhas sucessivas (por e-mail) ficou para uma próxima fase
- Contagem das tentativas: "5 tentativas" ([09:17] Larissa) e "total 5 tentativas" no resumo final ([09:48] Larissa) admitem duas leituras: 5 envios no total ou 1 envio inicial seguido de 5 retentativas (6 envios). Só a segunda usa os cinco intervalos e soma as "quase 15 horas entre primeira falha e última tentativa" citadas por Diego ([09:17] Diego); com 5 envios no total, o intervalo de 12 h não seria usado e a janela seria de 2 h 36 min. A reunião não fechou qual vale. O [FDD](../FDD.md#9-estratégias-de-resiliência) propõe 6 envios (proposta P-01), a validar por Larissa e Diego

## Prós e contras das alternativas

### 5 tentativas, 1m/5m/30m/2h/12h
- Bom, porque cobre manutenções de horas e ainda encerra o ciclo
- Ruim, porque o atraso máximo de entrega chega a quase 15 horas, na leitura de 6 envios (proposta P-01)

### Retry indefinido
- Bom, porque nunca desiste de um cliente que volta tarde
- Ruim, porque evento fica pendurado para sempre se o cliente sumiu

### 3 tentativas
- Bom, porque declara a falha mais cedo
- Ruim, porque a janela de cerca de 30 minutos não cobre indisponibilidades já observadas em clientes

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seção de resiliência
- PRD: [docs/PRD.md](../PRD.md), fora de escopo (notificação por e-mail)
- ADRs: [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md) (aplicado pelo worker), [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md) (destino após a última tentativa), [ADR-006](ADR-006-entrega-at-least-once-com-x-event-id.md)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Backoff exponencial com teto e DLQ | TRANSCRICAO | [09:15] Diego |
| Proposta de 5 e descarte do indefinido | TRANSCRICAO | [09:15] Diego |
| Alternativa de 3 tentativas e descarte | TRANSCRICAO | [09:16] Bruno, [09:16] Diego |
| Progressão 1m/5m/30m/2h/12h e "quase 15 horas" | TRANSCRICAO | [09:17] Diego |
| Aceite de 15 h pelo produto | TRANSCRICAO | [09:17] Marcos |
| Decisão | TRANSCRICAO | [09:17] Larissa |
| "Total 5 tentativas" no resumo final | TRANSCRICAO | [09:48] Larissa |
| E-mail ao cliente adiado | TRANSCRICAO | [09:37] Marcos, [09:37] Larissa |
