---
name: atendimento-diario
description: Gera mensagens consultivas de atendimento para clientes em WhatsApp/grupos. Use para atendimento, update, relatório, status, pauta de call, pós-call, follow-up, cobrança, reporte de lead e comunicação de rotina; investigar contexto, tom real, histórico recente e dados faltantes antes de escrever.
---

# Atendimento Diário

Gerar mensagens de atendimento que pareçam humanas, consultivas e específicas para cada cliente. A skill nunca deve funcionar como template fixo. O formato serve ao contexto, ao cliente e ao objetivo da conversa.

## Princípios Centrais

- Ser consultivo antes de ser produtivo: quando faltar dado essencial, perguntar.
- Não robotizar: evitar estrutura repetida, saudação repetida, fechamento repetido e frase com cara de IA.
- Personalizar por cliente: cada cliente tem um jeito próprio de receber update, cobrança, relatório e alinhamento.
- Separar "o que dizer" de "como dizer": o contexto define o conteúdo; o perfil e o histórico definem o tom.
- Não inventar dados, datas, status, resultados, responsáveis, links ou prazos.
- Preservar clareza operacional: mensagem bonita sem próximo passo claro não serve.
- Usar acentuação correta em toda mensagem final, em todo contexto criado e em toda atualização de `perfil.md`/`contexto.md`.

## Estrutura Esperada do Cliente

Tratar como cliente principal apenas pastas em `clientes/<cliente>/` que tenham `contexto.md`, `perfil.md`, `processos.md`, `historico/` ou arquivos de cliente. Pastas de app/deploy dentro de `clientes/` podem ter `.vercel`, `index.html` e assets, mas não devem ser tratadas como cliente principal se não tiverem contexto/perfil.

Ler, quando existirem:

```text
clientes/<cliente>/
  perfil.md
  historico/atendimentos-log.md
  contexto.md
  processos.md
  campanhas.md
  aprendizados.md
  historico/
  planejamento/
  entregas/
  outputs/
```

Ordem padrão de leitura:

1. `perfil.md`
2. `historico/atendimentos-log.md`
3. `contexto.md`
4. `processos.md`
5. `campanhas.md`
6. `aprendizados.md`
7. arquivos recentes em `historico/`
8. arquivos relevantes em `planejamento/`, `entregas/` e `outputs/`

## Bootstrap de Cliente

Se `clientes/<cliente>/` não existir, criar antes de gerar qualquer mensagem:

```text
clientes/<cliente>/
  contexto.md
  perfil.md
  historico/atendimentos-log.md
```

`contexto.md` mínimo:

```markdown
# Contexto: <cliente>

## Estado atual
- Criado a partir do pedido de atendimento.

## Informações conhecidas
- <registrar apenas o que o usuário informou>
```

`perfil.md` mínimo:

```markdown
# Perfil: <cliente>

## Como falar com este cliente
- Ainda sem histórico suficiente. Usar tom consultivo, humano e claro.

## Tom de comunicação no grupo
- Formalidade: a calibrar
- Emojis: a calibrar
- Tamanho médio: a calibrar
- Saudação: a calibrar
- Fechamento: a calibrar

## Padrão real de atendimento
- Ainda sem mensagens reais suficientes.

## Correções recorrentes do humano
- Ainda sem correções registradas.

## Rotação recente de atendimento
- Ainda sem atendimentos registrados.
```

Criar `historico/atendimentos-log.md` com:

```markdown
# Log de atendimentos gerados - <cliente>

> Registrar cada mensagem proposta pela skill. Quando houver WhatsApp conectado, comparar depois com o que foi realmente enviado.
```

## Inteligência de Contexto

Antes de gerar qualquer mensagem, montar mentalmente uma diretriz de comunicação:

```text
Cliente:
Pessoa/grupo:
Objetivo da mensagem:
Período:
Tom real:
Nível de detalhe:
Assuntos sensíveis:
Saudações recentes a evitar:
Fechamentos recentes a evitar:
Dados obrigatórios que faltam:
```

Usar `perfil.md` como fonte principal do jeito de falar. Usar `contexto.md`, `processos.md`, `campanhas.md`, `aprendizados.md`, `historico/`, `planejamento/`, `entregas/` e `outputs/` para entender o que precisa ser dito.

Se WhatsApp estiver conectado, ler as últimas mensagens reais do grupo/contato antes de calibrar o tom. Priorizar o que foi realmente enviado em WhatsApp acima do que a skill gerou anteriormente.

## Regra de Pergunta Obrigatória

Não gerar a mensagem final se faltar qualquer contexto essencial para o tipo de atendimento pedido.

Perguntar antes de gerar quando faltar:

- Cliente.
- Tipo de atendimento.
- Período de relatório, quando o pedido mencionar resultado, semana, mês, performance, parcial ou fechamento.
- Destinatário/tom, quando houver risco de falar com a pessoa errada ou em nível inadequado de formalidade.
- Dados numéricos obrigatórios, quando o pedido exigir métricas.
- Status real, quando o pedido for sobre tarefas, entregas, prazos ou pendências e o contexto local não deixar claro.
- Data/hora, quando for confirmação de call, agenda, lembrete, pré-call ou pós-call com próximos passos.
- Link, arquivo ou entrega mencionada pelo usuário mas não encontrada.
- Planilha de backup ou fonte de lead, quando o pedido for reportar lead e a base não estiver disponível.
- Decisão em caso de divergência entre fontes.

Fazer no máximo 1 a 3 perguntas objetivas por vez. Se houver muitas lacunas, perguntar primeiro a lacuna que mais muda a mensagem.

Exemplos:

- Pedido: "gera um relatório para a Gigaclima"
  Resposta: "Qual período você quer usar no relatório da Gigaclima: semana atual, última semana fechada ou mês de agosto até hoje?"
- Pedido: "manda confirmação de call para Nuveto"
  Resposta: "Qual data/horário da call e qual pauta principal eu devo confirmar?"
- Pedido: "reporta o lead novo da Meca"
  Ação: procurar lead em planilha de backup/contexto. Se não encontrar, perguntar: "Você consegue me enviar a planilha de backup ou os dados do lead para eu reportar com contexto?"

## Pedido Amplo: Menu Consultivo

Quando o usuário disser algo amplo como "quero um atendimento para <cliente>", não escrever a mensagem direto.

Primeiro investigar o cliente e responder com opções baseadas em:

- padrão do cliente em `perfil.md`;
- assuntos recentes em `historico/`;
- frentes abertas em `contexto.md`, `processos.md`, `campanhas.md` e `planejamento/`;
- entregas recentes em `entregas/` e `outputs/`;
- tarefas/status ao vivo, se ferramenta conectada e pertinente;
- leads recentes em planilha de backup ou dados de CRM, se houver.

Formato da resposta:

```markdown
Para <cliente>, hoje faz sentido seguir por um destes caminhos:

- Relatório semanal de resultados - porque <motivo contextual>.
- Status geral de tarefas - porque <motivo contextual>.
- Agenda/confirmação de call - porque <motivo contextual>.
- Reporte de lead novo - porque <motivo contextual>.
- Follow-up de <demanda específica encontrada no contexto> - porque <motivo contextual>.

Qual caminho você quer que eu gere?
```

As opções devem variar por cliente e contexto. Não oferecer sempre a mesma lista. Se o contexto indicar uma prioridade clara, dizer:

```markdown
Minha recomendação: <opção>, porque <motivo>.
```

Ainda assim, esperar escolha do usuário antes de escrever, exceto se o pedido já especificar tipo, período e objetivo.

## Tipos de Atendimento

### Relatório Semanal ou Periódico

Usar quando o usuário pedir resultados, performance, fechamento, parcial ou resumo de período.

Contexto obrigatório:

- período;
- fonte dos números ou números fornecidos;
- leitura do resultado;
- próximos passos.

Se faltar período, perguntar. Se faltar número e não houver fonte conectada confiável, perguntar ou gerar apenas uma mensagem sem métricas se o usuário aprovar.

O relatório deve parecer um atendimento consultivo, não uma tabela colada. Adaptar densidade ao contexto:

- **Curto:** usar quando o cliente precisa de visão rápida e poucos números.
- **Completo:** usar quando há meta, leitura comercial, pendências, backlog e próximos passos.
- **Híbrido:** usar quando os números são simples, mas existe uma decisão ou cobrança importante.

Usar emojis quando melhorarem leitura no WhatsApp e combinarem com o cliente. Bons marcadores: `📝`, `📊`, `🔁`, `🗂️`, `✅`, `⚠️`. Não transformar todo bloco em carnaval visual. O emoji serve como sinalização, não como enfeite.

Estrutura curta possível:

```text
Passando para trazer a visão dos nossos resultados da última semana (<período>):

📝 RESULTADOS DA SEMANA (<período>) 📝

- Investimento: <valor>
- Leads: <número>
- Custo por Clique (CPC): <valor>
- Custo por Lead (CPL): <valor>

📊 FOCO E PRÓXIMOS PASSOS 📊

<leitura objetiva: o que aconteceu, qual ação foi tomada e qual foco agora.>
```

Estrutura completa possível:

```text
Como prometido, segue o relatório das campanhas.

📊 Resultados parciais do mês de <mês>
Investido: <valor>
Leads: <número> (<contexto de meta, se houver>)
<métricas específicas do cliente, se houver>

📝 Contexto
<leitura consultiva: qualidade do resultado, relação com meta, ponto de atenção e impacto operacional.>

🔁 <pendência ou frente 1 com responsável claro>

🔁 <pendência ou frente 2 com próximo passo claro>

🔁 <pendência ou frente 3 com prazo claro>
```

Estrutura com backlog e convite para alinhamento:

```text
📊 CAMPANHAS (<período>)
Investido: <valor>
Mensagens iniciadas / leads / MQLs: <número>
Custo por Lead: <valor>

📝 CONTEXTO
<leitura sobre performance, causa provável, ajuste feito e risco/oportunidade.>

🗂️ BACKLOG DA SEMANA
→ <ação em andamento + por que importa>
→ <próxima entrega + critério de acompanhamento>

<ponte consultiva para decisão ou reunião, se fizer sentido.>

<pergunta objetiva com opções de horário ou próximo passo.>
```

Regras de escrita para relatórios:

- Começar direto quando o relacionamento permitir: "Como prometido..." ou "Passando para trazer...".
- Usar "Contexto" para transformar número em leitura, não para repetir métricas.
- Em resultado bom, comemorar com sobriedade e conectar ao próximo gargalo.
- Em resultado ruim, nomear causa provável + ação tomada + foco de acompanhamento.
- Em pendência comercial, explicar o impacto: sem preenchimento de planilha/CRM, a visibilidade fica limitada.
- Puxar o cliente para decisão quando fizer sentido: pedir horário, aprovação, retorno ou validação.
- Não usar sempre "RESULTADOS / CONTEXTO / BACKLOG"; variar os títulos e o volume conforme o cliente.

### Agenda ou Confirmação de Call

Usar para validar agenda, lembrar reunião, confirmar horário ou preparar check-in.

Contexto obrigatório:

- data, se houver;
- horário;
- pauta;
- objetivo da call;
- quem precisa participar, se relevante.

Não inventar link. Se o link não estiver disponível, dizer que será enviado perto do horário ou perguntar.

Estrutura possível:

```text
🗓️ AGENDA 🗓️
Nós teremos nosso check-in mensal, onde vamos apresentar o resultado do mês de <mês> e o andamento/próximos passos das tarefas do mês de <mês seguinte>.

Podemos confirmar essa agenda às <horário>?
```

Regras:

- Ser simples e direto quando a agenda já estiver validada.
- Nomear o objetivo da call, não só "nossa reunião".
- Se houver check-in mensal, conectar resultado do mês anterior + tarefas/próximos passos do mês atual.
- Se o horário não estiver confirmado, perguntar de forma objetiva.

### Status Geral de Tarefas

Usar para updates operacionais, entregas, pendências, aprovações, prazos e visão geral da semana.

Contexto obrigatório:

- tarefas ou frentes;
- status real;
- responsável quando houver pendência;
- prazo quando for prometido.

O status deve carregar contexto em cada item. Não fazer lista seca. Explicar o que está acontecendo, por que aquele status importa e o próximo passo.

Estrutura fluida possível:

```text
Passando uma atualização geral:

✅ <frente 1>

<status + contexto + prazo/responsável quando houver.>

✅ <frente 2>

<status + contexto + motivo da decisão ou investigação.>

✅ <frente 3>

<status + próximo passo + impacto esperado.>
```

Quando houver tarefas em aprovação e execução, usar blocos separados:

```text
🔄 PENDENTES APROVAÇÃO 🔄

-- <entrega pendente + contexto curto>:
<link>

-- <outra entrega pendente + contexto curto>:
<link>

📊 EM EXECUÇÃO 📊

-- <frente em execução + status claro>

-- <frente estratégica em estudo + próximo passo>
```

Regras:

- Usar `✅` para frentes encaminhadas, concluídas ou bem endereçadas.
- Usar `🔄 PENDENTES APROVAÇÃO 🔄` quando o foco for destravar o cliente.
- Usar `📊 EM EXECUÇÃO 📊` para frentes internas já andando.
- Incluir links inline logo abaixo do item quando o objetivo for aprovação.
- Se uma previsão depender de investigação, explicar a causa: "porque estamos investigando a melhor forma de...".
- Quando uma frente de mídia estiver performando bem, explicar como isso afeta a próxima decisão: começar com baixo investimento, proteger performance, calibrar sinal etc.

### Reporte de Lead Novo

Usar quando entrar um lead relevante, quando o usuário pedir para reportar lead ou quando o contexto indicar que vale sinalizar uma oportunidade.

Fonte prioritária:

1. planilha de backup do cliente;
2. CRM ou export em `dados/`;
3. contexto enviado pelo usuário;
4. WhatsApp, se conectado e houver mensagem com os dados.

Se a planilha de backup ou fonte do lead não estiver disponível, perguntar:

```text
Você consegue me enviar a planilha de backup ou os dados do lead para eu reportar com contexto?
```

Contexto obrigatório:

- nome;
- contato disponível;
- origem/interesse;
- leitura de qualificação;
- pedido de retorno para acompanhar qualidade do atendimento.

Estrutura possível:

```text
✅ NOVO LEAD

Entrou um lead hoje que parece bem quente:

<nome>
<telefone>
<e-mail>
<CNPJ, se houver>

Ele veio buscando por <interesse> e comentou <sinal de intenção>.

Pelo contexto e pelo e-mail comercial, parece ser uma oportunidade bem qualificada e com intenção mais imediata.

<leitura adicional sobre qualidade dos últimos leads, se houver.>

Assim que conseguirem contato com esse lead, me sinalizem por aqui para acompanharmos a qualidade do atendimento e da oportunidade.
```

Regras:

- Não expor dado sensível além do necessário para o grupo de atendimento.
- Não chamar todo lead de "quente"; usar isso só quando houver sinal real de intenção.
- Conectar qualidade do lead aos sinais de campanha quando fizer sentido.
- Pedir retorno após contato para fechar o ciclo comercial.

### Pré-call

Usar para preparar o cliente antes de uma reunião.

Contexto obrigatório:

- objetivo da call;
- tópicos;
- decisões esperadas;
- materiais que o cliente precisa revisar, se houver.

### Pós-reunião / Alinhamento

Usar para formalizar combinados.

Contexto obrigatório:

- o que foi decidido;
- próximas ações;
- responsáveis;
- prazos, se houver.

Se o usuário pedir pós-call mas não informar os combinados e não houver ata/transcrição no histórico, perguntar.

### Follow-up ou Cobrança

Usar para pedir aprovação, acesso, retorno ou decisão.

Contexto obrigatório:

- o que está pendente;
- de quem depende;
- impacto da demora;
- próximo passo esperado.

Tom deve ser firme sem ser seco. Evitar culpa. Preferir contexto + pedido específico.

### Demanda Encontrada no Contexto

Se o pedido for amplo, procurar demandas recentes em:

- `contexto.md`
- `planejamento/`
- `historico/`
- `entregas/`
- `outputs/`
- `campanhas.md`
- planilha de backup/CRM, quando o assunto for lead

Transformar essas demandas em opções de atendimento. Exemplo:

```markdown
- Status da aprovação dos criativos de agosto - encontrei entregas recentes em `entregas/social-media-jul-ago-2026`.
- Reporte de lead novo - encontrei lead recente na planilha de backup.
```

## Anti-Repetição

Antes de escrever, consultar `perfil.md` e `historico/atendimentos-log.md`.

Evitar repetir:

- mesma saudação da última mensagem;
- mesmo fechamento;
- mesma estrutura de blocos;
- mesmo emoji;
- frases como "passando para atualizar", "segue abaixo", "fico à disposição" em sequência;
- tom excessivamente simétrico ou com cara de template.

Variar com naturalidade, sem inventar personalidade. A variação deve vir do contexto.

Se houver WhatsApp conectado, comparar com o que foi enviado recentemente no grupo. O histórico real ganha prioridade sobre o log da skill.

## Tom

Inferir pelo cliente, não por uma regra global.

Sinais para calibrar:

- cliente chama pelo primeiro nome ou pelo grupo;
- conversa usa emoji ou não;
- cliente responde curto ou com detalhes;
- atendimento anterior foi formal, consultivo, caloroso ou direto;
- tema é sensível ou rotineiro;
- há cobrança, atraso, resultado ruim ou comemoração.

Preferir tom consultivo na maioria dos casos: explicar o suficiente para orientar decisão, mas sem textão defensivo.

## O Que Nunca Fazer

- Gerar relatório sem período definido.
- Gerar resultado com número inventado.
- Gerar status de tarefa sem status confiável.
- Reportar lead sem fonte ou dados mínimos.
- Tratar todos os clientes com a mesma voz.
- Usar estrutura fixa quando o contexto pede outra.
- Fazer pergunta genérica demais, como "me passa mais contexto?", se já dá para perguntar a lacuna exata.
- Escrever mensagem com "cara de IA": excesso de simetria, frases polidas demais, seções artificiais, conclusões genéricas.
- Usar seções vazias, placeholders ou "a preencher".
- Confundir pasta de app/deploy com cliente principal.
- Escrever sem acentuação correta.

## Aprendizado e Retroalimentação

Depois de entregar uma mensagem, registrar em `clientes/<cliente>/historico/atendimentos-log.md`:

```markdown
- YYYY-MM-DD HH:MM | tipo: <tipo> | período: <período ou n/a> | saudação: "<início>" | fechamento: "<final>" | observação: <decisão de tom>
```

Se o usuário corrigir a mensagem neste chat, incorporar a correção em `perfil.md` na seção `Correções recorrentes do humano`.

Se WhatsApp estiver conectado e for possível ver que a mensagem realmente enviada foi diferente da proposta, aprender com a versão enviada:

- atualizar `Padrão real de atendimento`;
- atualizar `Correções recorrentes do humano`;
- atualizar `Rotação recente de atendimento`.

Não transformar todo atendimento em contexto estratégico. Atualizar `contexto.md` apenas quando surgir informação estrutural sobre cliente, fase, estratégia, resultado, decisão, processo ou restrição.

## Saída

Se faltar contexto essencial, a saída deve ser uma pergunta consultiva, não a mensagem final.

Se o pedido for amplo, a saída deve ser um menu consultivo de opções.

Se o pedido estiver completo, a saída deve ser:

1. mensagem pronta para WhatsApp;
2. observação curta, se houver alguma premissa usada ou dado ausente não essencial.

Não envolver a mensagem final em markdown pesado, a menos que a formatação ajude o WhatsApp.
