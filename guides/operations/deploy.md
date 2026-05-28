# Deploy

## API (`vetequus-api`)
- Deploy via `deploy.ps1` (SSH para a VPS usando `vetequus.pem`). Host de produção:
  `https://vet.dominiodev.shop`.
- **Segredos fora do repo:** `vetequus.pem`, `api.zip` e `.env` são gitignored —
  mantenha-os fora do diretório versionado.
- Antes do deploy: `prisma migrate deploy` no banco de produção. Garanta que
  `ASAAS_WEBHOOK_TOKEN` está setado (sem ele o webhook recusa 401) e configurado
  igual no painel do Asaas.
- Após deploy: confirmar Swagger em `/api` e Scalar em `/reference`.

## Frontends
- Web / Admin: build Next.js (`yarn build && yarn start`) ou hospedagem gerenciada.
- App: build/submit via **EAS** (`eas.json`). Conferir `app.json` (version, bundle id).

## Checklist mínimo pós-deploy
- [ ] API sobe sem erro de env (zod valida no boot).
- [ ] Login funciona nos 3 frontends.
- [ ] Webhook Asaas responde 200 com token válido e 401 sem token.
- [ ] Upload `/file` exige token.
