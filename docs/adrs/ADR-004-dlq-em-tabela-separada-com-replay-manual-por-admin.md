# ADR-004: DLQ em tabela separada da outbox, com replay manual restrito a ADMIN

## Status

**Aceito** · **Data:** 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") · **Decisores:** Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Sofia (Engenheira de Segurança) · **Consultados:** Bruno (Engenheiro Pleno, Pedidos), Marcos (Product Manager)

## Contexto e problema

Pelo [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md), um evento que esgota as 5 tentativas vira falha permanente. É preciso decidir onde ele fica guardado, como pode ser investigado e como pode ser reenviado depois que o cliente voltar. Esse é o único caso de entrada na DLQ tratado na reunião; o FDD acrescenta outros dois (ver "O que fica em aberto").

A outbox é lida continuamente pelo worker em busca de pendentes ([ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md)), então o que se guarda nela afeta essa leitura. O reenvio mexe na fila de entrega de notificações, o que levanta a pergunta de quem pode executá-lo. O projeto já tem perfis `ADMIN` e `OPERATOR` no JWT e o middleware `requireRole` em `src/middlewares/auth.middleware.ts`.

## Fatores de decisão

- Leitura da outbox principal limpa para o worker
- Evidência preservada para debug e reprocessamento (payload, motivo da falha, momento)
- Controle de quem pode reenviar eventos e rastro para auditoria

## Alternativas consideradas

1. **Tabela `webhook_dead_letter` separada, com replay manual por endpoint admin exigindo role ADMIN**
2. **Marcar o evento como `failed` na própria outbox**

## Decisão

Adotaremos uma tabela `webhook_dead_letter` separada, que guarda o payload, o motivo da falha e o timestamp, com reprocessamento manual por um endpoint administrativo (`POST /admin/webhooks/dead-letter/:id/replay`) que recoloca o evento na outbox como pendente, porque isso mantém a outbox enxuta para o worker e preserva a evidência da falha.

O endpoint de replay exige role `ADMIN` e registra em log quem executou o replay, para auditoria, porque mexer na fila de entrega não é atividade de operador. O mecanismo de autorização é o já existente no projeto (ver [ADR-007](ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md)). A alternativa de marcar `failed` na outbox foi levantada como pergunta e não prevaleceu porque a tabela separada deixa mais limpa a leitura da outbox principal e serve de evidência para debug e reprocessamento.

## Consequências

**Positivas**
- A leitura da outbox principal fica mais limpa, sem acumular as falhas permanentes
- Cada falha permanente fica registrada com payload e motivo, disponível para investigação
- Reenvio controlado e auditável, restrito a ADMIN

**Negativas e trade-offs aceitos**
- Mais uma tabela e um fluxo de movimentação entre outbox e DLQ para manter consistentes
- O reprocessamento é manual; não há reenvio automático depois que o cliente volta
- O replay gera uma nova entrega do mesmo evento, o que o cliente precisa tolerar (ver [ADR-006](ADR-006-entrega-at-least-once-com-x-event-id.md))

**O que fica em aberto ou a revisitar**
- O CRUD de configuração de webhooks aceita qualquer role autenticada por enquanto; endurecer permissões ficou para depois
- O destino da linha da outbox quando o evento vai para a DLQ não foi fechado: a outbox tem um status "falhou" ([09:08] Diego) e o replay "recoloca na outbox como pendente" ([09:18] Diego), mas não ficou definido se a linha é movida, copiada ou mantida com status "falhou". O [FDD](../FDD.md#64-falha-definitiva-e-movimentação-para-a-dlq) propõe movê-la (proposta P-03), a validar por Diego
- Outras entradas na DLQ: a reunião tratou da DLQ só como destino do evento que esgota as tentativas ([09:15] Diego). O [FDD](../FDD.md#64-falha-definitiva-e-movimentação-para-a-dlq) também manda para lá, sem envio, os eventos pendentes de webhook inativo (proposta P-14, com o replay para webhook inativo respondendo 409) e os eventos com payload acima de 64KB, limite que a reunião fixou como erro ([09:24] Larissa) e que o FDD verifica no worker (proposta P-04). As duas propostas aguardam validação de Sofia, e a P-04 também de Larissa

## Prós e contras das alternativas

### Tabela `webhook_dead_letter` separada com replay ADMIN
- Bom, porque deixa mais limpa a leitura da outbox e guarda evidência para debug
- Ruim, porque adiciona tabela, movimentação e endpoint a manter

### Status `failed` na própria outbox
- Bom, porque evita uma tabela a mais e a cópia do evento (observação do autor)
- Ruim, porque as falhas permanentes se acumulam na tabela lida pelo worker, contra o fator de leitura limpa

## Relacionados

- RFC: [docs/RFC.md](../RFC.md)
- FDD: [docs/FDD.md](../FDD.md), seções de contratos HTTP e resiliência
- ADRs: [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md) (depende de), [ADR-007](ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md) (mecanismo de autorização reaproveitado)

## Origem

| Item | Fonte | Localização |
| --- | --- | --- |
| DLQ após o teto de tentativas | TRANSCRICAO | [09:15] Diego |
| Pergunta: tabela separada ou `failed` na outbox | TRANSCRICAO | [09:17] Larissa |
| Tabela `webhook_dead_letter` e justificativa | TRANSCRICAO | [09:18] Diego |
| Replay manual por endpoint admin | TRANSCRICAO | [09:18] Bruno, [09:18] Diego, [09:19] Larissa, [09:35] Diego |
| Role ADMIN e log de auditoria | TRANSCRICAO | [09:36] Sofia |
| Decisão de role ADMIN no replay | TRANSCRICAO | [09:36] Larissa |
| CRUD com qualquer role por enquanto | TRANSCRICAO | [09:37] Sofia |
| Status "falhou" na outbox | TRANSCRICAO | [09:08] Diego |
| Webhook com estado ativo | TRANSCRICAO | [09:21] Bruno |
| Limite de 64KB com erro | TRANSCRICAO | [09:24] Diego, [09:24] Larissa |
| Perfis `ADMIN` e `OPERATOR` e `requireRole` | CODIGO | src/middlewares/auth.middleware.ts |
