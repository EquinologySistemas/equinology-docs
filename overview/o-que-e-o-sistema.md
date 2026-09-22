# Visão do produto

O Equinology reúne a operação de clínicas e veterinários de equinos, o acompanhamento dos animais pelos tutores e a administração do serviço.

| Aplicação | Público | Atividades |
|---|---|---|
| Web profissional | Veterinários e gestores | Clientes, animais, propriedades, atendimentos, agenda, documentos, financeiro, estoque e CRM |
| App do tutor | Proprietários de animais | Animais, agenda, conteúdo compartilhado pelo veterinário, notas próprias, faturas e perfil |
| Painel administrativo | Equipe Equinology | Empresas, usuários, planos, assinaturas, cupons, anúncios e tutoriais |
| Site institucional | Visitantes e usuários | Apresentação, parceiros, tutoriais, termos, privacidade e orientações de conta |
| API | Aplicações do ecossistema | Regras de negócio, autenticação, dados e integrações |

## Operação da clínica

A clínica cadastra clientes, propriedades e animais. Os atendimentos agrupam os animais atendidos e seus registros clínicos, como odontologia, ortopedia, reprodução e atendimento geral. Fotos e documentos podem ser associados aos registros. Faturas, movimentações financeiras e estoque apoiam a operação.

A web também possui uma camada de uso offline para atendimentos e registros elegíveis. As operações ficam no navegador e podem ser acompanhadas na tela de sincronização. Veja o [manual do veterinário](../guides/client/veterinario-web.md).

## Acompanhamento pelo tutor

No primeiro acesso, o tutor informa o telefone cadastrado pela clínica. Quando o cadastro está disponível para criação de acesso, define e-mail e senha. Para contas já associadas a um e-mail, o app orienta a entrada ou recuperação de acesso.

O tutor acompanha seus animais, agenda, registros de saúde e o conteúdo que o veterinário compartilha, além de manter notas próprias e pagar faturas por PIX ou cartão. Veja o [manual do tutor](../guides/client/tutor-app.md).

## Administração e integrações

O painel concentra os cadastros de operação do SaaS. Anúncios e tutoriais alimentam as aplicações que os exibem. A API usa PostgreSQL, storage compatível com S3, Asaas e e-mail SMTP. Recursos de IA da web usam OpenRouter pelo servidor Next.js.

Para executar o sistema, consulte o [setup local](../guides/developer/setup.md).
