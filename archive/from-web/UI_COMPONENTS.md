# Guia de Componentes UI — equinology-web

> Componentes reutilizáveis baseados em **Radix UI** e estilizados com Tailwind.  
> **Regra:** Use sempre `@/components/ui/*`; nunca importe primitivos Radix diretamente nas páginas.

---

## 0. Problemas de layout corrigidos (autenticação)

- **Campos de entrada:** Fundo padronizado em branco, borda `#27323F/20`, focus com ring `#154734/30` (sem fundo azul claro fora da paleta).
- **Duplicação de layout:** Login, registro e recuperar senha passaram a usar `AuthLayoutShell` (duas colunas: formulário à esquerda, painel decorativo à direita), garantindo o mesmo espaçamento e rodapé.
- **Consistência de componentes:** Botões, inputs, labels, checkbox e radio passaram a usar apenas os componentes de `components/ui/`, com acessibilidade (Radix) e variantes padronizadas.
- **Planos:** Uso de `Card`, `CardHeader`, `CardTitle`, `CardDescription`, `CardContent`, `CardFooter` e `Button` para os cards de assinatura.
- **Checkout:** Formulário de cartão e PIX passaram a usar `Input`, `Label` e `Button` de `components/ui/`.

---

## 1. Uso obrigatório

- **NUNCA** importe `@radix-ui/*` em páginas ou em componentes de feature.
- **SEMPRE** importe de `@/components/ui` (ex: `import { Button } from "@/components/ui/button"` ou `import { Button } from "@/components/ui"`).
- Para ícones: use **lucide-react** (ex: `import { Mail, Lock } from "lucide-react"`).

### 1.1 Select, CurrencyInput e DateInput (sempre usar)

- **Select (dropdown):** Use sempre o componente `Select` de `@/components/ui/select` em vez de `<select>` nativo. Ele oferece busca quando há muitos itens ou dados da API, visual padronizado e acessibilidade.
- **Valores em Real (R$):** Use sempre `CurrencyInput` de `@/components/ui/currency-input` para qualquer campo de valor monetário. Exibe e formata em padrão BRL (ex.: 1.234,56).
- **Datas:** Use sempre `DateInput` de `@/components/ui/date-input` para campos de data. Permite digitar (dd/MM/yyyy) ou abrir o calendário personalizado; o valor é mantido em formato da API (yyyy-MM-dd).

### 1.2 Barra de busca (sem botão, com debounce)

- **Não** inclua botão “Buscar” ao lado do campo de busca. A busca deve ser disparada automaticamente após o usuário parar de digitar.
- Use **debounce** de cerca de **500 ms** (meio segundo): após o usuário parar de digitar por esse tempo, dispare a busca (ex.: `loadClients`, `loadAnimals`).
- Implementação típica: `useEffect` com `setTimeout` e dependências no termo de busca (e filtros, se aplicável); retorne a função de cleanup que chama `clearTimeout`.
- Headers de cards/tabelas (Agenda do dia, Animais, Clientes, etc.): use as classes de `app/(dashboard)/_components/tableStyles.ts` (`tableHeaderStrip`, `tableTitleRow`, `tableIconBox`, etc.) para manter o mesmo visual (degradê e estrutura).

---

## 2. Utilitário `cn()`

**Arquivo:** `lib/utils.ts`

```ts
import { cn } from "@/lib/utils";

<div className={cn("base-classes", condition && "conditional", className)} />
```

- Use para merge de classes Tailwind sem conflitos.
- Componentes UI usam `cn()` internamente para aceitar `className` do consumidor.

---

## 3. Componentes disponíveis

### 3.1 Button

**Arquivo:** `components/ui/button.tsx`  
**Base:** Radix Slot + class-variance-authority (CVA).

**Variantes:**

| Variant    | Uso                          |
|-----------|-------------------------------|
| `primary` | Ação principal (padrão). Cor `#154734`. |
| `secondary` | Borda, fundo branco.        |
| `ghost`   | Sem borda, hover suave.       |
| `link`    | Estilo de link (underline).   |

**Tamanhos:** `sm`, `default`, `lg`, `icon`.

**Exemplo:**

```tsx
import { Button } from "@/components/ui/button";

<Button>Salvar</Button>
<Button variant="secondary" size="sm">Cancelar</Button>
<Button variant="link" asChild>
  <Link href="/login">Entrar</Link>
</Button>
```

**Props:** Estende `React.ButtonHTMLAttributes`. `asChild` usa Radix Slot para renderizar como filho (ex: `Link`).

---

### 3.2 Input

**Arquivo:** `components/ui/input.tsx`

- Borda `#27323F/20`, fundo branco, focus ring `#154734/30`.
- **leftIcon:** opcional, ícone à esquerda (ex: `<Mail className="h-5 w-5" />`).

**Exemplo:**

```tsx
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Mail } from "lucide-react";

<Label htmlFor="email">Email</Label>
<Input
  id="email"
  type="email"
  placeholder="seu@email.com"
  leftIcon={<Mail className="h-5 w-5" />}
/>
```

---

### 3.3 Label

**Arquivo:** `components/ui/label.tsx`  
**Base:** `@radix-ui/react-label`.

- Texto `#27323F`, `text-sm`, `font-medium`.
- Associe com `htmlFor` ao id do input/checkbox.

**Exemplo:**

```tsx
<Label htmlFor="name">Nome</Label>
<Input id="name" />
```

---

### 3.4 Checkbox

**Arquivo:** `components/ui/checkbox.tsx`  
**Base:** `@radix-ui/react-checkbox`. Ícone de check: lucide-react `Check`.

- Estado controlado: `checked` + `onCheckedChange((checked) => setValue(checked === true))`.

**Exemplo:**

```tsx
import { Checkbox } from "@/components/ui/checkbox";

<Checkbox
  id="remember"
  checked={rememberMe}
  onCheckedChange={(c) => setRememberMe(c === true)}
/>
<Label htmlFor="remember">Lembrar-me</Label>
```

---

### 3.5 RadioGroup / RadioGroupItem

**Arquivo:** `components/ui/radio-group.tsx`  
**Base:** `@radix-ui/react-radio-group`.

- Valor controlado: `value` + `onValueChange`.
- Cada opção: `RadioGroupItem value="x"` + `Label` ao lado.

**Exemplo:**

```tsx
<RadioGroup value={type} onValueChange={setType} className="flex flex-col gap-2">
  <div className="flex items-center gap-2">
    <RadioGroupItem value="new" id="r1" />
    <Label htmlFor="r1">Nova empresa</Label>
  </div>
  <div className="flex items-center gap-2">
    <RadioGroupItem value="link" id="r2" />
    <Label htmlFor="r2">Vincular</Label>
  </div>
</RadioGroup>
```

---

### 3.6 Select (dropdown com busca)

**Arquivo:** `components/ui/select.tsx`

- Use **sempre** em vez de `<select>` nativo.
- Props: `options` (`{ value, label }[]`), `value`, `onChange`, `placeholder`, `emptyLabel?` (opção vazia, ex.: "Selecione (opcional)"), `fromApi?` (ativa busca), `searchable?`.
- Busca aparece automaticamente quando `fromApi` ou quando há mais de 8 opções.

**Exemplo:**

```tsx
<Select
  options={items.map((i) => ({ value: i.id, label: i.name }))}
  value={selectedId}
  onChange={setSelectedId}
  placeholder="Selecione"
  emptyLabel="Selecione (opcional)"
  fromApi
/>
```

---

### 3.7 CurrencyInput (valor em R$)

**Arquivo:** `components/ui/currency-input.tsx`

- Use **sempre** para campos de valor monetário. Formata em padrão BRL (1.234,56); `onChange` recebe **número**.

**Exemplo:**

```tsx
<CurrencyInput value={amount} onChange={setAmount} required />
```

---

### 3.8 DateInput (data + calendário)

**Arquivo:** `components/ui/date-input.tsx`

- Use **sempre** para datas. Permite digitar (dd/MM/yyyy) ou abrir o calendário. Valor em `yyyy-MM-dd` para a API.

**Exemplo:**

```tsx
<DateInput value={dateStr} onChange={setDateStr} required />
```

---

### 3.10 Card

**Arquivo:** `components/ui/card.tsx`

- Subcomponentes: `Card`, `CardHeader`, `CardTitle`, `CardDescription`, `CardContent`, `CardFooter`.
- Borda `#27323F/15`, fundo branco, sombra suave, hover com mais sombra.

**Exemplo:**

```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card";

<Card>
  <CardHeader>
    <CardTitle>Plano Básico</CardTitle>
    <CardDescription>Até 1 usuário</CardDescription>
  </CardHeader>
  <CardContent>...</CardContent>
  <CardFooter>
    <Button>Assinar</Button>
  </CardFooter>
</Card>
```

---

## 4. Layout de autenticação

### AuthLayoutShell

**Arquivo:** `components/auth/AuthLayoutShell.tsx`

- Duas colunas: esquerda (formulário em branco), direita (painel decorativo com gradiente).
- Props: `headerRight`, `children`, `showFooter?`.
- Use em login, registro, recuperar senha para manter o mesmo layout.

**Exemplo:**

```tsx
<AuthLayoutShell
  headerRight={
    <>Novo por aqui? <Link href="/register">Registrar</Link></>
  }
>
  <h1>Faça login</h1>
  <form>...</form>
</AuthLayoutShell>
```

---

## 5. Cores do sistema (tema light)

| Uso        | Variável / valor |
|-----------|-------------------|
| Texto     | `#27323F`         |
| Secundário (botão, links) | `#154734` |
| Fundo     | `#fff`            |
| Bordas/placeholder | `#27323F` com opacidade (ex: `/20`, `/70`) |

- Inputs: fundo branco, sem fundo azul; focus com ring `#154734/30`.

---

## 6. Quando criar novo componente UI

1. Verificar se já existe em `components/ui/`.
2. Se não existir: criar em `components/ui/[nome].tsx`, baseado em Radix quando fizer sentido (acessibilidade).
3. Usar `cn()` para className e CVA para variantes quando houver várias (ex: Button).
4. Exportar em `components/ui/index.ts`.
5. Atualizar este documento.

---

## 7. Máscaras de input

- **Regra:** Todos os campos que representam CPF, CNPJ, telefone, CEP, valor monetário ou data em input **devem** usar máscara (biblioteca **react-imask**).
- **Documentação:** Ver **docs/MASKS_GUIDE.md** para lista de campos obrigatórios e padrões de máscara.
- Quando existir componente `InputMask` (ou similar) em `components/ui/`, usá-lo em formulários em vez de `Input` para esses tipos.

---

## 8. Headers de cards/tabelas (dashboard)

Para padronizar o visual dos blocos do dashboard (Agenda do dia, Animais, Clientes e Animais, Setor comercial, Entradas e saídas):

- **Arquivo:** `app/(dashboard)/_components/tableStyles.ts`
- Use `tableCard`, `tableHeaderStrip`, `tableTitleRow`, `tableTitleBlock`, `tableIconBox` para o cabeçalho com degradê (`from-[var(--dash-accent-soft)] to-transparent`).
- A tela Clientes e Animais reexporta esses estilos em `app/(dashboard)/clients-equines/_components/tableStyles.ts`.

---

## 9. Referências

- **ARCHITECTURE.md** (raiz do repositório): regras gerais; componentes UI em `components/ui/`.
- **CONTEXT_GUIDE.md**: uso de Contexts; não misturar lógica de UI nos Contexts.
- **SERVICES_GUIDE.md**: chamadas API; formulários de auth podem usar fetch direto ou, no futuro, services de auth.
- **MASKS_GUIDE.md**: máscaras obrigatórias por tipo de campo.
