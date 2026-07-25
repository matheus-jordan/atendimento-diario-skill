# /atendimento-diario — Atendimento Diário 1.0

Skill para geração de mensagens de atendimento ao cliente (WhatsApp/grupos). Não é um template fixo — o formato serve a mensagem, não o contrário.

## Ativação

Usar quando o usuário solicitar: "manda um atendimento pro cliente X", "gera update de entregas", "relatório de resultados", "pauta da call", "pós-reunião", "passa um atendimento pra Jaque", etc. Também via comando explícito `/atendimento-diario`.

Quando o pedido menciona um período real ou pede pra puxar dado atualizado ("quero fazer um atendimento diário com o relatório da última semana do cliente X", "puxa o que tá aberto no Ekyte e manda um atendimento", "relatório com os números atualizados de julho"), ativar também o **Modo de dados ao vivo** (ver seção própria abaixo) em vez de esperar só o que foi digitado no prompt.

---

## Fluxo

1. **Identificar** — cliente + tipo de mensagem + tom (se informado)
2. **Consultar contexto** — ler na ordem (se existirem):
   - `clientes/<cliente>/contexto.md` — estado atual, métricas, Norte de Resultado
   - `clientes/<cliente>/perfil.md` — **obrigatório: ler as seções "Tom de comunicação no grupo" e "Rotação recente de atendimento"** antes de gerar qualquer mensagem. A primeira define saudação, formalidade, emoji e vocabulário calibrados por mensagens reais do Matheus com aquele cliente. A segunda (alimentada pelo `/contexto-refresh`) lista as últimas saudações/formatos/fechamentos realmente enviados — é o anti-repetição com dado real, não vibe
   - `clientes/<cliente>/processos.md` — como essa conta opera (rituais, cadência, quem faz o quê)
   Se o contexto estiver incompleto, delegar para `/cs-notebooklm-consulta-cliente`
3. **Calibrar tom pelo perfil** — a seção "Tom de comunicação no grupo" do `perfil.md` é a referência primária de tom. Se o usuário especificar um tom diferente, usar o do usuário. Se não houver seção de tom no perfil, usar o tom geral definido na seção Tom abaixo
4. **Consultar Ekyte** — somente se o usuário pedir prazos ou tarefas explicitamente. Se o pedido ativa o Modo de dados ao vivo (ver seção própria), este passo vira obrigatório e segue o fluxo de pull + reconciliação descrito lá
5. **Escolher formato, saudação e fechamento evitando repetição** — com base no tipo de mensagem, no conteúdo disponível e checando contra "Rotação recente de atendimento" em `perfil.md`: se a saudação, o formato ou o fechamento planejados coincidem com a entrada mais recente da lista, trocar por uma variação diferente antes de prosseguir. Sem essa seção no perfil (cliente novo ou primeiro ciclo), aplicar o critério geral da seção Anti-padrões abaixo
6. **Gerar mensagem** — com os dados reais fornecidos, sem inflar

---

## Bootstrap automático (repo sem a estrutura ainda)

Esta skill funciona em qualquer repositório, mesmo sem nenhuma pasta `clientes/` prévia. Se ao consultar o contexto (passo 2) algo estiver faltando, criar antes de prosseguir — nunca travar nem pular a personalização por falta de arquivo:

| Faltando | O que criar |
|---|---|
| `clientes/<cliente>/` inteira | Criar a pasta com `contexto.md` mínimo (nome do cliente + o que foi informado no prompt) e `perfil.md` no template abaixo |
| `clientes/<cliente>/perfil.md` | Criar com as seções vazias: `## Tom de comunicação no grupo` (sem exemplos ainda) e `## Rotação recente de atendimento` (lista vazia, comentário indicando que `/contexto-refresh` preenche depois) |
| `clientes/<cliente>/historico/` | Criar a pasta junto com o primeiro `atendimentos-log.md` (ver seção de log mais abaixo) |

Template mínimo de `perfil.md` quando criado do zero:
```markdown
# Perfil: <Nome do Cliente>

## Tom de comunicação no grupo
(ainda sem histórico suficiente — usar o tom geral da seção "Tom" desta skill até acumular mensagens reais)

## Rotação recente de atendimento (evitar repetir — atualizado por /contexto-refresh)
(vazio — sem atendimentos anteriores registrados ainda)
```
Sem `/contexto-refresh` instalado no repo, essas duas seções simplesmente nunca se preenchem sozinhas — a skill continua funcionando normalmente com o tom geral e o critério manual de variação da seção Anti-padrões. `/contexto-refresh` é um complemento opcional, não uma dependência obrigatória.

---

## Modo de dados ao vivo (relatório com pull direto)

Quando o pedido implica dado atual e não só o que foi digitado no prompt (período mencionado, "puxa os números", "o que está aberto no Ekyte", relatório sem números fornecidos manualmente), a skill busca direto nas fontes em vez de esperar o próximo ciclo do `/contexto-refresh`. Isso é um pull pontual pra gerar esta mensagem — nunca escreve em `contexto.md`, `campanhas.md` ou `aprendizados.md` (quem interpreta e persiste isso é sempre o `/contexto-refresh`, ver seção de retroalimentação).

**Pré-requisito:** as mesmas "IDs de referência" que o `/contexto-refresh` usa, em `clientes/<cliente>/contexto.md` (`client_documentid`, `Ekyte workspace`, `Growth Pack`). Sem elas, avisar que o pull ao vivo não é possível e seguir só com o que foi fornecido no prompt — nunca travar a geração da mensagem por causa disso.

**Passos:**
1. **Mídia** — puxar direto do Flow (`flow_media_campaign_summary` / `flow_media_ad_summary`) pro período pedido, mesma lógica do `/contexto-refresh`.
2. **Ekyte** — puxar tasks/tickets do projeto do mês corrente (`list_project_tasks` / `list_tickets`), especialmente o que está aberto/pendente se o pedido mencionar isso.
3. **Reconciliar mídia contra o Growth Pack** — mesma regra do `/contexto-refresh`: até 2 dias de defasagem é esperado (atualização manual), não é erro. Além disso, é divergência.
4. **Decisão:**
   - **Tudo bate (dentro da tolerância) e não há ambiguidade no Ekyte:** gerar a mensagem direto com os números pulled, sem pedir confirmação.
   - **Alguma nuance, divergência ou erro aparece** (números não reconciliam, task com status conflitante, dado que não fecha): **parar e perguntar antes de gerar.** Mostrar o número/fato específico que não bateu e as duas versões encontradas (ex: "Flow mostra R$X de investido, Growth Pack mostra R$Y — qual uso?"). Nunca escolher um lado sozinho, nunca gerar a mensagem com dado incerto.

Esse modo não substitui o `/contexto-refresh` — é um atalho pra quando você quer o relatório agora, com dado fresco, sem esperar o próximo sync. Se o `/contexto-refresh` já rodou recentemente e `campanhas.md`/`contexto.md` estão atualizados, geralmente não é preciso ativar este modo — o contexto já lido no passo 2 do Fluxo já é suficiente.

---

## Tom

O usuário pode especificar o tom. Quando não especificar, inferir pelo contexto do cliente e pelo tipo de mensagem.

| Tom | Quando usar |
|---|---|
| Próximo / caloroso | Clientes com relação estabelecida ("meu querido", primeiro nome, emoji leve) |
| Neutro / profissional | Grupos maiores, cliente novo, contexto formal |
| Direto | Updates rápidos, sem saudação longa |

Calibrar pelo histórico do cliente em `contexto.md`.

---

## Formatos disponíveis

### 1. FEITO / PENDENTE
Status geral de entregas. Usar ✅ / 🔁 como marcadores.
- Pendentes com prazo em linha separada abaixo do item
- Não misturar emojis de status diferentes no mesmo bloco

### 2. Entregas em andamento (➡️)
Update corrido sem divisão de status. Cada item carrega: o que é + contexto + prazo ou link.
- Prazo condicional quando há dependência: "Com o acesso, o prazo é: XX/XX"
- Bloqueio nomeado com diagnóstico + pedido específico

### 3. Pré-call
Compartilhar pauta antes de reunião. Lista limpa, sem emojis de status.
- Tom leve, encerramento com aviso do link ("10 min antes envio o link")

### 4. Pós-reunião / Alinhamento
Formalizar combinados + próximas ações com prazo por data.
- Pode incluir seção narrativa de validação de meta quando relevante
- Organizado por data, não por status

### 5. Relatório de resultados
Métricas + contexto + backlog. Referência canônica:

```
[Saudação nominal]. [Abertura de semana/mês].

📊 RESULTADOS ([MÊS/PERÍODO])

META MENSAL
Geração de demanda: [X]
Valor de venda:     R$ [meta]

GERAÇÃO DE DEMANDA
Investido:     R$ [valor]
MQLs / Leads:  [X]
CPL / CP_MQL:  R$ [valor]

CONVERSÃO EM VENDAS
SQLs:          [X]
Vendas:        [X]
CAC:           R$ [valor]
Valor vendido: R$ [valor]

📝 CONTEXTO
[1-2 frases honestas. Se bom: comemore + próximo passo. Se abaixo: reconheça + causa + plano.]

🗂️ BACKLOG DA SEMANA
→ [ação em andamento]
→ [o que será entregue]

📅 CRONOGRAMA
[DD.MM] | [HH:MM às HH:MM] — [Evento + pauta breve]
```

- Usar apenas as seções que têm dados — não preencher com placeholder
- Alinhamento por espaços nas métricas para criar coluna visual
- Separadores ━━━ são opcionais, usar só quando o volume de seções justificar

### 6. Resultados parciais
Mesmo formato do relatório, mas com framing honesto de período no header: `📊 RESULTADOS PARCIAIS (DD/MM a DD/MM)`. O contexto diagnóstico é mais importante que os números — conecta dado → problema → ação.

---

## Princípios

- **Trabalha com o que foi fornecido** — sem inventar dados, sem inflar com placeholder
- **Cada item carrega contexto** — não é lista seca; o "porquê" está no próprio item
- **Referência a combinados anteriores** — "como alinhamos", "como combinamos em call" cria continuidade
- **Resultado ruim vem com causa + plano** — nunca só o número negativo
- **CTA final é específico** — não "fico à disposição" genérico, mas o que exatamente se espera
- **Encerramento com disponibilidade** — toda mensagem termina com disponibilidade, mas personalizada ao contexto da mensagem quando possível: referenciar o próximo passo ("conforme avançarmos com os criativos, sinalizo aqui"), o que está pendente do cliente ("fico no aguardo da aprovação"), ou o que vem a seguir ("assim que tiver o retorno, atualizo aqui"). Só usar encerramento genérico ("qualquer dúvida me chama") quando não houver nada específico para ancorar
- **Saudação educada sempre** — toda mensagem começa com saudação que inclui "bom dia/boa tarde", pergunta como a pessoa está ou variação natural ("tudo bem?", "como vai?", "espero que esteja bem"). Nunca só "Oi, [Nome]" seco. Variar a formulação conforme o contexto e tom — não engessar em template fixo
- **Emoji no bom dia** — em mensagens mais simples e diretas, é natural incluir um emoji casual na saudação (😊 😄 🙏🏻 ✊); não obrigatório em mensagens mais densas ou formais
- **Links são inline** — nunca em seção separada
- **Tags nominais** para responsável quando o item está em alguém ou é uma pergunta direcionada

---

## Anti-padrões — nunca fazer

- Parágrafos longos justificativos onde uma lista resolve
- Numeração formal `1. 2.` para pontos distintos — soa corporativo
- Jargão: "devolutivas", "assertivos", "encaminhamento"
- Seções vazias ou com "a preencher" — se não tem dado, não tem seção
- Mensagem que mistura muitos assuntos sem hierarquia clara — preferir separar em mensagens distintas

---

## Atualização de contexto do cliente

Após gerar o atendimento, se novas informações sobre o cliente foram mencionadas (fase do projeto, estratégia, resultado, alinhamentos), atualizar `clientes/<cliente>/contexto.md` com o que foi aprendido. Não registrar histórico de conversa — só contexto estruturado.

---

## Log de retroalimentação (obrigatório em toda geração)

Depois de entregar a mensagem final ao usuário, anexar uma linha em `clientes/<cliente>/historico/atendimentos-log.md` (criar o arquivo e a pasta `historico/` se não existirem):

```markdown
# Log de atendimentos gerados — <Nome do Cliente>

> Gerado por /atendimento-diario a cada execução. Consumido e podado por /contexto-refresh (seção "Rotação recente" em perfil.md). Não editar manualmente.

- DD/MM/AAAA HH:MM | formato: <nome do formato usado> | saudação: "<primeiras ~6 palavras da mensagem>" | fechamento: "<última frase da mensagem>"
```

Esse log é só a matéria-prima: quem faz a leitura, cruza com o que realmente foi enviado no grupo e decide o que vira "Rotação recente" ou "Padrão de correção recorrente" em `perfil.md` é o `/contexto-refresh` (nunca esta skill). Registrar sempre — mesmo quando o usuário editar a mensagem manualmente depois, o que importa aqui é o que foi *proposto*, não o que foi de fato enviado.
