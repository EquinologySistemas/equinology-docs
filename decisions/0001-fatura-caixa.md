# ADR 0001 — Ponte Fatura → Caixa

**Status:** aceito (implementado).

## Contexto
Faturas (`Invoice`) e o caixa/movimentações (`Payment`) são conceitos distintos.
Quando uma fatura é recebida, a entrada precisa aparecer no caixa sem digitação
dupla, e sem duplicar se o recebimento for processado mais de uma vez.

## Decisão
Ao uma fatura transitar para `PAID` (edição manual ou pagamento via Asaas), a API
cria automaticamente uma `Payment` de entrada. O vínculo é guardado em
`Payment.invoiceId`, que dá **idempotência** (mesmo recebimento não duplica),
exibição da origem ("Fatura #...") e auditoria. A criação é disparada apenas na
**borda** (quando a fatura *acabou de* virar `PAID`), e a checagem
`ensureInvoicePaymentExists` é idempotente por defesa em dobro.

## Consequência
- `Payment.invoiceId` é `onDelete: SetNull` — apagar a fatura mantém a entrada de caixa.
- Falha ao auto-criar a movimentação **não** falha a operação da fatura (loga e segue);
  o usuário pode lançar manualmente.

Código: `vetequus-api/src/domain/application/services/invoice/invoice.service.ts`.
