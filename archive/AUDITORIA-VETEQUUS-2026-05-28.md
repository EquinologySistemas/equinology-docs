> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../README.md).

# Auditoria Profunda — Ecossistema VetEquus / Equinology

**Data:** 2026-05-28
**Escopo:** 4 repositórios no workspace (`vetequus-api`, `equinology-web-v2`, `equinology-app-v2`, `equinology-adm`).
**Método:** template `TEMPLATE-AUDITORIA-E-DOCUMENTACAO.md` (Fases 0→5). Toda classificação tem evidência (arquivo:linha). **Nenhuma alteração de código ou ação destrutiva foi executada** — esta é uma auditoria de leitura.

> ⚠️ Os achados 🔴 críticos de segurança foram verificados manualmente (lendo o arquivo), não apenas reportados por busca.

---

## Fase 0 — Inventário

### 0.1 Repositórios

| Repo | Stack | Propósito | Consumidor | Branch | Estado git |
|---|---|---|---|---|---|
| `vetequus-api` | NestJS + Prisma (Postgres), DDD-ish (`core`/`domain`/`infra`) | Backbone: toda regra de negócio, auth, pagamentos | os 3 frontends | `main` | limpo |
| `equinology-web-v2` | Next.js 16 (App Router), React 19, Tailwind 4 | App **profissional/veterinário** (web) | veterinários/gestores | `master` | limpo |
| `equinology-app-v2` | Expo SDK 54 / RN 0.81 / expo-router | App **cliente** (dono do cavalo) — mobile | tutores | `main` | limpo |
| `equinology-adm` | Next.js 16 (App Router) — pkg name `base-project` | **Painel super-admin** (gestão de tenants/planos) | equipe interna | `main` | limpo |

Entrypoint API: `src/infra/main.ts`, porta 3333, Swagger `/api`, Scalar `/reference`. Deploy via `deploy.ps1` (SSH com `vetequus.pem`) para `https://vet.dominiodev.shop`.

### 0.2 Modelo de dados (fonte da verdade: `prisma/schema.prisma`, 1947 linhas)

**~81 models, ~12 enums.** Multi-tenant por `companyId`. Três atores: `User` (vet/gestor/colaborador, enum `UserRole`), `Client` (tutor), `AdminUser` (painel, role `super_admin`/`support`).

Grupos de entidades:
- **Núcleo:** Company, User, Client, ClientCompany (N:N), Animal, StudFarm (haras), ClientStudFarm, AnimalNote.
- **Atendimento:** Appointment, AppointmentAnimal (pivô que ancora todos os registros clínicos).
- **Clínico (≈41 tipos):** Dentistry×6, General×4, Orthopedic×6, Reproduction×24 (Breeding/Donor/Receptor/Stallion).
- **Saúde recorrente:** Exam, Vaccine, Deworming, Shoeing, SanitaryProtocol(+Item).
- **Financeiro:** Payment (movimentação), Transaction, TransactionCategory, BankAccount, Invoice (fatura), CreditCard.
- **Assinatura/billing:** SignaturePlan, CompanySignature (Asaas), Coupon.
- **Estoque:** Product, ProductCategory, ProductStock, ProductUsage, FieldStock (estoque volante), Tag, ProductTag.
- **CRM:** Board, Lead.
- **Marketing/admin:** Advertisement(+State/+City), AdminUser.
- **Util:** Note, Reminder, RecoverPasswordCode.

### 0.3 Superfície de rotas (do código, ~90 controllers)

Grupos de prefixo: `user`/`company`/`password-code`; `client`/`client/*`; `admin/*` (auth, companies, users, admins, plans, coupons, signature, financial, ads); `animal`/`animal-note`/`deworming`/`vaccine`/`exam`/`shoeing` + clínicos (`:appointmentId`); `appointment`/`appointment-animal`; `transaction`/`payment`/`bank-account`/`credit-card`/`client-payment`; `invoice`/`client-invoice`; `signature`/`signature-plan`; `product`/`stock-*`/`field-stock`; `board`/`lead`; `note`/`reminder`/`tag`/`stud-farm`/`sanitary-protocol`; `file`; `scripts`; `ads`/`coupons`.
> Existe `API_ROUTES.md` no repo — **não usado como verdade**; a superfície acima vem dos controllers.

### 0.4 Documentação existente (21 `.md`, fora `node_modules`)

| Arquivo | Repo | Natureza |
|---|---|---|
| `API_ROUTES.md`, `docs/api-*.md` (5) | api | Spec de rotas por módulo |
| `README.md` | api | Doc da API |
| `AUDITORIA_REQUISICOES_CLIENTE.md` (26KB) | web | Auditoria de 56 requisições do cliente vs código |
| `PLANO_IMPLEMENTACAO_REQUISICOES.md` | web | Plano/status de implementação |
| `docs/MASKS_GUIDE.md`, `docs/UI_COMPONENTS.md` | web | Convenções de UI |
| `README.md` | web | **Boilerplate create-next-app** (sem conteúdo do projeto) |
| `ALTERACOES_18-05-2026.md`, `ALTERACOES_FATURA_APP.md` | app | Changelogs de refactor |
| `CHECKLIST_QA_APP.md` | app | Checklist de QA manual |
| `DOCUMENTACAO-API-INTEGRACAO.md` | app | Spec tela×endpoint (**desatualizada**) |
| `VET-TIPOS-ATENDIMENTO.md` | app | Mapa dos 41 tipos clínicos |
| `README.md` | adm | **Boilerplate starter** ("Projeto Padrão", React 18 — falso) |

**Anti-padrão presente:** docs espalhados por 4 repos, sem fonte única; specs de API replicadas/divergentes (API_ROUTES.md + docs/api-*.md + DOCUMENTACAO-API-INTEGRACAO.md). 2 READMEs são boilerplate de starter.

### 0.5 Fluxos críticos ponta a ponta

1. **Cadastro/login vet** (web) → JWT 90d → dashboard gated por assinatura.
2. **Atendimento clínico**: cria Appointment → AppointmentAnimal → registros (41 tipos) com upload de arquivo.
3. **Fatura→Caixa**: vet emite Invoice → cliente paga no app (Asaas PIX/cartão) → webhook → vira Payment (movimentação) idempotente por `invoiceId`.
4. **Assinatura**: empresa assina plano (Asaas subscription) → webhook valida status.
5. **Primeiro acesso cliente** (app): valida email+CPF → código → define senha.

---

## Fase 1 — Saúde (classificação com evidência)

### ✅ Implementado (ponta a ponta)
- **API**: codebase amplamente preenchida — zero `throw "not implemented"`; todos os controllers registrados em módulos (nenhum controller morto).
- **adm**: painel **real** (não é boilerplate apesar do nome `base-project`) — 10 páginas funcionais e integradas: login, dashboard, users, companies, plans, coupons, ads, subscriptions, financial, admins. Todo `href` do sidebar resolve para página real; todo endpoint existe como controller `admin/*` guardado.
- **web**: 10 itens de nav mapeiam para páginas existentes; todos os endpoints amostrados existem no backend. Sem TODO/"em breve"/placeholder em `app/**`.
- **app**: rotas (`(auth)`/`(tabs)`/`(animal)`) todas resolvem; sem rota fantasma.

### 🟡 Parcial / stub
- **app** `InvoicePaymentSheet.tsx:156,185,208,218,275` — 9+ `console.log("[PIX DEBUG]")` em produção, marcados "Remover depois" (`:183`). O `ALTERACOES_18-05-2026.md:59` afirma "zero console.log" (falso).
- **app** `notes.tsx:175-188` — Editar/Excluir implementados mas **comentados no JSX**; só criar funciona (decisão de produto pendente, `CHECKLIST_QA_APP.md:218`).
- **api** `prisma/update-codes.ts:18` — `// @ts-ignore` em `code: null`; cheira a workaround de drift.
- **api** `.env.example` — lista só 4 vars; schema exige 11 (AWS×3, Cloudflare×2, Asaas×2 faltando) → clone novo não sobe.
- **adm** `MockIndicator` + `FALLBACK_ADMINS` (`admins/page.tsx:17-34`) — mock só como fallback quando API falha (degradação graciosa, não bug).

### 👻 Fantasma (citado, ausente)
- **web** `middleware.ts:79,81` — matcher inclui `/stock2` e `/cooperators`, páginas inexistentes (stale, inofensivo).
- **app** `reproduction.tsx:42` / `vet/index.tsx:549` — label `"breeding-final"` sem rota/mapper correspondente (remapeado para `breeding-intermediate` em `VET-TIPOS-ATENDIMENTO.md:84`).

### 🔴 Quebrado / risco em runtime
- **app** `api-routes.ts:205` — rota `/reproduction-breedingPregnancy` em **kebab-case inconsistente** (falta hífen); se backend usa hifenizado, 404 silencioso (engolido por `Promise.allSettled`). Documentado em `VET-TIPOS-ATENDIMENTO.md:86`.
- **Vazamento multi-tenant em Invoice** (ver Fase 2, crítico).

### 🧟 Morto (zero call sites — confirmado por grep)
- **web** `components/ocr/ocr.tsx` — arquivo 100% comentado, importado em lugar nenhum (fluxo OpenAI client-side legado, substituído por `app/api/audio/*` server-side).
- **web** dependência `openai` (`package.json:30`) + `NEXT_PUBLIC_OPENAI_API_KEY` — sem nenhum import/uso (o caminho vivo usa OpenRouter server-side).
- **web** `DashboardHeader` — componente existe, comentado no layout (`app/(dashboard)/layout.tsx:20`).
- **app** `data/mock.ts` — zero imports (só citado em doc).
- **adm** `context/SampleContext.tsx` — leftover de starter; montado em `ContextProviders.tsx:11` mas **sem consumidor** + faz `console.log` de todos os cookies (`:21`).
- **adm** dependência `openai` + `OPENAI_API_KEY` no `.env.example` — nunca importado.
- **api** integração **OpenAI inexistente** no backend (grep `openai|gpt-` = 0) apesar de provável intenção de produto.

---

## Fase 2 — Pontos problemáticos (priorizados)

> **Não corrigir aqui** — decisão do dono. Ações são sugestões.

### 🔴 Crítico

1. **Backdoor público de criação de usuário** — `vetequus-api/src/infra/http/controllers/script.controller.ts` (verificado). `@IsPublic()` na classe; `GET /scripts` cria um `User` hardcoded (`vet@email.com` / `123456`) numa `companyId` fixa, sem auth. Qualquer pessoa na internet pode disparar.
   → **Remover o controller inteiro.**
2. **Webhook Asaas sem verificação de assinatura** — `companySignature.controller.ts:104` (verificado). `@IsPublic()`, confia em `body.event`/`payment.id` para ativar assinatura. Atacante forja `PAYMENT_CONFIRMED` e ativa plano pago de graça.
   → **Validar token/assinatura do webhook Asaas (header `asaas-access-token`).**
3. **Upload de arquivo público e irrestrito** — `file.controller.ts:14,24` (verificado). `@IsPublic()`, sem auth, **limite 200 MB** (comentário diz "10mb", errado), **sem allow-list de mimetype**. Qualquer um despeja arquivos arbitrários no bucket S3/Cloudflare.
   → **Exigir auth + validar mimetype + limite realista.**
4. **Vazamento multi-tenant em Invoice** — `invoice.service.ts:288-301` (verificado): `delete`/`findById`/`edit` resolvem só por `invoiceId`, **sem checar companyId**; controller `:id` não passa `@CurrentCompanyId()`. Usuário autenticado lê/edita/apaga fatura de outro tenant por ID.
   → **Adicionar escopo de companyId; auditar todos handlers `:id` que omitem `@CurrentCompanyId()` (note, reminder, etc.).**

### 🟡 Médio

5. **Sem rate-limiting** (api) — nenhum `ThrottlerModule`. Signin/register/reset-senha/webhook são brute-forçáveis.
6. **`ValidationPipe` sem `whitelist`/`forbidNonWhitelisted`** — `main.ts:11-15`. DTOs aceitam e propagam campos inesperados (mass-assignment).
7. **JWT de 90 dias** — `auth.module.ts:14`. Token roubado vale 3 meses; sem refresh.
8. **Cookie de auth NÃO httpOnly** (web) — `lib/auth.ts:16-23`, set/read via `document.cookie`. Exfiltrável por qualquer XSS.
9. **Exposição PCI no app** — `InvoicePaymentSheet.tsx:380`: PAN/CVV/CPF enviados em JSON puro ao backend, sem tokenização client-side. Backend precisa proxiar a Asaas com cuidado (e nunca logar).
10. **CORS aberto + sem helmet** — `main.ts:9 cors:true`. Body limit 50MB + upload 200MB = superfície de DoS.
11. **Sem health/readiness endpoint** (api) — ruim para orquestração de container.
12. **Auth do adm só client-side** — `middleware.ts:15-18` checa só *presença* do cookie (sem validar assinatura/expiração). Shell do admin renderiza para qualquer cookie não-vazio; enforcement real depende 100% do `AdminAuthGuard` da API.
13. **Drift de schema histórico** — migration `20260310134228_recreate_drift` recria tabelas core (sintoma de drift reconciliado à força); migrations de nome vazio (`20250730195055_`); `update_boards_leads.sql` é patch fora do Prisma. `clean-db.ts` é `deleteMany()` destrutivo (ferramenta de dev).
14. **Segredos no working tree** — `vetequus.pem` (chave SSH de deploy) e `api.zip` no diretório do repo. **Gitignored e não-trackeados** (verificado), mas hygiene/leak risk — mover para fora.

### 🟢 Baixo
- Logs de debug em produção (app), nome `base-project` + READMEs boilerplate (web/adm), deps mortas `openai` (web/adm), `ngrok-skip-browser-warning` hardcoded (adm), version mismatch `app.json` 2.0.0 vs `package.json` 1.0.0, bundle id iOS `com.equinollogy.app` (typo duplo-L), `console.log` de cookies em `SampleContext.tsx:21` (adm), middleware da web faz `fetch /signature/validation` a cada navegação (latência).

---

## Fase 3 — Modelo de documentação proposto

**Problema atual:** 21 `.md` espalhados em 4 repos; specs de API divergentes; 2 READMEs boilerplate; changelogs/QA soltos na raiz dos apps.

**Proposta — repo único `vetequus-docs` (fonte da verdade):**
```
vetequus-docs/
  README.md                  # índice + tabela de sistemas (a Fase 0 deste doc)
  overview/                  # arquitetura, modelo de dados, 3 atores, fluxos críticos
  systems/                   # 1 doc por app: api, web, app-cliente, admin
  guides/
    developer/               # setup local, env vars (a lista das 11), convenções, masks/UI
    client/                  # manual do tutor (app) e do vet (web) — linguagem de negócio
    operations/              # deploy (deploy.ps1), troubleshooting, qa-checklist
  decisions/                 # ADRs (ex.: "fatura→caixa idempotente", "OpenRouter vs OpenAI")
  archive/                   # docs históricos preservados (changelogs, auditorias antigas)
```
**Regras:** doc derivável do código (rotas/schema) **aponta para a fonte**, não copia. Cada `README.md` de repo vira stub curto que linka `vetequus-docs`. Doc de cliente separada da técnica.

---

## Fase 4 — Plano de migração (proposto — **aguardando sua confirmação**)

Não executei nada destrutivo. Quando autorizar:
1. Criar `vetequus-docs`; copiar (não mover) docs atuais para `archive/`.
2. Escrever `overview/` e `systems/` novos (reaproveitando Fase 0/1 deste doc).
3. Mover `VET-TIPOS-ATENDIMENTO`, `MASKS_GUIDE`, `UI_COMPONENTS`, specs de API para `guides/`/`systems/`.
4. Remover `.md` duplicados dos repos **após confirmar arquivamento**.
5. Reduzir cada `README.md` a stub. Um commit por repo (Conventional Commits).

---

## Fase 5 — Validação

- [x] Todo item do inventário classificado (Fase 1).
- [x] Todo ponto problemático tem ação sugerida + prioridade (Fase 2).
- [x] Nenhuma conclusão "morto/quebrado" sem evidência (grep/arquivo:linha).
- [x] 4 achados 🔴 verificados manualmente lendo o código.
- [ ] Doc de cliente revisada por quem entende o negócio (pendente — você).
- [ ] Repo `vetequus-docs` criado (pendente sua autorização — Fase 4).
- [ ] Backlog de riscos registrado em ferramenta rastreável (sugiro issues; hoje só neste doc).

---

### Resumo executivo

O ecossistema está **mais maduro do que a documentação sugere** — os 4 apps são reais e integrados, sem grandes fantasmas. O risco concentra-se em **segurança do backend**, com 4 itens críticos acionáveis hoje: (1) backdoor `/scripts`, (2) webhook Asaas sem verificação, (3) upload público irrestrito, (4) vazamento de fatura entre tenants. Dívida de documentação é alta mas estrutural (duplicação + boilerplate), resolvível com o repo `vetequus-docs`.
