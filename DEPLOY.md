# Deploy — Monitor CVM Fatos Relevantes (GitHub Actions)

Guia passo a passo para colocar a versão cloud no ar. Tempo total estimado: 30–45 minutos.

> **Importante:** este deploy NÃO substitui o monitor local. As duas versões rodam em paralelo. A local continua disparando a cada login do Windows da responsável; esta cloud roda no GitHub Actions todos os dias às 10h (horário de Brasília), independentemente de qualquer máquina estar ligada.

---

## Pré-requisitos

| Item | Onde / como |
|---|---|
| Conta GitHub | Conta pessoal da responsável (ex.: `mavipavan`) ou organização da Marvin |
| ClickUp workspace | Marvin Tecnologia → Produtos (já existe, mesmo do Monitor de Ratings) |
| Token pessoal ClickUp | Avatar (canto sup. direito) → Settings → Apps → Generate (formato `pk_...`) |
| User ID do ClickUp | Em Avatar → Settings, ver a URL: `app.clickup.com/{ID}/v/u/{user_id}` |

---

## Passo 1 — Criar lista de alertas no ClickUp

Antes do GitHub, prepare o destino dos alertas.

1. Acesse o ClickUp → Workspace **Marvin Tecnologia** → Space **Produtos**.
2. Clique no Folder onde vive o Monitor de Ratings (ou crie um novo Folder "CVM Monitoring").
3. Crie uma nova lista chamada **"Alertas CVM Fatos Relevantes"**.
4. Configure as tags do Space (uma vez): `cvm`, `fato-relevante`, `alta`, `média`, `baixa`. Para criar, abra qualquer task → Tags → digite o nome → Enter.
5. **Pegue o ID da lista:** abra a lista → URL do navegador termina em `/li/{LIST_ID}`. Copie o `LIST_ID`.
6. Verifique se a notificação por e-mail está ativa: Avatar → Settings → Notifications → confirme **Email "New task"** marcado.

---

## Passo 2 — Criar o repositório no GitHub

1. github.com → New repository.
2. Nome: `marvin-cvm-fatos-relevantes` (ou similar).
3. Privado.
4. **Não** inicialize com README (vamos pushar nosso conteúdo do zero).
5. Crie.

---

## Passo 3 — Pushar o conteúdo da pasta `github-actions/` para o repo

Na sua máquina local, abra o Prompt de Comando ou Git Bash. Cole os comandos um a um:

```bash
cd "C:\Users\MariaVitoria\OneDrive - Marvin\01. Marvin\Tarefas e Entregas\03. Produtizar Contratos ICFs e IAFs\Monitoramento\Monitoramento - Fatos Relevantes\github-actions"

git init
git branch -m main
git add .
git commit -m "Initial deploy - Monitor CVM Fatos Relevantes Cloud"

# Substitua SEUUSUARIO pelo seu usuário GitHub (ou pela org da Marvin)
git remote add origin https://github.com/SEUUSUARIO/marvin-cvm-fatos-relevantes.git
git push -u origin main
```

Se pedir login, autentique com sua conta GitHub (HTTPS + token ou GitHub CLI).

---

## Passo 4 — Configurar os Secrets no GitHub

Os tokens nunca vão para o repo. Vivem só como secrets.

1. Acesse o repo no GitHub → **Settings** (topo) → **Secrets and variables** → **Actions**.
2. Clique em **New repository secret** e adicione os três abaixo:

| Nome do secret | Valor |
|---|---|
| `CLICKUP_API_TOKEN` | seu token pessoal `pk_...` do ClickUp |
| `CLICKUP_LIST_ID_ALERTAS` | o ID da lista criada no Passo 1 (só números) |
| `CLICKUP_ASSIGNEE_IDS` | seu user ID do ClickUp (ex.: `112137833`); para múltiplos: `111,222,333` |

---

## Passo 5 — Disparar o primeiro run para validar

1. No repo no GitHub, clique em **Actions** (topo).
2. À esquerda, selecione o workflow **monitor-cvm-fatos-relevantes daily**.
3. À direita, clique em **Run workflow** → branch `main` → botão verde **Run workflow**.
4. Aguarde 1–3 minutos. O ícone vai mudar de amarelo (rodando) para verde (sucesso) ou vermelho (falha).

**Se verde:**
- Confira o ClickUp: se houver evento novo nos últimos 10 dias, uma task vai aparecer na lista "Alertas CVM Fatos Relevantes".
- Confira o repo: a pasta `historico/` e `data/` devem ter sido commitadas pelo bot `marvin-cvm-bot`.

**Se vermelho:**
- Clique no run → job `run` → expanda o step que falhou.
- 90% dos casos: secret faltando ou com nome errado.

---

## Passo 6 — A partir daqui, é automático

- **Diariamente às 10h BRT:** o cron dispara. Não precisa fazer nada.
- **Adicionar/remover devedor:** edita `watchlist.csv` no GitHub (interface web tem botão de editar) → commit → próximo run já considera.
- **Histórico:** sempre atualizado em `historico/historico.csv` e `historico/historico_eventos.xlsx` (faça o download pela interface do GitHub quando quiser olhar).

---

## Diferenças vs versão local

| Aspecto | Local | Cloud |
|---|---|---|
| Frequência | A cada login Windows | Diário às 10h BRT (cron fixo) |
| Disparo manual | Duplo-clique no `.bat` | Aba Actions → Run workflow |
| Notificação | E-mail via Outlook | Task no ClickUp (que dispara e-mail) |
| Dependência de máquina ligada | Sim (da responsável) | Não |
| Dependência de Outlook aberto | Sim | Não |
| Custo | R$ 0 | R$ 0 (GitHub Actions free tier) |
| Manutenção da watchlist | Edita CSV local | Edita CSV no GitHub |

---

## Problemas comuns

| Sintoma | Solução |
|---|---|
| Workflow falha em "Run monitor" | Verifique secrets: `CLICKUP_API_TOKEN` válido, `CLICKUP_LIST_ID_ALERTAS` correto. |
| Task não aparece no ClickUp | Confirme que o `CLICKUP_LIST_ID_ALERTAS` aponta para a lista certa (não para o Folder). |
| Workflow não dispara no horário | GitHub Actions pode atrasar 5–15 min em horários de pico. Normal. |
| Commit do bot falha | Verifique se `permissions: contents: write` está no workflow (já está no YAML). |
| E-mail do ClickUp não chega | Em Avatar → Notifications → Email, confirme "New task" marcado. |

---

## Operação contínua

Depois do deploy, o sistema é autônomo. Não há tarefa diária. As ações de manutenção:

- **Adicionar/remover devedor:** editar `watchlist.csv` no GitHub. 2 min.
- **Trocar destinatário do alerta:** atualizar o secret `CLICKUP_ASSIGNEE_IDS`. 2 min.
- **Pausar temporariamente:** Actions → workflow → Disable workflow. 1 clique.

Para reativar, mesmo caminho: Enable workflow.
