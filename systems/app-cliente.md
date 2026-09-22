# App do tutor — equinology-app-v2

**Stack:** Expo SDK 54, React Native 0.81.5, React 19.1, Expo Router e NativeWind. **Versão em app.json:** `10.0.0`.

## Estrutura e navegação

- `app/(auth)/`: login, primeiro acesso, cadastro e recuperação.
- `app/(tabs)/`: início, animais, agenda, finanças e perfil.
- `app/(animal)/`: animal, saúde, conteúdo do veterinário, notas e pagamentos.
- `contexts/SessionContext.tsx`: sessão, perfil e persistência com SecureStore.
- `contexts/ApiContext.tsx`: cliente Axios, autenticação e uploads.
- `lib/api-routes.ts`: caminhos da API.
- `lib/api-mappers.ts`: conversão das respostas para os tipos da interface.
- `lib/date-utils.ts`: apresentação de datas, incluindo horário de Brasília.

## Acesso

Na aba **Primeiro acesso**, o tutor informa o telefone cadastrado pela clínica. O app consulta `/client/first-access/lookup`. Para um cadastro disponível para criação de acesso, coleta e-mail e senha de pelo menos oito caracteres e envia a `/client/first-access`.

Se a conta já possui e-mail, a resposta orienta o login/recuperação com e-mail mascarado. A recuperação também oferece o fluxo **Esqueci meu e-mail** por telefone.

O login é por e-mail e senha. A opção **Manter conectado** controla a restauração da sessão. O perfil é carregado de `/client/profile`.

## Animais e conteúdo

O app apresenta os animais vinculados ao tutor, agenda, registros de saúde e conteúdo compartilhado. As telas clínicas são de consulta para o proprietário.

As rotas de `client-portal` distinguem:

- Anotação do veterinário destinada ao proprietário.
- Prescrições marcadas para compartilhamento.
- Notas próprias do tutor, identificadas por autoria `OWNER`.

Os contratos de compartilhamento são determinados pela API. `lib/api-mappers.ts` adapta o formato recebido às telas.

## Financeiro

`components/sheets/InvoicePaymentSheet.tsx` concentra as opções PIX e cartão. As rotas de fatura usam `/invoice/:id/pay/*`; outras movimentações usam seus endpoints financeiros.

A interface apresenta os itens da fatura e solicita os dados necessários ao pagamento. O vínculo financeiro do cliente no Asaas é preparado pela API conforme o fluxo da cobrança.

## Perfil e conta

O perfil oferece edição de dados e exclusão da própria conta por `DELETE /client/me`. A API remove os dados pessoais previstos no contrato e preserva as referências clínicas e financeiras. Veja [API](api.md).

## Build e distribuição

O projeto Expo é `@equinologys-team/equinology-app-v2`. Em `app.json`, `expo.owner` é `equinologys-team` e `expo.extra.eas.projectId` é `304c5e5a-a548-4593-9270-8abd5a010912`.

Os identificadores são `com.equinollogy.app` no iOS e `com.equinology.appv2` no Android. Preserve-os ao dar continuidade ao aplicativo. A numeração nativa é gerenciada remotamente pelo EAS; `package.json.version` identifica o pacote e não é o campo de versão exibido pela loja.

Consulte o [setup](../guides/developer/setup.md) e os comandos de [build e publicação](../guides/operations/deploy.md#app-expoeas).
