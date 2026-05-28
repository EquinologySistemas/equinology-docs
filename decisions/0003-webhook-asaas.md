# ADR 0003 — Endurecimento do webhook Asaas

**Status:** aceito (parcial — implementado em 2026-05-28).

## Contexto
O endpoint `POST /signature/webhook` era `@IsPublic()` **sem verificação** e
aplicava efeitos inline. Riscos: (1) qualquer um forjava `PAYMENT_CONFIRMED` e
ativava assinatura paga de graça; (2) reentrega do mesmo pagamento re-estendia a
expiração (tempo grátis). Referência mais madura: fluxo SaaS JuridIA.

## Decisão (implementado)
1. **Autenticação por token:** o webhook compara o header `asaas-access-token`
   com `ASAAS_WEBHOOK_TOKEN` (env, obrigatório). Inválido → `401`.
2. **Idempotência de cartão:** o branch `CREDIT_CARD` só estende a expiração
   quando `paymentId !== signature.paymentId` (id diferente do último processado).
   Evita o re-bump na reentrega e no webhook do primeiro pagamento (cuja expiração
   já foi setada na criação). PIX já era idempotente (só ativa se `status INACTIVE`).

## Pendente (proposto, requer migração)
- Tabela `WebhookEvent` (payload bruto + `asaasEventId` único + flag
  `alreadyApplied`) + **cron de retry com backoff** + painel de reprocessamento
  no admin. Dá idempotência forte por evento e resiliência a falhas de processamento.
- Validação de acesso por `expirationDate` (hoje `ACTIVE` libera sem checar data)
  + cron que desativa `ACTIVE` vencida.

Código: `vetequus-api/.../signature/service/companySignature.service.ts` e o
controller `companySignature.controller.ts`.
