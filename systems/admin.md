# Sistema: equinology-adm (painel super-admin)

**Stack:** Next.js 16 (App Router). **Repo:** `equinology-adm`. Público: equipe interna.

> O `package.json` ainda se chama `base-project` e o README é boilerplate de
> starter — **dívida de documentação, não de código**. O app em si é real e
> totalmente integrado.

## Rotas

Público: `/login`. Sob `(private)`: `/` (dashboard financeiro + métricas),
`/users`, `/companies` (tenants), `/plans`, `/coupons`, `/ads` (com targeting
geográfico + campo `description`), `/subscriptions`, `/financial`, `/admins`
(gated por `super_admin`).

Todo item do sidebar resolve para página real; todo endpoint existe como
controller `admin/*` guardado na API (`AdminAuthGuard`).

## Integração com a API

- Axios em `context/ApiContext.tsx`, base `NEXT_PUBLIC_API_URL`, Bearer (`auth=true`).
  401 → logout. Cookie: `equinologyAdminToken`.

## Pontos de atenção

- Auth do shell é **client-side** (`middleware.ts` checa só presença do cookie) —
  enforcement real depende do `AdminAuthGuard` da API.
- `context/SampleContext.tsx`: morto + `console.log` de cookies — remover.
- Renomear `package.json` (`base-project` → `equinology-adm`) e reescrever README.
- Remover dep `openai` / `OPENAI_API_KEY` do `.env.example` (não usados).
- Header `ngrok-skip-browser-warning` hardcoded — leftover de túnel de dev.
