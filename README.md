# rocketify-rules

Regras de commit e Trello da **Rocketify**. Skill única, replicada em todos os projetos.

## O que define

1. **Autoria** — código gerado por agente commita como `agent-<função>` com e-mail
   `agent-<função>@<domínio do sistema>` (VisitaPro, SocialRanking, Olfatto).
2. **Histórico** — normalização via `git filter-repo` + force-push.
3. **Conventional commits sempre.**
4. **Trello sempre atualizado** — checklist, log em comentário e movimento de card.
5. **Esta skill como fonte da verdade** — atualize aqui, replique com um `git pull`.

## Instalar (uma vez)

```powershell
git clone https://github.com/andrelbuzzo/rocketify-rules.git `
  "$env:USERPROFILE\.config\opencode\skills\rocketify-rules"
```

A pasta global de skills é `~/.config/opencode/skills/`. Qualquer projeto novo herda
as regras automaticamente — não precisa copiar nada para dentro do repositório.

## Atualizar

```powershell
cd "$env:USERPROFILE\.config\opencode\skills\rocketify-rules"
git pull
```

Reinicie o OpenCode para carregar a versão nova.

## Como um agente deve aplicar

- Antes de `git commit`: definir `user.name`/`user.email` do agente (`git -c ...`) e
  escrever a mensagem em conventional commits.
- Ao concluir tarefa ou mudar estado: atualizar o card do Trello (checklist + log +
  mover de coluna) na mesma hora.
- Em dúvida sobre qual `agent-<função>` usar: inferir pelo escopo/arquivos e, se
  persistir, perguntar.

Detalhes, tabela de ARIs das listas e roteiro de Trello: [`SKILL.md`](SKILL.md).