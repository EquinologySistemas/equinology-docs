# Personas e fluxos

| Persona | Aplicação | Papel |
|---|---|---|
| Veterinário / gestor | Web | Operação da clínica e atendimento |
| Tutor | App | Acompanhamento dos animais, conteúdo compartilhado e pagamentos |
| Equipe Equinology | Painel | Administração do SaaS e do conteúdo |
| Visitante | Institucional | Informações sobre o produto e tutoriais |

## Cadastro e acesso profissional

O cadastro e a autenticação passam pela API. A web consulta a situação da assinatura para orientar o acesso às áreas de trabalho ou contratação. A API considera situação e validade temporal da assinatura no fluxo correspondente.

## Atendimento e acompanhamento

A clínica cadastra clientes, propriedades e animais. `Appointment` organiza o atendimento; `AppointmentAnimal` liga os animais aos registros clínicos. O profissional preenche fichas, adiciona anexos, produz documentos e escolhe o conteúdo destinado ao proprietário.

O app apresenta o conteúdo compartilhado, registros de saúde e notas próprias do tutor. As autorias `VET` e `OWNER` têm usos distintos.

## Primeiro acesso do tutor

O tutor informa o telefone cadastrado pela clínica. O fluxo de lookup identifica o cadastro. Quando disponível para criação de acesso, o tutor define e-mail e senha e recebe sua sessão. Para uma conta que já possui e-mail, o app apresenta orientação com e-mail mascarado. A recuperação de senha usa o fluxo de e-mail/código.

## Fatura e recebimento

A clínica emite a fatura; o tutor escolhe PIX ou cartão. A API integra a cobrança ao Asaas e atualiza a situação conforme o retorno/evento recebido. O recebimento cria a movimentação e o lançamento de caixa associados. Veja [ADR 0001](../decisions/0001-fatura-caixa.md).

## Assinatura do SaaS

Planos, contratação, renovação e gestão de assinatura são tratados pela API. Eventos do gateway chegam a `/signature/webhook`, autenticado pelo token da integração. Veja [ADR 0003](../decisions/0003-webhook-asaas.md).

## Trabalho em campo

A web armazena dados consultados e enfileira as operações clínicas elegíveis quando a conexão não está disponível. A equipe acompanha o envio em `/sync`. A confirmação do servidor conclui a sincronização. Veja [ADR 0004](../decisions/0004-offline.md).

## Conta do tutor

A exclusão solicitada pelo tutor remove os dados pessoais previstos no contrato e encerra suas sessões. Registros clínicos e financeiros permanecem vinculados à referência da conta excluída. O [guia da API](../systems/api.md) descreve os pontos de manutenção.
