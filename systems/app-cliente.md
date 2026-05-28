# Sistema: equinology-app-v2 (app do tutor)

**Stack:** Expo SDK 54 / React Native 0.81 / expo-router, NativeWind. **Repo:** `equinology-app-v2`.
Público: tutor (dono do cavalo). Somente leitura para registros de saúde/clínicos.

## Telas (expo-router)

- **`(auth)`**: `login` (já tenho conta / primeiro acesso), `signup`, `forgot-password`.
- **`(tabs)`**: `index` (home), `animals`, `agenda`, `finances`, `profile`.
- **`(animal)`**: `[id]` (detalhe) → `health/*` (vacinas, vermífugos, exames, casqueamento, protocolos),
  `vet/*` (geral, odontologia, ortopedia, reprodução — leitura), `notes`, `payments`.

## Integração com a API

- Axios em `contexts/ApiContext.tsx`, base `EXPO_PUBLIC_API_URL`, Bearer por chamada
  (`getConfig`). Token em `expo-secure-store` (`SessionContext`).
- Rotas centralizadas em `lib/api-routes.ts`.
- Pagamento (Asaas): `components/sheets/InvoicePaymentSheet.tsx` — PIX (QR + copia/cola)
  e cartão. Faturas usam `/invoice/:id/pay/*`; movimentações usam `/transaction/*`.
- Upload (foto de animal): `UploadAPI` (multipart, autenticado).

## Pontos de atenção

- Cartão (PAN/CVV/CPF) enviado em JSON ao backend — sem tokenização client-side;
  o backend deve proxiar ao Asaas com cuidado e nunca logar.
- Sem deep-link de retorno de pagamento (PIX/cartão) — confirmação depende de re-fetch manual.
- `console.log("[PIX DEBUG]")` deixados em produção — remover.
- Typo no bundle id iOS (`com.equinollogy.app`) e divergência de versão `app.json`↔`package.json`.

## Referências de mudanças

- [ALTERACOES_18-05-2026](../archive/from-app/ALTERACOES_18-05-2026.md) · [ALTERACOES_FATURA_APP](../archive/from-app/ALTERACOES_FATURA_APP.md)
- [VET-TIPOS-ATENDIMENTO](../archive/from-app/VET-TIPOS-ATENDIMENTO.md) · [DOCUMENTACAO-API-INTEGRACAO](../archive/from-app/DOCUMENTACAO-API-INTEGRACAO.md) (parcialmente desatualizada)
