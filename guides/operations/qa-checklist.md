# Procedimentos de validação

Comandos e fluxos para validar alterações em ambiente de desenvolvimento.

## Verificação técnica

| Aplicação | Comandos / referência |
|---|---|
| API | `yarn prisma generate`, `yarn tsc --noEmit`, `yarn build` |
| API — unitários | `yarn test`, configurado em `vitest.config.ts` |
| API — integração HTTP | `yarn test:e2e`, configurado em `vitest.config.e2e.ts` |
| API — exclusão de conta | `yarn vitest run --config vitest.config.account-deletion.ts` |
| Web | `yarn lint`, `yarn build` |
| Painel | `yarn tsc --noEmit`, `yarn eslint .`, `yarn build` |
| Institucional | `yarn lint`, `yarn build` |
| App | `yarn tsc --noEmit`, execução no dispositivo e build EAS da plataforma |

No painel, execute o ESLint diretamente, como indicado acima.

### Banco dos testes

Os testes com banco usam **PostgreSQL local descartável**. O setup E2E lê `.env` e depois `.env.test.local`, ambos com override; defina as URLs locais nesses arquivos antes da execução. Ele cria um schema de teste, aplica migrations e remove esse schema ao concluir.

O teste específico de exclusão de conta usa `vitest.config.account-deletion.ts`, recebe `DATABASE_URL` do processo e exige banco local com nome `account_deletion_test`. A preparação da base e os cenários estão em `test/account-deletion.integration.ts`. Esse teste é executado separadamente do setup E2E.

## Preparação dos percursos

Use identidades e dados do ambiente de validação. Prepare uma clínica, usuário profissional, tutor com telefone e administrador. Integrações de pagamento/e-mail usam o ambiente e os destinatários acordados para a execução.

| Percurso | Ação | Resultado a conferir |
|---|---|---|
| Acesso profissional | Entrar na web e abrir o início | Sessão da clínica e dados correspondentes |
| Cadastros | Criar cliente com telefone, propriedade e animal | Registros visíveis após recarregar |
| Primeiro acesso | No app, informar o telefone e definir e-mail/senha | Sessão do tutor e perfil correspondente |
| Conta já cadastrada | Consultar primeiro acesso de conta com e-mail | Orientação de entrada/recuperação |
| Recuperação | Usar recuperação de senha e identificação de e-mail | Fluxo de acesso correspondente |
| Atendimento | Criar atendimento, selecionar animal, registrar ficha e anexo | Conteúdo persistido e consultável |
| Compartilhamento | Compartilhar anotação/prescrição e consultar no app | Conteúdo disponível ao tutor vinculado |
| Notas do tutor | Criar uma anotação própria no app | Nota disponível na área do proprietário |
| Fatura | Emitir e consultar itens, realizar pagamento de teste | Situação da fatura e lançamento financeiro correspondentes |
| Assinatura | Exercitar contratação/validação no ambiente de integração | Situação apresentada de acordo com a assinatura |
| Painel | Consultar empresas/planos e publicar um tutorial/anúncio de teste | Conteúdo persistido e retornado pela API |
| Institucional | Abrir parceiros, tutorial, termos, privacidade e exclusão | Páginas e conteúdo correspondentes |
| Exclusão de conta | Excluir uma conta descartável pelo perfil do app | Sessão encerrada e tratamento de dados conforme contrato |

## Percurso offline da web

1. Entrar e carregar dados de um atendimento com conexão.
2. Desconectar a rede e criar/editar um registro clínico elegível.
3. Conferir o indicador de gravação local e a fila em **Sincronização**.
4. Restabelecer a conexão e acompanhar o envio.
5. Reabrir o registro e conferir a persistência no servidor.
6. Conferir uma nova tentativa da mesma operação e as referências entre atendimento, ficha e anexos.
