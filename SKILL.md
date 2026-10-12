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

**O autor do git é SEMPRE o humano dono do repositório.** A função do agente vai num trailer
`Agent:` no corpo do commit, não no e-mail.

| Nome (git author) | E-mail |
|---|---|
| `André Buzzo` | `1138225+andrelbuzzo@users.noreply.github.com` |

```powershell
git commit -m "<tipo>(<escopo>): descrição" -m "corpo do commit

Agent: backend"
```

`Agent:` aceita exatamente um destes 9 valores:

| `Agent:` | Quando usar |
|---|---|
| `gerente` | coordenação, escopo, decisão de produto, priorização |
| `product-manager` | requisitos, backlog, critérios de aceite, escopo de card |
| `tech-lead` | arquitetura, **modelagem de dados**, **segurança**, decisões técnicas |
| `backend` | regras de negócio, API, persistência, integrações |
| `frontend` | telas, componentes, estado, chamadas de API |
| `ui-ux-designer` | design system, tokens, acessibilidade, copy |
| `devops` | infra, deploy, CI/CD, variáveis de ambiente, observabilidade |
| `qa` | testes automatizados, matriz de casos, verificação de correção |
| `revisor` | revisão de código, Simplificação, checagem antes do merge |

**Modelagem de dados e segurança vão para `tech-lead`** — não criar `data-arch` nem
`security`. São decisões técnicas, não agentes separados.

### Por que o e-mail do agente não pode ser o autor

Plataformas de hosting (Vercel Hobby) **bloqueiam o deploy** quando o e-mail do autor do commit
não resolve para uma conta do GitHub com acesso ao projeto:

> The deployment was blocked because the commit email `<email>` could not be matched to a
> GitHub account.

Isso vale para **qualquer** commit alcançável, não só os de `master`: o gate roda na criação do
deployment, antes de qualquer build step. Consequências práticas:

- O gate lê o commit que **dispara** o build — inclusive o push de branch, que dispara preview
  deployment. Por isso "só o master está limpo" **não** basta.
- `Ignored Build Step` **não resolve**. Ele pula o *build*, mas o commit já foi rejeitado antes.
  Pior: depois do autor do git corrigido, ele só serve pra branch não deployar, engolindo
  deploys em silêncio. **Não configure** — a raiz (autor do git) já basta. Se algum dia o consumo
  de builds/minutos do Hobby apertar, use Project Settings → Git → Deployments → desmarcar
  branches, que mexe só em preview e é mais explícito.
- `Co-authored-by:` também não resolve: o Vercel lê o campo de autor, não o trailer.

Um e-mail `agent-<função>@<dominio>` que não seja conta do GitHub sempre falha no gate. O trailer
`Agent:` preserva a autoria por função no histórico sem expor o e-mail.

### Nunca dar amend + force-push em branch já pushada

O commit antigo fica órfão (nenhuma branch o referencia) mas continua vivo porque o PR o cita.
O Vercel tenta validá-lo e falha com:

> This commit does not belong to any branch on this repository, and may belong to a fork
> outside of the repository.

Se errar a mensagem depois de pushar, **commit novo**. Nunca `git commit --amend` + `force-push`.

### Fluxo obrigatório

1. Branch `feature/<slug>` ou `fix/<slug>` — **nunca** commit direto em `master`.
2. Commitar com o e-mail do dono + trailer `Agent:`.
3. PR para `master`.
4. **Squash merge** — o autor do commit final passa a ser quem fez o merge, reforçando o gate.
5. Apagar a branch local e remota.

O squash merge resolve o commit final de `master`, mas **não** limpa os commits da branch — por
isso o trailer no commit é obrigatório desde o primeiro push.

### Inferir a função quando houver dúvida

Na ordem: 1) a tarefa em andamento no Trello; 2) o escopo da mensagem e os arquivos
tocados (`src/main/java` → `backend`, `frontend/` → `frontend`, `prisma/`,
`migrations/`, schema → `tech-lead`, `render.yaml`/`.github/workflows/` → `devops`,
`*Test.java` → `qa`); 3) quem pediu o trabalho. Se continuar ambíguo, **pergunte** — não chute.

Trabalhos que tocam mais de uma área vão para quem coordena (`tech-lead` ou `gerente`),
não para o agente da área mais visível.

### Exceção: normalizar histórico

Só para repositórios já existentes com commits de agente no histórico — o §2 explica. Todo commit
novo já sai no padrão acima, então não há o que normalizar.

## 2. Histórico anterior

**Só para repositórios com commits de agente já no histórico.** Todo commit novo já sai no padrão
do §1, então na maioria dos casos não há o que fazer.

Quando precisar: `git filter-repo` com `--mailmap` (troca o e-mail de agente pelo do dono) e
`--message-callback` (insere o trailer `Agent:`). Backups em mirror antes de reescrever.
Mensagens de merge (`Merge pull request #N`) são preservadas como estão.

Cuidado: reescrever histórico força push e **invalida branches e PRs abertos**. Num repo com
Hobby na Vercel, o commit antigo órfão passa a bloquear deploy — limpe os PRs antes.

## 3. Conventional commits — sempre

```
<tipo>(<escopo>): <descrição em minúsculas, imperativo, sem ponto final>
```

Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`,
`chore`, `revert`.

Escopos usuais: `backend`, `frontend`, `auth`, `tests`, `infra`, `docs`.
Corpo opcional (linha em branco depois do subject); use-o para o "porquê", não para
descrever o diff. Trailer `Agent:` no final do corpo (ver §1). Nunca coloque trailer
`Author:` — a autoria vem do git, e um e-mail de agente ali bloqueia o deploy.

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