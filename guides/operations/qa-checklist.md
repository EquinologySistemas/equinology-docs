# Checklist de QA

O checklist de QA manual end-to-end do **app do tutor** está preservado em
[archive/from-app/CHECKLIST_QA_APP.md](../../archive/from-app/CHECKLIST_QA_APP.md)
(auth, tabs, cadastro de animal, saúde/vet somente-leitura, pagamentos).

## Smoke test do ecossistema (pós-deploy)
- [ ] **API:** sobe sem erro de env; Swagger `/api` acessível.
- [ ] **Web:** login → dashboard → criar cliente/animal → criar atendimento → emitir fatura.
- [ ] **App:** login do tutor → ver animal → pagar fatura (PIX e cartão).
- [ ] **Admin:** login → listar companies/planos → criar cupom/anúncio.
- [ ] **Webhook:** evento Asaas com token válido ativa/renova assinatura; sem token → 401; reentrega do mesmo pagamento de cartão **não** re-estende expiração.
- [ ] **Multi-tenant:** usuário de uma empresa não acessa fatura de outra (`/invoice/:id`).
