# Monitor CVM Fatos Relevantes — Cloud Edition (GitHub Actions)

Versão cloud do monitor de Fatos Relevantes da CVM da Marvin. Roda diariamente em GitHub Actions, sem dependência de máquina ligada, e notifica via task no ClickUp.

> **Convivência com a versão local:** este projeto não substitui o monitor local que roda na máquina da responsável. As duas versões existem em paralelo (cinto + suspensórios). A local continua disparando a cada login do Windows; esta cloud roda no GitHub às 10h BRT diariamente.

## Como funciona

```
   GitHub Actions (cron 10h BRT)
            │
            ▼
   ┌────────────────────┐
   │  monitor.py        │
   │  - baixa CVM IPE   │
   │  - filtra watchlist│
   │  - classifica      │
   │  - dedup           │
   └─────────┬──────────┘
             │
   ┌─────────┴──────────┐
   ▼                    ▼
   ClickUp           Git commit
   (cria task)       (historico/state)
```

## Estrutura

| Arquivo / pasta | Descrição |
|---|---|
| `monitor.py` | Orquestrador principal. Roda diariamente. |
| `watchlist.csv` | Devedores monitorados. Editado aqui no repo. |
| `requirements.txt` | Dependências Python. |
| `.github/workflows/daily.yml` | Define o cron e os passos do GitHub Actions. |
| `historico/historico.csv` | Fonte-verdade auditável (commitada pelo bot). |
| `historico/historico_eventos.xlsx` | Planilha formatada (commitada pelo bot). |
| `data/seen_events.json` | Controle de deduplicação (commitado pelo bot). |
| `data/last_run.json` | Metadados do último run. |
| `DEPLOY.md` | Passo a passo para deploy inicial. |

## Setup

Leia `DEPLOY.md`. Resumo: cria repo no GitHub, pushea esse conteúdo, configura 3 secrets, dispara primeiro run manualmente para validar.

## Operação diária

Nenhuma. O sistema é totalmente autônomo. As únicas ações de manutenção são:

- **Adicionar/remover devedor:** editar `watchlist.csv` direto no GitHub (botão de lápis).
- **Trocar destinatário no ClickUp:** atualizar o secret `CLICKUP_ASSIGNEE_IDS`.
- **Pausar:** Actions → workflow → Disable.

## Disparo manual

Útil para testar ou rodar fora do horário programado.

1. Aba **Actions** do repo.
2. Workflow **monitor-cvm-fatos-relevantes daily**.
3. **Run workflow** → branch `main` → confirmar.

## Variáveis de ambiente (Secrets)

| Secret | Onde obter |
|---|---|
| `CLICKUP_API_TOKEN` | ClickUp → Avatar → Settings → Apps → Generate. Formato `pk_...` |
| `CLICKUP_LIST_ID_ALERTAS` | URL da lista de alertas no ClickUp: `/li/{ID}` |
| `CLICKUP_ASSIGNEE_IDS` | Seu user ID no ClickUp. Vários: separados por `,` |

## Custos

R$ 0. GitHub Actions free tier cobre 2.000 min/mês para repos privados, e este monitor consome <2 min por execução = ~60 min/mês.
