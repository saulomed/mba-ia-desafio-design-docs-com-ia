# Architecture Decision Records

Decisões arquiteturais do Sistema de Webhooks de Notificação de Pedidos, uma por arquivo, no formato MADR. Nome: `ADR-NNN-titulo-em-kebab-case.md`. ADRs aceitos não mudam de sentido; mudanças geram um ADR novo que substitui o anterior. Todas as decisões foram extraídas de [TRANSCRICAO.md](../../TRANSCRICAO.md), com a localização `[hh:mm] Nome` na seção Origem de cada ADR.

| ADR | Título | Status | Data |
| --- | --- | --- | --- |
| [ADR-001](ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md) | Outbox no MySQL existente, gravada na mesma transação da mudança de status do pedido | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-002](ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md) | Worker em processo separado da API, lendo a outbox por polling a cada 2 segundos | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-003](ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md) | Retry com backoff exponencial em 5 tentativas (1m, 5m, 30m, 2h, 12h) | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-004](ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md) | DLQ em tabela separada da outbox, com replay manual restrito a ADMIN | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-005](ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md) | Assinatura HMAC-SHA256 do corpo com secret única por endpoint e rotação com 24 horas de convivência | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-006](ADR-006-entrega-at-least-once-com-x-event-id.md) | Garantia de entrega at-least-once, com deduplicação pelo cliente via X-Event-Id | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-007](ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md) | Módulo de webhooks construído com os padrões já existentes no projeto, sem bibliotecas ou estruturas novas | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
| [ADR-008](ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md) | Payload do evento renderizado como snapshot no momento da inserção na outbox | Aceito | 2026-11-08 (data do registro; a transcrição só informa "quinta-feira, 09:00") |
