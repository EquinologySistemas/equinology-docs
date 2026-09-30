# ADR 0003 — Processamento de eventos Asaas

## Entrada

O endpoint é `POST /signature/webhook`. O controller compara o header `asaas-access-token` com `ASAAS_WEBHOOK_TOKEN`, configurado no ambiente da API e no painel Asaas.

O webhook recebe eventos financeiros e os encaminha aos fluxos de assinatura, fatura e movimentação correspondentes. Os serviços usam identificadores da cobrança para localizar o registro e tratar repetições conforme o estado da operação.

## Assinaturas

O serviço `CompanySignatureService` trata os eventos de pagamento e os efeitos sobre a assinatura. A validade de acesso considera status e data de expiração, complementada pelos schedulers.

Mudanças de plano, crédito proporcional, recorrência e conciliação pertencem aos respectivos métodos do serviço. Ao alterar esse código, acompanhe a contratação, a chegada do evento e a validação de acesso.

## Teste grátis

Modelo definido com o cliente em 30/09/2026:
1. A clínica se cadastra e ativa o teste grátis na tela de planos (`POST /signature/start-trial/:planId`). O botão só aparece quando o plano tem `trialDays > 0` e `GET /signature/trial-available` indica que a empresa ainda não usou o teste. O teste vale uma vez por empresa.
2. Na véspera do fim do teste, o admin da empresa recebe o e-mail "seu acesso termina amanhã".
3. Quando o teste vence, o acesso é bloqueado: `GET /signature/validation` devolve 403 e a web leva o usuário à tela de planos. O e-mail de teste encerrado é enviado.
4. O acesso volta com o pagamento: cartão libera na contratação e PIX quando chega o webhook. Os dois viram assinatura recorrente no Asaas.

Os dias de teste são configurados por plano no painel (Planos → dias de trial).

## Referências

- `src/infra/http/controllers/signature/companySignature.controller.ts`.
- `src/domain/application/services/signature/service/companySignature.service.ts`.
- `src/domain/application/services/invoice/invoice.service.ts`.
- `src/infra/shared/bank/asaas.ts`.
- [Integração Asaas](../guides/onboarding/asaas.md).

Os caminhos acima são relativos à API. O contrato de eventos deve ser exercitado no ambiente de integração correspondente, incluindo repetição de entrega e conferência dos registros resultantes.
