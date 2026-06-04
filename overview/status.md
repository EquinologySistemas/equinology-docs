# Status do ecossistema

> **Snapshot:** 2026-06-02. Resume o que se moveu em cada repo **desde a
> [auditoria de 2026-05-28](../archive/AUDITORIA-VETEQUUS-2026-05-28.md)**, que é a
> linha de base desta documentação. Para o estado canônico, sempre o código manda;
> este doc é um mapa de "onde a frente de trabalho está agora".

## Visão rápida por repo

| Repo | Último commit | Frente atual |
|---|---|---|
| `vetequus-api` | 2026-06-02 | Automações de e-mail; campos de endereço/responsável em `StudFarm`; `description` em `Advertisement` |
| `equinology-web-v2` | 2026-06-02 | Exibição de patrocinadores (SponsorModal); refino de auth/dashboard; deploy |
| `equinology-adm` | 2026-06-02 | Form de anúncios (targeting geo + `description`); tratamento de token/401; deploy |
| `equinology-app-v2` | 2026-05-28 | Sem mudança funcional desde a centralização dos docs (última feature em 2026-05-18) |

## Destaques desde 2026-05-28

### Automações de e-mail (API) — subsistema novo
Setup de e-mails transacionais e agendados (`feat: setup email automations`):

- **Provider selecionável** via `EnvService` (Brevo / ZeptoMail / SMTP) — não mais
  lido direto de `process.env`. ZeptoMail usa `emailapikey` como usuário SMTP (não
  precisa ser e-mail).
- **Templates** centralizados (`domain/application/shared/email/templates.ts`),
  com o verde da marca (`#154734`).
- **Schedulers**: `inactiveUsers.scheduler` (usuários inativos) e
  `expireTrialSignatures.scheduler` (expiração de trial).
- **Recuperação de senha** (user e client) usando os templates; corrigido o
  ordenamento de rotas literais de `user` antes de `:userId` (resolvia 401 na
  recuperação).
- Onboarding do provedor: [guia E-mail/Zoho](../guides/onboarding/email-automation-zoho.md).

### Patrocinadores no web profissional
O sistema de **Anúncios** (`Advertisement` com escopo geo, ver
[modelo de dados](data-model.md)) ganhou consumo no `equinology-web-v2`:
`SponsorModal` + animações exibem o patrocinador segmentado por estado/cidade ao
vet. Lado de gestão segue no painel admin (`/ads`).

### Anúncios — campo `description`
`Advertisement.description` (opcional) adicionado na API e no formulário de
anúncios do painel admin (`AdsForm`/`AdsPage`).

### `StudFarm` (haras) — endereço e responsável
Novos campos opcionais: `street`, `number`, `neighborhood`, `responsibleName`,
`responsiblePhone` (migration `add_studfarm_address_fields`).

## Consistência de schema

As mudanças acima vieram com migrations versionadas
(`advertisement_description`, `add_studfarm_address_fields`) — schema e histórico
estão alinhados. Após editar o schema, rode `prisma generate` para o Client não
ficar defasado (tipos sem os campos novos). Os sinais de drift histórico
continuam catalogados no [modelo de dados](data-model.md#drift-de-schema-atenção).
