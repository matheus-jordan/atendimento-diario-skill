# /atendimento-diario — Documentação da Skill

Skill de geração de mensagens de atendimento ao cliente (WhatsApp, grupos internos, pré-call, pós-reunião, relatórios de resultado). Produz mensagens no tom certo para cada momento, usando o contexto real do cliente, sem inventar dados.

---

## Pré-requisitos

Para usar esta skill você precisa de:

1. **Claude Code** — CLI da Anthropic. [Instalar](https://docs.anthropic.com/pt/docs/claude-code)
2. **MCPs configurados** — os mesmos da conta base:
   - `mcp__ekyte` — para consultar tarefas e prazos (opcional, apenas quando o usuário pedir)
   - `mcp__bigquery-whatsapp` — para contexto adicional via histórico (opcional)
   - NotebookLM CLI — `npx notebooklm` para consultar bases de conhecimento do cliente
3. **Repositório clonado** — o clone precisa ter a pasta `clientes/` com os contextos preenchidos. Sem `clientes/<slug>/contexto.md`, a skill funciona com o que você fornecer no prompt, mas perde a personalização automática.

---

## Ativação

No chat do Claude Code, basta digitar:

```
/atendimento-diario
```

Ou invocar diretamente descrevendo o pedido:

```
manda um atendimento pro cliente Meca atualizando sobre os criativos entregues hoje
```

```
gera uma pré-call pra Nuveto com pauta de acompanhamento comercial, call às 14h
```

```
preciso de um relatório de resultados de maio pra Destra
```

A skill identifica o cliente, o tipo de mensagem e o tom automaticamente. Você não precisa especificar o formato.

---

## Como o contexto do cliente funciona

Antes de gerar qualquer mensagem, a skill lê `clientes/<slug>/contexto.md`. Esse arquivo contém:

- Nome do cliente e responsável pelo atendimento
- Tom de comunicação validado (próximo, neutro, formal)
- Produto/serviço e fase atual do projeto
- Histórico de alinhamentos relevantes
- Norte de Resultado (metas e métricas acompanhadas)

**O que fazer se o contexto estiver incompleto:** a skill sinaliza o que falta e gera com o que foi fornecido no prompt. Você pode alimentar o contexto diretamente no chat ("o cliente é X, está na fase Y, o tom é próximo") e a skill usa isso.

**Slugs dos clientes disponíveis:**

| Nome no chat | Slug (pasta) |
|---|---|
| Meca | `meca-automatizadores` |
| Destra | `destra-consultoria` |
| Nuveto | `nuveto` |
| Aya | `aya-cleaning` |
| Sol | `sol-interiors` |
| Reveservice | `reveservice` |

---

## Formatos disponíveis

A skill escolhe o formato automaticamente. Você também pode pedir explicitamente:

| Formato | Quando usar | Como pedir |
|---|---|---|
| Feito / Pendente | Status geral de entregas | "status das entregas" |
| Entregas em andamento | Update corrido com contexto e prazo | "update do que está rolando" |
| Dois times | Pendências dos dois lados (agência + cliente) | "o que está parado dos dois lados" |
| Pré-call | Pauta antes de reunião | "pré-call pra call das 14h" |
| Pós-reunião | Formalizar combinados com prazo | "resumo do que combinamos na call" |
| Relatório de resultados | Métricas + contexto + backlog | "relatório de resultados de maio" |
| Resultados parciais | Mesmo relatório, período parcial | "resultados parciais da semana" |

---

## Exemplos de uso

**Update de entrega:**
```
manda um atendimento pra Meca dizendo que entregamos 2 criativos estáticos e 2 vídeos
para otimizar as campanhas do Meta, como combinamos na última call.
links: [drive estáticos] e [drive vídeos]
```

**Pré-call:**
```
atendimento pra Nuveto confirmando call às 14h, pauta: volume e qualidade dos leads,
estrutura de campanhas, próximos passos
```

**Relatório:**
```
relatório de resultado de maio pra Destra. Investido: R$2.800. Leads: 14. CPL: R$200.
Vendas: 2. CAC: R$1.400.
```

**Pós-resultado rápido:**
```
atendimento pra Destra dizendo que as campanhas do Meta voltaram ao ar,
já gerou 1 lead a R$20, fiz algumas otimizações pela manhã
```

---

## Princípios que guiam as mensagens

- Saudação sempre com "bom dia/boa tarde" e pergunta natural ("tudo bem?"). Nunca seco.
- Cada item carrega contexto: o "porquê" está no próprio item, não em parágrafos separados.
- Resultado ruim vem com causa + plano. Nunca só o número.
- CTA final é específico, nunca "fico à disposição" genérico.
- Links são inline, nunca em seção separada.
- Sem jargão: "devolutivas", "assertivos", "encaminhamento" nunca aparecem.
- Sem travessão (—) em nenhuma mensagem.

---

## Atualização de contexto

Quando informações novas sobre o cliente aparecerem no chat (resultado de campanha, alinhamento, mudança de estratégia), a skill propõe registrar em `clientes/<slug>/contexto.md`. Sempre confirma antes de gravar.

---

## Arquivos desta skill

```
.claude/
  commands/
    atendimento-diario.md       ← instrução da skill (carregada automaticamente pelo Claude Code)
    README-atendimento-diario.md ← este arquivo
clientes/
  <slug>/
    contexto.md                 ← dossiê do cliente (necessário para personalização)
CLAUDE.md                       ← constituição do repositório (carregada automaticamente)
```

---

## Dúvidas frequentes

**A skill funciona sem o `contexto.md` do cliente?**
Sim. Você fornece os dados no prompt e a skill usa o que foi dado. A personalização de tom e vocabulário fica limitada, mas a estrutura e os princípios funcionam normalmente.

**Posso usar para clientes que não estão na lista de slugs?**
Sim. Se o cliente não tiver pasta em `clientes/`, a skill trabalha apenas com o que você fornecer no prompt.

**Como alterar o tom da mensagem?**
Diga no prompt: "tom mais formal", "tom mais próximo", "mais direto". A skill respeita a instrução e calibra sobre o histórico do cliente.

**A skill envia a mensagem automaticamente?**
Não. Ela gera o texto para você copiar e enviar no WhatsApp, grupo ou canal que for. Nenhum envio automático.
