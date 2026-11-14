# Manual da spec OpenAPI dos webhooks

O arquivo [`webhooks.openapi.yaml`](webhooks.openapi.yaml) descreve, em OpenAPI 3.1, os contratos da feature de webhooks de notificação de pedidos: os sete endpoints da API e a requisição que o OMS envia ao endpoint do cliente. Ele detalha a [seção 7 do FDD](../FDD.md#7-contratos-públicos), que resume os mesmos contratos. A spec é a referência de formato de campos, validação e exemplos de erro. Fluxos, transações e comportamento do worker ficam no FDD.

A API ainda não existe no código. A spec descreve o que será implementado e acompanha o status do FDD, "Em revisão".

## O que está na spec

| Seção | Conteúdo |
| --- | --- |
| `info` | Versão, regras comuns e as convenções de rastreabilidade |
| `servers` | Ambiente local, `http://localhost:3000/api/v1` |
| `paths` | Os sete endpoints da API |
| `webhooks` | `orderStatusChanged`, a requisição que o endpoint do cliente recebe, com o texto-base para o portal de desenvolvedores |
| `components/schemas` | Modelos de requisição, resposta, erro e evento, com as regras de validação |
| `components/parameters` e `components/responses` | Parâmetros e respostas de erro reusados entre os endpoints |
| `components/examples` | Exemplos de requisição, de resposta e de cada erro |

| `operationId` | Método e caminho | Seção do FDD |
| --- | --- | --- |
| `createWebhook` | `POST /webhooks` | 7.1 |
| `listWebhooks` | `GET /webhooks` | 7.2 |
| `updateWebhook` | `PATCH /webhooks/{id}` | 7.3 e 6.7 |
| `deleteWebhook` | `DELETE /webhooks/{id}` | 7.4 e 6.7 |
| `rotateWebhookSecret` | `POST /webhooks/{id}/rotate-secret` | 7.5 e 6.6 |
| `listWebhookDeliveries` | `GET /webhooks/{id}/deliveries` | 7.6 |
| `replayDeadLetter` | `POST /admin/webhooks/dead-letter/{id}/replay` | 7.7 e 6.5 |
| `orderStatusChanged`, em `webhooks` | `POST <url do webhook>` | 7.8 e 10.3 |

## Como visualizar

O GitHub mostra o YAML como texto, sem renderizar a spec. Para navegar pelos endpoints, schemas e exemplos, use uma das opções abaixo. Nenhuma delas instala dependências no projeto.

**Redoc em HTML estático.** O Redocly CLI gera uma página única, que abre em qualquer navegador. A pasta `dist/` já está no `.gitignore`.

```bash
npx @redocly/cli@2.76.0 build-docs docs/openapi/webhooks.openapi.yaml --output dist/webhooks-api.html
```

**Swagger UI com Docker.** Sobe a interface em `http://localhost:8080`, com o evento de saída na seção "Webhooks". O botão "Try it out" só vai funcionar depois que a API for implementada; até lá, as chamadas para `localhost:3000` falham.

```bash
docker run --rm -p 8080:8080 -e SWAGGER_JSON=/spec/webhooks.openapi.yaml -v "$(pwd)/docs/openapi:/spec" swaggerapi/swagger-ui
```

**No editor.** Extensões de OpenAPI do VS Code, como a OpenAPI (Swagger) Editor, da 42Crunch, mostram a spec com navegação e pré-visualização ao lado do YAML.

## Como validar

```bash
npx @redocly/cli@2.76.0 lint docs/openapi/webhooks.openapi.yaml
```

O resultado esperado é "Your API description is valid", com dois avisos conhecidos e aceitos:

| Aviso | Por que fica |
| --- | --- |
| `info-license` | O `package.json` do projeto não declara licença, e a spec não inventa uma |
| `no-server-example.com` | O único servidor conhecido é o ambiente local; as fontes não citam URL de outros ambientes |

Qualquer outro aviso ou erro deve ser corrigido antes do commit.

## Convenções

- **Rastreabilidade.** As descrições seguem a convenção do FDD: `[hh:mm] Nome` remete à `TRANSCRICAO.md`, e **(AUTOR, P-xx)** marca uma proposta do autor, listada na seção 18.2 do FDD com o número indicado.
- **Nomes.** O JSON da API usa camelCase, como o resto do OMS, e o evento enviado ao cliente usa snake_case ([09:43] Diego). Schemas usam PascalCase em inglês, como as classes do código.
- **Exemplos.** Os exemplos são os mesmos da seção 7 do FDD e usam os mesmos ids em toda a spec: um cliente, um webhook, um pedido (`ORD-000123`), um evento e uma entrada da DLQ.
- **Vetor de teste.** O exemplo de `X-Signature` é o HMAC-SHA256 real do corpo de exemplo, serializado sem espaços, com a secret do exemplo de criação. O valor com duas assinaturas usa também a secret do exemplo de rotação. Os dois servem para testar a verificação no cliente e o teste T-02 do FDD.

## Como manter

1. Uma mudança de contrato altera a spec e, no mesmo commit, a seção 7 do FDD (exemplos e matriz de status codes), além de uma linha no histórico de revisão do FDD (seção 19) com o motivo.
2. Os `operationId` não mudam de nome: o FDD e o tracker se referem a eles.
3. `info.version` sobe a cada mudança de contrato.
4. O lint roda antes de cada commit que toca a spec.

## Fora da spec

- Os endpoints que já existem no OMS (`auth`, `users`, `customers`, `products`, `orders`). O JWT exigido pelos endpoints de webhooks vem de `POST /api/v1/auth/login`, que não está descrito aqui.
- A listagem da DLQ, que não foi discutida na reunião. O `id` do replay vem do log `webhook_dead_lettered` ou de consulta ao banco (FDD, seção 3).
