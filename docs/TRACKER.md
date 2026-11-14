# Tracker de rastreabilidade

**Status:** Em revisão · **Data:** 2026-11-14 · **Documentos cobertos:** [PRD](PRD.md) · [RFC](RFC.md) · [ADRs](adrs/README.md) · [FDD](FDD.md) · **Fontes:** [TRANSCRICAO.md](../TRANSCRICAO.md) e o código do OMS

Cada linha liga um item dos documentos à origem: um trecho da reunião (`TRANSCRICAO`, localização `[hh:mm] Nome`) ou um arquivo do código (`CODIGO`, caminho real no repositório). Se um item não tem localização possível, ele não tem origem e não deveria estar nos documentos.

## Convenções

- **IDs.** PRD e RFC usam o ID do próprio documento com o prefixo dele: `PRD-F03` é a F03 do PRD, e `RFC-ALT-02` é a ALT-02 do RFC. Os critérios da seção 12 do PRD são `PRD-CA-F01-01` (funcionalidade e ordem no texto), com `PRD-CA-INT` e `PRD-CA-NF` para os de integração e os não funcionais, e os itens da seção 13 são `PRD-TESTE`. Os itens do RFC sem ID no texto ganharam um prefixo por seção (`RFC-META`, `RFC-CTX`, `RFC-OBJ`, `RFC-NOBJ`, `RFC-COMP`, `RFC-GAR`, `RFC-IMP`, `RFC-RISCO`, `RFC-PLANO`), numerado na ordem em que aparecem.
- **ADRs.** `ADR-00N` é a decisão do ADR. Os sufixos marcam o resto do ADR: `DEC` para detalhe da decisão, `ALT` para alternativa, `CON` para cada consequência da seção Consequências, `ABERTO` para ponto em aberto e `COD` para referência ao código.
- **FDD.** `FDD-OBJ`, `FDD-CA`, `FDD-TESTE` e `FDD-PROP` mantêm o número de OBJ, CA, T e P do FDD. `FDD-QA` são as linhas da seção 18.1. Os demais prefixos seguem a seção, na ordem do texto (em `FDD-ERRO`, as tabelas 8.1 e 8.2 vêm antes dos parágrafos): `FDD-ESC` (3), `FDD-DADOS` (5), `FDD-FLUXO` (6), `FDD-CONTRATO` (7, com o `operationId` da [spec OpenAPI](openapi/webhooks.openapi.yaml)), `FDD-ERRO` (8), `FDD-RNF` (9), `FDD-SEG` (10), `FDD-OBS` (11), `FDD-CONFIG` (12), `FDD-INTEG` (13), `FDD-DEP` (14) e `FDD-RISCO` (17).
- **Localização.** Quando há mais de um trecho, eles vêm separados por vírgula, e o primeiro é onde o item foi decidido ou confirmado. Uma linha `CODIGO` cita um único arquivo.
- **Propostas e premissas.** O que os documentos definiram sem a reunião (as propostas P-xx do FDD, as premissas do PRD e as observações do autor) aponta para o trecho ou o arquivo que deixou a lacuna, e o resumo diz que é proposta ou premissa.

## Cobertura

| Documento | Itens identificáveis considerados | Linhas | Fonte `TRANSCRICAO` | Fonte `CODIGO` |
| --- | --- | --- | --- | --- |
| `docs/PRD.md` | Todos os itens com ID (F, RNF, OBJ, FE, PREM, DEC, QA, DEP e RISCO), os critérios de aceitação da seção 12 e os itens em lista da estratégia de testes da seção 13 | 144 | 139 | 5 |
| `docs/RFC.md` | Metadados, contexto, objetivos, não objetivos, componentes da visão geral, garantias, alternativas, impactos, riscos, plano de entrega e questões em aberto | 60 | 55 | 5 |
| `docs/adrs/` | Decisão, detalhes da decisão, alternativas, todas as consequências (positivas e negativas), pontos em aberto e referências ao código de cada ADR | 119 | 101 | 18 |
| `docs/FDD.md` | Objetivos, exclusões, tabelas, fluxos, contratos, erros, parâmetros de resiliência, regras de segurança, métricas, logs, tracing, alertas, variáveis, arquivos da integração, dependências, testes, critérios de aceite, riscos, questões e propostas | 190 | 152 | 38 |
| **Total** | | **513** | **447 (87%)** | **66** |

Todos os itens das categorias acima têm linha no tracker. Ficam de fora: os históricos de revisão, os diagramas e os exemplos de requisição e resposta; as seções narrativas do PRD (contexto, problema, público e cenários) e os parágrafos de níveis de teste e de validação com clientes da seção 13, que citam a origem no próprio texto; e a [spec OpenAPI](openapi/webhooks.openapi.yaml), que detalha os contratos do FDD já rastreados aqui.

## Tabela

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
| PRD-F01 | `docs/PRD.md` | Requisito Funcional | Cadastro de webhook com URL `https`, status de interesse e secret gerada pela plataforma, devolvida no cadastro | TRANSCRICAO | [09:31] Marcos, [09:23] Sofia, [09:33] Marcos |
| PRD-F02 | `docs/PRD.md` | Requisito Funcional | Listar, editar (URL, status de interesse, estado ativo), desativar e remover os webhooks de um cliente | TRANSCRICAO | [09:33] Bruno, [09:21] Sofia |
| PRD-F03 | `docs/PRD.md` | Requisito Funcional | Notificar cada mudança de status assinada, com os dados do pedido no momento da mudança, em menos de 10 s | TRANSCRICAO | [09:40] Bruno, [09:02] Marcos, [09:52] Larissa |
| PRD-F04 | `docs/PRD.md` | Requisito Funcional | Assinar cada notificação com HMAC-SHA256 do corpo, usando a secret do webhook, no cabeçalho `X-Signature` | TRANSCRICAO | [09:22] Sofia, [09:20] Sofia |
| PRD-F05 | `docs/PRD.md` | Requisito Funcional | Rotação de secret pelo cliente, com a anterior válida por 24 h | TRANSCRICAO | [09:21] Sofia |
| PRD-F06 | `docs/PRD.md` | Requisito Funcional | Reenvio automático das falhas em 5 tentativas, com intervalos de 1 min, 5 min, 30 min, 2 h e 12 h | TRANSCRICAO | [09:17] Larissa, [09:17] Diego, [09:48] Larissa |
| PRD-F07 | `docs/PRD.md` | Requisito Funcional | Guarda das falhas permanentes (DLQ) com corpo, motivo e momento da falha, fora do ciclo de entrega | TRANSCRICAO | [09:18] Diego |
| PRD-F08 | `docs/PRD.md` | Requisito Funcional | Reprocessamento manual da DLQ restrito a `ADMIN`, com registro de quem fez | TRANSCRICAO | [09:36] Sofia, [09:36] Larissa |
| PRD-F09 | `docs/PRD.md` | Requisito Funcional | Histórico dos últimos 100 envios por webhook, com sucesso ou falha, payload, resposta e tempo de resposta | TRANSCRICAO | [09:34] Marcos |
| PRD-RNF-01 | `docs/PRD.md` | Requisito Não Funcional | Primeiro envio em menos de 10 s após a mudança, com até 2 s de espera até a leitura | TRANSCRICAO | [09:02] Marcos, [09:10] Larissa |
| PRD-RNF-02 | `docs/PRD.md` | Requisito Não Funcional | A mudança de status não chama nem espera o endpoint do cliente | TRANSCRICAO | [09:04] Bruno |
| PRD-RNF-03 | `docs/PRD.md` | Requisito Não Funcional | Timeout de 10 s por envio; sem resposta, falha com reenvio | TRANSCRICAO | [09:42] Diego |
| PRD-RNF-04 | `docs/PRD.md` | Requisito Não Funcional | 100% das mudanças confirmadas para status assinado com notificação; nenhuma notificação de mudança revertida | TRANSCRICAO | [09:06] Diego, [09:40] Bruno, [09:41] Diego |
| PRD-RNF-05 | `docs/PRD.md` | Requisito Não Funcional | Entrega pelo menos uma vez, com duplicatas trazendo o mesmo identificador e corpo | TRANSCRICAO | [09:24] Diego, [09:25] Diego |
| PRD-RNF-06 | `docs/PRD.md` | Requisito Não Funcional | Endpoint fora do ar por até quase 15 h desde a primeira falha recebe a notificação quando volta | TRANSCRICAO | [09:17] Diego, [09:17] Marcos, [09:16] Diego |
| PRD-RNF-07 | `docs/PRD.md` | Requisito Não Funcional | Ordem por pedido e por webhook com um único processo de entrega; o reprocessamento manual pode chegar fora de ordem (retenção com reenvio: proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego, [09:13] Larissa |
| PRD-RNF-08 | `docs/PRD.md` | Requisito Não Funcional | Reiniciar a API não interrompe as entregas | TRANSCRICAO | [09:11] Diego |
| PRD-RNF-09 | `docs/PRD.md` | Requisito Não Funcional | 100% das notificações assinadas com HMAC-SHA256 e secret exclusiva por webhook | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| PRD-RNF-10 | `docs/PRD.md` | Requisito Não Funcional | Só URLs `https`; `http` recusada no cadastro e na edição | TRANSCRICAO | [09:23] Sofia |
| PRD-RNF-11 | `docs/PRD.md` | Requisito Não Funcional | Secret exibida só no cadastro e na rotação, nunca em listagens, histórico ou logs do OMS (logs: proposta do autor, P-24) | TRANSCRICAO | [09:31] Marcos, [09:21] Sofia, [09:29] Bruno |
| PRD-RNF-12 | `docs/PRD.md` | Requisito Não Funcional | Reprocessamento só por `ADMIN`; cadastro, manutenção, rotação e histórico por qualquer usuário autenticado | TRANSCRICAO | [09:36] Sofia, [09:37] Sofia |
| PRD-RNF-13 | `docs/PRD.md` | Requisito Não Funcional | Todo reprocessamento registra usuário, momento e notificação | TRANSCRICAO | [09:36] Sofia |
| PRD-RNF-14 | `docs/PRD.md` | Requisito Não Funcional | Limite de 64KB por notificação, sem envio e sem truncar acima disso | TRANSCRICAO | [09:24] Larissa, [09:23] Sofia |
| PRD-RNF-15 | `docs/PRD.md` | Requisito Não Funcional | Alertas para processo de entrega parado, primeiro envio acima de 10 s e falha permanente, com limiares a validar (proposta do autor, P-19) | TRANSCRICAO | [09:02] Marcos |
| PRD-RNF-16 | `docs/PRD.md` | Requisito Não Funcional | Nenhum endpoint existente muda de contrato; a consulta de pedidos continua disponível | CODIGO | `src/modules/orders/order.routes.ts` |
| PRD-RNF-17 | `docs/PRD.md` | Requisito Não Funcional | Últimos 100 envios por webhook consultáveis enquanto o webhook existir, sem expurgo nesta versão (apagados na remoção: proposta do autor, P-13) | TRANSCRICAO | [09:34] Marcos, [09:08] Diego |
| PRD-OBJ-01 | `docs/PRD.md` | Objetivo | Notificar em menos de 10 s, do registro da notificação à entrega confirmada no primeiro envio, em 95% dessas entregas (premissa, PREM-07) | TRANSCRICAO | [09:02] Marcos |
| PRD-OBJ-02 | `docs/PRD.md` | Objetivo | Reter Atlas, MaxDistribuição e Nova Cargo: 3 de 3 com webhook ativo até o fim do trimestre (premissa, PREM-08) | TRANSCRICAO | [09:00] Marcos |
| PRD-OBJ-03 | `docs/PRD.md` | Objetivo | Entregar até o fim de novembro, em três sprints, com revisão de segurança de pelo menos 2 dias úteis; prazo a confirmar com a Atlas | TRANSCRICAO | [09:45] Marcos, [09:47] Larissa, [09:46] Sofia, [09:47] Marcos |
| PRD-OBJ-04 | `docs/PRD.md` | Objetivo | Métrica de guarda: zero mudanças assinadas sem notificação e zero notificações de mudanças revertidas | TRANSCRICAO | [09:40] Bruno, [09:06] Diego |
| PRD-OBJ-05 | `docs/PRD.md` | Objetivo | Reduzir em 50% a consulta à listagem de pedidos pelos clientes integrados, contra os 30 dias anteriores ao lançamento, pelo log `http_request` (premissa, PREM-09) | TRANSCRICAO | [09:00] Marcos |
| PRD-FE-01 | `docs/PRD.md` | Fora de escopo (Descartado) | Webhooks de entrada: os clientes só recebem | TRANSCRICAO | [09:02] Marcos |
| PRD-FE-02 | `docs/PRD.md` | Fora de escopo (Descartado) | Envio síncrono dentro da mudança de status | TRANSCRICAO | [09:04] Bruno, [09:06] Diego |
| PRD-FE-03 | `docs/PRD.md` | Fora de escopo (Descartado) | Infraestrutura nova de fila, como Redis Streams | TRANSCRICAO | [09:07] Diego |
| PRD-FE-04 | `docs/PRD.md` | Fora de escopo (Descartado) | Garantia de ordem entre pedidos diferentes | TRANSCRICAO | [09:13] Larissa, [09:14] Marcos |
| PRD-FE-05 | `docs/PRD.md` | Fora de escopo (Descartado) | Entrega exatamente uma vez | TRANSCRICAO | [09:25] Diego |
| PRD-FE-06 | `docs/PRD.md` | Fora de escopo (Descartado) | Reenvio sem limite de tentativas | TRANSCRICAO | [09:15] Diego |
| PRD-FE-07 | `docs/PRD.md` | Fora de escopo (Descartado) | Itens do pedido na notificação | TRANSCRICAO | [09:43] Diego, [09:44] Bruno |
| PRD-FE-08 | `docs/PRD.md` | Fora de escopo (Adiado) | Aviso por e-mail ao cliente após falhas seguidas, para a próxima fase | TRANSCRICAO | [09:37] Larissa, [09:38] Marcos |
| PRD-FE-09 | `docs/PRD.md` | Fora de escopo (Adiado) | Limite de taxa de notificações por cliente: observar e decidir depois | TRANSCRICAO | [09:39] Larissa, [09:39] Diego |
| PRD-FE-10 | `docs/PRD.md` | Fora de escopo (Adiado) | Painel visual para o cliente, projeto separado do time de frontend | TRANSCRICAO | [09:40] Larissa |
| PRD-FE-11 | `docs/PRD.md` | Fora de escopo (Adiado) | Arquivamento das notificações entregues depois de cerca de 30 dias | TRANSCRICAO | [09:08] Diego |
| PRD-FE-12 | `docs/PRD.md` | Fora de escopo (Adiado) | Mais de um processo de entrega em paralelo | TRANSCRICAO | [09:13] Diego, [09:12] Diego |
| PRD-PREM-01 | `docs/PRD.md` | Premissa | Clientes usam a API com usuários do OMS que os representam; o cliente é informado no cadastro, não vem do login | TRANSCRICAO | [09:32] Marcos, [09:32] Larissa |
| PRD-PREM-02 | `docs/PRD.md` | Premissa | Integradores verificam a assinatura e deduplicam pelo identificador do evento | TRANSCRICAO | [09:24] Diego, [09:26] Marcos |
| PRD-PREM-03 | `docs/PRD.md` | Premissa | Volume desta fase: três clientes; pico de mudanças por minuto não levantado, a validar | TRANSCRICAO | [09:00] Marcos |
| PRD-PREM-04 | `docs/PRD.md` | Restrição | Time pequeno, sem infraestrutura nova: mesmo MySQL e mesma stack | TRANSCRICAO | [09:07] Diego, [09:11] Diego |
| PRD-PREM-05 | `docs/PRD.md` | Restrição | Três sprints, com pelo menos 2 dias úteis de revisão de segurança antes do deploy; duração e início das sprints não informados | TRANSCRICAO | [09:47] Larissa, [09:46] Larissa, [09:46] Sofia |
| PRD-PREM-06 | `docs/PRD.md` | Restrição | Reuso dos padrões do OMS: erros e autenticação no mesmo formato da API atual | TRANSCRICAO | [09:30] Larissa |
| PRD-PREM-07 | `docs/PRD.md` | Premissa | Meta de 95% das entregas no primeiro envio abaixo de 10 s, a validar com o Marcos (a reunião fixou só os 10 s) | TRANSCRICAO | [09:02] Marcos |
| PRD-PREM-08 | `docs/PRD.md` | Premissa | Retenção medida como 3 de 3 clientes integrados até o fim do trimestre, a validar com o Marcos | TRANSCRICAO | [09:00] Marcos |
| PRD-PREM-09 | `docs/PRD.md` | Premissa | Queda de 50% na consulta à listagem de pedidos em 60 dias, contra a linha de base dos 30 dias anteriores ao lançamento, a validar com o Marcos | TRANSCRICAO | [09:00] Marcos |
| PRD-DEC-01 | `docs/PRD.md` | Decisão | Notificação registrada com a mudança de status e enviada depois, por processo separado a cada 2 s | TRANSCRICAO | [09:08] Larissa, [09:10] Larissa |
| PRD-DEC-02 | `docs/PRD.md` | Trade-off | Entrega pelo menos uma vez: nada se perde, mas o cliente deduplica | TRANSCRICAO | [09:26] Larissa |
| PRD-DEC-03 | `docs/PRD.md` | Trade-off | Reenvio em 5 tentativas ao longo de quase 15 h e depois DLQ com reprocessamento manual por `ADMIN`, sem aviso ao cliente nesta fase | TRANSCRICAO | [09:17] Larissa, [09:17] Diego, [09:19] Larissa, [09:37] Larissa |
| PRD-DEC-04 | `docs/PRD.md` | Decisão | HMAC-SHA256 com secret por webhook e rotação com 24 h de convivência | TRANSCRICAO | [09:22] Sofia |
| PRD-DEC-05 | `docs/PRD.md` | Trade-off | Notificação com os dados do momento da mudança, sem itens; detalhes exigem consulta ao pedido | TRANSCRICAO | [09:52] Bruno, [09:43] Diego |
| PRD-DEC-06 | `docs/PRD.md` | Trade-off | Ordem só por pedido com um único processo de entrega | TRANSCRICAO | [09:13] Larissa |
| PRD-DEC-07 | `docs/PRD.md` | Decisão | Filtro de status aplicado no registro da notificação | TRANSCRICAO | [09:34] Bruno, [09:34] Diego |
| PRD-DEC-08 | `docs/PRD.md` | Decisão | Padrões existentes do OMS, sem dependências novas | TRANSCRICAO | [09:30] Larissa |
| PRD-QA-01 | `docs/PRD.md` | Questão em aberto | Limitar a taxa de notificações por cliente? (QA-01 do RFC) | TRANSCRICAO | [09:39] Larissa, [09:38] Diego |
| PRD-QA-02 | `docs/PRD.md` | Questão em aberto | Exigir perfil específico para cadastrar e manter webhooks? (QA-03 do RFC) | TRANSCRICAO | [09:37] Sofia |
| PRD-QA-03 | `docs/PRD.md` | Questão em aberto | Aviso por e-mail ao cliente entra na próxima fase? (QA-04 do RFC) | TRANSCRICAO | [09:37] Larissa, [09:37] Marcos |
| PRD-QA-04 | `docs/PRD.md` | Questão em aberto | "5 tentativas" são 5 reenvios depois do envio inicial, como lê a proposta P-01 do FDD? | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| PRD-QA-05 | `docs/PRD.md` | Questão em aberto | Como o cliente verifica a assinatura nas 24 h de convivência, com a proposta P-02 do FDD levando uma assinatura de cada secret? | TRANSCRICAO | [09:21] Sofia |
| PRD-QA-06 | `docs/PRD.md` | Questão em aberto | `X-Timestamp` deve entrar na assinatura? A proposta P-22 do FDD o deixa fora e orienta deduplicar pelo identificador do evento | TRANSCRICAO | [09:44] Diego, [09:22] Sofia |
| PRD-DEP-01 | `docs/PRD.md` | Dependência | Endpoints `https` dos três clientes, prontos para verificar a assinatura e deduplicar | TRANSCRICAO | [09:02] Marcos, [09:24] Diego |
| PRD-DEP-02 | `docs/PRD.md` | Dependência | Documentação no portal de desenvolvedores, a cargo do Marcos | TRANSCRICAO | [09:26] Marcos, [09:40] Marcos |
| PRD-DEP-03 | `docs/PRD.md` | Dependência | Revisão de segurança da Sofia, de pelo menos 2 dias úteis, antes do deploy | TRANSCRICAO | [09:46] Sofia, [09:49] Sofia |
| PRD-DEP-04 | `docs/PRD.md` | Dependência | Usuários do OMS que representam cada cliente | TRANSCRICAO | [09:32] Marcos |
| PRD-DEP-05 | `docs/PRD.md` | Dependência | Banco MySQL existente, compartilhado pela API e pelo processo de entrega | TRANSCRICAO | [09:11] Diego, [09:07] Diego |
| PRD-DEP-06 | `docs/PRD.md` | Dependência | Implantação e supervisão do processo de entrega com uma única instância | TRANSCRICAO | [09:11] Larissa, [09:12] Diego |
| PRD-RISCO-01 | `docs/PRD.md` | Risco | Atraso leva a Atlas ao concorrente; duração e início das sprints não informados, e o prazo ainda será confirmado com a Atlas | TRANSCRICAO | [09:00] Marcos, [09:45] Marcos, [09:46] Larissa, [09:47] Marcos |
| PRD-RISCO-02 | `docs/PRD.md` | Risco | Cliente não deduplica e processa a mesma mudança duas vezes | TRANSCRICAO | [09:25] Sofia, [09:26] Marcos |
| PRD-RISCO-03 | `docs/PRD.md` | Risco | Secret vazada no ambiente do cliente permite forjar notificações | TRANSCRICAO | [09:22] Diego |
| PRD-RISCO-04 | `docs/PRD.md` | Risco | Endpoint lento atrasa as notificações dos demais além de 10 s, com um único processo de entrega | TRANSCRICAO | [09:42] Diego, [09:12] Diego |
| PRD-RISCO-05 | `docs/PRD.md` | Risco | Cliente que verifica só uma assinatura rejeita notificações durante a rotação (proposta do autor, P-02) | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| PRD-RISCO-06 | `docs/PRD.md` | Risco | Rajada de mudanças sobrecarrega o endpoint do cliente sem limite de taxa; o limiar do alerta de mitigação é a proposta P-19 do FDD | TRANSCRICAO | [09:38] Diego, [09:39] Larissa |
| PRD-CA-F01-01 | `docs/PRD.md` | Critério de Aceitação | Cadastro com URL `https` e status `SHIPPED` e `DELIVERED` cria o webhook ativo e devolve o identificador e a secret | TRANSCRICAO | [09:31] Marcos, [09:33] Marcos, [09:21] Sofia |
| PRD-CA-F01-02 | `docs/PRD.md` | Critério de Aceitação | URL `http` recusada com erro de validação no campo da URL, sem criar webhook | TRANSCRICAO | [09:23] Sofia |
| PRD-CA-F01-03 | `docs/PRD.md` | Critério de Aceitação | Cadastro com cliente inexistente recusado como cliente não encontrado | TRANSCRICAO | [09:32] Larissa |
| PRD-CA-F01-04 | `docs/PRD.md` | Critério de Aceitação | Lista de status vazia ou com `PENDING` recusada com erro de validação (proposta do autor, P-15) | TRANSCRICAO | [09:33] Marcos |
| PRD-CA-F02-01 | `docs/PRD.md` | Critério de Aceitação | Listagem de um cliente com dois webhooks devolve os dois, paginados e sem a secret | TRANSCRICAO | [09:33] Bruno, [09:31] Marcos |
| PRD-CA-F02-02 | `docs/PRD.md` | Critério de Aceitação | Depois de trocar os status de interesse, só o novo status gera notificação | TRANSCRICAO | [09:34] Bruno, [09:33] Marcos |
| PRD-CA-F02-03 | `docs/PRD.md` | Critério de Aceitação | Webhook desativado não gera notificações novas, e as pendentes vão para a guarda de falhas sem chamada (proposta do autor, P-14) | TRANSCRICAO | [09:21] Bruno |
| PRD-CA-F02-04 | `docs/PRD.md` | Critério de Aceitação | Edição ou remoção de webhook inexistente recusada como não encontrado; edição sem nenhum campo recusada com erro de validação | TRANSCRICAO | [09:33] Bruno |
| PRD-CA-F02-05 | `docs/PRD.md` | Critério de Aceitação | Depois da remoção, o webhook some da listagem e o histórico dele deixa de ser consultável (proposta do autor, P-13) | CODIGO | `prisma/schema.prisma` |
| PRD-CA-F03-01 | `docs/PRD.md` | Critério de Aceitação | Mudança de `PROCESSING` para `SHIPPED` assinada chega ao endpoint em menos de 10 s, com o total como dado básico, o momento da mudança e sem itens (proposta do autor, P-08 e P-18) | TRANSCRICAO | [09:43] Diego, [09:44] Diego, [09:02] Marcos |
| PRD-CA-F03-02 | `docs/PRD.md` | Critério de Aceitação | Mudança para status que nenhum webhook do cliente assina não gera chamada | TRANSCRICAO | [09:34] Bruno |
| PRD-CA-F03-03 | `docs/PRD.md` | Critério de Aceitação | Dois webhooks assinando o mesmo status recebem notificações com identificadores de evento diferentes (proposta do autor, P-05) | TRANSCRICAO | [09:25] Diego, [09:33] Marcos |
| PRD-CA-F03-04 | `docs/PRD.md` | Critério de Aceitação | Falha ao registrar a notificação desfaz status, histórico e estoque, e o operador recebe erro interno | TRANSCRICAO | [09:40] Bruno, [09:41] Diego |
| PRD-CA-F03-05 | `docs/PRD.md` | Critério de Aceitação | Endpoint sem resposta por 10 s não afeta a mudança de status, e o envio é registrado como falha por tempo esgotado | TRANSCRICAO | [09:42] Diego, [09:04] Bruno |
| PRD-CA-F03-06 | `docs/PRD.md` | Critério de Aceitação | Corpo enviado, inclusive nos reenvios, mantém os dados do momento da mudança mesmo com o pedido alterado depois | TRANSCRICAO | [09:52] Larissa, [09:52] Bruno |
| PRD-CA-F03-07 | `docs/PRD.md` | Critério de Aceitação | Notificação acima de 64KB não gera chamada e vai para a guarda de falhas com o motivo de tamanho (proposta do autor, P-04) | TRANSCRICAO | [09:24] Larissa, [09:23] Sofia |
| PRD-CA-F04-01 | `docs/PRD.md` | Critério de Aceitação | Em 100% das notificações, o HMAC-SHA256 do corpo bruto com a secret confere com `X-Signature`, e o vetor de teste do FDD confere | TRANSCRICAO | [09:22] Sofia, [09:20] Sofia |
| PRD-CA-F04-02 | `docs/PRD.md` | Critério de Aceitação | Com dois webhooks, a assinatura de um não confere com a secret do outro | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-F04-03 | `docs/PRD.md` | Critério de Aceitação | Depois de cadastro, rotação e envios, nenhuma secret aparece nos logs do OMS (proposta do autor, P-24) | CODIGO | `src/shared/logger/index.ts` |
| PRD-CA-F05-01 | `docs/PRD.md` | Critério de Aceitação | Rotação devolve secret nova, diferente da anterior, e o momento em que a anterior expira, cerca de 24 h depois | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-F05-02 | `docs/PRD.md` | Critério de Aceitação | Nas 24 h após a rotação, cada notificação traz assinaturas com a secret nova e com a anterior; depois, só com a nova (proposta do autor, P-02) | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| PRD-CA-F05-03 | `docs/PRD.md` | Critério de Aceitação | Rotação de webhook inexistente recusada como não encontrado | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-F06-01 | `docs/PRD.md` | Critério de Aceitação | Reenvios 1 min, 5 min, 30 min, 2 h e 12 h depois de cada falha, com o mesmo identificador e o mesmo corpo (proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| PRD-CA-F06-02 | `docs/PRD.md` | Critério de Aceitação | Endpoint que volta antes do terceiro envio recebe a notificação nele, sem novos reenvios | TRANSCRICAO | [09:15] Diego, [09:17] Diego |
| PRD-CA-F06-03 | `docs/PRD.md` | Critério de Aceitação | Notificação aguardando reenvio segura as posteriores do mesmo pedido e webhook, mas não as de outros pedidos (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego |
| PRD-CA-F07-01 | `docs/PRD.md` | Critério de Aceitação | Envio inicial e 5 reenvios com falha levam a notificação à guarda com motivo, corpo original e momento, sem novos reenvios (proposta do autor, P-01) | TRANSCRICAO | [09:18] Diego, [09:17] Larissa, [09:48] Larissa |
| PRD-CA-F07-02 | `docs/PRD.md` | Critério de Aceitação | Toda notificação guardada gera aviso à operação com o identificador da entrada e o motivo (proposta do autor, P-12) | TRANSCRICAO | [09:29] Bruno |
| PRD-CA-F08-01 | `docs/PRD.md` | Critério de Aceitação | Reprocessamento por `ADMIN` reenvia a notificação com o identificador original e o mesmo corpo e registra o usuário | TRANSCRICAO | [09:18] Diego, [09:36] Sofia |
| PRD-CA-F08-02 | `docs/PRD.md` | Critério de Aceitação | Reprocessamento por `OPERATOR` recusado por falta de permissão | TRANSCRICAO | [09:36] Sofia, [09:36] Larissa |
| PRD-CA-F08-03 | `docs/PRD.md` | Critério de Aceitação | Segundo reprocessamento da mesma entrada, em sequência ou simultâneo, recusado como conflito, com um único reenvio (proposta do autor, P-20) | TRANSCRICAO | [09:35] Diego, [09:36] Sofia |
| PRD-CA-F08-04 | `docs/PRD.md` | Critério de Aceitação | Reprocessamento de entrada inexistente recusado como não encontrado, e o de webhook desativado como conflito (proposta do autor, P-14) | TRANSCRICAO | [09:18] Diego, [09:21] Bruno |
| PRD-CA-F09-01 | `docs/PRD.md` | Critério de Aceitação | Com 150 envios, o histórico mostra os 100 mais recentes, do mais novo ao mais antigo, com resultado, status HTTP, corpo, resposta e tempo (proposta do autor, P-19) | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-F09-02 | `docs/PRD.md` | Critério de Aceitação | Histórico de webhook inexistente recusado como não encontrado | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-INT-01 | `docs/PRD.md` | Critério de Aceitação | F01 → F02: o webhook cadastrado aparece na listagem com a mesma URL e os mesmos status | TRANSCRICAO | [09:33] Bruno |
| PRD-CA-INT-02 | `docs/PRD.md` | Critério de Aceitação | F01 → F03: a notificação vai para a URL cadastrada, só nos status assinados e só com o webhook ativo | TRANSCRICAO | [09:33] Marcos, [09:34] Bruno, [09:21] Sofia |
| PRD-CA-INT-03 | `docs/PRD.md` | Critério de Aceitação | F01 e F03 → F04: a secret devolvida no cadastro confere a assinatura do corpo enviado | TRANSCRICAO | [09:31] Marcos, [09:22] Sofia |
| PRD-CA-INT-04 | `docs/PRD.md` | Critério de Aceitação | F01 e F04 → F05: depois da rotação, a assinatura confere com a secret nova e, por 24 h, com a substituída | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-INT-05 | `docs/PRD.md` | Critério de Aceitação | F03 → F06: uma falha registrada agenda o reenvio com o mesmo identificador de evento e o mesmo corpo | TRANSCRICAO | [09:15] Diego, [09:25] Diego |
| PRD-CA-INT-06 | `docs/PRD.md` | Critério de Aceitação | F03 e F06 → F07: tentativas esgotadas, webhook desativado e corpo acima de 64KB chegam à guarda com corpo idêntico e motivo (proposta do autor, P-04 e P-14) | TRANSCRICAO | [09:18] Diego, [09:24] Larissa, [09:21] Bruno |
| PRD-CA-INT-07 | `docs/PRD.md` | Critério de Aceitação | F07 → F08: o identificador da entrada guardada é o aceito no reprocessamento, que mantém o identificador de evento original | TRANSCRICAO | [09:18] Diego |
| PRD-CA-INT-08 | `docs/PRD.md` | Critério de Aceitação | F03 → F09: cada envio, inclusive os reenvios, aparece no histórico com o número da tentativa | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-NF-01 | `docs/PRD.md` | Critério de Aceitação | RNF-02: com o endpoint fora do ar ou sem responder, a mudança de status responde no mesmo tempo que com ele no ar | TRANSCRICAO | [09:04] Bruno |
| PRD-CA-NF-02 | `docs/PRD.md` | Critério de Aceitação | RNF-08: reiniciar a API durante um lote de envios não interrompe as entregas nem perde notificações | TRANSCRICAO | [09:11] Diego |
| PRD-CA-NF-03 | `docs/PRD.md` | Critério de Aceitação | RNF-15: com o processo de entrega parado, o alerta de processo parado dispara dentro do limiar proposto (proposta do autor, P-19) | TRANSCRICAO | [09:11] Larissa |
| PRD-CA-NF-04 | `docs/PRD.md` | Critério de Aceitação | RNF-16: os testes existentes de pedidos e de autenticação passam sem alteração | CODIGO | `tests/orders.test.ts` |
| PRD-TESTE-01 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: falha ao registrar a notificação desfaz a mudança de status inteira | TRANSCRICAO | [09:40] Bruno, [09:41] Diego |
| PRD-TESTE-02 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: endpoint lento, fora do ar, com erro de rede ou resposta fora de 2xx | TRANSCRICAO | [09:42] Diego, [09:04] Bruno |
| PRD-TESTE-03 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: esgotamento das tentativas e guarda da falha com o corpo idêntico | TRANSCRICAO | [09:18] Diego, [09:17] Larissa |
| PRD-TESTE-04 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: reprocessamento por perfil sem permissão, repetido e simultâneo (simultâneo: proposta do autor, P-20) | TRANSCRICAO | [09:36] Sofia, [09:35] Diego |
| PRD-TESTE-05 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: duplicata por queda do processo de entrega entre o envio e o registro | TRANSCRICAO | [09:24] Diego, [09:25] Diego |
| PRD-TESTE-06 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: dupla assinatura durante a rotação e assinatura única depois das 24 h (proposta do autor, P-02) | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| PRD-TESTE-07 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: ordem por pedido com uma notificação aguardando reenvio (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego |
| PRD-TESTE-08 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: notificação acima de 64KB e webhook desativado (proposta do autor, P-04 e P-14) | TRANSCRICAO | [09:24] Larissa, [09:21] Bruno |
| PRD-TESTE-09 | `docs/PRD.md` | Estratégia de Teste | Cenário crítico: secret ausente de logs, listagens e histórico (logs: proposta do autor, P-24) | TRANSCRICAO | [09:31] Marcos, [09:29] Bruno |
| PRD-TESTE-10 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-01: latência do primeiro envio medida em homologação e acompanhada em produção pela métrica do OBJ-01 | TRANSCRICAO | [09:02] Marcos |
| PRD-TESTE-11 | `docs/PRD.md` | Estratégia de Teste | Validação de RNF-02 e RNF-03: endpoint que não responde, medindo a mudança de status e o registro da falha em 10 s | TRANSCRICAO | [09:42] Diego, [09:04] Bruno |
| PRD-TESTE-12 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-04: conciliação entre o histórico de status e as notificações registradas | TRANSCRICAO | [09:06] Diego, [09:40] Bruno |
| PRD-TESTE-13 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-06: reenvios com relógio controlado, conferindo os intervalos e a guarda depois da última tentativa (proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| PRD-TESTE-14 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-08: reinício da API durante um lote de envios | TRANSCRICAO | [09:11] Diego |
| PRD-TESTE-15 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-11: busca da secret nos logs gerados pelos testes de cadastro, rotação e envio (proposta do autor, P-24) | CODIGO | `src/shared/logger/index.ts` |
| PRD-TESTE-16 | `docs/PRD.md` | Estratégia de Teste | Validação do RNF-15: parada do processo de entrega e conferência do alerta (proposta do autor, P-19) | TRANSCRICAO | [09:11] Larissa |
| PRD-TESTE-17 | `docs/PRD.md` | Estratégia de Teste | Pronto para lançamento: critérios da seção 12 e critérios técnicos do FDD passando | TRANSCRICAO | [09:46] Larissa |
| PRD-TESTE-18 | `docs/PRD.md` | Estratégia de Teste | Pronto para lançamento: revisão de segurança da Sofia concluída, com pelo menos 2 dias úteis | TRANSCRICAO | [09:46] Sofia, [09:49] Sofia |
| PRD-TESTE-19 | `docs/PRD.md` | Estratégia de Teste | Pronto para lançamento: portal de desenvolvedores com a verificação da assinatura e a deduplicação documentadas | TRANSCRICAO | [09:26] Marcos, [09:40] Marcos |
| PRD-TESTE-20 | `docs/PRD.md` | Estratégia de Teste | Pronto para lançamento: processo de entrega com uma única instância e alertas do RNF-15 ativos (alertas: proposta do autor, P-19) | TRANSCRICAO | [09:12] Diego, [09:11] Diego |
| PRD-TESTE-21 | `docs/PRD.md` | Estratégia de Teste | Pronto para lançamento: QA-04 e QA-05 validadas, porque mudam o que o portal promete aos clientes | TRANSCRICAO | [09:17] Larissa, [09:48] Larissa, [09:21] Sofia |
| RFC-META-01 | `docs/RFC.md` | Metadado | Autoria da Larissa, que abre o doc de design, e revisão com Bruno e Diego antes da implementação | TRANSCRICAO | [09:50] Larissa |
| RFC-CTX-01 | `docs/RFC.md` | Contexto | Três clientes B2B fazem polling em `GET /orders`, o que torna a integração lenta e cara | TRANSCRICAO | [09:00] Marcos |
| RFC-CTX-02 | `docs/RFC.md` | Contexto | Abaixo de 10 s já é tempo real para os clientes | TRANSCRICAO | [09:02] Marcos |
| RFC-CTX-03 | `docs/RFC.md` | Contexto | A Atlas pode migrar para um concorrente se a entrega passar do fim do trimestre | TRANSCRICAO | [09:00] Marcos |
| RFC-CTX-04 | `docs/RFC.md` | Contexto | Prazo pedido pela Atlas: fim de novembro | TRANSCRICAO | [09:45] Marcos |
| RFC-CTX-05 | `docs/RFC.md` | Contexto | Fluxo só de saída: os clientes recebem, não enviam | TRANSCRICAO | [09:02] Marcos, [09:03] Sofia |
| RFC-CTX-06 | `docs/RFC.md` | Contexto | A transação de mudança de status já é considerada pesada | TRANSCRICAO | [09:04] Bruno |
| RFC-CTX-07 | `docs/RFC.md` | Contexto | Mudança de status em `OrderService.changeStatus`, numa transação que valida, mexe no estoque e grava o histórico | CODIGO | `src/modules/orders/order.service.ts` |
| RFC-CTX-08 | `docs/RFC.md` | Contexto | Aplicação em um único processo | CODIGO | `src/server.ts` |
| RFC-CTX-09 | `docs/RFC.md` | Contexto | Time pequeno, sem querer subir infraestrutura nova | TRANSCRICAO | [09:07] Diego, [09:07] Larissa |
| RFC-OBJ-01 | `docs/RFC.md` | Objetivo | Notificar o cliente em menos de 10 s após a mudança de status | TRANSCRICAO | [09:02] Marcos |
| RFC-OBJ-02 | `docs/RFC.md` | Objetivo | Toda mudança assinada por um webhook ativo gera evento, e nenhuma mudança revertida gera evento | TRANSCRICAO | [09:06] Diego, [09:34] Bruno |
| RFC-OBJ-03 | `docs/RFC.md` | Objetivo | A indisponibilidade do endpoint do cliente não afeta a mudança de status | TRANSCRICAO | [09:04] Bruno |
| RFC-OBJ-04 | `docs/RFC.md` | Objetivo | O cliente verifica origem e integridade de cada chamada | TRANSCRICAO | [09:19] Sofia |
| RFC-OBJ-05 | `docs/RFC.md` | Objetivo | Cliente gerencia webhooks e consulta o histórico de entregas pela API | TRANSCRICAO | [09:33] Bruno, [09:34] Marcos |
| RFC-NOBJ-01 | `docs/RFC.md` | Não objetivo | Webhooks de entrada | TRANSCRICAO | [09:02] Marcos |
| RFC-NOBJ-02 | `docs/RFC.md` | Não objetivo | Garantia de ordem global entre pedidos | TRANSCRICAO | [09:14] Marcos |
| RFC-NOBJ-03 | `docs/RFC.md` | Não objetivo | Exactly-once | TRANSCRICAO | [09:25] Diego |
| RFC-NOBJ-04 | `docs/RFC.md` | Não objetivo | Aviso por e-mail quando o webhook falha | TRANSCRICAO | [09:37] Larissa |
| RFC-NOBJ-05 | `docs/RFC.md` | Não objetivo | Painel visual para o cliente | TRANSCRICAO | [09:40] Larissa |
| RFC-NOBJ-06 | `docs/RFC.md` | Não objetivo | Arquivamento das linhas entregues da outbox | TRANSCRICAO | [09:08] Diego |
| RFC-COMP-01 | `docs/RFC.md` | Componente | Serviço de pedidos grava o evento na outbox dentro da transação de mudança de status, se algum webhook ativo assina o status | CODIGO | `src/modules/orders/order.service.ts` |
| RFC-COMP-02 | `docs/RFC.md` | Componente | Outbox de eventos no MySQL, lida pelo worker | TRANSCRICAO | [09:06] Diego, [09:08] Diego |
| RFC-COMP-03 | `docs/RFC.md` | Componente | Worker de entrega em processo próprio, com entry-point `src/worker.ts` | TRANSCRICAO | [09:11] Larissa, [09:11] Diego |
| RFC-COMP-04 | `docs/RFC.md` | Componente | DLQ com evento, motivo e momento da falha | TRANSCRICAO | [09:18] Diego |
| RFC-COMP-05 | `docs/RFC.md` | Componente | Cadastro e rotação de secret no módulo `src/modules/webhooks` | TRANSCRICAO | [09:27] Bruno, [09:21] Sofia |
| RFC-COMP-06 | `docs/RFC.md` | Componente | Histórico de entregas exposto ao cliente | TRANSCRICAO | [09:34] Marcos |
| RFC-COMP-07 | `docs/RFC.md` | Componente | Replay da DLQ restrito a `ADMIN` | TRANSCRICAO | [09:36] Larissa, [09:18] Diego |
| RFC-GAR-01 | `docs/RFC.md` | Garantia | Atomicidade entre mudança de status e registro do evento | TRANSCRICAO | [09:06] Diego |
| RFC-GAR-02 | `docs/RFC.md` | Garantia | Entrega pelo menos uma vez com `X-Event-Id` estável para deduplicação | TRANSCRICAO | [09:25] Diego |
| RFC-GAR-03 | `docs/RFC.md` | Garantia | Até 2 s até a leitura do evento e timeout de 10 s por chamada | TRANSCRICAO | [09:10] Larissa, [09:42] Diego |
| RFC-GAR-04 | `docs/RFC.md` | Garantia | "5 tentativas" em 1m, 5m, 30m, 2h e 12h, quase 15 h de janela; a contagem de envios é a proposta P-01 do FDD | TRANSCRICAO | [09:17] Larissa, [09:17] Diego, [09:48] Larissa |
| RFC-GAR-05 | `docs/RFC.md` | Garantia | Ordem por pedido e por webhook com um único worker; com retentativas, depende da P-06; replay pode chegar fora de ordem | TRANSCRICAO | [09:13] Larissa, [09:12] Diego |
| RFC-GAR-06 | `docs/RFC.md` | Garantia | HMAC-SHA256 do corpo, secret por endpoint e 24 h de convivência; assinar o `X-Timestamp` fica para a revisão (P-22) | TRANSCRICAO | [09:22] Sofia, [09:44] Diego |
| RFC-GAR-07 | `docs/RFC.md` | Garantia | Payload como snapshot do momento da transição, sem itens | TRANSCRICAO | [09:52] Larissa, [09:43] Diego |
| RFC-ALT-01 | `docs/RFC.md` | Alternativa descartada | HTTP síncrono na mudança de status | TRANSCRICAO | [09:04] Bruno, [09:06] Diego |
| RFC-ALT-02 | `docs/RFC.md` | Alternativa descartada | Redis Streams como fila | TRANSCRICAO | [09:07] Larissa, [09:07] Diego |
| RFC-ALT-03 | `docs/RFC.md` | Alternativa descartada | Trigger no banco para acordar o worker | TRANSCRICAO | [09:09] Bruno, [09:09] Diego |
| RFC-ALT-04 | `docs/RFC.md` | Alternativa descartada | Worker no processo da API | TRANSCRICAO | [09:11] Diego |
| RFC-ALT-05 | `docs/RFC.md` | Alternativa descartada | Retry indefinido | TRANSCRICAO | [09:15] Diego |
| RFC-ALT-06 | `docs/RFC.md` | Alternativa descartada | Três tentativas | TRANSCRICAO | [09:16] Bruno, [09:16] Diego |
| RFC-ALT-07 | `docs/RFC.md` | Alternativa descartada | Status `failed` na outbox, sem DLQ | TRANSCRICAO | [09:17] Larissa, [09:18] Diego |
| RFC-ALT-08 | `docs/RFC.md` | Alternativa descartada | Secret global da plataforma | TRANSCRICAO | [09:21] Sofia |
| RFC-ALT-09 | `docs/RFC.md` | Alternativa descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| RFC-ALT-10 | `docs/RFC.md` | Alternativa descartada | Renderizar o payload no envio | TRANSCRICAO | [09:51] Bruno, [09:52] Larissa |
| RFC-IMP-01 | `docs/RFC.md` | Impacto | Código existente: `changeStatus` grava na outbox; módulo na composição e nas rotas; logger oculta a secret | CODIGO | `src/modules/orders/order.service.ts` |
| RFC-IMP-02 | `docs/RFC.md` | Impacto | Dados: tabelas de webhooks, outbox, DLQ e histórico de entregas | CODIGO | `prisma/schema.prisma` |
| RFC-IMP-03 | `docs/RFC.md` | Impacto | Operação: novo processo `npm run worker` | TRANSCRICAO | [09:11] Larissa |
| RFC-IMP-04 | `docs/RFC.md` | Impacto | Segurança: secrets por endpoint, rotação, replay `ADMIN` e revisão de 2 dias úteis | TRANSCRICAO | [09:46] Sofia, [09:36] Sofia |
| RFC-IMP-05 | `docs/RFC.md` | Impacto | Clientes validam o HMAC e deduplicam, com documentação no portal; nenhuma infraestrutura nova | TRANSCRICAO | [09:26] Marcos, [09:25] Diego, [09:07] Diego |
| RFC-RISCO-01 | `docs/RFC.md` | Risco | Perder a Atlas por atraso na entrega; o Marcos confirma o prazo com a Atlas | TRANSCRICAO | [09:00] Marcos, [09:47] Marcos |
| RFC-RISCO-02 | `docs/RFC.md` | Risco | Cliente não deduplica e processa o mesmo evento duas vezes | TRANSCRICAO | [09:25] Sofia, [09:26] Marcos |
| RFC-RISCO-03 | `docs/RFC.md` | Risco | Vazamento de secret de um cliente | TRANSCRICAO | [09:22] Diego |
| RFC-RISCO-04 | `docs/RFC.md` | Risco | Crescimento contínuo da outbox degrada o polling | TRANSCRICAO | [09:07] Bruno, [09:08] Diego |
| RFC-RISCO-05 | `docs/RFC.md` | Risco | Cliente lento segura a fila: um worker, envios em sequência e até 10 s por chamada (observação do autor) | TRANSCRICAO | [09:12] Diego, [09:42] Diego |
| RFC-PLANO-01 | `docs/RFC.md` | Plano de entrega | Três sprints, com pelo menos 2 dias úteis de revisão de segurança antes do deploy | TRANSCRICAO | [09:46] Larissa, [09:46] Sofia |
| RFC-QA-01 | `docs/RFC.md` | Questão em aberto | Limite de taxa de envio por cliente: observar e decidir depois | TRANSCRICAO | [09:39] Larissa, [09:38] Diego |
| RFC-QA-02 | `docs/RFC.md` | Questão em aberto | Escala para mais de um worker sem perder a ordem por pedido | TRANSCRICAO | [09:13] Diego |
| RFC-QA-03 | `docs/RFC.md` | Questão em aberto | Quando e como restringir o cadastro de webhooks por perfil ou por cliente | TRANSCRICAO | [09:37] Sofia |
| RFC-QA-04 | `docs/RFC.md` | Questão em aberto | Aviso por e-mail ao cliente após falhas seguidas, na próxima fase | TRANSCRICAO | [09:37] Larissa, [09:48] Larissa, [09:37] Marcos |
| ADR-001 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Decisão | Outbox no MySQL existente, gravada na mesma transação de `changeStatus` e lida por um worker | TRANSCRICAO | [09:08] Larissa, [09:06] Diego |
| ADR-001-DEC-01 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Detalhe da decisão | Falha ao inserir na outbox reverte a mudança de status | TRANSCRICAO | [09:40] Bruno, [09:41] Diego |
| ADR-001-ALT-01 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Alternativa descartada | Chamada HTTP síncrona dentro de `changeStatus` | TRANSCRICAO | [09:04] Bruno, [09:06] Diego |
| ADR-001-ALT-02 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Alternativa descartada | Redis Streams ou Redis Cluster como fila de eventos | TRANSCRICAO | [09:07] Larissa, [09:07] Diego |
| ADR-001-CON-01 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência positiva | Nenhuma mudança de status que um webhook ativo escolheu receber fica sem evento, e nenhum evento sobra de mudança revertida | TRANSCRICAO | [09:06] Diego, [09:34] Bruno |
| ADR-001-CON-02 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência positiva | A transação de pedidos não faz I/O de rede nem depende da disponibilidade dos clientes | TRANSCRICAO | [09:04] Bruno |
| ADR-001-CON-03 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência positiva | Nenhuma peça nova de infraestrutura para operar | TRANSCRICAO | [09:07] Diego |
| ADR-001-CON-04 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência negativa | `changeStatus`, já pesada, ganha mais uma escrita e falha também quando a inserção na outbox falha | TRANSCRICAO | [09:04] Bruno, [09:40] Bruno |
| ADR-001-CON-05 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência negativa | Entrega assíncrona, dependente de um processo leitor (ADR-002) | TRANSCRICAO | [09:06] Diego |
| ADR-001-CON-06 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência negativa | Tabela cresce com o volume; depende de índices em status e `created_at`, e o arquivamento ficou fora do escopo | TRANSCRICAO | [09:08] Diego |
| ADR-001-CON-07 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Consequência negativa | `OrderService` passa a depender do módulo de webhooks para enfileirar o evento na transação | TRANSCRICAO | [09:41] Bruno, [09:41] Diego |
| ADR-001-ABERTO-01 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Questão em aberto | Arquivamento das linhas entregues, fora do escopo desta feature | TRANSCRICAO | [09:08] Diego |
| ADR-001-COD-01 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Gancho com o código | Transação de mudança de status em `changeStatus` | CODIGO | `src/modules/orders/order.service.ts` |
| ADR-001-COD-02 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Gancho com o código | Máquina de estados do pedido | CODIGO | `src/modules/orders/order.status.ts` |
| ADR-001-COD-03 | `docs/adrs/ADR-001-outbox-no-mysql-na-transacao-de-mudanca-de-status.md` | Gancho com o código | MySQL como banco da aplicação, onde a outbox é criada | CODIGO | `prisma/schema.prisma` |
| ADR-002 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Decisão | Worker em processo separado, lendo a outbox por polling a cada 2 s | TRANSCRICAO | [09:10] Larissa, [09:09] Diego |
| ADR-002-DEC-01 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Detalhe da decisão | Entry-point `src/worker.ts` e script `npm run worker` | TRANSCRICAO | [09:11] Larissa |
| ADR-002-DEC-02 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Detalhe da decisão | Mesmo banco, com `PrismaClient` próprio por processo | TRANSCRICAO | [09:11] Diego, [09:30] Bruno |
| ADR-002-DEC-03 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Detalhe da decisão | Leitura dos eventos pendentes mais antigos, em lotes pequenos | TRANSCRICAO | [09:09] Diego, [09:08] Diego |
| ADR-002-DEC-04 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Detalhe da decisão | Lógica de processamento dentro do módulo, em `webhook.worker.ts` ou `webhook.processor.ts` | TRANSCRICAO | [09:28] Bruno |
| ADR-002-ALT-01 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Alternativa descartada | Trigger no banco para tornar o worker reativo | TRANSCRICAO | [09:09] Bruno, [09:09] Diego |
| ADR-002-ALT-02 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Alternativa descartada | Worker dentro do processo da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-CON-01 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência positiva | Em operação normal, evento novo lido em até 2 s, com folga para o limite de 10 s | TRANSCRICAO | [09:09] Diego, [09:10] Marcos |
| ADR-002-CON-02 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência positiva | Entregas continuam durante reinícios e deploys da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-CON-03 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência positiva | Implementação com a stack atual, sem recurso que o MySQL não tem | TRANSCRICAO | [09:09] Diego |
| ADR-002-CON-04 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Até 2 s de espera até o evento ser lido, aceito pela equipe | TRANSCRICAO | [09:10] Larissa |
| ADR-002-CON-05 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Consultas periódicas ao banco mesmo sem eventos | TRANSCRICAO | [09:09] Diego |
| ADR-002-CON-06 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Mais um processo para implantar, monitorar e encerrar com segurança | TRANSCRICAO | [09:11] Diego, [09:11] Larissa |
| ADR-002-CON-07 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Ordem só por `order_id` e com um único worker; sem ordem global | TRANSCRICAO | [09:12] Diego, [09:13] Larissa |
| ADR-002-CON-08 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Cliente lento atrasa eventos de outros clientes e pode estourar os 10 s (observação do autor) | TRANSCRICAO | [09:42] Diego, [09:12] Diego |
| ADR-002-CON-09 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Consequência negativa | Evento retido atrás de outro em retentativa pode esperar cerca de 14 h 36 min (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego, [09:17] Diego |
| ADR-002-ABERTO-01 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Questão em aberto | Escalar para múltiplos workers exige particionar por `order_id` ou lock pessimista | TRANSCRICAO | [09:13] Diego |
| ADR-002-ABERTO-02 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Questão em aberto | Retentativas podem quebrar a ordem com um worker; o FDD retém o evento seguinte do mesmo pedido e webhook (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego |
| ADR-002-COD-01 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Gancho com o código | Entry-point atual da API, modelo para o do worker | CODIGO | `src/server.ts` |
| ADR-002-COD-02 | `docs/adrs/ADR-002-worker-em-processo-separado-com-polling-de-2-segundos.md` | Gancho com o código | Criação do client Prisma | CODIGO | `src/config/database.ts` |
| ADR-003 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Decisão | 5 tentativas com backoff 1m, 5m, 30m, 2h e 12h, depois falha permanente e DLQ | TRANSCRICAO | [09:17] Larissa, [09:17] Diego |
| ADR-003-DEC-01 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Detalhe da decisão | Cliente fora por mais de 15 h perde a entrega automática, aceito pelo produto | TRANSCRICAO | [09:17] Marcos |
| ADR-003-ALT-01 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Alternativa descartada | Retry indefinido com backoff | TRANSCRICAO | [09:15] Diego |
| ADR-003-ALT-02 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Alternativa descartada | Três tentativas | TRANSCRICAO | [09:16] Bruno, [09:16] Diego |
| ADR-003-CON-01 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Consequência positiva | Indisponibilidade de até quase 15 h não perde a entrega automática, se valer a leitura de 6 envios (proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Marcos |
| ADR-003-CON-02 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Consequência positiva | Todo evento termina entregue ou movido para a DLQ | TRANSCRICAO | [09:15] Diego, [09:17] Larissa |
| ADR-003-CON-03 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Consequência negativa | Evento pode chegar com até quase 15 h de atraso, na leitura de 6 envios (proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego |
| ADR-003-CON-04 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Consequência negativa | Cliente fora por mais tempo que a janela depende de replay manual pela DLQ | TRANSCRICAO | [09:18] Diego |
| ADR-003-CON-05 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Consequência negativa | Retentativas reenviam o mesmo evento, e o cliente precisa deduplicar | TRANSCRICAO | [09:24] Diego, [09:25] Diego |
| ADR-003-ABERTO-01 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Questão em aberto | Aviso ao cliente por e-mail fica para a próxima fase | TRANSCRICAO | [09:37] Larissa, [09:37] Marcos |
| ADR-003-ABERTO-02 | `docs/adrs/ADR-003-retry-com-backoff-exponencial-em-5-tentativas.md` | Questão em aberto | Contagem: "5 tentativas" admite 5 envios ou 1 envio e 5 retentativas; o FDD adota 6 envios (proposta do autor, P-01) | TRANSCRICAO | [09:17] Larissa, [09:48] Larissa, [09:17] Diego |
| ADR-004 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Decisão | DLQ em tabela `webhook_dead_letter` separada, com replay manual por endpoint admin | TRANSCRICAO | [09:18] Diego, [09:35] Diego |
| ADR-004-DEC-01 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Detalhe da decisão | Replay exige role `ADMIN` e loga quem executou | TRANSCRICAO | [09:36] Sofia, [09:36] Larissa |
| ADR-004-DEC-02 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Detalhe da decisão | Replay recoloca o evento na outbox como pendente | TRANSCRICAO | [09:18] Diego |
| ADR-004-ALT-01 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Alternativa descartada | Marcar o evento como `failed` na própria outbox | TRANSCRICAO | [09:17] Larissa |
| ADR-004-CON-01 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência positiva | Leitura da outbox principal mais limpa, sem as falhas permanentes | TRANSCRICAO | [09:18] Diego |
| ADR-004-CON-02 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência positiva | Cada falha permanente registrada com payload e motivo, para investigação | TRANSCRICAO | [09:18] Diego |
| ADR-004-CON-03 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência positiva | Reenvio controlado e auditável, restrito a ADMIN | TRANSCRICAO | [09:36] Sofia, [09:36] Larissa |
| ADR-004-CON-04 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência negativa | Mais uma tabela e um fluxo de movimentação entre outbox e DLQ a manter consistentes | TRANSCRICAO | [09:18] Diego |
| ADR-004-CON-05 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência negativa | Reprocessamento manual, sem reenvio automático quando o cliente volta | TRANSCRICAO | [09:18] Diego |
| ADR-004-CON-06 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Consequência negativa | Replay gera nova entrega do mesmo evento, que o cliente precisa tolerar | TRANSCRICAO | [09:24] Diego, [09:18] Diego |
| ADR-004-ABERTO-01 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Questão em aberto | CRUD de configuração com qualquer role por enquanto | TRANSCRICAO | [09:37] Sofia |
| ADR-004-ABERTO-02 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Questão em aberto | Destino da linha da outbox ao ir para a DLQ; o FDD a move (proposta do autor, P-03) | TRANSCRICAO | [09:08] Diego, [09:18] Diego |
| ADR-004-ABERTO-03 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Questão em aberto | DLQ também recebe, sem envio, pendentes de webhook inativo e payload acima de 64KB (proposta do autor, P-14 e P-04) | TRANSCRICAO | [09:21] Bruno, [09:24] Larissa |
| ADR-004-COD-01 | `docs/adrs/ADR-004-dlq-em-tabela-separada-com-replay-manual-por-admin.md` | Gancho com o código | Perfis `ADMIN` e `OPERATOR` no JWT e middleware `requireRole` | CODIGO | `src/middlewares/auth.middleware.ts` |
| ADR-005 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Decisão | HMAC-SHA256 sobre o corpo, secret única por endpoint e rotação com 24 h de convivência | TRANSCRICAO | [09:22] Sofia |
| ADR-005-DEC-01 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Detalhe da decisão | Secret gerada pela plataforma e devolvida na criação do webhook | TRANSCRICAO | [09:31] Marcos |
| ADR-005-DEC-02 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Detalhe da decisão | SHA-256 escolhido por ser o padrão de mercado | TRANSCRICAO | [09:20] Sofia, [09:20] Bruno |
| ADR-005-ALT-01 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Alternativa descartada | Secret global da plataforma | TRANSCRICAO | [09:21] Sofia |
| ADR-005-CON-01 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência positiva | Cliente valida origem e integridade com bibliotecas padrão | TRANSCRICAO | [09:20] Sofia |
| ADR-005-CON-02 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência positiva | Vazamento de uma secret afeta só um endpoint | TRANSCRICAO | [09:21] Sofia |
| ADR-005-CON-03 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência positiva | Rotação sem janela de indisponibilidade para o cliente | TRANSCRICAO | [09:21] Sofia |
| ADR-005-CON-04 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência negativa | Plataforma armazena e gerencia uma secret por endpoint, com o estado de rotação | TRANSCRICAO | [09:21] Bruno, [09:21] Sofia |
| ADR-005-CON-05 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência negativa | Duas secrets válidas para o mesmo endpoint durante as 24 h | TRANSCRICAO | [09:21] Sofia |
| ADR-005-CON-06 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência negativa | Verificação depende de o cliente implementá-la | TRANSCRICAO | [09:20] Sofia |
| ADR-005-CON-07 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Consequência negativa | Revisão de segurança do HMAC e da geração de secret antes do deploy | TRANSCRICAO | [09:46] Sofia |
| ADR-005-ABERTO-01 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Questão em aberto | `X-Timestamp` fora da assinatura não protege contra replay; o cliente deduplica pelo `event_id` (proposta do autor, P-22) | TRANSCRICAO | [09:44] Diego, [09:22] Sofia |
| ADR-005-ABERTO-02 | `docs/adrs/ADR-005-assinatura-hmac-sha256-com-secret-por-endpoint-e-rotacao.md` | Questão em aberto | Assinatura durante as 24 h de convivência; o FDD envia as duas (proposta do autor, P-02) | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| ADR-006 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Decisão | Entrega at-least-once com `X-Event-Id` (UUID gerado na outbox) para deduplicação | TRANSCRICAO | [09:26] Larissa, [09:25] Diego |
| ADR-006-ALT-01 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Alternativa descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| ADR-006-CON-01 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência positiva | Retentativas e replay podem reenviar sem risco de perda | TRANSCRICAO | [09:24] Diego |
| ADR-006-CON-02 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência positiva | Contrato conhecido pelos clientes, igual ao de Stripe e GitHub | TRANSCRICAO | [09:25] Diego |
| ADR-006-CON-03 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência positiva | Implementação sem protocolo de confirmação entre as partes | TRANSCRICAO | [09:25] Diego |
| ADR-006-CON-04 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência negativa | Cliente pode receber o mesmo evento mais de uma vez e deduplica por `X-Event-Id` | TRANSCRICAO | [09:24] Diego, [09:25] Diego |
| ADR-006-CON-05 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência negativa | Correção do processamento no cliente depende da deduplicação, fora do controle da plataforma | TRANSCRICAO | [09:25] Sofia |
| ADR-006-CON-06 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Consequência negativa | Regra documentada com destaque no portal de desenvolvedores | TRANSCRICAO | [09:26] Marcos |
| ADR-006-ABERTO-01 | `docs/adrs/ADR-006-entrega-at-least-once-com-x-event-id.md` | Questão em aberto | Um `event_id` por webhook de destino e o mesmo `event_id` no replay da DLQ, a validar (proposta do autor, P-05) | TRANSCRICAO | [09:25] Diego, [09:33] Marcos, [09:18] Diego |
| ADR-007 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Decisão | Reuso máximo dos padrões: módulo, `AppError`, Pino, error middleware, schemas Zod e códigos de erro | TRANSCRICAO | [09:30] Larissa |
| ADR-007-DEC-01 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | Módulo `src/modules/webhooks` com controller, service, repository, routes e schemas | TRANSCRICAO | [09:27] Bruno |
| ADR-007-DEC-02 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | Prefixo `WEBHOOK_` em todos os códigos de erro do módulo | TRANSCRICAO | [09:29] Larissa, [09:28] Bruno |
| ADR-007-DEC-03 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | Reuso de `requireRole` no replay | TRANSCRICAO | [09:36] Larissa |
| ADR-007-DEC-04 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | `src/worker.ts` ao lado de `src/server.ts`, com a lógica dentro do módulo | TRANSCRICAO | [09:28] Bruno |
| ADR-007-DEC-05 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | Código compartilhado só com acréscimos; a secret no `redactPaths` é a única mudança de comportamento (proposta do autor, P-24) | TRANSCRICAO | [09:29] Bruno, [09:22] Diego |
| ADR-007-DEC-06 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | `WEBHOOK_INVALID_URL` sai em `details` de `VALIDATION_ERROR`, sem `error.code` próprio (proposta do autor, P-21) | TRANSCRICAO | [09:23] Sofia, [09:28] Bruno |
| ADR-007-DEC-07 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Detalhe da decisão | `WEBHOOK_SECRET_REQUIRED` fica sem uso, porque a secret é gerada pela plataforma | TRANSCRICAO | [09:31] Marcos, [09:28] Bruno |
| ADR-007-ALT-01 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Alternativa (observação do autor) | Estrutura e bibliotecas próprias para a feature, não discutida na reunião (observação do autor) | TRANSCRICAO | [09:29] Bruno, [09:30] Larissa |
| ADR-007-CON-01 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência positiva | Quem conhece um módulo do OMS entende o de webhooks | TRANSCRICAO | [09:27] Bruno |
| ADR-007-CON-02 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência positiva | Erros de webhook no formato `{ error: { code, message, details? } }` sem mudar o error middleware | TRANSCRICAO | [09:29] Bruno |
| ADR-007-CON-03 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência positiva | Nenhuma dependência nova a avaliar, aprovar ou manter | TRANSCRICAO | [09:30] Larissa, [09:29] Bruno |
| ADR-007-CON-04 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência negativa | Feature limitada ao que Pino e a estrutura existente oferecem, como nas métricas de entrega (observação do autor) | TRANSCRICAO | [09:29] Bruno |
| ADR-007-CON-05 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência negativa | Worker fora do error middleware e do log de requisição; trata e loga os próprios erros (observação do autor) | TRANSCRICAO | [09:29] Bruno |
| ADR-007-CON-06 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência negativa | URL sem `https` sai como `VALIDATION_ERROR`, e o cliente lê o motivo em `details` (proposta do autor, P-21) | TRANSCRICAO | [09:23] Sofia, [09:28] Bruno |
| ADR-007-CON-07 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Consequência negativa | Código compartilhado recebe acréscimos e a secret no `redactPaths`, que vale para todos os logs (proposta do autor, P-24) | TRANSCRICAO | [09:29] Bruno, [09:22] Diego |
| ADR-007-COD-01 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Middleware de autorização `requireRole` | CODIGO | `src/middlewares/auth.middleware.ts` |
| ADR-007-COD-02 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Hierarquia de erros `AppError` | CODIGO | `src/shared/errors/app-error.ts` |
| ADR-007-COD-03 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Error middleware central | CODIGO | `src/middlewares/error.middleware.ts` |
| ADR-007-COD-04 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Logger Pino e `redactPaths` | CODIGO | `src/shared/logger/index.ts` |
| ADR-007-COD-05 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Composição dos módulos em `buildControllers` e prefixo `/api/v1` | CODIGO | `src/app.ts` |
| ADR-007-COD-06 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Subclasses de erro por status HTTP e erros de domínio | CODIGO | `src/shared/errors/http-errors.ts` |
| ADR-007-COD-07 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Montagem dos routers dos módulos em `buildApiRouter` | CODIGO | `src/routes/index.ts` |
| ADR-007-COD-08 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Exportação das classes de erro | CODIGO | `src/shared/errors/index.ts` |
| ADR-007-COD-09 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Conversão de falha de schema em `VALIDATION_ERROR` | CODIGO | `src/middlewares/validate.middleware.ts` |
| ADR-007-COD-10 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Log de requisição com `X-Request-Id`, fora do caminho do worker | CODIGO | `src/middlewares/request-logger.middleware.ts` |
| ADR-007-COD-11 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Validação das variáveis de ambiente | CODIGO | `src/config/env.ts` |
| ADR-007-COD-12 | `docs/adrs/ADR-007-reuso-dos-padroes-existentes-do-projeto-no-modulo-de-webhooks.md` | Gancho com o código | Entry-point da API, ao lado do qual fica o do worker | CODIGO | `src/server.ts` |
| ADR-008 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Decisão | Payload renderizado e gravado na inserção da outbox (snapshot) | TRANSCRICAO | [09:52] Bruno, [09:52] Larissa |
| ADR-008-DEC-01 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Detalhe da decisão | Campos do payload, sem itens; detalhes por `GET /orders/:id` | TRANSCRICAO | [09:43] Diego |
| ADR-008-DEC-02 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Detalhe da decisão | Dos campos básicos do pedido, só `total_cents` entra no payload (proposta do autor, P-08) | TRANSCRICAO | [09:43] Diego |
| ADR-008-ALT-01 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Alternativa descartada | Guardar só `order_id` e renderizar no envio | TRANSCRICAO | [09:51] Bruno, [09:52] Larissa |
| ADR-008-CON-01 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Consequência positiva | Evento fiel à transição que o originou | TRANSCRICAO | [09:52] Larissa |
| ADR-008-CON-02 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Consequência positiva | Reenvios repetem o corpo e o `event_id`; a assinatura muda se a secret for rotacionada (observação do autor) | TRANSCRICAO | [09:52] Larissa, [09:21] Sofia |
| ADR-008-CON-03 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Consequência negativa | Outbox armazena o corpo completo de cada evento | TRANSCRICAO | [09:52] Larissa, [09:51] Bruno |
| ADR-008-CON-04 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Consequência negativa | Montagem do payload dentro da transação já pesada de `changeStatus` (observação do autor) | TRANSCRICAO | [09:04] Bruno |
| ADR-008-CON-05 | `docs/adrs/ADR-008-payload-do-evento-como-snapshot-na-insercao-da-outbox.md` | Consequência negativa | Evento entregue tarde mostra o estado da mudança, que pode estar desatualizado (observação do autor) | TRANSCRICAO | [09:52] Larissa |
| FDD-OBJ-01 | `docs/FDD.md` | Objetivo | OBJ-01: eventos gravados em `webhook_outbox` na transação de `changeStatus`; falha reverte a mudança | TRANSCRICAO | [09:40] Bruno |
| FDD-OBJ-02 | `docs/FDD.md` | Objetivo | OBJ-02: primeiro envio em menos de 10 s, com polling de 2 s e timeout de 10 s | TRANSCRICAO | [09:02] Marcos, [09:09] Diego |
| FDD-OBJ-03 | `docs/FDD.md` | Objetivo | OBJ-03: retentar em 1m, 5m, 30m, 2h e 12h e mover para a DLQ ao esgotar | TRANSCRICAO | [09:17] Larissa |
| FDD-OBJ-04 | `docs/FDD.md` | Objetivo | OBJ-04: assinar todo envio com HMAC-SHA256 e manter a secret anterior por 24 h | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| FDD-OBJ-05 | `docs/FDD.md` | Objetivo | OBJ-05: mesmo `X-Event-Id` e mesmo corpo em retentativas e replay | TRANSCRICAO | [09:25] Diego, [09:52] Larissa |
| FDD-OBJ-06 | `docs/FDD.md` | Objetivo | OBJ-06: reusar os padrões compartilhados sem mudar o comportamento; só acréscimos e o `redactPaths` do logger (P-24) | TRANSCRICAO | [09:30] Larissa, [09:29] Bruno |
| FDD-OBJ-07 | `docs/FDD.md` | Objetivo | OBJ-07: expor cadastro, edição, remoção, listagem, rotação, histórico e replay | TRANSCRICAO | [09:33] Bruno, [09:21] Sofia |
| FDD-ESC-01 | `docs/FDD.md` | Escopo | Exclusão: itens que a reunião adiou ou descartou, listados no RFC (QA-01 a QA-04 e não objetivos) | TRANSCRICAO | [09:48] Larissa |
| FDD-ESC-02 | `docs/FDD.md` | Escopo | Exclusão: evento na criação do pedido; o ponto de integração é `changeStatus` | TRANSCRICAO | [09:40] Bruno |
| FDD-ESC-03 | `docs/FDD.md` | Escopo | Exclusão: listagem da DLQ pela API, não discutida; o id do replay vem do log ou do banco | TRANSCRICAO | [09:35] Diego |
| FDD-DADOS-01 | `docs/FDD.md` | Modelo de dados | Convenções do schema: ids `CHAR(36)` UUID, colunas camelCase, tabelas com `@@map` | CODIGO | `prisma/schema.prisma` |
| FDD-DADOS-02 | `docs/FDD.md` | Modelo de dados | 5.2 Tabela `webhook_outbox` com status, índices e id UUID igual ao `event_id`; índice composto e payload em texto são proposta do autor (P-23) | TRANSCRICAO | [09:08] Diego, [09:51] Larissa |
| FDD-DADOS-03 | `docs/FDD.md` | Modelo de dados | 5.2 Tabela `webhook_outbox` com status, índice em status e criação, e id UUID igual ao `event_id` | TRANSCRICAO | [09:08] Diego, [09:51] Larissa |
| FDD-DADOS-04 | `docs/FDD.md` | Modelo de dados | 5.3 Tabela `webhook_dead_letter` com payload, motivo, timestamp e dados do replay | TRANSCRICAO | [09:18] Diego |
| FDD-DADOS-05 | `docs/FDD.md` | Modelo de dados | Convenções do schema: ids `CHAR(36)` UUID (exceto `OrderNumberSequence`), colunas camelCase, tabelas com `@@map` | CODIGO | `prisma/schema.prisma` |
| FDD-FLUXO-01 | `docs/FDD.md` | Fluxo | 6.1 Criação do evento na outbox via `publishWebhookEvent(tx, order, from, to)` dentro de `changeStatus` | TRANSCRICAO | [09:41] Bruno, [09:40] Bruno |
| FDD-FLUXO-02 | `docs/FDD.md` | Fluxo | 6.2 Processamento pelo worker: órfãos, seleção, reserva, envio e heartbeat | TRANSCRICAO | [09:09] Diego, [09:08] Diego |
| FDD-FLUXO-03 | `docs/FDD.md` | Fluxo | 6.3 Falha de entrega, classificação do erro e retentativa | TRANSCRICAO | [09:15] Diego, [09:17] Diego |
| FDD-FLUXO-04 | `docs/FDD.md` | Fluxo | 6.4 Falha definitiva e movimentação para a DLQ | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-05 | `docs/FDD.md` | Fluxo | 6.5 Replay manual da DLQ por `ADMIN`, com reserva condicional e log de auditoria | TRANSCRICAO | [09:18] Diego, [09:36] Sofia |
| FDD-FLUXO-06 | `docs/FDD.md` | Fluxo | 6.6 Rotação de secret com convivência de 24 h | TRANSCRICAO | [09:21] Sofia |
| FDD-FLUXO-07 | `docs/FDD.md` | Fluxo | 6.7 Efeito do cadastro sobre eventos pendentes (filtro na inserção) | TRANSCRICAO | [09:34] Bruno |
| FDD-FLUXO-08 | `docs/FDD.md` | Fluxo | 6.8 Estados da linha na outbox: pendente, processando, falhou, entregue | TRANSCRICAO | [09:08] Diego |
| FDD-CONTRATO-01 | `docs/FDD.md` | Contrato | 7.1 `POST /api/v1/webhooks` (`createWebhook`): cria o webhook e devolve a secret | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-02 | `docs/FDD.md` | Contrato | 7.2 `GET /api/v1/webhooks` (`listWebhooks`): lista os webhooks de um cliente, com `customerId` na query (proposta do autor, P-10) | TRANSCRICAO | [09:33] Bruno, [09:32] Larissa |
| FDD-CONTRATO-03 | `docs/FDD.md` | Contrato | 7.3 `PATCH /api/v1/webhooks/:id` (`updateWebhook`): edita URL, status ou estado ativo | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-04 | `docs/FDD.md` | Contrato | 7.4 `DELETE /api/v1/webhooks/:id` (`deleteWebhook`): remove o webhook | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-05 | `docs/FDD.md` | Contrato | 7.5 `POST /api/v1/webhooks/:id/rotate-secret` (`rotateWebhookSecret`): gera nova secret | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-06 | `docs/FDD.md` | Contrato | 7.6 `GET /api/v1/webhooks/:id/deliveries` (`listWebhookDeliveries`): últimas entregas | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-07 | `docs/FDD.md` | Contrato | 7.7 `POST /api/v1/admin/webhooks/dead-letter/:id/replay` (`replayDeadLetter`): recoloca o evento | TRANSCRICAO | [09:35] Diego, [09:18] Diego |
| FDD-CONTRATO-08 | `docs/FDD.md` | Contrato | 7.8 POST de saída (`orderStatusChanged`): payload snake_case e cabeçalhos `X-Event-Id`, `X-Webhook-Id`, `X-Timestamp`, `X-Signature` | TRANSCRICAO | [09:43] Diego, [09:44] Diego, [09:44] Sofia |
| FDD-ERRO-01 | `docs/FDD.md` | Erro | `WEBHOOK_NOT_FOUND` (404) para `:id` inexistente | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-02 | `docs/FDD.md` | Erro | `WEBHOOK_CUSTOMER_NOT_FOUND` (404) para `customerId` inexistente | TRANSCRICAO | [09:32] Larissa |
| FDD-ERRO-03 | `docs/FDD.md` | Erro | `WEBHOOK_INVALID_URL` em `details` de `VALIDATION_ERROR` (400) para URL sem `https` (proposta do autor, P-21) | TRANSCRICAO | [09:23] Sofia, [09:28] Bruno, [09:29] Larissa |
| FDD-ERRO-04 | `docs/FDD.md` | Erro | `WEBHOOK_DEAD_LETTER_NOT_FOUND` (404) no replay | TRANSCRICAO | [09:35] Diego |
| FDD-ERRO-05 | `docs/FDD.md` | Erro | `WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED` (409), inclusive no replay simultâneo (proposta do autor, P-20) | TRANSCRICAO | [09:35] Diego, [09:36] Sofia |
| FDD-ERRO-06 | `docs/FDD.md` | Erro | `WEBHOOK_INACTIVE` (409) no replay para webhook inativo (proposta do autor, P-14) | TRANSCRICAO | [09:21] Bruno, [09:46] Sofia |
| FDD-ERRO-07 | `docs/FDD.md` | Erro | `WEBHOOK_DELIVERY_TIMEOUT`: sem status de resposta em 10 s, com retentativa | TRANSCRICAO | [09:42] Diego |
| FDD-ERRO-08 | `docs/FDD.md` | Erro | `WEBHOOK_DELIVERY_HTTP_ERROR`: status fora de 2xx, com retentativa | TRANSCRICAO | [09:15] Diego |
| FDD-ERRO-09 | `docs/FDD.md` | Erro | `WEBHOOK_DELIVERY_NETWORK_ERROR`: DNS, conexão ou TLS, com retentativa | TRANSCRICAO | [09:14] Larissa |
| FDD-ERRO-10 | `docs/FDD.md` | Erro | `WEBHOOK_MAX_ATTEMPTS_EXCEEDED`: sexto envio com falha, vai para a DLQ (contagem: proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| FDD-ERRO-11 | `docs/FDD.md` | Erro | `WEBHOOK_PAYLOAD_TOO_LARGE`: corpo acima de 65.536 bytes, DLQ sem envio | TRANSCRICAO | [09:24] Larissa, [09:23] Sofia |
| FDD-ERRO-12 | `docs/FDD.md` | Erro | `WEBHOOK_INACTIVE` no worker: webhook inativo no envio, DLQ sem envio (proposta do autor, P-14) | TRANSCRICAO | [09:21] Bruno, [09:46] Sofia |
| FDD-ERRO-13 | `docs/FDD.md` | Decisão | `WEBHOOK_SECRET_REQUIRED`, citado como exemplo na reunião, não é usado: a secret é gerada pela plataforma | TRANSCRICAO | [09:28] Bruno, [09:31] Marcos |
| FDD-ERRO-14 | `docs/FDD.md` | Erro | Prefixo `WEBHOOK_` e reuso de `VALIDATION_ERROR`, `UNAUTHORIZED`, `FORBIDDEN` e `INTERNAL_SERVER_ERROR`; nomes além dos citados são proposta do autor (P-25) | TRANSCRICAO | [09:29] Larissa, [09:28] Bruno |
| FDD-RNF-01 | `docs/FDD.md` | Resiliência | Intervalo de polling de 2 s | TRANSCRICAO | [09:09] Diego, [09:10] Larissa |
| FDD-RNF-02 | `docs/FDD.md` | Resiliência | Lote de 10 eventos por ciclo ("batch pequeno"; o valor é proposta do autor) | TRANSCRICAO | [09:08] Diego |
| FDD-RNF-03 | `docs/FDD.md` | Resiliência | Timeout de 10 s por envio, cobrindo conexão, resposta e leitura do corpo | TRANSCRICAO | [09:42] Diego |
| FDD-RNF-04 | `docs/FDD.md` | Resiliência | 5 retentativas depois do envio inicial, 6 envios no total (proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| FDD-RNF-05 | `docs/FDD.md` | Resiliência | Intervalos 1m, 5m, 30m, 2h e 12h; fórmula de `nextAttemptAt` sem jitter (proposta do autor, P-16) | TRANSCRICAO | [09:17] Diego |
| FDD-RNF-06 | `docs/FDD.md` | Resiliência | Limite do corpo de 64KB, lidos como 65.536 bytes (conversão: proposta do autor, P-23) | TRANSCRICAO | [09:24] Diego, [09:24] Larissa |
| FDD-RNF-07 | `docs/FDD.md` | Resiliência | Um worker, com envios em sequência (a sequência é proposta do autor, P-16) | TRANSCRICAO | [09:12] Diego |
| FDD-RNF-08 | `docs/FDD.md` | Resiliência | Recuperação de órfãos em `PROCESSING` há mais de 60 s (proposta do autor, P-11) | TRANSCRICAO | [09:08] Diego |
| FDD-RNF-09 | `docs/FDD.md` | Resiliência | Fallback: sem canal alternativo; aviso por e-mail adiado | TRANSCRICAO | [09:37] Larissa |
| FDD-RNF-10 | `docs/FDD.md` | Resiliência | Banco indisponível: o ciclo do worker aborta com log e o loop continua (proposta do autor; o client Prisma não trata indisponibilidade) | CODIGO | `src/config/database.ts` |
| FDD-RNF-11 | `docs/FDD.md` | Resiliência | Encerramento com `SIGTERM` e `SIGINT` no padrão do entry-point da API | CODIGO | `src/server.ts` |
| FDD-SEG-01 | `docs/FDD.md` | Segurança | 10.1 CRUD com qualquer role autenticada; replay com `requireRole('ADMIN')` | TRANSCRICAO | [09:36] Sofia, [09:37] Sofia |
| FDD-SEG-02 | `docs/FDD.md` | Segurança | 10.2 Secret gerada pela plataforma, uma por webhook, devolvida só na criação e na rotação | TRANSCRICAO | [09:31] Marcos, [09:21] Sofia |
| FDD-SEG-03 | `docs/FDD.md` | Segurança | 10.3 `X-Signature` com HMAC-SHA256 sobre os bytes do corpo; deduplicação pelo `event_id` contra replay | TRANSCRICAO | [09:22] Sofia, [09:25] Diego |
| FDD-SEG-04 | `docs/FDD.md` | Segurança | 10.4 Secret fora dos logs, com `secret` e `previousSecret` no `redactPaths` | CODIGO | `src/shared/logger/index.ts` |
| FDD-SEG-05 | `docs/FDD.md` | Segurança | 10.5 Só `https`; redirect não seguido e resposta lida até 2.048 caracteres (propostas do autor, P-16 e P-11); corpo até 64KB | TRANSCRICAO | [09:23] Sofia, [09:24] Larissa |
| FDD-OBS-01 | `docs/FDD.md` | Observabilidade | Métrica `webhook_deliveries_total` por resultado, código de erro e webhook | TRANSCRICAO | [09:34] Marcos |
| FDD-OBS-02 | `docs/FDD.md` | Observabilidade | Métrica `webhook_delivery_duration_ms`, tempo de resposta frente ao timeout de 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-OBS-03 | `docs/FDD.md` | Observabilidade | Métrica `webhook_first_delivery_latency_ms`, meta de menos de 10 s | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-04 | `docs/FDD.md` | Observabilidade | Métrica `webhook_dead_letters_total` por motivo | TRANSCRICAO | [09:18] Diego |
| FDD-OBS-05 | `docs/FDD.md` | Observabilidade | Métrica `webhook_replays_total` | TRANSCRICAO | [09:36] Sofia |
| FDD-OBS-06 | `docs/FDD.md` | Observabilidade | Métrica `webhook_outbox_backlog` por status | TRANSCRICAO | [09:08] Diego |
| FDD-OBS-07 | `docs/FDD.md` | Observabilidade | Métrica `webhook_outbox_oldest_pending_age_ms`, atraso do primeiro envio | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-08 | `docs/FDD.md` | Observabilidade | Métrica `webhook_worker_up`, presença do heartbeat a cada 30 s | TRANSCRICAO | [09:11] Diego |
| FDD-OBS-09 | `docs/FDD.md` | Observabilidade | Logs do ciclo do worker: `worker_started`, `worker_stopped`, `worker_bootstrap_failed`, `webhook_worker_cycle_failed`, `webhook_worker_heartbeat`, `webhook_orphans_recovered` | CODIGO | `src/server.ts` |
| FDD-OBS-10 | `docs/FDD.md` | Observabilidade | Logs de entrega no Pino: `webhook_delivery_succeeded`, `webhook_delivery_failed`, `webhook_dead_lettered` | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-11 | `docs/FDD.md` | Observabilidade | Logs de auditoria da API: `webhook_dead_letter_replayed` e `webhook_secret_rotated`, com `userId` e `requestId` | TRANSCRICAO | [09:36] Sofia |
| FDD-OBS-12 | `docs/FDD.md` | Observabilidade | Tracing por `X-Request-Id`, `orderId`, `eventId` e `X-Webhook-Id`, sem tracing distribuído | CODIGO | `src/middlewares/request-logger.middleware.ts` |
| FDD-OBS-13 | `docs/FDD.md` | Observabilidade | Alerta crítico: nenhum heartbeat do worker por 90 s (limiar: proposta do autor, P-19) | TRANSCRICAO | [09:11] Diego |
| FDD-OBS-14 | `docs/FDD.md` | Observabilidade | Alerta alto: pendente mais antigo acima de 10 s por 5 min (limiar: proposta do autor, P-19) | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-15 | `docs/FDD.md` | Observabilidade | Alerta médio: aumento de `webhook_dead_letters_total` (proposta do autor, P-19) | TRANSCRICAO | [09:18] Diego |
| FDD-OBS-16 | `docs/FDD.md` | Observabilidade | Alerta médio: mais de 50% de falhas de um webhook em 15 min (limiar: proposta do autor, P-19) | TRANSCRICAO | [09:37] Marcos |
| FDD-CONFIG-01 | `docs/FDD.md` | Configuração | `WEBHOOK_POLL_INTERVAL_MS`, padrão 2000; em variável por exceção à regra de constantes (proposta do autor, P-16) | TRANSCRICAO | [09:09] Diego |
| FDD-CONFIG-02 | `docs/FDD.md` | Configuração | `WEBHOOK_BATCH_SIZE`, padrão 10 (proposta do autor, P-11) | TRANSCRICAO | [09:08] Diego |
| FDD-CONFIG-03 | `docs/FDD.md` | Configuração | `WEBHOOK_HTTP_TIMEOUT_MS`, padrão 10000 | TRANSCRICAO | [09:42] Diego |
| FDD-CONFIG-04 | `docs/FDD.md` | Configuração | `WEBHOOK_MAX_PAYLOAD_BYTES`, padrão 65536 | TRANSCRICAO | [09:24] Larissa |
| FDD-CONFIG-05 | `docs/FDD.md` | Configuração | `WEBHOOK_HEARTBEAT_INTERVAL_MS`, padrão 30000, no schema Zod de ambiente (valor: proposta do autor, P-11) | CODIGO | `src/config/env.ts` |
| FDD-CONFIG-06 | `docs/FDD.md` | Configuração | Script `worker` no `package.json`; o `worker:dev` é proposta do autor (P-19) | TRANSCRICAO | [09:11] Larissa |
| FDD-INTEG-01 | `docs/FDD.md` | Integração | Alterado: `changeStatus` chama `publishWebhookEvent(tx, order, from, to)` depois do histórico, sem mudar o construtor | CODIGO | `src/modules/orders/order.service.ts` |
| FDD-INTEG-02 | `docs/FDD.md` | Integração | Reusado: referência dos status aceitos em `events`, sem `PENDING` | CODIGO | `src/modules/orders/order.status.ts` |
| FDD-INTEG-03 | `docs/FDD.md` | Integração | Alterado: classes 404 `WebhookNotFoundError`, `WebhookCustomerNotFoundError` e `WebhookDeadLetterNotFoundError`; 409 com `ConflictError` | CODIGO | `src/shared/errors/http-errors.ts` |
| FDD-INTEG-04 | `docs/FDD.md` | Integração | Alterado: exportação das novas classes de erro | CODIGO | `src/shared/errors/index.ts` |
| FDD-INTEG-05 | `docs/FDD.md` | Integração | Reusado sem mudança: serializa os erros `WEBHOOK_*`; replay concorrente não depende de P2002 | CODIGO | `src/middlewares/error.middleware.ts` |
| FDD-INTEG-06 | `docs/FDD.md` | Integração | Reusado sem mudança: `authenticate` no CRUD e `requireRole('ADMIN')` no replay | CODIGO | `src/middlewares/auth.middleware.ts` |
| FDD-INTEG-07 | `docs/FDD.md` | Integração | Reusado sem mudança: `validate` com os schemas Zod; `refine` de `https` com a mensagem `WEBHOOK_INVALID_URL` | CODIGO | `src/middlewares/validate.middleware.ts` |
| FDD-INTEG-08 | `docs/FDD.md` | Integração | Alterado: caminhos da secret no `redactPaths` e logger filho do worker | CODIGO | `src/shared/logger/index.ts` |
| FDD-INTEG-09 | `docs/FDD.md` | Integração | Reusado sem mudança: `requestId` nos logs de replay e rotação; `uuid` v4 para o `event_id` | CODIGO | `src/middlewares/request-logger.middleware.ts` |
| FDD-INTEG-10 | `docs/FDD.md` | Integração | Reusado sem mudança: `paginated()` na listagem e no histórico de entregas | CODIGO | `src/shared/http/response.ts` |
| FDD-INTEG-11 | `docs/FDD.md` | Integração | Alterado: `buildControllers` instancia repository, service e controller de webhooks | CODIGO | `src/app.ts` |
| FDD-INTEG-12 | `docs/FDD.md` | Integração | Alterado: monta `/webhooks` e `/admin/webhooks` | CODIGO | `src/routes/index.ts` |
| FDD-INTEG-13 | `docs/FDD.md` | Integração | Alterado: cinco variáveis `WEBHOOK_*` com padrão no `envSchema` | CODIGO | `src/config/env.ts` |
| FDD-INTEG-14 | `docs/FDD.md` | Integração | Reusado sem mudança: o worker importa `prisma` e ganha instância própria por processo | CODIGO | `src/config/database.ts` |
| FDD-INTEG-15 | `docs/FDD.md` | Integração | Reusado sem mudança: padrão de bootstrap e shutdown copiado por `src/worker.ts` | CODIGO | `src/server.ts` |
| FDD-INTEG-16 | `docs/FDD.md` | Integração | Alterado: enum `WebhookOutboxStatus`, quatro modelos e relação `webhooks` em `Customer` | CODIGO | `prisma/schema.prisma` |
| FDD-INTEG-17 | `docs/FDD.md` | Integração | Alterado: nova migração `add_webhooks` ao lado da migração inicial | CODIGO | `prisma/migrations/20260519182739_init/migration.sql` |
| FDD-INTEG-18 | `docs/FDD.md` | Integração | Alterado: limpeza das quatro tabelas novas antes de `customer` | CODIGO | `tests/setup.ts` |
| FDD-INTEG-19 | `docs/FDD.md` | Integração | Alterado: fábrica `createTestWebhook(customerId, overrides)` | CODIGO | `tests/helpers/factories.ts` |
| FDD-INTEG-20 | `docs/FDD.md` | Integração | Alterado: scripts `worker` e `worker:dev`, sem dependência nova | CODIGO | `package.json` |
| FDD-INTEG-21 | `docs/FDD.md` | Integração | Alterado: variáveis `WEBHOOK_*` com os padrões comentados | CODIGO | `.env.example` |
| FDD-DEP-01 | `docs/FDD.md` | Dependência | Nenhuma biblioteca nova: `node:crypto`, `fetch`, `uuid`, Prisma, Zod e Pino já instalados | CODIGO | `package.json` |
| FDD-DEP-02 | `docs/FDD.md` | Dependência | MySQL 8 com quatro tabelas novas e nenhuma coluna existente alterada | CODIGO | `docker-compose.yml` |
| FDD-DEP-03 | `docs/FDD.md` | Compatibilidade | Nenhum endpoint existente muda; `PATCH /orders/:id/status` ganha o modo de falha 500 | CODIGO | `src/modules/orders/order.routes.ts` |
| FDD-DEP-04 | `docs/FDD.md` | Dependência | Novo processo `npm run worker` com réplica única | TRANSCRICAO | [09:12] Diego, [09:11] Larissa |
| FDD-DEP-05 | `docs/FDD.md` | Dependência | Revisão de segurança da Sofia e documentação no portal pelo Marcos | TRANSCRICAO | [09:46] Sofia, [09:26] Marcos |
| FDD-TESTE-01 | `docs/FDD.md` | Teste | T-01: `nextAttemptAt` para `attempts` de 1 a 5 e DLQ em 6 (contagem: proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:48] Larissa |
| FDD-TESTE-02 | `docs/FDD.md` | Teste | T-02: HMAC do vetor de teste; duas assinaturas antes do fim da convivência e uma depois (proposta do autor, P-02) | TRANSCRICAO | [09:22] Sofia, [09:21] Sofia |
| FDD-TESTE-03 | `docs/FDD.md` | Teste | T-03: payload snake_case com 9 campos e sem itens | TRANSCRICAO | [09:43] Diego |
| FDD-TESTE-04 | `docs/FDD.md` | Teste | T-04: classificação de timeout, erro HTTP e erro de rede | TRANSCRICAO | [09:42] Diego |
| FDD-TESTE-05 | `docs/FDD.md` | Teste | T-05: `PROCESSING → SHIPPED` com webhook assinando `SHIPPED` gera 1 linha `PENDING` | TRANSCRICAO | [09:40] Bruno |
| FDD-TESTE-06 | `docs/FDD.md` | Teste | T-06: status não assinado ou webhook inativo não geram linha; dois webhooks geram duas | TRANSCRICAO | [09:34] Bruno |
| FDD-TESTE-07 | `docs/FDD.md` | Teste | T-07: erro no publicador devolve 500 e mantém status, histórico e estoque | TRANSCRICAO | [09:40] Bruno, [09:41] Diego |
| FDD-TESTE-08 | `docs/FDD.md` | Teste | T-08: CRUD com 201, 200, 204, 404 e 400 | TRANSCRICAO | [09:33] Bruno, [09:23] Sofia |
| FDD-TESTE-09 | `docs/FDD.md` | Teste | T-09: rotação com expiração em cerca de 24 h e secret nova | TRANSCRICAO | [09:21] Sofia |
| FDD-TESTE-10 | `docs/FDD.md` | Teste | T-10: replay com 202 para `ADMIN`, 403, 404, 409 e chamadas simultâneas | TRANSCRICAO | [09:36] Sofia, [09:35] Diego |
| FDD-TESTE-11 | `docs/FDD.md` | Teste | T-11: `runOnce()` com 200 leva a `DELIVERED`; 503 leva a `FAILED` com +1 min | TRANSCRICAO | [09:17] Diego |
| FDD-TESTE-12 | `docs/FDD.md` | Teste | T-12: seis falhas levam a linha à DLQ com payload idêntico (contagem: proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa |
| FDD-TESTE-13 | `docs/FDD.md` | Teste | T-13: corpo acima de 65.536 bytes e webhook inativo vão à DLQ sem envio | TRANSCRICAO | [09:24] Larissa |
| FDD-TESTE-14 | `docs/FDD.md` | Teste | T-14: evento `FAILED` segura o posterior do mesmo pedido e webhook (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego |
| FDD-TESTE-15 | `docs/FDD.md` | Teste | T-15: linha em `PROCESSING` há mais de 60 s volta a ser elegível (proposta do autor, P-11) | TRANSCRICAO | [09:08] Diego |
| FDD-TESTE-16 | `docs/FDD.md` | Teste | T-16: servidor local recebe os cabeçalhos, a assinatura verificável e o mesmo `X-Event-Id` na retentativa | TRANSCRICAO | [09:44] Diego, [09:44] Sofia |
| FDD-TESTE-17 | `docs/FDD.md` | Teste | T-17: histórico em ordem decrescente com no máximo 100 itens | TRANSCRICAO | [09:34] Marcos |
| FDD-TESTE-18 | `docs/FDD.md` | Teste | T-18: testes existentes de pedidos e autenticação passam sem alteração | CODIGO | `tests/orders.test.ts` |
| FDD-TESTE-19 | `docs/FDD.md` | Teste | T-19: heartbeat em cerca de 40 s, abaixo dos 90 s do alerta, com lote de envios lentos (proposta do autor, P-16) | TRANSCRICAO | [09:11] Diego |
| FDD-CA-01 | `docs/FDD.md` | Critério de aceite | CA-01: mudança para status assinado cria linha `PENDING` com os 9 campos e sem itens | TRANSCRICAO | [09:40] Bruno, [09:43] Diego |
| FDD-CA-02 | `docs/FDD.md` | Critério de aceite | CA-02: sem webhook interessado, nenhuma linha é criada | TRANSCRICAO | [09:34] Bruno |
| FDD-CA-03 | `docs/FDD.md` | Critério de aceite | CA-03: falha na outbox devolve 500 e mantém status, histórico e estoque | TRANSCRICAO | [09:40] Bruno |
| FDD-CA-04 | `docs/FDD.md` | Critério de aceite | CA-04: primeiro envio em menos de 10 s após o commit | TRANSCRICAO | [09:02] Marcos |
| FDD-CA-05 | `docs/FDD.md` | Critério de aceite | CA-05: cinco cabeçalhos e `X-Signature` conferindo com o HMAC do corpo bruto | TRANSCRICAO | [09:44] Diego, [09:44] Sofia, [09:22] Sofia |
| FDD-CA-06 | `docs/FDD.md` | Critério de aceite | CA-06: sem resposta em 10 s, `WEBHOOK_DELIVERY_TIMEOUT` e nova tentativa em 1 min | TRANSCRICAO | [09:42] Diego |
| FDD-CA-07 | `docs/FDD.md` | Critério de aceite | CA-07: envio inicial e 5 retentativas falhando levam à DLQ com `attempts = 6` (contagem: proposta do autor, P-01) | TRANSCRICAO | [09:17] Diego, [09:17] Larissa, [09:48] Larissa |
| FDD-CA-08 | `docs/FDD.md` | Critério de aceite | CA-08: corpo acima de 65.536 bytes vai à DLQ com `WEBHOOK_PAYLOAD_TOO_LARGE` | TRANSCRICAO | [09:24] Larissa |
| FDD-CA-09 | `docs/FDD.md` | Critério de aceite | CA-09: replay por `ADMIN` responde 202 e loga `userId`; `OPERATOR` recebe 403 | TRANSCRICAO | [09:36] Sofia |
| FDD-CA-10 | `docs/FDD.md` | Critério de aceite | CA-10: segundo replay, sequencial ou simultâneo, responde 409 com uma única linha na outbox | TRANSCRICAO | [09:18] Diego |
| FDD-CA-11 | `docs/FDD.md` | Critério de aceite | CA-11: URL `http` e URL malformada respondem 400; cliente inexistente responde 404 | TRANSCRICAO | [09:23] Sofia |
| FDD-CA-12 | `docs/FDD.md` | Critério de aceite | CA-12: secret só nas respostas de criação e rotação, nunca em logs | TRANSCRICAO | [09:31] Marcos, [09:21] Sofia |
| FDD-CA-13 | `docs/FDD.md` | Critério de aceite | CA-13: duas assinaturas nas 24 h após a rotação e uma depois (proposta do autor, P-02) | TRANSCRICAO | [09:21] Sofia |
| FDD-CA-14 | `docs/FDD.md` | Critério de aceite | CA-14: mesmo corpo e mesmo `X-Event-Id` em retentativas e replay | TRANSCRICAO | [09:25] Diego |
| FDD-CA-15 | `docs/FDD.md` | Critério de aceite | CA-15: evento `FAILED` de um pedido segura os posteriores do mesmo pedido e webhook (proposta do autor, P-06) | TRANSCRICAO | [09:12] Diego |
| FDD-CA-16 | `docs/FDD.md` | Critério de aceite | CA-16: histórico com até 100 entradas, das mais recentes para as mais antigas | TRANSCRICAO | [09:34] Marcos |
| FDD-CA-17 | `docs/FDD.md` | Critério de aceite | CA-17: erros no formato `{ error: { code, message, details? } }` sem alterar o error middleware | CODIGO | `src/middlewares/error.middleware.ts` |
| FDD-CA-18 | `docs/FDD.md` | Critério de aceite | CA-18: `npm run worker` em processo separado; reiniciar a API não interrompe entregas | TRANSCRICAO | [09:11] Diego, [09:11] Larissa |
| FDD-CA-19 | `docs/FDD.md` | Critério de aceite | CA-19: testes de pedidos e autenticação passam sem alteração | CODIGO | `tests/auth.test.ts` |
| FDD-CA-20 | `docs/FDD.md` | Critério de aceite | CA-20: logs com os campos listados e heartbeat em cerca de 40 s (intervalo: proposta do autor, P-16) | TRANSCRICAO | [09:29] Bruno |
| FDD-CA-21 | `docs/FDD.md` | Critério de aceite | CA-21: listagem, edição, remoção em cascata, rotação e 404 do CRUD | TRANSCRICAO | [09:33] Bruno |
| FDD-CA-22 | `docs/FDD.md` | Critério de aceite | CA-22: órfãos voltam a ser elegíveis (P-11); webhook inativo leva pendentes à DLQ sem envio (P-14) (propostas do autor) | TRANSCRICAO | [09:08] Diego, [09:21] Bruno |
| FDD-RISCO-01 | `docs/FDD.md` | Risco | Cliente lento segura a fila e estoura a meta de 10 s para os demais (observação do autor) | TRANSCRICAO | [09:42] Diego, [09:12] Diego |
| FDD-RISCO-02 | `docs/FDD.md` | Risco | Segunda instância do worker por engano: duplicatas e ordem quebrada | TRANSCRICAO | [09:12] Diego |
| FDD-RISCO-03 | `docs/FDD.md` | Risco | Crescimento da outbox e do histórico sem limpeza | TRANSCRICAO | [09:08] Diego |
| FDD-RISCO-04 | `docs/FDD.md` | Risco | Transação de `changeStatus`, já pesada, fica mais lenta | TRANSCRICAO | [09:04] Bruno |
| FDD-RISCO-05 | `docs/FDD.md` | Risco | Secret em texto no banco exposta por acesso indevido (proposta do autor, tema da revisão de segurança) | TRANSCRICAO | [09:46] Sofia |
| FDD-RISCO-06 | `docs/FDD.md` | Risco | URL `https` apontando para endereço interno (SSRF) (observação do autor) | TRANSCRICAO | [09:23] Sofia |
| FDD-RISCO-07 | `docs/FDD.md` | Risco | Entrega duplicada por queda entre o envio e a gravação | TRANSCRICAO | [09:24] Diego |
| FDD-RISCO-08 | `docs/FDD.md` | Risco | Cliente que só verifica a primeira assinatura falha durante a rotação (consequência da proposta P-02) | TRANSCRICAO | [09:21] Sofia |
| FDD-RISCO-09 | `docs/FDD.md` | Risco | Eventos seguidos do mesmo pedido saem um por ciclo pela regra de ordem, e o último pode passar dos 10 s (observação do autor) | TRANSCRICAO | [09:12] Larissa, [09:12] Diego |
| FDD-QA-01 | `docs/FDD.md` | Questão em aberto | 18.1 Sem limite por cliente; lote e envio em sequência são o único freio (RFC QA-01) | TRANSCRICAO | [09:39] Larissa |
| FDD-QA-02 | `docs/FDD.md` | Questão em aberto | 18.1 Réplica única do worker; a regra de ordem depende disso (RFC QA-02) | TRANSCRICAO | [09:13] Diego |
| FDD-QA-03 | `docs/FDD.md` | Questão em aberto | 18.1 CRUD com qualquer role; endurecer é acrescentar `requireRole` (RFC QA-03) | TRANSCRICAO | [09:37] Sofia |
| FDD-QA-04 | `docs/FDD.md` | Questão em aberto | 18.1 Sem gancho de e-mail; o histórico guarda dados para medir o impacto (RFC QA-04) | TRANSCRICAO | [09:37] Larissa |
| FDD-PROP-01 | `docs/FDD.md` | Proposta do autor | P-01: 6 envios, o inicial e 5 retentativas | TRANSCRICAO | [09:17] Diego, [09:48] Larissa |
| FDD-PROP-02 | `docs/FDD.md` | Proposta do autor | P-02: duas assinaturas em `X-Signature` nas 24 h de convivência | TRANSCRICAO | [09:21] Sofia, [09:22] Sofia |
| FDD-PROP-03 | `docs/FDD.md` | Proposta do autor | P-03: linha movida da outbox para a DLQ; `FAILED` é "aguardando retentativa" | TRANSCRICAO | [09:08] Diego, [09:18] Diego |
| FDD-PROP-04 | `docs/FDD.md` | Proposta do autor | P-04: limite de 64KB verificado no worker, com DLQ, e não na inserção | TRANSCRICAO | [09:24] Larissa |
| FDD-PROP-05 | `docs/FDD.md` | Proposta do autor | P-05: uma linha da outbox por webhook de destino, com `event_id` próprio | TRANSCRICAO | [09:25] Diego, [09:33] Marcos |
| FDD-PROP-06 | `docs/FDD.md` | Proposta do autor | P-06: evento retido enquanto houver anterior do mesmo pedido e webhook não entregue | TRANSCRICAO | [09:12] Diego |
| FDD-PROP-07 | `docs/FDD.md` | Proposta do autor | P-07: formatos `sha256=<hex>`, `X-Timestamp` ISO 8601 e secret `whsec_` com 64 hex | TRANSCRICAO | [09:20] Sofia, [09:44] Diego |
| FDD-PROP-08 | `docs/FDD.md` | Proposta do autor | P-08: `total_cents` como único campo básico do pedido no payload | TRANSCRICAO | [09:43] Diego |
| FDD-PROP-09 | `docs/FDD.md` | Proposta do autor | P-09: toda resposta fora de 2xx é retentada; 2xx no prazo é sucesso mesmo com leitura lenta do corpo | TRANSCRICAO | [09:15] Diego, [09:42] Diego |
| FDD-PROP-10 | `docs/FDD.md` | Proposta do autor | P-10: `customerId` na query da listagem, e não no path citado na reunião; `rotate-secret` e replay sob `/api/v1` com 202 | TRANSCRICAO | [09:32] Larissa, [09:18] Diego |
| FDD-PROP-11 | `docs/FDD.md` | Proposta do autor | P-11: lote de 10, órfãos em 60 s, heartbeat de 30 s, resposta e URL até 2.048 | TRANSCRICAO | [09:08] Diego, [09:34] Marcos |
| FDD-PROP-12 | `docs/FDD.md` | Proposta do autor | P-12: métricas derivadas dos logs Pino | TRANSCRICAO | [09:29] Bruno |
| FDD-PROP-13 | `docs/FDD.md` | Proposta do autor | P-13: exclusão em cascata; outbox sem chave estrangeira para `orders`, para não bloquear `OrderService.delete` | CODIGO | `src/modules/orders/order.service.ts` |
| FDD-PROP-14 | `docs/FDD.md` | Proposta do autor | P-14: secret em texto no banco; inativo leva pendentes à DLQ; replay para inativo responde 409 | TRANSCRICAO | [09:21] Bruno, [09:46] Sofia |
| FDD-PROP-15 | `docs/FDD.md` | Proposta do autor | P-15: `events` aceita `PAID`, `PROCESSING`, `SHIPPED`, `DELIVERED` e `CANCELLED` | TRANSCRICAO | [09:33] Marcos |
| FDD-PROP-16 | `docs/FDD.md` | Proposta do autor | P-16: lote cheio sem espera, envio em sequência, sem jitter, redirect não seguido, heartbeat no loop e polling em variável | TRANSCRICAO | [09:12] Diego, [09:23] Sofia |
| FDD-PROP-17 | `docs/FDD.md` | Proposta do autor | P-17: replay reinicia o ciclo de 6 envios; nova rotação em 24 h descarta a anterior | TRANSCRICAO | [09:18] Diego, [09:21] Sofia |
| FDD-PROP-18 | `docs/FDD.md` | Proposta do autor | P-18: `timestamp` do corpo é o momento da mudança; `X-Timestamp` é o momento do envio | TRANSCRICAO | [09:43] Diego, [09:44] Diego |
| FDD-PROP-19 | `docs/FDD.md` | Proposta do autor | P-19: histórico em ordem decrescente e com `pageSize` 100, script `worker:dev`, `requestId` fora da outbox e limiares de alerta | TRANSCRICAO | [09:34] Marcos, [09:11] Larissa |
| FDD-PROP-20 | `docs/FDD.md` | Proposta do autor | P-20: replay concorrente detectado por reserva condicional da entrada da DLQ, com 409 | TRANSCRICAO | [09:35] Diego, [09:36] Sofia |
| FDD-PROP-21 | `docs/FDD.md` | Proposta do autor | P-21: `WEBHOOK_INVALID_URL` em `details` de um `400 VALIDATION_ERROR`, e não como `error.code` | TRANSCRICAO | [09:23] Sofia, [09:28] Bruno, [09:29] Larissa |
| FDD-PROP-22 | `docs/FDD.md` | Proposta do autor | P-22: `X-Timestamp` fora da assinatura; deduplicar pelo `event_id`, não usar o cabeçalho como janela antirreplay | TRANSCRICAO | [09:22] Sofia, [09:44] Diego |
| FDD-PROP-23 | `docs/FDD.md` | Proposta do autor | P-23: modelagem física (nomes, tipos, tamanhos, índice composto, `replayedById` sem FK, 64KB = 65.536 bytes) | CODIGO | `prisma/schema.prisma` |
| FDD-PROP-24 | `docs/FDD.md` | Proposta do autor | P-24: caminhos da secret no `redactPaths`, única mudança de comportamento em código compartilhado | CODIGO | `src/shared/logger/index.ts` |
| FDD-PROP-25 | `docs/FDD.md` | Proposta do autor | P-25: nomes dos códigos `WEBHOOK_*` além dos citados na reunião | TRANSCRICAO | [09:28] Bruno, [09:29] Larissa |
| FDD-PROP-26 | `docs/FDD.md` | Proposta do autor | P-26: texto-base do portal na spec, com exemplo de verificação em TypeScript | TRANSCRICAO | [09:26] Marcos, [09:40] Marcos |
| FDD-PROP-27 | `docs/FDD.md` | Proposta do autor | P-27: validação do cadastro e da edição (status sem repetição, edição com ao menos um campo, cliente e secret fora da edição, webhook criado ativo) | TRANSCRICAO | [09:33] Marcos, [09:33] Bruno, [09:31] Marcos |
