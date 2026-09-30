# E-mail transacional — SMTP e ZeptoMail

A API usa uma interface `SendEmail`, templates de mensagem e um provedor configurado por ambiente. O driver `smtp` usa Nodemailer; o driver `log` registra a mensagem no console local.

## Configuração

| Variável | Uso |
|---|---|
| `MAIL_DRIVER` | `log` ou `smtp` |
| `SMTP_HOST` | Host informado pelo provedor |
| `SMTP_PORT` | Porta de envio |
| `SMTP_USER` | Usuário SMTP |
| `SMTP_KEY` | Credencial SMTP |
| `SMTP_FROM` | Remetente |

O contrato está em `src/infra/shared/env/env.ts`. A seleção fica em `src/infra/shared/email/email.module.ts`: com driver omitido, uma `SMTP_KEY` preenchida seleciona SMTP; caso contrário, usa log.

O provedor SMTP é implementado na classe `BrevoMailProvider`; o host, a porta e o remetente são configuráveis. Portanto, a mesma integração pode usar ZeptoMail ou outro SMTP compatível.

## Fluxos implementados

- Boas-vindas no cadastro de usuário profissional.
- Recuperação de senha de profissional e tutor.
- Aviso de inatividade de usuário profissional, com verificação horária e referência de 48 horas.
- Aviso de véspera do fim do teste grátis ("seu acesso termina amanhã"), todo dia às 9h de Brasília, para os trials que vencem no dia seguinte (6º dia de um teste de 7).
- Notificação do encerramento de trial, na verificação horária que marca o trial vencido como inativo.

Os dois e-mails de trial vão para o usuário ADMIN da empresa e estão em `expireTrialSignatures.scheduler.ts`.

Os templates ficam em `src/domain/application/shared/email/templates.ts`. O acompanhamento de atividade e os schedulers ficam nos serviços de conta/assinatura da API.

## Configurar o provedor

1. Confirmar a conta transacional e o domínio remetente.
2. Configurar os registros DNS solicitados pelo provedor.
3. Obter host, porta, usuário, credencial e remetente.
4. Preencher as variáveis do ambiente com `MAIL_DRIVER=smtp`.
5. Reiniciar/publicar a API e conferir uma mensagem de teste em uma conta do ambiente.

Para usar ZeptoMail, os dados específicos são obtidos no [painel e documentação do produto](https://www.zoho.com/zeptomail/).
