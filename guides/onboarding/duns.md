# D‑U‑N‑S Number

## O que é
Número de **9 dígitos** emitido pela **Dun & Bradstreet (D&B)** que identifica
uma **empresa** (entidade jurídica) de forma única no mundo — uma espécie de
"CNPJ internacional" para sistemas de verificação. É **gratuito**.

## Por que precisamos
Apple e Google usam o D‑U‑N‑S para confirmar que o **Equinology é uma empresa
real e juridicamente constituída** antes de liberar conta de desenvolvedor
**como organização**. Sem ele, o cadastro de organização não avança — foi
provavelmente a causa da falha na tentativa anterior na Apple.

## Pontos-chave
- Vinculado à **entidade jurídica (CNPJ)**, não a uma pessoa. Uma empresa = **um**
  D‑U‑N‑S (por sede).
- O **mesmo número** serve para Apple **e** Google → pedir **uma vez só**.
- A empresa **pode já ter um** sem saber → sempre **verificar antes de solicitar**.
- É o item **mais demorado**: ~5 dias úteis pela ferramenta da Apple; até ~30
  dias pelo caminho geral da D&B. **Fazer primeiro.**

## Processo
1. **Verificar / solicitar** pela ferramenta gratuita da Apple (consulta a D&B):
   `https://developer.apple.com/enroll/duns-lookup/`. Informa nome, país e
   endereço; diz se já existe e, se não, permite solicitar ali (grátis).
2. Sai por e-mail em ~5 dias úteis; +2 dias para a Apple sincronizar.
3. Com o número, faz-se o cadastro de organização na Apple e no Google.

## O que pedir para o Equinology fazer
Uma pessoa com **autoridade legal** (sócio/responsável), com dados **idênticos
ao CNPJ/contrato social**:

- [ ] Acessar `https://developer.apple.com/enroll/duns-lookup/` e **verificar** se
      já existe D‑U‑N‑S. Se sim, anotar. Se não, **solicitar** (grátis).

Dados a informar (todos batendo com o CNPJ):
- [ ] **Razão social** exata (não o nome fantasia)
- [ ] **Endereço completo** da sede
- [ ] **Telefone** ativo da empresa (podem ligar para confirmar)
- [ ] **Nome e cargo** do responsável legal
- [ ] CNPJ (referência)

## Armadilhas (avisar o cliente)
1. ⚠️ **Dados idênticos em todo lugar** — razão social e endereço precisam ser
   **exatamente iguais** no D‑U‑N‑S, no comprovante de registro e no perfil de
   pagamentos das lojas. Divergência ("LTDA" vs "Ltda.", endereço abreviado) é a
   causa nº 1 de rejeição.
2. ⚠️ **Não duplicar** — verificar antes de solicitar; pedir novo quando já existe
   cria dois registros e confunde a verificação.

## Resultado esperado
Receber de volta: o **número D‑U‑N‑S (9 dígitos)** + **razão social e endereço
exatos** registrados — usados para preencher os cadastros de organização na
Apple e no Google.
