# Painel administrativo — equinology-adm-v2

**Stack:** Next.js 16, React 19 e Tailwind 4.

## Áreas

A entrada pública é `src/app/login/page.tsx`. As páginas de operação ficam em `src/app/(private)/`:

| Área | Caminho |
|---|---|
| Indicadores e visão financeira | `page.tsx` |
| Usuários e clínicas | `users/`, `companies/` |
| Planos, cupons e assinaturas | `plans/`, `coupons/`, `subscriptions/` |
| Financeiro | `financial/` |
| Anúncios e segmentação | `ads/` |
| Biblioteca de tutoriais | `tutorials/` |
| Equipe administrativa | `admins/` |

## Integração

`src/context/ApiContext.tsx` usa Axios e `NEXT_PUBLIC_API_URL`. O nome do cookie é resolvido por `src/lib/auth-cookies.ts`, com configuração por `NEXT_PUBLIC_USER_TOKEN`.

O middleware do painel orienta a navegação com base na sessão. Na API, `AdminAuthGuard`, `AdminSuperAdminGuard` e a verificação da situação do administrador autorizam as operações.

Os usuários administrativos são registros `AdminUser`, separados dos profissionais das clínicas. As permissões de `support` e `super_admin` seguem os guards e os controles das telas.

## Conteúdo publicado

Os anúncios têm descrição e segmentação geográfica. A API fornece conteúdo de anúncios para a web e uma consulta pública de patrocinadores para o institucional.

Os tutoriais possuem metadados e conteúdo organizado pela API. O institucional consome essa biblioteca em `/tutoriais` e nas páginas de detalhe.

## Desenvolvimento

Use [setup](../guides/developer/setup.md) e [validação](../guides/operations/qa-checklist.md). Os comandos de publicação Next.js estão em [deploy](../guides/operations/deploy.md).

Os relatórios em `docs/auditoria/`, `docs/auditoria-lancamento/` e os documentos de status datados registram etapas de trabalho. Esta página e o índice de `equinology-docs` são a entrada para a manutenção atual.
