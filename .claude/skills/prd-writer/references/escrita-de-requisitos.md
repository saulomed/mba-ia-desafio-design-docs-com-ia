# Escrita de Requisitos

Como redigir cada tipo de item do PRD para que seja específico, testável e na altura certa. A estrutura das seções está em `template-prd.md`; aqui está a qualidade do texto que vai dentro delas.

## O teste básico de qualquer item

Um item do PRD está bem escrito quando um engenheiro e um QA, lendo separadamente, descreveriam a mesma entrega e o mesmo teste. Três perguntas rápidas:

1. **Dá para reprovar?** Se nenhuma entrega poderia violar o item, ele não é requisito. "O sistema deve ser confiável" não reprova nada; "nenhum lembrete se perde se o provedor de SMS ficar indisponível por até 2 h" reprova.
2. **Tem um só assunto?** Um "e" no meio costuma esconder dois requisitos com prioridades ou testes diferentes. Separe.
3. **Diz o quê, não o como?** "Gravar o lembrete na mesma transação da confirmação" é implementação. "Nenhuma consulta confirmada fica sem lembrete programado" é o requisito que motivou essa implementação.

## Palavras que pedem número

Quando um destes termos aparecer, substitua por medida ou pergunte o número: rápido, lento, em tempo real, imediato, escalável, alta disponibilidade, seguro, robusto, confiável, intuitivo, amigável, fácil, simples, grande volume, muitos usuários, eventualmente, em breve, periodicamente, adequado, suficiente, razoável, otimizado, melhor, moderno.

Exemplos de substituição:

| Vago | Específico |
| --- | --- |
| lembrete enviado na hora certa | lembrete entregue entre 23 h e 25 h antes da consulta em 95% dos casos |
| suportar grande volume | programar 2.000 lembretes por dia no pico sem atrasar a confirmação de consultas além do RNF-01 |
| dados seguros | telefone e e-mail do paciente visíveis só para recepção e médico responsável; nunca em logs |
| reenvio adequado | até 3 tentativas com intervalo crescente, depois marcado como falha definitiva e visível à recepção |

## Requisitos funcionais

Padrões úteis, do mais simples ao condicional:

- **Sempre:** "O sistema deve <ação observável> <complemento>."
- **Orientado a evento:** "Quando <gatilho>, o sistema deve <ação> <em até prazo, se houver>."
- **Orientado a estado:** "Enquanto <condição>, o sistema deve <comportamento>."
- **Comportamento indesejado:** "Se <falha ou condição anormal>, o sistema deve <resposta>."
- **Opcional ou configurável:** "Quando <configuração> estiver ativa, o sistema deve <comportamento>."

Nos blocos **Capacidades** e **Experiência** de cada funcionalidade:

- Capacidades listam limites e regras: tamanhos, quantidades, formatos, prazos, estados possíveis, quem pode fazer o quê. Prefira tabela ou bullets curtos, cada um com um número ou uma regra.
- Experiência descreve o fluxo na ordem em que acontece, do gatilho ao resultado, incluindo o que o usuário ou integrador vê em cada passo, validações e mensagens. Para funcionalidades de sistema sem tela, descreva o comportamento observável por quem integra ou opera: o que recebe, em que ordem, o que vê quando falha.
- Tratamento de erros nomeia o cenário, o comportamento do sistema e o que o usuário ou operador vê. Não invente códigos técnicos; isso é FDD. Diga o efeito: "o lembrete é marcado como falha definitiva e aparece na lista de pendências da recepção".

Exemplo ruim: "Permitir configurar lembretes de forma flexível."

Exemplo bom: "O paciente escolhe até 2 canais de lembrete (e-mail, SMS) e informa um contato por canal. Contatos em formato inválido são recusados no ato com mensagem indicando o formato esperado. Sem nenhum canal escolhido, a recepção vê o paciente como 'sem lembrete' na agenda."

## Histórias de usuário

Formato: "Como <ator específico>, quero <ação concreta no produto> para <benefício verificável>."

- O ator é um perfil da seção 3, não "usuário" genérico, a menos que o público seja homogêneo.
- A ação é algo que se faz no produto, não uma meta de vida: "quero confirmar presença respondendo ao lembrete" e não "quero ser mais organizado".
- O benefício explica por que a ação importa; se não houver benefício claro, a história talvez não devesse existir.
- Funcionalidades sem interação direta usam "Como o sistema" ou "Como integrador".
- Histórias não repetem as Capacidades. Elas dão a perspectiva de quem usa; o detalhe fica na seção 7.

## Métricas e objetivos

Um objetivo bem escrito tem verbo de ação e resultado verificável; a métrica tem quatro partes:

| Parte | Pergunta | Exemplo |
| --- | --- | --- |
| Indicador | o que medir | taxa de faltas sem aviso prévio |
| Linha de base | quanto é hoje, ou "a medir" | 18% das consultas agendadas no último trimestre |
| Meta | quanto queremos | 10% ou menos |
| Prazo e condição | até quando, medido como | em 3 meses após o lançamento, pelo relatório mensal de consultas |

Distinga **métrica de resultado** (o que muda para o usuário ou negócio: faltas reduzidas, horários ociosos recuperados) de **métrica de saída** (o que o sistema faz: lembretes enviados). Prefira pelo menos uma de resultado; saída sozinha mede esforço, não valor. Inclua uma **métrica de guarda** quando houver algo que não pode piorar (reclamações por excesso de mensagens, tempo de confirmação de consulta, custo de SMS).

## Requisitos não funcionais

Cada RNF tem categoria, requisito e critério mensurável com número e condição:

- Desempenho: latência em percentil (p50, p95, p99) e carga em que vale.
- Disponibilidade e confiabilidade: o que não pode se perder, tempo máximo de indisponibilidade tolerado, comportamento quando uma dependência cai.
- Segurança: autenticação, autorização, proteção de segredos, verificação de origem, auditoria.
- Observabilidade: o que a operação precisa ver e em quanto tempo detecta um problema.
- Volume e escalabilidade: números de hoje e crescimento esperado.
- Retenção e dados: por quanto tempo guardar, quando apagar ou arquivar.
- Compatibilidade: versões, plataformas, contratos existentes que não podem quebrar.

RNFs são compromissos de produto. Eles citam números livremente, mas não citam a solução: "nenhum lembrete se perde se o provedor de SMS ficar fora por 2 h" é RNF; "usar fila com persistência" é decisão técnica que vai para o ADR.

## Critérios de aceitação

Cada critério é uma verificação com resultado binário. Formato recomendado: "Dado <contexto>, quando <ação>, então <resultado observável com número ou estado>."

- Cubra sucesso e falha para cada funcionalidade. Um critério só de caminho feliz deixa o comportamento de erro ao acaso.
- Use os mesmos números dos requisitos. Se o RNF diz 2 h, o critério diz 2 h.
- Critérios de integração entre funcionalidades testam que o dado declarado em Consome chega de fato: "canal escolhido em F01 é o usado no envio de F03".
- Evite critérios que só repetem o requisito com "deve funcionar". Diga como se verifica.

Exemplo ruim: "Lembretes funcionam corretamente."

Exemplo bom: "Dado um paciente com SMS como canal, quando a consulta é confirmada para daqui a 48 h, então um lembrete é programado e o SMS chega entre 23 h e 25 h antes da consulta, contendo data, hora e nome do profissional."

## Riscos

Um risco é um evento incerto com consequência. Escreva "<evento> pode causar <consequência>", classifique probabilidade e impacto, e dê mitigação com ação, responsável quando a fonte disser, e gatilho quando aplicável. "Monitorar" não é mitigação; "alertar a recepção quando mais de 50 lembretes do dia estiverem pendentes a 2 h do envio" é.

## Fora de escopo

Cada item diz o que não será feito, se foi **descartado** (não planejado) ou **adiado** (válido, fase futura), e o motivo em uma frase. Sem o motivo, a próxima pessoa reabre a discussão. Sem o tipo, o RFC não sabe se registra como alternativa rejeitada ou como evolução prevista.

## Premissas e questões em aberto

- **Premissa** é algo que o PRD assume como verdade sem confirmação: "PREM-02: o volume de pico é de 2.000 lembretes por dia (estimativa da recepção, a confirmar com a agenda dos últimos 6 meses)". Toda inferência sua em Modo Descoberta é premissa.
- **Questão em aberto** é uma decisão que alguém precisa tomar: pergunta, por que importa, quem decide e até quando, se a fonte disser. "QA-01: o paciente pode desativar os lembretes sozinho ou só a recepção? Importa porque define F01 inteira. Decisão da coordenação até o início do desenvolvimento."

Uma lacuna marcada como premissa ou questão é informação útil. A mesma lacuna preenchida com um palpite apresentado como fato vira requisito sem dono, que será implementado com o mesmo empenho que os reais.
