# O que é o sistema — visão geral

> Documento de apresentação, em linguagem de negócio. Para detalhes técnicos,
> veja [Arquitetura](architecture.md), [Modelo de dados](data-model.md) e a
> pasta [`systems/`](../systems).

## Em uma frase

O **Equinology / VetEquus** é uma plataforma para **clínicas e veterinários de
equinos** gerenciarem todo o atendimento ao cavalo — do cadastro do animal ao
registro clínico, à cobrança e ao recebimento — com um **aplicativo para o tutor
(dono do cavalo)** acompanhar o histórico e pagar as faturas.

É um **SaaS multiempresa**: cada clínica é uma "empresa" isolada, com seus
próprios clientes, animais e finanças, e paga uma **assinatura** para usar.

## As quatro peças

O sistema é formado por quatro aplicações que conversam entre si:

| Peça | Quem usa | Para quê |
|---|---|---|
| **Web profissional** | Veterinário / gestor da clínica | Trabalho do dia a dia: cadastros, atendimentos, agenda, financeiro, estoque, CRM |
| **App do tutor** | Dono do cavalo | Acompanhar os animais e o histórico, e pagar faturas (celular) |
| **Painel administrativo** | Equipe Equinology (dona do produto) | Gerir as clínicas-clientes, planos, cupons e anúncios; ver o financeiro do SaaS |
| **API (servidor central)** | — (ninguém acessa direto) | O "cérebro": guarda tudo, aplica todas as regras e fala com os serviços externos |

## Como funciona (o "cérebro" no meio)

Toda a inteligência mora na **API central**. As três telas — web, app e painel —
são apenas "vitrines": elas **nunca acessam o banco de dados nem os pagamentos
diretamente**. Tudo passa pela API, que é a única que conhece as regras de
negócio.

```
   Web (vet)        App (tutor)        Painel (Equinology)
       \                |                    /
        \               |                   /
         \              |                  /
          ────►   API central (cérebro)  ◄────
                         |
        ┌────────────────┼────────────────┐
   Banco de dados   Pagamentos (Asaas)   Armazenamento de
   (todos os dados)                       arquivos / e-mail
```

Isso traz duas garantias importantes:

- **Isolamento entre clínicas.** Cada requisição carrega, de forma segura, a
  identidade de quem está logado. A API usa isso para mostrar a cada clínica
  **apenas os seus próprios dados** — uma clínica nunca enxerga a outra.
- **Regra única.** Como toda a lógica está num só lugar, web, app e painel se
  comportam de forma consistente, sem cada um "reinventar" a regra.

### Serviços externos que a API usa

- **Asaas** — processa os pagamentos: tanto a **assinatura** que a clínica paga à
  Equinology quanto as **faturas** que o tutor paga à clínica (PIX ou cartão).
- **Armazenamento de arquivos** — guarda fotos, exames e documentos anexados aos
  atendimentos.
- **E-mail** — envia mensagens como recuperação de senha.
- **IA** — recursos de transcrição/assistência usados pela web profissional.

## Quem é quem (os três tipos de usuário)

- **Veterinário / gestor** — trabalha na **web**. Cadastra clientes, animais e
  propriedades; faz e registra atendimentos; emite faturas; controla caixa e
  estoque; acompanha leads no CRM; gerencia a própria assinatura.
- **Tutor (dono do cavalo)** — usa o **app no celular**. Vê seus animais e o
  histórico clínico (apenas **leitura**, ele não edita registros de saúde) e
  **paga as faturas**.
- **Equipe Equinology** — usa o **painel administrativo**. É quem opera o negócio
  por trás: aprova/gerencia as clínicas-clientes, define planos e cupons,
  publica anúncios e acompanha o faturamento do SaaS.

## Fluxos que o usuário pode fazer

### 1. Clínica começa a usar (veterinário)
O veterinário se cadastra e faz login. Para usar o sistema, precisa de uma
**assinatura ativa** — se não tiver, é levado à página de planos para contratar.
Com plano ativo, tem acesso a todas as áreas.

### 2. Atendimento clínico (veterinário)
O vet cadastra o cliente, o animal e a propriedade (haras). Ao atender, registra
o que foi feito — odontologia, ortopedia, reprodução, exame geral, vacinas,
vermífugos, casqueamento etc. — e pode **anexar arquivos** (fotos, exames). Tudo
isso forma a **ficha do animal**.

### 3. Faturar e receber (veterinário + tutor) — a "ponte automática"
1. O vet **emite uma fatura** para o cliente (valor, vencimento, animal).
2. O tutor **paga pelo app** (PIX ou cartão).
3. Ao confirmar o pagamento, a fatura vira **PAGA** e o sistema **lança
   automaticamente a entrada no caixa** da clínica — sem digitação manual.

### 4. Primeiro acesso do tutor (app)
O tutor abre o app, escolhe **"Primeiro acesso"** e informa o **e-mail e CPF**
que o veterinário cadastrou. Recebe um **código** e define a senha. A partir daí,
acompanha animais, histórico, agenda e finanças.

### 5. Tutor acompanha e paga (app)
No app, o tutor vê seus cavalos, a ficha clínica de cada um (só leitura), a
agenda de atendimentos e suas faturas — e **paga direto** pelo celular.

### 6. Gestão do SaaS (equipe Equinology)
Pelo painel, a equipe gerencia as clínicas-clientes (os "tenants"), os planos e
cupons de assinatura, os anúncios exibidos aos veterinários e acompanha o
financeiro do produto.

## Resumo

- **Para a clínica:** um sistema completo de gestão clínica + financeira de
  equinos, na web.
- **Para o tutor:** um app simples para acompanhar o cavalo e pagar.
- **Para a Equinology:** um painel para operar o negócio.
- **No centro:** uma API que guarda tudo, garante o isolamento entre clínicas e
  conecta os pagamentos — fazendo as três telas funcionarem em conjunto.
</content>
</invoke>
