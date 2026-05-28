# Procedimento — Regeneração do Token ClickUp

**Sistema:** Monitor CVM Fatos Relevantes (versão cloud, GitHub Actions)
**Repositório:** github.com/marvin-monitoramento/marvin-cvm-fatos-relevantes

---

## Quando aplicar

Use este procedimento quando:

- O token atual for revogado, expirado ou comprometido.
- For necessário trocar o titular institucional do token.

**Tempo total:** 10 minutos.
**Pré-requisito:** conta GitHub do novo responsável precisa ter acesso (Admin) ao repositório na organização `marvin-monitoramento`.

---

## Procedimento

### 1. Gerar novo token no ClickUp (3 min)

- Faça login no ClickUp com a conta do novo responsável.
- Clique no **avatar** (canto superior direito) → **Settings** → menu lateral **Apps**.
- Em **API Token**, clique em **Generate** (ou **Regenerate** se já existir um).
- **Copie o token completo** imediatamente (formato `pk_xxxxxxxxxxxxxxxxx`).
- ⚠️ O ClickUp **não permite ver o token de novo** após sair da tela. Se perder, regenere outra vez.

### 2. Atualizar o secret CLICKUP_API_TOKEN no GitHub (2 min)

- Acesse: `https://github.com/marvin-monitoramento/marvin-cvm-fatos-relevantes`
- **Settings** (topo, ao lado de "Insights") → menu lateral **Secrets and variables** → **Actions**.
- Localize **`CLICKUP_API_TOKEN`** → clique no botão **Update** (ou no ícone de lápis).
- Cole o novo token → **Update secret**.

### 3. Atualizar CLICKUP_ASSIGNEE_IDS se o destinatário mudou (2 min)

- Se o novo responsável é uma pessoa diferente da anterior, atualize também os IDs de quem recebe as tasks.
- **Como pegar o user_id do ClickUp:** Avatar → Settings → o ID aparece na URL no formato `app.clickup.com/{ID}/v/u/{user_id}`. Ou peça para a pessoa: ClickUp → Profile → o ID está na URL pessoal.
- No GitHub, no mesmo painel de secrets, edite **`CLICKUP_ASSIGNEE_IDS`** → cole o novo ID (ou múltiplos separados por vírgula, sem espaço: `111,222,333`) → **Update**.

### 4. Validar com run manual (3 min)

- Ainda no repositório no GitHub: aba **Actions** (topo).
- Selecione o workflow **monitor-cvm-fatos-relevantes daily** (menu lateral esquerdo).
- À direita, clique em **Run workflow** → branch `main` → botão verde **Run workflow**.
- Aguarde 1 a 3 minutos.
- Resultado esperado: ícone verde de sucesso em todos os steps.
- Confirme no ClickUp: lista **Alertas CVM Fatos Relevantes** deve receber tasks novas (se houver eventos pendentes) ou simplesmente não criar nada (se já estiverem todas no `seen_events.json`).

### 5. Confirmar e arquivar

- Anote no controle interno da Marvin: nome do novo titular, data da troca, motivo.
- O token antigo continua válido até alguém regenerar de novo. Se a pessoa antiga não deve mais ter acesso, peça para ela regenerar (o que invalida o token que estava no GitHub) — mas só depois de você ter atualizado o secret com o novo. Senão você fica sem token funcional no meio da operação.

---

## Procedimento alternativo (emergência)

Se o token vazou ou foi comprometido e você precisa **invalidar imediatamente o token antigo antes de gerar o novo**:

1. Vá em ClickUp → Avatar → Settings → Apps → **Regenerate**. Token antigo é invalidado na hora.
2. Copie o novo token.
3. Atualize no GitHub (passo 2 do procedimento acima).
4. Sistema fica fora do ar entre a invalidação e a atualização do secret (~5 min, no pior caso).
5. Próximo run automático (10h BRT) pega o novo token e segue normalmente.

---

## Limitação reconhecida

O token continua sendo **pessoal de quem o gera**. Isso significa que sempre existe alguém individualmente associado a ele.

A evolução desejada é migrar para uma **conta de serviço institucional** no ClickUp (ex.: `monitor@marvin.com.br`), que ocuparia 1 licença paga mas tiraria o vínculo pessoal. Esta evolução está prevista quando a Marvin tiver demanda para mais de um monitoramento usando o mesmo padrão.
