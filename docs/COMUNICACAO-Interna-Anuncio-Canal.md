# Comunicação Interna — Anúncio do Canal Automatizado CVM

Modelo de mensagem para anunciar internamente a entrada em operação do monitoramento automatizado de Fatos Relevantes da CVM. Adapte a saudação e o canal (e-mail, Slack, Teams) conforme o destinatário.

---

## Mensagem

**Assunto / título:** Novo canal automatizado — Monitoramento de Fatos Relevantes da CVM (devedores da carteira)

---

Equipe,

A partir desta semana, a Marvin passa a operar um canal automatizado de inteligência sobre eventos materiais dos devedores da carteira de garantias. O sistema captura, em D+1, fatos relevantes, comunicados ao mercado e demais documentos divulgados oficialmente na CVM pelos devedores listados.

**Como funciona:**

- Execução autônoma, diariamente, às 10h (BRT), sem necessidade de qualquer ação manual.
- Cobertura atual: 20 devedores da carteira, dos quais 5 listados na CVM — Casas Bahia (Via S.A.), Marisa, Americanas, GPA e Casa & Video.
- Cada evento detectado vira uma task no ClickUp, na lista **Alertas CVM Fatos Relevantes** (Space Produtos → Folder Mon. CVM Fatos Relevantes).
- Quem estiver atribuído à task recebe notificação automática por e-mail do próprio ClickUp.
- Sistema redundante (camada cloud no GitHub Actions + camada local), versionado no GitHub `marvin-monitoramento/marvin-cvm-fatos-relevantes`.

**Por que esse canal importa:**

Eventos como mudança de auditor, renegociação de covenants, pedido ou deferimento de recuperação judicial e demissão abrupta de executivos financeiros frequentemente precedem em semanas — ou meses — a deterioração observável no ICG ou na agenda performada. Ter detecção tempestiva desses sinais permite ao time de Risco agir antes (retenção, alerta ao credor, ajuste de covenants), em vez de reagir depois.

**Próximas evoluções:**

- Fase 2 (até 3 meses): cobertura de devedores privados via DataJud (processos judiciais) e DOU (Diário Oficial).
- Fase 3 (até 6 meses): widget cliente-facing no dashboard do credor, expondo os eventos lado a lado com o ICG.

Quem quiser receber os alertas em primeira mão, é só me avisar — incluo na lista de assignees do ClickUp em minutos. Toda a documentação técnica e operacional está no GitHub do projeto.

---

## Como usar este modelo

| Cenário | Adaptação sugerida |
|---|---|
| E-mail formal ao Head de Risco / COO | Cola integralmente. Ajusta a saudação (ex.: "Olá [Nome]"). |
| Mensagem no Slack ou Teams para o time de Risco | Reduza para os 2 primeiros parágrafos + os bullets de "Como funciona". Posta o link do GitHub no final. |
| Apresentação verbal (5 min) ao comitê de risco | Use o texto como roteiro. Foco nos blocos "Como funciona" e "Por que esse canal importa". |
| Comunicado ao credor (futuro, Fase 3) | Reescreva removendo referências internas; foque na proposta de valor para o credor (antecipação de risco no colateral). |

---

## Referências mencionadas

- Repositório do sistema: `https://github.com/marvin-monitoramento/marvin-cvm-fatos-relevantes`
- Lista de Alertas no ClickUp: Space **Produtos** → Folder **Mon. CVM Fatos Relevantes** → Lista **Alertas CVM Fatos Relevantes**
- Manual técnico: `docs/2026.04.24 - Monitoramento Automatico - Fatos Relevantes CVM.pdf`
- Relatório do MVP e plano de sucessão: `docs/Relatorio MVP - Monitoramento Fatos Relevantes CVM.docx`
- Working Plan da implementação institucional: `docs/Working Plan - Monitoramento CVM Fatos Relevantes.docx`
- Procedimento de regeneração do token ClickUp: `PROCEDIMENTO-Regeneracao-Token-ClickUp.md`
