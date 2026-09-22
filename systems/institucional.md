# Site institucional — equinology-institutional

**Stack:** Next.js 16.1.4, React 19.2.3, Tailwind 4, Framer Motion e Lenis.

## Páginas e conteúdo

| Caminho | Conteúdo |
|---|---|
| `src/app/page.tsx` | Apresentação do produto, recursos e parceiros |
| `src/app/tutoriais/page.tsx` | Biblioteca de tutoriais |
| `src/app/tutoriais/[id]/page.tsx` | Detalhe de tutorial, com conteúdo de vídeo/PDF |
| `src/app/termos/page.tsx` | Termos de uso |
| `src/app/privacidade/page.tsx` | Política de privacidade |
| `src/app/excluir-conta/page.tsx` | Orientações para exclusão da conta |

As páginas informativas usam conteúdo centralizado em `src/lib/legal.ts` e componentes em `src/components/legal/`.

## Integração

`src/lib/config.ts` reúne as URLs da API, da web profissional, contatos e endpoints públicos.

- API padrão: `https://api.equinology.com.br`.
- Web padrão: `https://app.equinology.com.br`.
- Patrocinadores: `/ads/sponsors`, com consumo em `src/lib/sponsors.ts`.
- Tutoriais: `/tutorials`, com consumo em `src/lib/tutorials.ts`.
- O conteúdo é gerido pelo painel administrativo.
- Links de entrada/contratação levam à web; contatos comerciais usam a configuração do institucional.

Confira as variáveis de ambiente no [setup](../guides/developer/setup.md). O endpoint de patrocinadores possui um default próprio, portanto configure-o explicitamente ao usar uma API local.

Publique como uma aplicação Next.js independente, conforme [deploy](../guides/operations/deploy.md).
