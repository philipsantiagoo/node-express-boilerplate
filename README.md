# node-express-boilerplate

Boilerplate pessoal de API REST com Node.js, Express, Prisma, TypeScript, Vitest e Docker. Criado para eliminar setup repetitivo e garantir consistência entre projetos de backend.

> **Importante**: Este é um boilerplate, não um projeto pronto. Ele fornece a estrutura, configurações e convenções base. Você vai adicionar models, rotas, controllers e lógica de negócio por cima disso.

---

## Índice

- [O que é uma API REST?](#o-que-é-uma-api-rest)
- [Como funciona o fluxo de uma requisição](#como-funciona-o-fluxo-de-uma-requisição)
- [Stack](#stack)
- [Pré-requisitos](#pré-requisitos)
- [Como usar este boilerplate](#como-usar-este-boilerplate)
- [Primeiros passos após clonar](#primeiros-passos-após-clonar)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Banco de dados](#banco-de-dados)
- [Testes](#testes)
- [CI/CD](#cicd)
- [Hooks de commit (Husky + lint-staged)](#hooks-de-commit-husky--lint-staged)
- [Scripts disponíveis](#scripts-disponíveis)
- [O que você precisa criar ao usar em um projeto real](#o-que-você-precisa-criar-ao-usar-em-um-projeto-real)
- [Como manter o boilerplate atualizado](#como-manter-o-boilerplate-atualizado)
- [Convenções de commit](#convenções-de-commit)

---

## O que é uma API REST?

Uma API REST é um servidor que recebe requisições HTTP (GET, POST, PUT, DELETE) e devolve respostas — geralmente em JSON. É o "meio de campo" entre o frontend (o que o usuário vê) e o banco de dados (onde os dados ficam guardados).

Exemplo: quando você faz login em um app, o frontend envia uma requisição `POST /auth/login` com seu email e senha. A API recebe, verifica no banco se o usuário existe, e devolve um token de acesso. O frontend usa esse token pra acessar rotas protegidas.

---

## Como funciona o fluxo de uma requisição

Toda requisição que chega na API passa por camadas bem definidas, em ordem:

```
Request (HTTP)
    ↓
Routes         → define qual controller vai tratar aquela rota
    ↓
Middlewares    → verifica auth, valida body, loga a requisição
    ↓
Controller     → recebe req/res, chama o service, devolve a resposta HTTP
    ↓
Service        → lógica de negócio (regras, cálculos, validações)
    ↓
Repository     → acessa o banco de dados via Prisma
    ↓
Database       → PostgreSQL
    ↑
(resposta sobe pelo mesmo caminho)
```

Cada camada tem uma responsabilidade única. Isso facilita testes, manutenção e entendimento do código.

---

## Stack

| Categoria             | Tecnologia                 | Versão | Por quê                                                 |
| --------------------- | -------------------------- | ------ | ------------------------------------------------------- |
| Runtime               | Node.js                    | 20.x   | Ambiente de execução JavaScript no servidor             |
| Framework             | Express                    | 5.x    | Minimalista, flexível, padrão do mercado para APIs Node |
| Linguagem             | TypeScript                 | 6.x    | Tipagem estática, autocomplete, segurança em refactors  |
| ORM                   | Prisma                     | 7.x    | Tipagem automática do banco, migrations, Prisma Studio  |
| Banco de dados        | PostgreSQL                 | 16     | Relacional, robusto, padrão do mercado                  |
| Containerização       | Docker + Docker Compose    | -      | Banco local sem instalar Postgres na máquina            |
| Validação             | Zod                        | 4.x    | Validação de schemas com tipagem TypeScript automática  |
| Segurança             | Helmet                     | 8.x    | Define headers HTTP de segurança automaticamente        |
| CORS                  | cors                       | 2.x    | Permite requisições de origens diferentes (frontend)    |
| Hash de senha         | bcrypt                     | 6.x    | Criptografia de senhas de forma segura                  |
| Variáveis de ambiente | dotenv                     | 17.x   | Carrega variáveis do `.env` para `process.env`          |
| Testes                | Vitest                     | 4.x    | Mais rápido que Jest, integração nativa com ESM         |
| Formatação            | Prettier                   | 3.x    | Formato de código consistente e automático              |
| Linting               | ESLint + TypeScript ESLint | 10.x   | Detecta erros e más práticas                            |
| CI                    | GitHub Actions             | -      | Roda lint, format check e testes em todo push           |
| Pre-commit            | Husky + lint-staged        | -      | Garante qualidade antes de cada commit                  |
| Runner de TS          | tsx                        | 4.x    | Executa TypeScript diretamente sem compilar             |

---

## Pré-requisitos

Antes de usar este boilerplate, você precisa ter instalado na sua máquina:

- **Node.js** 20 ou superior — [nodejs.org](https://nodejs.org)
- **pnpm** — `npm install -g pnpm`
- **Docker** e **Docker Compose** — [docs.docker.com](https://docs.docker.com/get-docker)
- **Git** — [git-scm.com](https://git-scm.com)

---

## Como usar este boilerplate

Este repositório é um **GitHub Template**. Para usá-lo em um novo projeto:

### Opção A — Via GitHub (recomendado)

1. Acesse o repositório no GitHub
2. Clique em **"Use this template"** → **"Create a new repository"**
3. Dê um nome ao novo repositório e crie
4. Clone o novo repositório na sua máquina

### Opção B — Via degit (sem histórico do boilerplate)

```bash
npx degit seu-usuario/node-express-boilerplate nome-do-projeto
cd nome-do-projeto
git init
git remote add origin https://github.com/seu-usuario/nome-do-projeto.git
```

> **Nunca** clone diretamente este repositório para iniciar um projeto. Use uma das opções acima para manter o histórico de commits limpo.

---

## Primeiros passos após clonar

Execute os passos abaixo **na ordem** sempre que iniciar um projeto a partir deste boilerplate:

```bash
# 1. Instalar dependências
pnpm install

# 2. Criar o arquivo de ambiente
cp .env.example .env
# Edite o .env com os valores corretos para o seu projeto

# 3. Subir o banco de dados local
docker compose up -d

# 4. Rodar as migrations (após criar seus models no schema.prisma)
pnpm db:migrate

# 5. Iniciar o servidor em modo desenvolvimento
pnpm dev
```

O servidor sobe em [http://localhost:3333](http://localhost:3333) por padrão.

---

## Estrutura do projeto

```
node-express-boilerplate/
├── .github/
│   └── workflows/
│       └── ci.yml                  # Pipeline de CI (lint, format, testes)
├── .husky/
│   └── pre-commit                  # Roda lint-staged antes de cada commit
├── .vscode/
│   └── settings.json               # Configs do VSCode específicas do projeto
├── prisma/
│   ├── schema.prisma               # Definição dos models do banco
│   ├── migrations/                 # Histórico de migrations (gerado pelo Prisma)
│   └── seed.ts                     # Script de seed — você cria
├── src/
│   ├── config/
│   │   └── env.ts                  # Variáveis de ambiente tipadas e validadas com Zod
│   ├── controllers/
│   │   └── (ex: user.controller.ts)# Recebe req/res, chama service, devolve resposta HTTP
│   ├── services/
│   │   └── (ex: user.service.ts)   # Lógica de negócio — regras, cálculos, validações
│   ├── repositories/
│   │   └── (ex: user.repository.ts)# Acesso ao banco via Prisma — isola queries
│   ├── routes/
│   │   └── (ex: user.routes.ts)    # Define rotas e aponta para os controllers
│   ├── middlewares/
│   │   └── (ex: auth.middleware.ts)# Auth, validação de body, error handler, logger
│   ├── lib/
│   │   └── prisma.ts               # Instância singleton do Prisma Client
│   ├── utils/
│   │   └── (ex: format.ts)         # Funções utilitárias puras e genéricas
│   ├── validations/
│   │   └── (ex: user.validation.ts)# Schemas Zod para validar req.body, params, query
│   ├── types/
│   │   └── (ex: express.d.ts)      # Tipos e interfaces TypeScript globais
│   ├── app.ts                      # Monta o Express — registra middlewares e rotas
│   └── server.ts                   # Ponto de entrada — sobe o servidor na porta
├── tests/
│   ├── unit/                       # Testes unitários — testam funções isoladas
│   ├── integration/                # Testes de integração — testam rotas completas
│   ├── setup.ts                    # Setup global do Vitest
│   └── example.test.ts             # Teste placeholder — substitua pelos seus
├── .env                            # Variáveis de ambiente locais — NÃO commitar
├── .env.example                    # Template das variáveis de ambiente — commitar sempre
├── .gitignore                      # Arquivos ignorados pelo Git
├── .prettierignore                 # Arquivos ignorados pelo Prettier
├── .prettierrc                     # Configuração do Prettier
├── docker-compose.yml              # Sobe o PostgreSQL local
├── eslint.config.mjs               # Configuração do ESLint
├── package.json                    # Dependências e scripts
├── prisma.config.ts                # Configuração do Prisma 7 (connection URL, paths)
├── tsconfig.json                   # Configuração do TypeScript
└── vitest.config.ts                # Configuração do Vitest
```

### Por que essa estrutura?

- **`routes/`** só define caminhos e aponta para controllers. Não tem lógica.
- **`controllers/`** só lida com HTTP — recebe req, chama service, devolve res. Não acessa banco diretamente.
- **`services/`** contém as regras de negócio. Não sabe nada de HTTP nem de banco diretamente.
- **`repositories/`** é o único lugar que fala com o Prisma. Se um dia trocar de ORM, só mexe aqui.
- **`middlewares/`** intercepta requisições antes de chegarem no controller. Usado para auth, validação, logs.
- **`config/env.ts`** valida as variáveis de ambiente na inicialização. Se uma variável obrigatória estiver faltando, o servidor não sobe e já avisa qual.
- **`validations/`** centraliza os schemas Zod. Um schema pode ser reutilizado no middleware de validação e nos testes.

### Exemplo de fluxo completo

```
GET /users/:id

→ routes/user.routes.ts         define a rota e aponta para o controller
→ middlewares/auth.middleware.ts verifica se o token JWT é válido
→ controllers/user.controller.ts recebe req.params.id, chama UserService.findById()
→ services/user.service.ts       valida o id, chama UserRepository.findById()
→ repositories/user.repository.ts faz prisma.user.findUnique({ where: { id } })
→ resposta volta pelo mesmo caminho até o controller que devolve res.json(user)
```

---

## Variáveis de ambiente

O arquivo `.env.example` contém todas as variáveis necessárias com valores de exemplo. **Nunca commite o `.env`**.

```env
# App
NODE_ENV="development"
PORT=3333

# Database
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/myapp"
```

As variáveis são validadas em `src/config/env.ts` usando Zod. Se uma variável obrigatória estiver ausente ou com formato inválido, o servidor não sobe:

```
Invalid environment variables: { DATABASE_URL: ['Required'] }
```

Ao adicionar uma nova variável de ambiente:

1. Adicione no `.env` com o valor real
2. Adicione no `.env.example` com valor de exemplo ou vazio
3. Adicione no schema de validação em `src/config/env.ts`
4. Documente aqui neste README

---

## Banco de dados

### Subir o banco local

```bash
docker compose up -d
```

Isso sobe um container PostgreSQL na porta `5432` com:

- **Usuário**: `postgres`
- **Senha**: `postgres`
- **Database**: `myapp`

> Ao usar em um projeto real, altere os valores no `docker-compose.yml` e no `.env`.

### Parar o banco

```bash
docker compose down
```

> Use sempre `docker compose down` antes de desligar a máquina para evitar processos zumbis ocupando a porta 5432.

### Workflow de migrations

Migrations são arquivos que registram cada alteração feita no banco de dados. O Prisma gerencia isso automaticamente.

```bash
# Criar e aplicar uma nova migration após editar o schema.prisma
pnpm db:migrate

# Regenerar o Prisma Client após mudanças no schema (sem criar migration)
pnpm db:generate

# Abrir o Prisma Studio (interface visual do banco no navegador)
pnpm db:studio

# Rodar o seed (dados iniciais para desenvolvimento)
pnpm db:seed

# Resetar o banco (apaga tudo e reaplica migrations + seed)
pnpm db:reset
```

### Após resetar volumes do Docker

Se você apagou os volumes do Docker, as migrations precisam ser reaplicadas:

```bash
docker compose down -v        # Para e apaga volumes
docker compose up -d          # Sobe novamente
pnpm db:migrate               # Reaplica as migrations
pnpm db:seed                  # Roda o seed (se necessário)
```

### Instância do Prisma Client

O arquivo `src/lib/prisma.ts` exporta uma instância singleton do Prisma Client. Use sempre esse import nos repositories:

```typescript
import { prisma } from "../lib/prisma";
```

O padrão singleton evita criar múltiplas conexões com o banco durante o desenvolvimento.

---

## Testes

O projeto usa **Vitest** para testes.

```bash
# Rodar em modo watch (re-executa ao salvar)
pnpm test

# Rodar todos os testes uma vez (usado no CI)
pnpm test:run

# Abrir a interface visual do Vitest
pnpm test:ui
```

### Tipos de teste

**Unitários** (`tests/unit/`) — testam uma função ou classe isolada, sem banco de dados:

```typescript
// tests/unit/user.service.test.ts
it("should hash password", async () => {
  const hash = await hashPassword("123456");
  expect(hash).not.toBe("123456");
});
```

**Integração** (`tests/integration/`) — testam uma rota completa, do HTTP até o banco:

```typescript
// tests/integration/user.test.ts
it("GET /users/:id should return user", async () => {
  const response = await request(app).get("/users/1");
  expect(response.status).toBe(200);
  expect(response.body).toHaveProperty("email");
});
```

### Convenção de nomenclatura

```
NomeDoArquivo.test.ts         # teste unitário
NomeDoArquivo.integration.ts  # teste de integração
```

---

## CI/CD

O pipeline de CI roda automaticamente em todo push e pull request para `main` e `develop`.

### O que o CI verifica

1. **Lint** — `pnpm lint` — garante que não há erros de ESLint
2. **Format check** — `pnpm format:check` — garante que o código está formatado com Prettier
3. **Testes** — `pnpm test:run` — garante que todos os testes passam

---

## Hooks de commit (Husky + lint-staged)

O projeto usa **Husky** para rodar verificações automaticamente antes de cada commit, eliminando a necessidade de rodar `pnpm format` e `pnpm lint` manualmente.

### O que roda no pre-commit

| Arquivos                      | O que executa           |
| ----------------------------- | ----------------------- |
| `*.ts, *.js`                  | ESLint --fix + Prettier |
| `*.json, *.md, *.yml, *.yaml` | Prettier                |

O lint-staged garante que **apenas os arquivos staged** sejam verificados — não o projeto inteiro. Isso torna o processo rápido mesmo em projetos grandes.

### Como funciona

Ao rodar `git commit`, o Husky intercepta e executa `pnpm lint-staged`. Se algum arquivo tiver erro de lint que não possa ser corrigido automaticamente, o commit é bloqueado até você corrigir.

> O Husky é inicializado automaticamente via script `prepare` no `package.json` ao rodar `pnpm install`.

---

## Scripts disponíveis

| Script          | Comando             | O que faz                                                       |
| --------------- | ------------------- | --------------------------------------------------------------- |
| Desenvolvimento | `pnpm dev`          | Inicia o servidor com hot reload via tsx watch                  |
| Build           | `pnpm build`        | Compila TypeScript para JavaScript em `dist/`                   |
| Produção        | `pnpm start`        | Inicia o servidor compilado (requer build)                      |
| Lint            | `pnpm lint`         | Roda o ESLint em `src/`                                         |
| Formatar        | `pnpm format`       | Formata todos os arquivos com Prettier                          |
| Checar formato  | `pnpm format:check` | Verifica se os arquivos estão formatados (usado no CI)          |
| Migration       | `pnpm db:migrate`   | Cria e aplica uma nova migration                                |
| Generate        | `pnpm db:generate`  | Regenera o Prisma Client                                        |
| Studio          | `pnpm db:studio`    | Abre o Prisma Studio na porta 5555                              |
| Seed            | `pnpm db:seed`      | Executa o arquivo `prisma/seed.ts`                              |
| Reset banco     | `pnpm db:reset`     | Apaga o banco e reaplica tudo do zero                           |
| Testes          | `pnpm test`         | Roda Vitest em modo watch                                       |
| Testes (CI)     | `pnpm test:run`     | Roda Vitest uma vez e encerra                                   |
| Testes UI       | `pnpm test:ui`      | Abre a interface visual do Vitest                               |
| Setup           | `pnpm setup`        | Instala dependências e cria o `.env` a partir do `.env.example` |

---

## O que você precisa criar ao usar em um projeto real

Ao iniciar um novo projeto a partir deste boilerplate, as seguintes coisas **não estão incluídas** e precisam ser criadas:

### Obrigatório

- [ ] **Models no `prisma/schema.prisma`** — defina as entidades do seu projeto (User, Post, etc)
- [ ] **Migrations** — rode `pnpm db:migrate` após definir os models
- [ ] **`.env`** — preencha com os valores reais (`cp .env.example .env`)
- [ ] **`prisma/seed.ts`** — crie dados iniciais para desenvolvimento
- [ ] **Rotas** em `src/routes/` — defina os endpoints da API
- [ ] **Controllers** em `src/controllers/` — trate as requisições HTTP
- [ ] **Services** em `src/services/` — implemente a lógica de negócio
- [ ] **Repositories** em `src/repositories/` — implemente o acesso ao banco

### Dependendo do projeto

- [ ] **Autenticação** — middleware JWT em `src/middlewares/auth.middleware.ts`
- [ ] **Error handler global** — middleware em `src/middlewares/error.middleware.ts`
- [ ] **Logger** — middleware de log de requisições (ex: morgan, pino)
- [ ] **`Dockerfile`** — necessário para deploy em VPS, Railway, Fly.io, etc
- [ ] **Variáveis de ambiente adicionais** — adicione no `.env`, `.env.example` e `src/config/env.ts`
- [ ] **Deploy workflow** — adicione `.github/workflows/deploy.yml` para CD automático

### Exemplo de estrutura de arquivos para um recurso `User`

```
src/
  routes/
    user.routes.ts
  controllers/
    user.controller.ts
  services/
    user.service.ts
  repositories/
    user.repository.ts
  validations/
    user.validation.ts
  types/
    user.types.ts
```

### Dockerfile base (quando necessário)

```dockerfile
FROM node:20-alpine AS base
RUN npm install -g pnpm

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3333
CMD ["node", "dist/server.js"]
```

### Registrando rotas no `app.ts`

Ao criar novas rotas, registre no `src/app.ts`:

```typescript
import userRoutes from "./routes/user.routes";

app.use("/users", userRoutes);
```

---

## Como manter o boilerplate atualizado

O boilerplate deve evoluir conforme você aprende e resolve problemas recorrentes. Regra geral: **se você configurou algo chato em um projeto, volta aqui e atualiza**.

### Quando atualizar

- Resolveu um problema de configuração (ESLint, Prisma, Docker, CI)
- Adicionou um middleware que faz sentido em todo projeto (ex: error handler global)
- Encontrou uma estrutura melhor
- Atualizou uma dependência major e precisou adaptar configs

### Como atualizar

```bash
# Clone o boilerplate diretamente (não via template)
git clone https://github.com/seu-usuario/node-express-boilerplate.git
cd node-express-boilerplate

# Faça as alterações necessárias
git add .
git commit -m "chore: add global error handler middleware"
git push origin main
```

### Atualizando dependências

```bash
# Ver dependências desatualizadas
pnpm outdated

# Atualizar interativamente
pnpm update --interactive --latest
```

> Após atualizar dependências major, sempre rode `pnpm dev`, `pnpm build` e `pnpm test:run` para garantir que nada quebrou.

### Convenção de commits no boilerplate

| Prefixo     | Quando usar                                      |
| ----------- | ------------------------------------------------ |
| `chore:`    | Atualização de dependências, configs, scripts    |
| `feat:`     | Adição de nova lib ou funcionalidade base        |
| `fix:`      | Correção de configuração quebrada                |
| `docs:`     | Atualização do README                            |
| `style:`    | Formatação, sem mudança de lógica                |
| `refactor:` | Reorganização de estrutura de pastas ou arquivos |

---

## Convenções de commit

Este boilerplate segue o padrão **Conventional Commits**:

```
tipo(escopo opcional): descrição curta

corpo opcional

rodapé opcional
```

**Exemplos:**

```
feat(users): add create user endpoint
fix(auth): fix token expiration check
chore: update dependencies
docs: update README with deployment section
refactor(repositories): extract base repository class
```
