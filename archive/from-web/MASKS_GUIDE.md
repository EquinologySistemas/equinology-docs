> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Guia de Máscaras de Input — equinology-web

> **Regra:** Todos os campos que representam dados formatados (CPF, CNPJ, telefone, CEP, etc.) **devem** usar máscara. Use **react-imask** através do componente de input mascarado (ver ARCHITECTURE.md).

---

## 1. Uso obrigatório

- **Biblioteca:** `react-imask` (ver dependência no projeto).
- **Componente:** Use o componente de input com máscara de `components/ui/` (ex.: `InputMask` ou input que aceita `mask`). Nunca implemente máscara manual com `onChange` + regex em formulários — centralize no componente UI.
- **Onde:** Login, registro, recuperar senha, checkout, cadastros (cliente, animal, propriedade, empresa), financeiro (valores quando aplicável), qualquer formulário com os tipos abaixo.

---

## 2. Campos que DEVEM ter máscara

| Tipo de campo            | Máscara / formato         | Exemplo            | Observação                              |
| ------------------------ | ------------------------- | ------------------ | --------------------------------------- |
| **CPF**                  | `000.000.000-00`          | 123.456.789-00     | 11 dígitos.                             |
| **CNPJ**                 | `00.000.000/0000-00`      | 12.345.678/0001-90 | 14 dígitos.                             |
| **CPF ou CNPJ**          | Dinâmica (CPF ou CNPJ)    | Conforme tamanho   | Até 11 → CPF; 12+ → CNPJ.               |
| **Telefone**             | `(00) 0000-0000`          | (11) 3456-7890     | Fixo 10 dígitos.                        |
| **Celular**              | `(00) 00000-0000`         | (11) 91234-5678    | 11 dígitos; pode unificar com tel.      |
| **Telefone/Celular**     | Dinâmica                  | (11) 91234-5678    | 10 dígitos → fixo; 11 → celular.        |
| **CEP**                  | `00000-000`               | 01310-100          | 8 dígitos.                              |
| **Valor monetário (R$)** | `#.##0,00` ou lib         | 1.234,56           | Opcional: react-number-format ou imask. |
| **Data**                 | `00/00/0000`              | 31/12/2025         | date-fns para validação.                |
| **Código animal**        | Conforme regra de negócio | —                  | Se houver padrão definido.              |

---

## 3. Implementação

- **react-imask:** Máscaras definidas com padrão IMask (ex.: `mask: "000.000.000-00"` para CPF).
- **Valor enviado à API:** Sempre enviar **apenas dígitos** (ou valor normalizado) no payload; a máscara é apenas visual.
- **Validação:** Manter validação com Zod no schema (ex.: `cpf`, `cnpj`, `phone`) em `@schemas/`; o componente de máscara não substitui validação.

---

## 4. Checklist por tela

- [ ] **Login:** não exige máscara (email, senha).
- [ ] **Registro:** CPF/CNPJ, telefone, CEP com máscara.
- [ ] **Recuperar senha:** código e senha — sem máscara de formato.
- [ ] **Checkout:** cartão (número, validade, CVV conforme padrão), CPF/CNPJ se aplicável.
- [ ] **Cadastro de cliente:** CPF/CNPJ, telefone, CEP.
- [ ] **Cadastro de animal / propriedade:** telefone, CEP, datas, códigos conforme definição.
- [ ] **Financeiro:** valores monetários e datas com máscara quando em inputs de formulário.

---

## 5. Referências

- **ARCHITECTURE.md:** biblioteca padronizada — `react-imask` para máscaras.
- **UI_COMPONENTS.md:** componente de input com máscara (quando criado) em `components/ui/`.
- **Schemas Zod:** em `@schemas/` para validação; máscara não substitui validação.
