# Sistema: equinology-web-v2 (web profissional)

**Stack:** Next.js 16 (App Router), React 19, Tailwind 4. **Repo:** `equinology-web-v2`.
Público: veterinário / gestor.

## Rotas

- **`(auth)`** (público): `/login`, `/register`, `/recover-password`, `/mail-code`, `/plans`, `/checkout/[id]`.
- **`(dashboard)`** (protegido): `/` (home), `/clients-equines` (+`/animals/[id]`),
  `/services` (+`/[id]`, atendimentos), `/calendar`, `/notes`, `/stock`, `/financial`,
  `/subscription`, `/crm`, `/clinic` (+`/odontograma`).
- `app/fatura/[token]` — visualização pública de fatura (payload no token, sem chamada à API).
- `app/api/*` — proxies server-side de IA (chat, transcrição) via OpenRouter.

## Patrocinadores (Anúncios)

`SponsorModal` (em `(dashboard)/_components`) exibe o anúncio segmentado por
estado/cidade ao vet — lado de consumo do sistema de `Advertisement` (escopo geo,
gerido em `equinology-adm` `/ads`). Ver [status do ecossistema](../overview/status.md#patrocinadores-no-web-profissional).

## Integração com a API

- Wrapper `fetch` em `context/ApiContext.tsx` (`GetAPI/PostAPI/PutAPI/DeleteAPI`),
  base `NEXT_PUBLIC_API_URL`, Bearer do cookie. 401 → logout.
- Camada `services/*.ts` mapeia endpoints (cobertura parcial; algumas tabelas chamam `GetAPI` direto).
- Upload: `lib/upload.ts` (`fetch` multipart com Bearer — `/file` agora exige auth).
- Middleware (`middleware.ts`): gating por token + `GET /signature/validation`.

## Convenções

- Máscaras de input: [MASKS_GUIDE](../archive/from-web/MASKS_GUIDE.md).
- Componentes de UI: [UI_COMPONENTS](../archive/from-web/UI_COMPONENTS.md).

## Pontos de atenção

- Cookie de auth **não httpOnly** (`lib/auth.ts`) — exposto a XSS; migrar para httpOnly.
- Dependência `openai` e `NEXT_PUBLIC_OPENAI_API_KEY` **mortas** (IA viva usa OpenRouter server-side — [ADR 0002](../decisions/0002-ia-openrouter.md)).
- Matcher do middleware tem entradas obsoletas (`/stock2`, `/cooperators`).
- Backlog de requisições do cliente: [AUDITORIA_REQUISICOES_CLIENTE](../archive/from-web/AUDITORIA_REQUISICOES_CLIENTE.md) e [PLANO_IMPLEMENTACAO](../archive/from-web/PLANO_IMPLEMENTACAO_REQUISICOES.md).
