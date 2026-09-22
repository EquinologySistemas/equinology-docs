> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Vetequus api API

A **Vetequus api API** foi desenvolvida para coleta e armazenamento de notícias. É parte do sistema **Vetequus api**, que coleta, analisa e gera notícias baseado em IA, alimentando diversos blogs com conteúdo.

## Sumário

- [Objetivo](#objetivo)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Principais Funcionalidades](#principais-funcionalidades)
- [Instalação](#instalação)
- [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
- [Execução do Projeto](#execução-do-projeto)
- [Testes](#testes)
- [Rotas e Documentação](#rotas-e-documentação)
- [Deploy da Aplicação](#deploy-da-aplicação)

## Objetivo

A API Vetequus api foi criada para o sistema **Vetequus api**, com o propósito de:

- Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the i

## Tecnologias Utilizadas

- **Linguagem de desenvolvimento**: JavaScript/TypeScript
- **Framework**: [NestJS](https://nestjs.com/)
- **Banco de dados**: PostgreSQL
- **Gerenciamento de dependências**: Yarn
- **Documentação de rotas**: Disponível em `/reference`

## Principais Funcionalidades

A API Vetequus api oferece as seguintes funcionalidades:

- **Lorem Ipsum is simply dummy text of the printing**: Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the i.

## Instalação

### Pré-requisitos

Certifique-se de ter os seguintes requisitos instalados:

- [Node.js](https://nodejs.org) v14 ou superior
- [Yarn](https://yarnpkg.com/) v1.22 ou superior
- [Docker](https://www.docker.com/) e [Docker Compose](https://docs.docker.com/compose/) para o gerenciamento do banco de dados PostgreSQL

### Passos para instalação

1. Clone o repositório:
   ```bash
   git clone https://github.com/vetequus-api vetequus-api-api
   cd vetequus-api-api
   ```
2. Instale as dependências:

   ```bash
   yarn
   ```

3. Crie e inicialize o banco de dados utilizando Docker:

   ```bash
   docker-compose up -d
   ```

4. Aplique as migrações do banco de dados:

   ```bash
   yarn prisma migrate deploy
   ```

5. Crie um arquivo `.env` na raiz do projeto com base no arquivo `.env.example` e configure suas variáveis de ambiente.

## Configuração do Banco de Dados

O banco de dados utilizado pela API é o **PostgreSQL**. Para configurar corretamente o banco de dados, siga os passos mencionados na seção de [Instalação](#instalação).

Você pode ajustar as variáveis de ambiente no arquivo `.env` para definir a conexão com o banco de dados.

## Execução do Projeto

Após configurar o ambiente, você pode iniciar o servidor de desenvolvimento:

```bash
yarn start:dev
```

- O servidor será iniciado na porta `3333` por padrão.
- A documentação das rotas estará acessível em `http://localhost:3333/reference`.

## Testes

A API Vetequus api oferece suporte a testes unitários, de integração e end-to-end. Abaixo estão os comandos para executar cada tipo de teste:

- **Testes de Integração**:

  ```bash
  yarn test:watch
  ```

- **Testes de Integração (UI + Coverage)**:

  ```bash
  yarn test:watch:ui
  ```

- **Testes End-to-End**:

  ```bash
  yarn test:e2e:watch
  ```

- **Testes End-to-End (UI + Coverage)**:

  ```bash
  yarn test:e2e:watch:ui
  ```

- **Verificação de formatação de código**:

  ```bash
  yarn lint
  ```

- **Verificação de erros de TypeScript**:
  ```bash
  yarn tsc --noEmit
  ```

## Rotas e Documentação

A documentação completa das rotas da API está disponível localmente após o servidor ser iniciado. Acesse a documentação em:

```
http://localhost:3333/reference
```

Aqui, você encontrará detalhes sobre todas as rotas, métodos de requisição, parâmetros e exemplos de uso.

## Deploy da Aplicação

A API foi desenvolvida e pensada para deploy em servidores como o AWS EC2, para facilitar o deploy e evitar problemas de build em produção
o sistema possui um arquivo de deploy automático configurado para Windows Powershell, chamado `deploy.ps1` crie esse arquivo baseado no `example.deploy.ps1` alterando as informações iniciais.

- `PEM_KEY`: Caminho para a chave de acesso do servidor (ex: Vetequus api.pem)
- `USER`: usuário do servidor (ex: Ubuntu)
- `SERVER`: string de conexão do servidor (ex: ec2-123-123-123.compute-1.amazonaws.com)

- **Deploy da Aplicação**:

  ```bash
  ./deploy.ps1
  ```

  Caso esteja utilizando outro sistema operacional considere adaptar o arquivo de deploy de maneira que funcione em seu sistema.
