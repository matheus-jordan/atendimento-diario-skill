# /atendimento-diario — Documentação da Skill

Skill para Claude Code que gera mensagens de atendimento ao cliente (WhatsApp, grupos internos, pré-call, pós-reunião, relatório de resultados) prontas pra copiar e enviar. **Você não escreve a mensagem, você descreve o que aconteceu em uma frase** — a skill identifica o cliente, escolhe o formato certo, calibra o tom pelo histórico real daquele cliente e entrega o texto final personalizado. Sem inventar dado, sem soar repetitivo, sem "fico à disposição para o que precisar".

---

## Em uma frase

> Você diz o que quer comunicar. A skill decide o formato, o tom e a estrutura, e devolve a mensagem pronta — personalizada pra aquele cliente específico, não um template genérico preenchido.

Exemplo mínimo:
```
manda um atendimento pro Cliente X dizendo que subimos 2 criativos novos hoje
```
A skill faz o resto: identifica quem é "Cliente X" (se houver pasta em `clientes/`), lê como você normalmente fala com esse cliente, escolhe se o formato é um update corrido ou uma lista de status, escreve a saudação, o corpo e o fechamento — sem você precisar especificar nada disso.

---

## O que a skill faz

- **Identifica o cliente e o tipo de mensagem** a partir de uma frase em linguagem natural — não exige comando estruturado nem parâmetros.
- **Escolhe o formato automaticamente** entre 7 formatos diferentes (status, update corrido, pendências dos dois lados, pré-call, pós-reunião, relatório de resultados, resultados parciais) com base no que você descreveu.
- **Calibra tom e vocabulário pelo histórico real daquele cliente** — se o repo tiver `clientes/<slug>/perfil.md` com exemplos de mensagens reais, a skill usa exatamente aquele padrão de saudação, formalidade e emoji. Sem isso, usa um tom geral neutro/profissional calibrado por padrões validados de atendimento B2B.
- **Evita repetição entre mensagens** — se houver histórico de rotação (ver seção de retroalimentação abaixo), a skill não repete a mesma saudação/fechamento da mensagem anterior.
- **Nunca inventa dado** — trabalha só com o que foi fornecido no prompt ou existe no contexto do cliente. Métrica sem dado não aparece como placeholder, simplesmente não entra na mensagem.
- **Pode puxar dado ao vivo quando pedido** — se você pedir um período real ("relatório da última semana") em vez de digitar os números, a skill puxa direto do Flow (mídia) e do Ekyte (tasks), reconcilia contra o Growth Pack, e só gera sem perguntar se tudo bater. Qualquer divergência, ela para e confirma com você antes de escrever a mensagem.
- **Funciona com ou sem a estrutura de pastas** — ver "Bootstrap automático" abaixo. Não é preciso nenhuma configuração prévia pra começar a usar.

---

## Instalação

### 1. Pré-requisito
[Claude Code](https://docs.anthropic.com/pt/docs/claude-code) instalado e rodando no repositório onde você quer usar a skill.

### 2. Copiar os arquivos da skill
Copie a pasta deste repositório inteira para dentro do seu projeto, no caminho:
```
<raiz do seu repo>/.claude/skills/atendimento-diario/
  SKILL.md
  README.md
```
Claude Code carrega qualquer skill que esteja em `.claude/skills/<nome>/SKILL.md` automaticamente — não precisa registrar em nenhum outro lugar. Reinicie a sessão do Claude Code (ou abra uma nova) depois de copiar os arquivos.

### 3. Não precisa de mais nada
Ao contrário de outras skills que dependem de MCPs externos (Ekyte, BigQuery), esta skill funciona 100% com o que está no repositório e no prompt. O único MCP opcional é o de gestão de tarefas (Ekyte ou equivalente), usado só se você pedir prazos explicitamente — sem ele, a skill simplesmente não consulta prazos e segue normalmente.

### 4. Bootstrap automático (não precisa criar nada antes)
Se o seu repo ainda não tem a pasta `clientes/` ou o cliente que você mencionar ainda não existe, a skill cria a estrutura mínima sozinha na primeira execução:

| Se não existir | A skill cria |
|---|---|
| `clientes/<slug>/` (a pasta do cliente inteira) | Cria com `contexto.md` mínimo (o que você informou no prompt) e `perfil.md` no template abaixo |
| `clientes/<slug>/perfil.md` | Cria com duas seções vazias: `Tom de comunicação no grupo` e `Rotação recente de atendimento` |
| `clientes/<slug>/historico/` | Cria a pasta + o primeiro `atendimentos-log.md` (ver seção de retroalimentação) |

`slug` é o nome da pasta em formato kebab-case — um cliente "Empresa ABC" vira `clientes/empresa-abc/`. Você não precisa decidir o slug: a skill infere a partir do nome do cliente mencionado.

---

## Estrutura de arquivos (caminhos exatos)

```
<raiz do repo>/
  .claude/
    skills/
      atendimento-diario/
        SKILL.md                        ← instrução do agente, carregada automaticamente pelo Claude Code
        README.md                       ← este arquivo (não é lido pelo agente, é documentação humana)
  clientes/
    <slug-do-cliente>/
      contexto.md                       ← estado atual do cliente: fase, métricas, Norte de Resultado (lido, não escrito por esta skill exceto aprendizados pontuais)
      perfil.md                         ← tom de comunicação + rotação recente de atendimento (lido antes de toda geração)
      processos.md                      ← como a conta opera na prática — cadência, rituais (opcional, só lido se existir)
      historico/
        atendimentos-log.md             ← escrito por esta skill a cada geração (formato/saudação/fechamento usados)
```

Nenhum desses arquivos é exclusivo desta skill — todos fazem parte da arquitetura de contexto de cliente usada em conjunto com a skill complementar `/contexto-refresh` (ver seção final). Esta skill só **lê** `contexto.md` e `processos.md`, e **lê + escreve** `perfil.md` (só a criação inicial, nunca a interpretação) e `historico/atendimentos-log.md`.

---

## Como usar

Ative pelo comando `/atendimento-diario` ou descrevendo o que aconteceu em linguagem natural — não existe uma sintaxe obrigatória:

```
/atendimento-diario
```
```
manda um atendimento pro cliente [Nome] atualizando sobre as entregas de hoje
```
```
gera uma pré-call pra [Nome] com pauta de acompanhamento comercial, call às 14h
```
```
preciso de um relatório de resultados de maio pra [Nome]
```
```
atendimento pra [Nome] dizendo que as campanhas voltaram ao ar, já gerou 1 lead a R$20
```
```
quero fazer um atendimento diário com o relatório da última semana do cliente [Nome]
```

A skill identifica o cliente, o tipo de mensagem e o tom sozinha. Nos primeiros exemplos, você fornece o dado bruto (o que aconteceu, números se tiver, links se tiver) e a redação/estrutura/tom ficam por conta da skill. No último exemplo — quando você pede um período real ("última semana", "de julho") ou pra puxar dado atualizado, sem digitar os números você mesmo — a skill ativa o **Modo de dados ao vivo**, descrito a seguir.

### Modo de dados ao vivo: relatório com pull direto da fonte

Quando o pedido implica dado atual em vez do que foi digitado no prompt, a skill busca direto nas fontes (Flow para mídia, Ekyte para tasks/tickets abertos) em vez de esperar o próximo ciclo do `/contexto-refresh`:

1. Puxa mídia direto do Flow para o período pedido.
2. Puxa tasks/tickets do Ekyte (o que está aberto, pendente, concluído no período).
3. Reconcilia o número de mídia contra o Growth Pack (mesma tolerância de até 2 dias de defasagem usada pelo `/contexto-refresh` — atualização manual do Adveronix é esperada, não é erro).
4. **Se tudo bater, gera a mensagem direto, sem perguntar nada.** Se aparecer alguma nuance, divergência ou erro (Flow e Growth Pack não batem, task com status confuso), a skill **para e pergunta antes de gerar** — mostra os dois números encontrados e pede pra você confirmar qual usar. Nunca escolhe um lado sozinha, nunca gera com dado incerto.

Esse modo exige as mesmas "IDs de referência" que o `/contexto-refresh` usa (`client_documentid`, `Ekyte workspace`, `Growth Pack`) em `contexto.md`. Sem elas, a skill avisa que não consegue puxar ao vivo e segue só com o que você forneceu no prompt — nunca trava a geração por isso.

**Quer especificar algo manualmente?** Pode, a qualquer momento:
- Tom: "tom mais formal", "tom mais próximo", "mais direto"
- Formato: "quero no formato de dois times", "manda como pré-call"
- Cliente sem pasta ainda: descreva os dados do cliente direto no prompt ("o cliente é X, está na fase de captação, tom é próximo") — a skill usa isso e ainda cria a pasta (ver Bootstrap automático)

---

## Formatos disponíveis

A skill escolhe sozinha, mas você pode pedir explicitamente:

| Formato | Quando a skill escolhe | Como pedir manualmente |
|---|---|---|
| FEITO / PENDENTE | Status geral de entregas, ✅/🔁 como marcadores | "status das entregas" |
| Entregas em andamento (➡️) | Update corrido, cada item com contexto e prazo/link | "update do que está rolando" |
| Dois times (🟧🟥) | Pendências dos dois lados (agência e cliente) | "o que está parado dos dois lados" |
| Pré-call | Pauta antes de reunião | "pré-call pra call das 14h" |
| Pós-reunião / Alinhamento | Formalizar combinados com prazo por data | "resumo do que combinamos na call" |
| Relatório de resultados | Métricas + contexto + backlog do período | "relatório de resultados de maio" |
| Resultados parciais | Mesmo relatório, framing honesto de período parcial | "resultados parciais da semana" |

Detalhe de cada formato (estrutura exata, emojis, regras de alinhamento) está em `SKILL.md`, seção "Formatos disponíveis".

---

## Como o tom é calibrado

1. **Sem nenhum contexto de cliente:** a skill usa a tabela de tom geral (Próximo/caloroso, Neutro/profissional, Direto) inferida pelo tipo de mensagem, e os princípios/anti-padrões descritos em `SKILL.md` (nunca "fico à disposição" genérico, nunca jargão corporativo, saudação sempre variada).
2. **Com `clientes/<slug>/perfil.md` preenchido:** a skill lê a seção `## Tom de comunicação no grupo` — exemplos reais de como aquela pessoa específica fala com aquele cliente (saudação, formalidade, emoji, vocabulário) — e usa isso como referência primária, acima do tom genérico.
3. **Com histórico de rotação (`## Rotação recente de atendimento` em `perfil.md`):** a skill checa as últimas mensagens reais enviadas e evita repetir a saudação/formato/fechamento mais recente, garantindo variação real em vez de instinto.

O item 2 e 3 não são preenchidos por esta skill — eles vêm da skill complementar `/contexto-refresh` (ver abaixo). Sem ela instalada, os itens 2 e 3 simplesmente ficam vazios e a skill opera só com o item 1, normalmente.

---

## Retroalimentação com `/contexto-refresh` (opcional, mas recomendado)

Sem memória entre conversas, é fácil a skill cair sempre na mesma fórmula de saudação ou fechamento — o sintoma clássico é precisar reescrever manualmente porque "ficou robótico". Para resolver isso com dado real (não com a IA "tentando lembrar"), esta skill funciona em par com [`/contexto-refresh`](https://github.com/matheus-jordan/contexto-refresh-skill):

1. A cada mensagem gerada, `/atendimento-diario` anexa uma linha em `clientes/<slug>/historico/atendimentos-log.md` — formato, saudação e fechamento usados. **Só grava, nunca interpreta.**
2. `/contexto-refresh` lê esse log periodicamente, cruza com as mensagens que você realmente enviou no grupo (via WhatsApp) e mantém em `perfil.md`:
   - `## Rotação recente de atendimento` — as últimas 5 mensagens reais, sempre substituída pela leitura mais atual.
   - `## Padrões de correção recorrente` — quando o mesmo tipo de ajuste se repete 2+ vezes (ex: "o rascunho sempre propõe X, você sempre reescreve pra Y"), fica registrado aqui.
3. Na próxima geração, `/atendimento-diario` lê essas duas seções e evita repetir o que está no topo.

Instalar `/contexto-refresh` é opcional — sem ele, esta skill funciona normalmente, só sem o anti-repetição orientado a dado real (fica só com o critério manual das regras de Anti-padrões).

---

## Princípios que guiam as mensagens

- Saudação sempre com "bom dia/boa tarde" e variação natural ("tudo bem?", "como vai?"). Nunca seco, nunca a mesma fórmula duas vezes seguidas.
- Cada item carrega contexto: o "porquê" está no próprio item, não em parágrafo separado.
- Resultado ruim vem com causa + plano. Nunca só o número.
- CTA final é específico, nunca "fico à disposição" genérico — ancorado no próximo passo real da conversa.
- Links são inline, nunca em seção separada.
- Sem jargão: "devolutivas", "assertivos", "encaminhamento" nunca aparecem.
- Sem travessão (—) em nenhuma mensagem.
- Sem numeração formal `1. 2. 3.` para pontos distintos — soa corporativo.

Lista completa de anti-padrões e o detalhe de cada princípio está em `SKILL.md`.

---

## Exemplos de uso

**Update de entrega:**
```
manda um atendimento pra [Cliente] dizendo que entregamos 2 criativos estáticos e 2 vídeos
para otimizar as campanhas, como combinamos na última call.
links: [link 1] e [link 2]
```

**Pré-call:**
```
atendimento pra [Cliente] confirmando call às 14h, pauta: volume e qualidade dos leads,
estrutura de campanhas, próximos passos
```

**Relatório:**
```
relatório de resultado de maio pra [Cliente]. Investido: R$2.800. Leads: 14. CPL: R$200.
Vendas: 2. CAC: R$1.400.
```

**Pós-resultado rápido:**
```
atendimento pra [Cliente] dizendo que as campanhas voltaram ao ar,
já gerou 1 lead a R$20, fiz algumas otimizações pela manhã
```

---

## Dúvidas frequentes

**Preciso escrever a mensagem eu mesmo e só pedir pra "formatar"?**
Não. Você descreve o que aconteceu, em uma frase, com os dados que tiver. A skill escreve a mensagem inteira do zero, incluindo saudação, estrutura e fechamento.

**A skill funciona sem `clientes/<slug>/` existir?**
Sim. Ela cria a pasta e os arquivos mínimos sozinha (ver "Bootstrap automático"). Você também pode informar os dados do cliente direto no prompt sem esperar a criação de arquivo nenhum.

**Preciso instalar `/contexto-refresh` junto?**
Não é obrigatório. Sem ele, a skill funciona com tom genérico calibrado por tipo de mensagem. Com ele, o tom fica calibrado pelo histórico real do cliente e a skill para de repetir saudação/fechamento.

**Como alterar o tom de uma mensagem específica?**
Diga no prompt: "tom mais formal", "tom mais próximo", "mais direto". A skill respeita a instrução acima de qualquer calibragem automática.

**A skill envia a mensagem automaticamente?**
Não. Ela gera o texto para você copiar e colar no WhatsApp, grupo ou canal que for. Nenhum envio automático.

**Preciso de MCPs específicos para usar?**
Não. O único MCP relevante (gestão de tarefas/prazos) é opcional e só é consultado se você pedir prazos explicitamente.

**A skill pode puxar os números sozinha, sem eu digitar?**
Sim, se você pedir um período real ou dado atualizado (ver "Modo de dados ao vivo" acima). Ela puxa do Flow e do Ekyte, reconcilia contra o Growth Pack e só gera direto se tudo bater — se houver qualquer divergência, ela para e te pergunta antes de escrever a mensagem.

**Onde fica gravado o que a skill gerou?**
Em `clientes/<slug>/historico/atendimentos-log.md` — um log leve de trabalho (formato, saudação, fechamento), não a mensagem inteira. Serve de insumo pro `/contexto-refresh`, se instalado; sem ele, o arquivo só acumula e não é lido por mais nada.
