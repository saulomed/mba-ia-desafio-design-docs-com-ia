# Arquivo de Progresso

Registro determinístico, legível por máquina, do status de implementação de cada funcionalidade do PRD. Esta skill cria e faz o merge do arquivo; skills de implementação e avaliação, quando existirem no projeto, leem e escrevem nele durante suas execuções.

**Quando gerar:** somente se `CAMINHO_PROGRESSO` foi informado ou se já existe um `prd_progress.json` na pasta do PRD. Caso contrário, pule esta etapa.

As chaves e os valores de `status` ficam em inglês, pois são identificadores consumidos por outras ferramentas.

## Esquema

Estrutura de topo:

```json
{
  "schema_version": 1,
  "prd_path": "<caminho do PRD>",
  "generated_at": "<timestamp RFC 3339 UTC, definido só na primeira criação>",
  "features": {
    "F01": { },
    "F02": { }
  }
}
```

Estrutura por funcionalidade:

```json
{
  "name": "Preferências de contato",
  "priority": 1,
  "wave": 1,
  "dependencies": [],
  "status": "pending",
  "cycles": 0,
  "failure_reason": null,
  "report_path": null,
  "started_at": null,
  "updated_at": "2026-05-02T14:30:00Z",
  "completed_at": null
}
```

## Semântica dos campos

- `name`, `priority`, `wave`, `dependencies`: copiados da tabela da seção 10.2 do PRD. Atualizados a cada regeneração.
- `status`, um de:
  - `pending` (terminal): não iniciada.
  - `implementing` (transitório): implementação em andamento. Raramente visível em um arquivo em repouso.
  - `implemented` (checkpoint): implementação concluída, aguardando veredito da avaliação.
  - `done` (terminal): avaliação aprovada.
  - `fail` (terminal): avaliação reprovada de forma definitiva, implementação abortada ou orçamento de tentativas esgotado.
  - `pr-blocked` (terminal): implementação válida, mas o PR não pôde ser aberto por conflito de merge não resolvido automaticamente. Resolução humana.
  - `removed` (terminal): existia em uma versão anterior do PRD e não está na atual.
- `cycles`: número de ciclos de correção consumidos. Sempre maior ou igual a zero.
- `failure_reason`: obrigatório em `fail` e `pr-blocked`; texto curto (até 200 caracteres) com a causa. Pode persistir em `removed` como registro forense. `null` nos demais.
- `report_path`: caminho do último relatório de avaliação, quando houver.
- `started_at`: quando o status saiu de `pending` pela primeira vez. Volta a `null` só quando uma funcionalidade `removed` ressuscita.
- `updated_at`: última escrita na entrada. Sempre preenchido.
- `completed_at`: quando o status virou `done`; `null` nos demais.

## Invariantes

- Todo ID da seção 7 do PRD existe como chave em `features`. O inverso não é exigido, pois entradas `removed` persistem.
- Todo ID em `dependencies` existe como chave em `features`, atual ou `removed`.
- Todos os timestamps em UTC, RFC 3339 (`2026-05-02T14:30:00Z`).

## Contrato de criação e merge

Use o timestamp atual em RFC 3339 UTC como `agora`.

**Caso A: o arquivo não existe.**
- `schema_version: 1`, `prd_path` com o caminho do PRD, `generated_at: agora`.
- Para cada funcionalidade da tabela: `name`, `priority`, `wave`, `dependencies` do PRD; `status: "pending"`, `cycles: 0`, campos opcionais `null`, `updated_at: agora`.

**Caso B: o arquivo existe (regeneração do PRD).**
- Leia e faça o parse. Se falhar, veja "Arquivo corrompido".
- Se `schema_version` for maior que 1, pare e reporte. Não rebaixe a versão nem escreva nada.
- Preserve `generated_at`. Atualize `prd_path`.
- Por ID (IDs são imutáveis):
  - **No PRD antigo e no novo, status diferente de `removed`:** preserve `status`, `cycles`, `failure_reason`, `report_path`, `started_at`, `completed_at`. Atualize `name`, `priority`, `wave`, `dependencies`. Mude `updated_at` para `agora` somente se algum campo atualizado mudou de fato.
  - **No PRD antigo e no novo, status `removed` (ressuscitada):** reinicie o ciclo de vida com `status: "pending"`, `cycles: 0` e campos opcionais `null`. Atualize os campos do PRD e `updated_at: agora`.
  - **Só no PRD novo:** crie como no Caso A.
  - **Só no arquivo antigo (removida do PRD):** mantenha a entrada. Se o status ainda não for `removed`, defina `status: "removed"` e `updated_at: agora`, preservando os demais campos como registro forense. Nunca apague entradas.

**Escrita atômica:** grave em `{CAMINHO_PROGRESSO}.tmp` e renomeie para `{CAMINHO_PROGRESSO}`, para que nenhuma outra ferramenta leia um arquivo parcial.

**Verificação:** releia o arquivo, confirme que é JSON válido e que contém todos os IDs da seção 7.

**Arquivo corrompido:** se o arquivo existir mas não for JSON válido, não sobrescreva em silêncio. Renomeie para `{CAMINHO_PROGRESSO}.broken-<timestamp-unix>`, crie um novo como no Caso A e informe o usuário do caminho preservado.
