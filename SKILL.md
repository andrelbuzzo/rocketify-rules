---
name: rocketify-rules
description: >
  Regras de commit e Trello da Rocketify. SEMPRE usar antes de criar commit, squash,
  rebase ou atualizar cards no Trello em qualquer projeto.
  Use quando o usuário pedir para commitar, gerar mensagem de commit, corrigir autoria
  de commits, ou atualizar/mover cards, checklists ou logs no Trello.
  Aplica-se a todos os projetos Rocketify (VisitaPro, SocialRanking, Olfatto e novos).
---

# Regras Rocketify — commits e Trello

Empresa: **Rocketify**. Cada sistema tem seu próprio domínio de e-mail.

## 1. Autoria de commit

Todo código gerado por agente vira commit com autor `agent-<função>`:

| Nome | E-mail |
|---|---|
| `agent-backend` | `agent-backend@<dominio>` |
| `agent-frontend` | `agent-frontend@<dominio>` |
| `agent-data-arch` | `agent-data-arch@<dominio>` |
| `agent-gerente` | `agent-gerente@<dominio>` |

Domínio por sistema:

| Sistema | Domínio |
|---|---|
| VisitaPro | `visitapro.com.br` |
| SocialRanking | `socialranking.com.br` |
| Olfatto | `olfatto.store` |

Commits humanos: `André Buzzo <1138225+andrelbuzzo@users.noreply.github.com>`.

**Definir autoria antes de commitar:**

```powershell
git -c user.name="agent-backend" -c user.email="agent-backend@visitapro.com.br" commit -m "..."
```

### Inferir a função quando houver dúvida

Na ordem: 1) o escopo da mensagem (`backend`, `frontend`, `auth`, `data`); 2) os arquivos
tocados (`src/main/java` → backend, `frontend/` → frontend, `prisma/`, `migrations/` →
data-arch); 3) a tarefa em andamento. Se continuar ambíguo, **pergunte** — não chute.

## 2. Histórico anterior

Commits antigos devem ter autoria e mensagem no padrão. Para normalizar um histórico
existente, use `git filter-repo` com `--mailmap` (autoria) e `--message-callback`
(mensagem) e force-push em todos os branches. Backups em mirror antes de reescrever.
Mensagens de merge (`Merge pull request #N`) são preservadas como estão.

## 3. Conventional commits — sempre

```
<tipo>(<escopo>): <descrição em minúsculas, imperativo, sem ponto final>
```

Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`,
`chore`, `revert`.

Escopos usuais: `backend`, `frontend`, `auth`, `tests`, `infra`, `docs`.
Corpo opcional (linha em branco depois do subject); use-o para o "porquê", não para
descrever o diff. Nunca coloque trailer `Author:` na mensagem — a autoria vem do git.

```powershell
git commit -m "feat(backend): anticonflito por corretor em vez de corretor+imovel"
```

## 4. Trello — atualizar sempre

Acompanhe o card **sempre** que uma tarefa for concluída, mudar de estado ou gerar
subtarefas. Checklist novo vai com os itens já preenchidos conforme o andamento.

Ordem canônica das colunas de fluxo (todos os boards):

`🎯 Backlog` → `🏃🏻‍♂️‍➡️ Em desenvolvimento` → `🧪🐞 Testes` → `✅ Pronto`

Listas temáticas/fases existentes (MVP, Fluxos, Sprint N, Backlog Roadmap) continuam
valendo como **fila de origem**: os cards migram para as colunas acima conforme são
trabalhados e finalizados.

### Roteiro

1. Ao iniciar: card em `🏃🏻‍♂️‍➡️ Em desenvolvimento`; crie checklist com as subtarefas.
2. Ao concluir item: marque o item do checklist (`update_item` com `checked: true`).
3. Ao concluir a tarefa: comente o log no card, mova para `🧪🐞 Testes` (se houver
   testes) ou direto para `✅ Pronto`.
4. Comentário de log no card ao concluir, sempre — formato:

```
✅ <tarefa concluída>
- <entrega 1>
- <entrega 2>
Commits: <sha> <sha>
```

### Boards

| Sistema | Board |
|---|---|
| VisitaPro | https://trello.com/b/ATlxXKs2 |
| SocialRanking | https://trello.com/b/CqOR3Qp5 |
| Olfatto | https://trello.com/b/MVe8Wz1k |

Listas de fluxo (ARIs):

- **VisitaPro** — Backlog `.../6ac94362daabf22a0c6243b3` · Em desenvolvimento
  `.../6ac9436123083362738eacc4` · Testes `.../6ac943612ddedd7ef091d04d` · Pronto
  `.../6ac94360dc3fc8909aca1f1f`
- **SocialRanking** — Backlog `.../6abc46aa921332ca75a18d89` · Em desenvolvimento
  `.../6ac5719aaf91e03c8331917c` · Testes `.../6ac5719ff4400250d85ea3b5` · Pronto
  `.../6ac571a2af1877a0d2786989`
- **Olfatto** — Backlog `.../6ac943642854a3954c475786` · Em desenvolvimento
  `.../6ac94364abf1ec720c93f05c` · Testes `.../6ac9436359767c34aba48a5c` · Pronto
  `.../6ac94362a1e241e0be0b2863`

Prefixo `ari:cloud:trello::list/workspace/6abc3932e4b6b6ead374ebe5/`.

## 5. Onde esta skill vive

`github.com/andrelbuzzo/rocketify-rules` — fonte única da verdade. Instalada globalmente
em `~/.config/opencode/skills/rocketify-rules/`. Atualizar: `git pull` nesse diretório.
Projetos novos herdam automaticamente.