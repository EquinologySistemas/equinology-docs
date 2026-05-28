# Setup local + variáveis de ambiente

## Pré-requisitos
- Node LTS, Yarn, PostgreSQL (ou Docker — `vetequus-api/docker-compose.yml`).

## API (`vetequus-api`)
```bash
yarn
npx prisma generate
npx prisma migrate deploy   # aplica migrations no banco
yarn start:dev
```
Variáveis (validadas por zod em `src/infra/shared/env/env.ts` — boot falha se faltar):

| Var | Uso |
|---|---|
| `DATABASE_URL` | Postgres |
| `JWT_SECRET` | assinatura do JWT (min 10 chars) |
| `SMTP_USER`, `SMTP_KEY` | e-mail (nodemailer) |
| `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY_ID`, `AWS_BUCKET_NAME` | storage S3 |
| `CLOUDFLARE_ACCOUNT_ID`, `CLOUDFLARE_URL` | storage R2 |
| `ASAAS_URL`, `ASAAS_KEY` | gateway de pagamento |
| `ASAAS_WEBHOOK_TOKEN` | **obrigatório** — autentica o webhook (header `asaas-access-token`); configure o mesmo valor no painel do Asaas |

> Modelo completo em `vetequus-api/.env.example`.

## Web / Admin (`equinology-web-v2`, `equinology-adm`)
```bash
yarn && yarn dev
```
| Var | Uso |
|---|---|
| `NEXT_PUBLIC_API_URL` | base da API |
| `NEXT_PUBLIC_USER_TOKEN` | nome do cookie do token |
| `OPENROUTER_API_KEY` (só web, **server-side**) | IA/transcrição |

## App (`equinology-app-v2`)
```bash
yarn && yarn start
```
| Var | Uso |
|---|---|
| `EXPO_PUBLIC_API_URL` | base da API |
