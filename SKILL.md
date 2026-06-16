# /atendimento-diario — Atendimento Diário 1.0

Skill para geração de mensagens de atendimento ao cliente (WhatsApp/grupos). Não é um template fixo — o formato serve a mensagem, não o contrário.

## Ativação

Usar quando o usuário solicitar: "manda um atendimento pro cliente X", "gera update de entregas", "relatório de resultados", "pauta da call", "pós-reunião", "passa um atendimento pra Jaque", etc.

---

## Fluxo

1. **Identificar** — cliente + tipo de mensagem + tom (se informado)
2. **Consultar contexto** — verificar `clientes/<cliente>/contexto.md`; se incompleto, delegar para `/cs-notebooklm-consulta-cliente`
3. **Consultar Ekyte** — somente se o usuário pedir prazos ou tarefas explicitamente
4. **Escolher formato** — com base no tipo de mensagem e no conteúdo disponível
5. **Gerar mensagem** — com os dados reais fornecidos, sem inflar

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

### 3. Dois times (🟧 🟥)
Quando há pendências dos dois lados (agência e cliente). Bloco separado por time.
- 🟧 TIME [CLIENTE] → ações que dependem deles
- 🟥 TIME V4 → entregas da agência com prazo ou status
- Tags nominais quando o item está em alguém específico

### 4. Pré-call
Compartilhar pauta antes de reunião. Lista limpa, sem emojis de status.
- Tom leve, encerramento com aviso do link ("10 min antes envio o link")

### 5. Pós-reunião / Alinhamento
Formalizar combinados + próximas ações com prazo por data.
- Pode incluir seção narrativa de validação de meta quando relevante
- Organizado por data, não por status

### 6. Relatório de resultados
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

### 7. Resultados parciais
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
- Mensagem que mistura muitos assuntos sem hierarquia clara — preferir separar ou usar formato de dois times

---

## Atualização de contexto do cliente

Após gerar o atendimento, se novas informações sobre o cliente foram mencionadas (fase do projeto, estratégia, resultado, alinhamentos), atualizar `clientes/<cliente>/contexto.md` com o que foi aprendido. Não registrar histórico de conversa — só contexto estruturado.
