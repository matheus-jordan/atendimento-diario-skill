# Atendimento Diário Skill

Skill para gerar mensagens consultivas de atendimento para clientes em WhatsApp/grupos.

A skill investiga contexto, perfil de comunicação, histórico recente, pendências, entregas, campanhas e dados faltantes antes de escrever. Ela não usa template fixo: adapta o formato ao cliente, ao momento e ao objetivo da mensagem.

## Quando usar

Use para:

- relatório semanal ou periódico;
- status geral de tarefas;
- agenda ou confirmação de call;
- pré-call;
- pós-reunião;
- follow-up ou cobrança;
- reporte de lead novo;
- comunicação de rotina com cliente.

## Comportamento esperado

- Se faltar contexto essencial, a skill pergunta antes de gerar.
- Se o pedido for amplo, como "quero um atendimento para Gigaclima", a skill oferece opções consultivas baseadas no contexto atual do cliente.
- Se houver WhatsApp conectado, o tom real das mensagens enviadas tem prioridade sobre qualquer padrão genérico.
- Cada cliente deve ter sua própria forma de comunicação registrada em `clientes/<cliente>/perfil.md`.
- Mensagens finais e arquivos de contexto devem usar acentuação correta.

## Estrutura recomendada do workspace

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

## Instalação

Copie `SKILL.md` para a pasta de skills do seu ambiente Codex/Claude Code, por exemplo:

```text
.codex/skills/atendimento-diario/SKILL.md
```

A fonte de verdade da skill é o arquivo `SKILL.md`.
