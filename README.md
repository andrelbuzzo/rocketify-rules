# rocketify-rules

Regras de commit e Trello da **Rocketify**. Skill única, replicada em todos os projetos.

## O que define

1. **Autoria** — autor do git é sempre o dono do repositório; a função do agente vai no
   trailer `Agent:` do commit. 9 funções: `gerente`, `product-manager`, `tech-lead`,
   `backend`, `frontend`, `ui-ux-designer`, `devops`, `qa`, `revisor`.
2. **Histórico** — normalização via `git filter-repo` + force-push.
3. **Conventional commits sempre.**
4. **Trello sempre atualizado** — checklist, log em comentário e movimento de card.
5. **Esta skill como fonte da verdade** — atualize aqui, replique com um `git pull`.

Motivo do trailer em vez do e-mail: Vercel Hobby bloqueia o deploy quando o e-mail do
autor do commit não resolve para uma conta do GitHub. Detalhes em [`SKILL.md`](SKILL.md).

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

- Antes de `git commit`: usar o e-mail do dono e escrever a mensagem em conventional
  commits com o trailer `Agent:` no final do corpo.
- Ao concluir tarefa ou mudar estado: atualizar o card do Trello (checklist + log +
  mover de coluna) na mesma hora.
- Em dúvida sobre qual função usar: inferir pelo escopo/arquivos e, se persistir, perguntar.

Detalhes, tabela de ARIs das listas e roteiro de Trello: [`SKILL.md`](SKILL.md).