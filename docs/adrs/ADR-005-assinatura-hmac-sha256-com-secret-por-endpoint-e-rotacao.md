# ADR-005: Assinatura HMAC-SHA256 do corpo com secret única por endpoint e rotação com 24 horas de convivência

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Sofia (Engenheira de Segurança), Larissa (Tech Lead) · **Consultados:** Bruno (Engenheiro Pleno, Pedidos), Diego (Engenheiro Sênior, Plataforma)

## Contexto e problema

Os webhooks enviam dados de pedidos para endpoints fora da infraestrutura da empresa. O cliente precisa conseguir verificar que a requisição veio realmente da plataforma e que o payload não foi adulterado no caminho.

A forma de autenticar a origem define o impacto de um vazamento de credencial. A equipe já teve um cliente que vazou uma secret em log de aplicação. Clientes também precisam conseguir trocar credenciais sem parar a integração.

## Fatores de decisão

- Integridade e autenticidade verificáveis pelo cliente
- Raio de impacto limitado em caso de vazamento de credencial
- Troca de credencial sem interromper a integração do cliente
- Facilidade de implementação do lado do cliente

## Alternativas consideradas

1. **HMAC-SHA256 sobre o corpo, com secret única por endpoint e rotação com grace period de 24 h**
2. **Secret global da plataforma, compartilhada por todos os clientes**

## Decisão

Adotaremos assinatura HMAC-SHA256 calculada sobre o corpo da requisição, enviada no header `X-Signature`, com uma secret única por endpoint de webhook, gerada pela plataforma, e suporte a rotação em que a secret antiga continua válida por 24 horas em paralelo com a nova, porque isso dá integridade verificável, limita o impacto de um vazamento a um único endpoint e permite migrar sem interrupção.

A secret global foi descartada porque o vazamento de uma comprometeria todos os clientes. SHA-256 foi escolhido por ser o padrão de mercado, com biblioteca disponível para qualquer cliente. A secret é gerada pela plataforma e devolvida na criação do webhook, e o cliente pode pedir uma nova pela API.

## Consequências

**Positivas**
- Cliente valida origem e integridade com bibliotecas padrão
- Vazamento de uma secret afeta apenas um endpoint
- Rotação sem janela de indisponibilidade para o cliente

**Negativas e trade-offs aceitos**
- A plataforma armazena e gerencia uma secret por endpoint, incluindo o estado de rotação (atual e anterior com prazo)
- Durante as 24 horas de convivência, duas secrets são válidas para o mesmo endpoint
- A verificação depende de o cliente implementá-la; a plataforma não tem como garantir isso
- O código de geração de secret e de HMAC exige revisão de segurança dedicada (pelo menos dois dias úteis antes do deploy)

**O que fica em aberto ou a revisitar**
- Proteção contra replay attack: Diego propôs o header `X-Timestamp`, com o instante do envio, para o cliente detectar replay "se quiser" ([09:44] Diego). Como o HMAC é calculado só sobre o corpo ([09:22] Sofia), o `X-Timestamp` não é autenticado: quem captura uma requisição pode trocar o header sem invalidar a assinatura, e por isso ele não protege (observação do autor). O [FDD](../FDD.md#103-assinatura) orienta o cliente a deduplicar pelo `event_id` em vez de usar o header como janela antirreplay (proposta P-22). Incluir o timestamp na assinatura fica para a revisão de segurança da Sofia ([QA-06 do PRD](../PRD.md#92-questões-em-aberto))
- Comportamento durante a convivência de 24 horas: a reunião definiu que "a antiga fica válida", mas, como quem assina é a plataforma, não ficou definido se o envio leva assinatura com as duas secrets ou só com a nova. O [FDD](../FDD.md#103-assinatura) propõe as duas assinaturas no header durante a convivência (proposta P-02), a validar por Sofia

## Prós e contras das alternativas

### HMAC-SHA256 com secret por endpoint e rotação
- Bom, porque isola o impacto de vazamento e permite troca sem interrupção
- Ruim, porque exige gestão de muitas secrets e do estado de rotação

### Secret global
- Bom, porque há uma única credencial a gerenciar (observação do autor)
- Ruim, porque um vazamento compromete todos os clientes, contra o fator de raio de impacto

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seções de segurança e contratos HTTP; os cabeçalhos do envio (`X-Event-Id`, `X-Signature`, `X-Timestamp`, `X-Webhook-Id` e `Content-Type`) estão na [seção 7.8](../FDD.md#78-chamada-de-saída-post-url-do-webhook)
- PRD: [docs/PRD.md](../PRD.md), QA-05 e QA-06
- ADRs: [ADR-006](ADR-006-entrega-at-least-once-com-x-event-id.md) (`X-Event-Id` e deduplicação pelo cliente)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| Necessidade de autenticidade e integridade | TRANSCRICAO | [09:19] Sofia |
| HMAC no header `X-Signature` | TRANSCRICAO | [09:20] Sofia |
| Algoritmo SHA-256 | TRANSCRICAO | [09:20] Bruno, [09:20] Sofia |
| Secret por endpoint, descarte da global | TRANSCRICAO | [09:21] Sofia |
| Rotação com 24 h de convivência | TRANSCRICAO | [09:21] Sofia |
| Configuração com url, secret, customer_id e estado ativo | TRANSCRICAO | [09:21] Bruno |
| Histórico de vazamento em cliente | TRANSCRICAO | [09:22] Diego |
| Decisão, com HMAC sobre o corpo | TRANSCRICAO | [09:22] Sofia |
| Secret gerada pela plataforma | TRANSCRICAO | [09:31] Marcos |
| `X-Timestamp` para replay attack | TRANSCRICAO | [09:44] Diego |
| Revisão de segurança antes do deploy | TRANSCRICAO | [09:46] Sofia |
