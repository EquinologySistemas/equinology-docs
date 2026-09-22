# Asaas — configuração da integração

A API centraliza a integração de assinaturas, cobranças e recebimentos em `src/infra/shared/bank/asaas.ts`.

## Configuração

A API recebe:

| Variável | Uso |
|---|---|
| `ASAAS_URL` | URL base do ambiente do gateway |
| `ASAAS_KEY` | Credencial usada nas chamadas |
| `ASAAS_WEBHOOK_TOKEN` | Segredo compartilhado com o webhook |

Os dados de recebimento da clínica, incluindo o identificador de carteira, são tratados pelos cadastros e serviços financeiros da API. Conferir a configuração da clínica ao exercitar cobranças.

## Webhook

- Endpoint: `https://api.equinology.com.br/signature/webhook`.
- Método: `POST`.
- Autenticação: header `asaas-access-token`, com o mesmo valor de `ASAAS_WEBHOOK_TOKEN`.
- Configuração e eventos devem corresponder ao ambiente da chave utilizada.

Use os controllers e serviços para conferir os eventos processados. O [ADR do webhook](../../decisions/0003-webhook-asaas.md) e o [fluxo de fatura/caixa](../../decisions/0001-fatura-caixa.md) orientam a manutenção.

Para validar a integração, crie uma cobrança de teste e confira os efeitos do webhook em assinatura, fatura e caixa. Os contratos do gateway estão na [documentação do Asaas](https://docs.asaas.com/).
