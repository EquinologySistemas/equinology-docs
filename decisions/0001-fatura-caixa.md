# ADR 0001 — Fatura e caixa

## Decisão

Fatura (`Invoice`), movimentação (`Payment`) e lançamento/parcela (`Transaction`) têm responsabilidades distintas. Ao receber a fatura, a API associa uma movimentação de entrada por `Payment.invoiceId` e cria seu lançamento pago.

`ensureInvoicePaymentExists` consulta o vínculo antes de criar a movimentação. A categoria utilizada é `Faturas recebidas`; cliente e animal da fatura são transportados para a movimentação quando presentes. A data de pagamento é preservada no lançamento.

A confirmação pode ser acionada pelo fluxo de edição/recebimento e pelos retornos da integração financeira. O serviço trata também eventos de cobrança, incluindo estorno, vencimento e exclusão no gateway. No estorno, a situação financeira e os lançamentos vinculados são atualizados pelo fluxo correspondente.

## Referências de manutenção

- `vetequus-api/src/domain/application/services/invoice/invoice.service.ts`.
- Models `Invoice`, `Payment` e `Transaction` em `prisma/schema.prisma`.
- Controllers financeiros e webhook de assinatura.
- [Modelo de dados](../overview/data-model.md) e [webhook](0003-webhook-asaas.md).

Para alterar esse fluxo, confira tanto a fatura quanto o caixa, a situação retornada pelo gateway e as novas tentativas da mesma operação.
