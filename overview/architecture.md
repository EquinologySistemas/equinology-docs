# Arquitetura

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│ equinology-web-v2│     │ equinology-app-v2│     │  equinology-adm  │
│  (vet / gestor)  │     │   (tutor/mobile) │     │   (super-admin)  │
└────────┬─────────┘     └────────┬─────────┘     └────────┬─────────┘
         │  HTTPS + JWT (Bearer)  │                        │
         └────────────────────────┼────────────────────────┘
                                   ▼
                        ┌──────────────────────┐
                        │     vetequus-api      │  NestJS (porta 3333)
                        │  core / domain / infra│  Swagger /api · Scalar /reference
                        └──────────┬────────────┘
              ┌────────────────────┼───────────────────────┐
              ▼                    ▼                        ▼
        ┌──────────┐        ┌────────────┐          ┌──────────────┐
        │ Postgres │        │   Asaas    │          │ Cloudflare R2│
        │ (Prisma) │        │ (pagamento)│          │   / AWS S3   │
        └──────────┘        └────────────┘          └──────────────┘
                                   ▲
                            email via SMTP (Hostinger/nodemailer)
```

## Princípios

- **API é a única dona da regra de negócio.** Os 3 frontends não falam com banco
  nem com Asaas diretamente — tudo passa pela `vetequus-api`.
- **Multi-tenant por `companyId`.** O `companyId` vem **sempre do JWT** (decorator
  `@CurrentCompanyId()`), nunca do corpo da requisição.
- **Três atores com tokens distintos:** `User` (vet/gestor/colaborador, com
  `companyId`), `Client` (tutor, `companyId: 'no-company'`), `AdminUser` (painel,
  `type: 'admin'`, sem `companyId`).
- **DDD-ish na API:** `core` (tipos base, `Either`), `domain` (repositórios como
  interfaces + serviços), `infra` (HTTP, Prisma, integrações).

## Deploy

- API: VPS via SSH (`deploy.ps1`, chave `vetequus.pem`) em `https://vet.dominiodev.shop`.
- Frontends: Next.js (web/adm) e EAS (app). Ver [guias de operações](../guides/operations/deploy.md).

## Integrações externas

| Serviço | Uso | Onde no código |
|---|---|---|
| **Asaas** | Assinaturas (cartão/PIX recorrente), pagamento de faturas pelo tutor | `src/infra/shared/bank/asaas.ts` |
| **Cloudflare R2 / AWS S3** | Upload de arquivos (`POST /file`, requer auth) | `src/infra/shared/storage` |
| **SMTP (nodemailer)** | E-mails (recuperação de senha) | `src/infra/shared/email` |
| **OpenRouter** | Transcrição/IA — **server-side apenas** (web `app/api/*`) | ver [ADR 0002](../decisions/0002-ia-openrouter.md) |
