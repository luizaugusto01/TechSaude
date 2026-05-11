# 10 — Stack Tecnológica

## Frontend

| Tecnologia | Versão | Uso | Justificativa |
|---|---|---|---|
| React | 18+ | Framework UI | Ecossistema maduro, componentes reutilizáveis, grande comunidade |
| TypeScript | 5+ | Linguagem | Tipagem estática previne erros e melhora DX |
| Tailwind CSS | 3+ | Estilo | Utilitário, rápido de iterar, fácil de criar design system consistente |
| React Router | 6+ | Roteamento | Padrão de facto para SPAs React |
| React Query | 5+ | Cache de dados | Gerencia estado de servidor, cache e revalidação automaticamente |
| Zustand | 4+ | Estado global | Simples, sem boilerplate, escala bem |
| React Hook Form | 7+ | Formulários | Performance, validação integrada, fácil de usar |
| Axios | 1+ | HTTP Client | Interceptors facilitam auth e tratamento de erros global |

---

## Backend

| Tecnologia | Versão | Uso | Justificativa |
|---|---|---|---|
| Node.js | 20 LTS | Runtime | JavaScript/TypeScript no servidor, mesmo ecossistema do frontend |
| NestJS | 10+ | Framework | Modular, TypeScript nativo, DI, guards, pipes — ótimo para APIs complexas |
| Prisma | 5+ | ORM | Tipagem automática, migrations declarativas, DX excelente |
| PostgreSQL | 15+ | Banco de dados | Relacional robusto, suporte a JSON, performance sólida |
| JWT | — | Autenticação | Stateless, escalável, padrão de mercado |
| Passport.js | — | Auth middleware | Integração nativa com NestJS, estratégias extensíveis |
| class-validator | — | Validação | Decorators para validar DTOs de forma declarativa |
| Bcrypt | — | Hashing | Padrão para hashing de senhas com salt |
| @nestjs/throttler | — | Rate limiting | Proteção contra abuso de APIs |
| Nodemailer | — | E-mail | Envio de notificações por e-mail |

---

## Inteligência Artificial

| Tecnologia | Uso | Quando |
|---|---|---|
| OpenAI API (GPT-4o) | Chatbot de triagem, perguntas interativas | Fase 1 |
| Anthropic Claude API | Resumos de histórico, análise clínica longa | Fase 2 |
| Python + PyMuPDF | Extração de texto de PDFs de exames | Fase 3 |
| OpenAI Vision API | Análise de imagens de exames | Fase 3 |

---

## Tooling e DevOps

| Ferramenta | Uso |
|---|---|
| pnpm Workspaces | Monorepo com compartilhamento de dependências |
| ESLint + Prettier | Lint e formatação consistentes |
| Husky + lint-staged | Hooks de pré-commit para qualidade |
| Docker Compose | Ambiente de desenvolvimento local (banco, app) |
| GitHub Actions | CI/CD (testes, build, deploy) |
| dotenv | Gerenciamento de variáveis de ambiente |

---

## Estrutura de Variáveis de Ambiente

```env
# apps/api/.env

# Banco
DATABASE_URL="postgresql://user:password@localhost:5432/medai"

# JWT
JWT_SECRET="sua-chave-secreta-aqui"
JWT_EXPIRES_IN="15m"
REFRESH_TOKEN_EXPIRES_IN="7d"

# IA
OPENAI_API_KEY="sk-..."
ANTHROPIC_API_KEY="sk-ant-..."

# E-mail
SMTP_HOST="smtp.mailtrap.io"
SMTP_PORT=2525
SMTP_USER="..."
SMTP_PASS="..."
EMAIL_FROM="noreply@medai.com"

# App
PORT=3001
FRONTEND_URL="http://localhost:3000"
NODE_ENV="development"
```

---

## Por que NestJS e não Express?

Express é mais simples de começar, mas para um sistema deste porte:

- **NestJS** oferece estrutura modular nativa, ideal para separar os domínios (auth, patients, doctors, ai)
- **Injeção de dependências** torna o código testável e desacoplado
- **Guards e interceptors** simplificam autenticação e autorização sem repetição
- **Decorators** tornam rotas e validações declarativas e legíveis
- TypeScript é cidadão de primeira classe, sem configuração extra

---

## Por que Prisma e não TypeORM?

- **DX superior**: autocomplete e tipos gerados automaticamente
- **Migrations** declarativas e legíveis
- **Prisma Studio**: interface visual para o banco durante desenvolvimento
- **Query builder** tipado elimina categoria inteira de erros de runtime

---

## Decisão: Monorepo Simples vs Turborepo

Por ora, **pnpm workspaces simples** é suficiente:
- Sem necessidade de cache de build distribuído na fase inicial
- Menor overhead de configuração
- Fácil migrar para Turborepo se o projeto crescer e o tempo de build se tornar problema
