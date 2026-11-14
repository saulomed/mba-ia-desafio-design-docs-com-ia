# Extração de Fontes

Guia para transformar transcrições, atas, tickets e código em um inventário classificado antes de escrever o PRD. O objetivo é impedir duas falhas típicas de documentos gerados por IA: inventar o que a fonte não disse e promover a requisito o que a fonte descartou.

## Tipos de fonte e formato de localização

| Fonte | Rótulo | Formato da localização | Exemplo |
| --- | --- | --- | --- |
| Transcrição ou ata de reunião | `TRANSCRICAO` | `[hh:mm] Nome do falante` | `[14:12] Ana` |
| Código ou configuração do projeto | `CODIGO` | caminho relativo do arquivo, opcionalmente com símbolo | `src/appointments/appointment.service.ts` (`confirm`) |
| Documento existente | `DOCUMENTO` | caminho e seção | `docs/RFC.md#alternativas` |
| Ticket ou issue | `TICKET` | identificador | `PROJ-123` |
| Resposta do usuário na entrevista | `USUARIO` | data da conversa ou "entrevista" | `entrevista` |

Regras de localização:
- Use o timestamp do trecho em que a informação **foi decidida ou confirmada**, não o primeiro em que foi mencionada. Se a decisão se consolidou ao longo de vários trechos, cite o de fechamento e, se útil, o de origem (`[14:05] Paulo; [14:12] Ana`).
- Para código, cite apenas arquivos que existem. Verifique o caminho antes de citar.
- Nunca cite uma localização que você não leu.

## Classificação dos itens

Leia a fonte inteira uma vez antes de classificar. Decisões são frequentemente revistas mais adiante na mesma reunião.

| Categoria | Sinais típicos na fonte | Destino no PRD |
| --- | --- | --- |
| **Decisão fechada** | "tá decidido", "fechado", "vamos com", concordância explícita de quem decide | Seção 9 (efeito para o produto) e, se técnica, apenas referência ao ADR/RFC |
| **Requisito funcional** | "o cliente precisa", "tem que permitir", "o sistema deve" | Seção 7 |
| **Requisito não funcional** | prazos, latência, volume, disponibilidade, segurança, auditoria, retenção | Seção 8 |
| **Restrição** | "não pode", "obrigatório", limite de time, prazo comercial, compliance, infraestrutura disponível | Seção 5 (premissas e restrições) ou 8 |
| **Métrica ou meta** | números ligados a sucesso, SLA, prazo de negócio | Seção 4 |
| **Descartado** | "fora de questão", "não rola", "overengineering", "não vamos fazer" | Seção 5, Fora de escopo, tipo **Descartado** |
| **Adiado** | "fica pra fase 2", "depois a gente vê", "fora do escopo dessa feature", "não agora" | Seção 5, Fora de escopo, tipo **Adiado** |
| **Questão em aberto** | pergunta sem resposta, "precisa validar com", "não sei se", divergência sem fechamento | Seção 9, Questões em aberto |
| **Risco** | "e se", "o problema é que", preocupação de segurança, operação ou prazo | Seção 11 |
| **Detalhe técnico secundário** | formato de payload, nomes de cabeçalhos, valores de timeout, índices | Fora do PRD (vai para FDD/ADR). No PRD, só se tiver efeito visível ao usuário |
| **Gancho com o código** | menção a tabela, serviço, método, fluxo existente | Seção 1 (contexto) e seção 14; o detalhe vai para o FDD |

## Armadilhas comuns

- **Alternativa descartada vira requisito:** se a reunião rejeitou uma tecnologia ou abordagem, ela aparece apenas em "Fora de escopo" (e na RFC como alternativa considerada).
- **Opinião individual vira decisão:** uma proposta só é decisão quando há fechamento explícito ou concordância de quem decide. Proposta sem fechamento é questão em aberto.
- **Número aproximado vira contrato:** "uns 30 dias" deve ser registrado como dito e, se crítico, sinalizado como premissa.
- **Detalhe de implementação no PRD:** nomes de tabelas, endpoints e headers pertencem ao FDD. O PRD diz o que o usuário recebe e com que garantia.
- **Informação de fase futura misturada com a atual:** o que foi adiado não entra nem como "Full Scope". Escopo completo é só para o que pertence a esta versão.
- **Fonte lida pela metade:** uma decisão no início pode ser revertida no fim. Classifique só depois da leitura completa.

## Formato do inventário

Apresente ao usuário, antes da entrevista, um resumo neste formato:

```markdown
**Inventário da fonte** (N itens)

| Categoria | Item | Localização |
| --- | --- | --- |
| Decisão | Lembrete de consulta enviado 24 h antes do horário | [14:12] Ana |
| Descartado | Lembrete por ligação telefônica automática | [14:05] Paulo |
| Adiado | Reagendamento pelo próprio paciente | [14:31] Ana |
| Questão em aberto | Se o paciente pode desativar os lembretes | [14:40] Júlia |
| Gancho com o código | Status de consulta já existente com estado CONFIRMADA | `src/appointments/appointment.status.ts` |

**Conflitos:** ...
**Lacunas que afetam critérios de aceite ou métricas:** ...
```

Os exemplos acima são ilustrativos do formato. Use apenas o conteúdo da fonte real.
