# Automação de e-mail (Zoho)

Objetivo: 3 e-mails automáticos — **boas-vindas**, **inatividade 48h** e
**fim do trial (encerrar acesso)**.

## Qual produto Zoho usar
Os 3 são **transacionais/comportamentais** (disparados por evento do sistema),
não marketing. Produto certo: **Zoho ZeptoMail**.

| Produto | Serve para | Uso aqui |
|---|---|---|
| **ZeptoMail** | E-mail transacional via SMTP/API | ✅ **É o necessário** — 10.000 e-mails grátis, depois ~US$ 2,50/10k. SMTP drop-in. |
| Zoho Campaigns | Marketing/newsletter por lista (free: 2.000 contatos/6.000 e-mails/mês) | Opcional, só se quiserem drip de marketing depois |
| Zoho Mail | Caixas de e-mail do domínio (`contato@…`) | Separado; só se quiserem mailbox próprio |

**Vantagem:** o sistema **já tem** um provedor SMTP (`BrevoMailProvider`, na
verdade nodemailer→Hostinger). Migrar para ZeptoMail é trocar host/credenciais —
quase zero código.

## Conta Zoho + verificação por telefone
- Criar conta Zoho exige **OTP no celular**. Pode-se conduzir o cadastro e usar o
  número de alguém do Equinology na etapa de verificação (eles relatam o código).
- Login pode ser `sistemas.equinology@gmail.com`.
- **Independente do telefone, é preciso acesso ao DNS do domínio do Equinology**
  para verificar o domínio remetente (SPF, DKIM, DMARC) — sem isso não envia / cai
  em spam.

## Estado da implementação

**Infra (feito):** porta `SendEmail.sendMail({to,subject,html})` genérica;
camada de **templates** em código (layout base Equinology + boas-vindas +
recuperação refeita — corrigido o branding "Adv Space"); **provider de log**
(`MAIL_DRIVER=log`) para rodar sem e-mail real; SMTP com host/porta/remetente
por env → **migrar para ZeptoMail é só preencher `SMTP_HOST/PORT/USER/KEY/FROM`**.

**a) Boas-vindas** — ✅ *feito* — enviado no cadastro (`user/register`, ambos os
fluxos), de forma não-bloqueante. Falta só ligar no `client/register` (tutor) se
desejado.

**b) Inatividade 48h** — ✅ *implementado (requer rodar a migração)*
- ✅ Campos `lastLoginAt` e `inactivityEmailSentAt` adicionados em `User`
      (migração `20260528120000_add_user_activity_tracking` — **rodar
      `prisma migrate deploy`** no banco; ainda não aplicada).
- ✅ `lastLoginAt` gravado no login (zera `inactivityEmailSentAt`).
- ✅ Cron `InactiveUsersScheduler` (horário): acha quem está ≥48h sem login e
      sem aviso, envia e marca `inactivityEmailSentAt`.

**c) Fim do trial (encerrar acesso)** — ✅ *implementado*
- ✅ O cron `expireTrialSignatures` agora **envia o e-mail** ao admin da empresa
      antes de desativar (busca trials expirados → notifica → desativa, uma vez só).

## Esforço / pendências
Implementação concluída e com typecheck/testes ok. Pendências **externas**:
1. **Rodar a migração** `20260528120000_add_user_activity_tracking` (`prisma migrate deploy`).
2. Conta **ZeptoMail** + **DNS** do domínio verificado.
3. Definir `MAIL_DRIVER=smtp` + `SMTP_HOST/PORT/USER/KEY/FROM` em produção
   (hoje, sem isso, roda em modo `log` — não envia, só registra).
