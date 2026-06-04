# Conta Asaas

**Onde:** `https://www.asaas.com/` → "Criar conta" (ou app Asaas).
**Titularidade:** conta **PJ com o CNPJ do Equinology** — é a conta que recebe os
pagamentos e cujos dados (API key, walletId) entram no sistema. **Não** usar a
conta de operações/Gmail aqui; o cadastro é com a identidade da empresa.

## Pré-requisito
- [ ] CNPJ **ativo e regular** na Receita.

## Documentos do responsável (sócio admin / presidente / tesoureiro)
- [ ] Identificação com foto: RG **ou** CNH
- [ ] CPF
- [ ] Comprovante de endereço
- [ ] (Se profissão regulamentada) carteira profissional (CRMV, OAB, CREA…)

## Documentos da empresa
- [ ] CNPJ, razão social e endereço
- [ ] Documento societário (contrato social / requerimento de empresário)
- [ ] Se LTDA e cadastrado por procurador: **procuração**
- [ ] Se ONG/associação: procuração **+ ata de eleição** do presidente

## Verificação de identidade (fim do cadastro)
- [ ] Pelo **app**: selfie + foto do documento
- [ ] Pelo **navegador**: informar uma **conta bancária** da empresa

## O que entregar ao dev depois de criada (para plugar no sistema)
- [ ] **API Key** de produção (e, se possível, a de sandbox) → vai em `ASAAS_KEY`
- [ ] **walletId** da conta (split / recebimento de faturas pelos tutores)
- [ ] Definir um valor secreto para **`ASAAS_WEBHOOK_TOKEN`** e configurar o
      webhook no painel Asaas apontando para
      `https://vet.dominiodev.shop/signature/webhook` com esse token

> Conta gratuita e sem mensalidade; a análise/aprovação pode levar alguns dias úteis.
