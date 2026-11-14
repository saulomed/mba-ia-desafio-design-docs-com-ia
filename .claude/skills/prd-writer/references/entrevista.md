# Roteiro de Entrevista

A entrevista existe para fechar lacunas que impediriam um requisito testável, uma métrica com número ou um escopo sem ambiguidade. Ela não existe para coletar tudo o que o usuário sabe. Este arquivo traz o roteiro por área, os sinais de que uma área está completa e as regras de condução.

## Regras de condução

- **Comece pelo inventário.** Em Modo Fontes, antes de perguntar, mostre o que a fonte já respondeu. Perguntar o que a reunião decidiu irrita quem participou dela e sugere que a fonte não foi lida.
- **Agrupe de duas a quatro perguntas relacionadas por mensagem**, com uma linha de contexto dizendo por que importam. Perguntas soltas sem motivo parecem formulário; perguntas demais de uma vez ficam sem resposta.
- **Ofereça uma proposta junto da pergunta** quando tiver uma hipótese razoável: "Pela descrição, a meta parece ser reduzir X; faz sentido? Qual número seria sucesso?". É mais fácil o usuário corrigir uma proposta do que preencher um vazio.
- **Pergunte por números, não por adjetivos.** Em vez de "precisa ser rápido?", pergunte "qual latência o cliente percebe como aceitável? segundos, minutos?".
- **Separe adiado de descartado** sempre que algo ficar de fora: "isso fica para uma próxima fase ou não faremos?". A resposta muda a seção de escopo e o que o RFC registra como alternativa.
- **Pare quando os sinais de completude estiverem atendidos.** Continuar perguntando depois disso produz detalhe de implementação que não pertence ao PRD.
- **Se o usuário não estiver disponível** ou pedir para pular, registre cada lacuna como `QA-xx` ou `PREM-xx` e siga. Diga explicitamente no relatório final quais premissas precisam de validação.

## Roteiro por área

Cada área traz as perguntas que mais frequentemente faltam e o sinal de que está completa. Em Modo Fontes, percorra as áreas só para checar lacunas.

### 1. Problema e motivação

- Quem sente o problema hoje e o que faz para contornar?
- O que custa esse contorno: tempo, dinheiro, risco, clientes perdidos?
- O que acontece se nada for feito nos próximos meses?
- Por que agora? Houve gatilho (pedido de cliente, incidente, prazo, concorrência)?

**Completa quando:** dá para escrever o problema em uma frase com ator, dor e consequência, e há pelo menos um impacto quantificado ou um gatilho datado.

### 2. Público e cenários

- Quem usa diretamente? Quem é afetado sem usar (operação, suporte, financeiro)?
- Os usuários têm perfis diferentes de uso ou permissão?
- Qual a situação típica em que a feature é acionada? E a situação de exceção mais provável?
- Há integradores ou sistemas externos como "usuários"?

**Completa quando:** cada perfil tem uma necessidade distinta que justifica existir, e há pelo menos um cenário de uso normal e um de exceção.

### 3. Objetivos e métricas

- Como saberemos, em três meses, que deu certo?
- Qual o valor atual dessa métrica (linha de base) e qual seria sucesso?
- Em quanto tempo?
- De onde vem o dado para medir? Já existe ou precisa ser criado?
- Há métrica de guarda, algo que não pode piorar?

**Completa quando:** há pelo menos um objetivo com métrica, linha de base ou marcação de "a medir", meta numérica, prazo e fonte do dado.

### 4. Escopo

- O que está claramente dentro desta versão?
- O que foi pensado e deixado de fora? Para depois ou para nunca?
- Que limites a solução tem de respeitar: prazo comercial, tamanho do time, infraestrutura disponível, compliance, orçamento?
- Há algo que o usuário assume como óbvio e que vale escrever (por exemplo, direção de uma integração, idioma, plataformas)?

**Completa quando:** há pelo menos dois itens fora de escopo com tipo e motivo, e as restrições que moldam a solução estão escritas.

### 5. Funcionalidades

- Para cada capacidade: o que o usuário faz, o que o sistema responde, o que acontece quando falha?
- Quais limites: tamanhos, quantidades, prazos, número de tentativas, formatos aceitos?
- Que dado uma capacidade precisa que outra produza?
- Dentro de uma capacidade, o que é indispensável e o que é melhoria nesta mesma versão?

**Completa quando:** cada funcionalidade tem fluxo com começo, meio e fim, limites numéricos onde cabem, e as dependências de dados entre elas estão claras.

### 6. Requisitos não funcionais

- Quanto tempo o usuário tolera esperar? Em que percentil ou condição?
- Que volume esperar: por dia, por hora, no pico?
- O que acontece se um componente externo cair? O que pode se perder e o que não pode?
- Quem pode acessar o quê? O que precisa ficar registrado para auditoria?
- Por quanto tempo os dados ficam guardados?
- Como a operação saberá que algo está errado?

**Completa quando:** cada RNF tem número e condição de medição, e os cenários de indisponibilidade têm comportamento definido.

### 7. Dependências e riscos

- De quem ou de quê a entrega depende: outro time, fornecedor, contrato, decisão pendente?
- O que mais preocupa quem está pedindo? E quem vai construir?
- Houve experiência anterior parecida que deu errado?
- Se a feature falhar em produção, qual o pior cenário plausível?

**Completa quando:** há pelo menos dois riscos com probabilidade, impacto e mitigação concreta, e as dependências externas têm impacto descrito.

### 8. Aceitação e validação

- Quem aprova a entrega e com base em quê?
- Há cliente ou usuário piloto para validar antes de liberar geral?
- Que cenários de falha precisam estar provados antes do lançamento?
- Existe convenção de testes no projeto que a feature deve seguir?

**Completa quando:** há critério objetivo de "pronto para lançar" e os cenários críticos de falha estão nomeados.

## Exemplo de mensagem de entrevista

Ilustrativo, domínio fictício:

> Para fechar a seção de objetivos preciso de números. Pela descrição, o problema central é a taxa de faltas sem aviso nas consultas. Três perguntas:
> 1. Qual a taxa de faltas hoje e qual valor vocês considerariam sucesso em três meses?
> 2. Esse dado já existe em algum relatório ou precisaria ser criado?
> 3. Há algo que não pode piorar com os lembretes, como reclamações de pacientes por excesso de mensagens?

Note o padrão: contexto em uma linha, hipótese explícita, perguntas que pedem números e fonte do dado.
