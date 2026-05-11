# 02 — Arquitetura do Sistema

## Visão Geral da Arquitetura

O sistema segue uma arquitetura **monorepo com separação clara entre frontend, backend e módulo de IA**.

```
┌─────────────────────────────────────────────────────────┐
│                        CLIENTE                          │
│                                                         │
│   ┌──────────────┐  ┌──────────────┐  ┌─────────────┐  │
│   │  Portal do   │  │  Dashboard   │  │   Painel    │  │
│   │  Paciente    │  │   Médico     │  │    Admin    │  │
│   └──────┬───────┘  └──────┬───────┘  └──────┬──────┘  │
│          └─────────────────┼──────────────────┘         │
│                     React + TypeScript                   │
└─────────────────────────┬───────────────────────────────┘
                          │ HTTPS / REST
┌─────────────────────────▼───────────────────────────────┐
│                        BACKEND                          │
│                                                         │
│   ┌─────────────────────────────────────────────────┐   │
│   │              API REST (NestJS)                  │   │
│   │                                                 │   │
│   │  Auth │ Patients │ Doctors │ Appointments │ AI  │   │
│   └──────────────────────┬──────────────────────────┘   │
│                          │                              │
│   ┌───────────┐  ┌───────▼────────┐  ┌──────────────┐  │
│   │  Prisma   │  │  AI Service    │  │   File Store │  │
│   │   ORM     │  │  (OpenAI/      │  │  (Exames,    │  │
│   └─────┬─────┘  │   Claude)      │  │   Receitas)  │  │
│         │        └────────────────┘  └──────────────┘  │
└─────────┼───────────────────────────────────────────────┘
          │
┌─────────▼───────────────────────────────────────────────┐
│                      BANCO DE DADOS                     │
│                      PostgreSQL                         │
└─────────────────────────────────────────────────────────┘
```

---

## Estrutura de Pastas (Monorepo)

```
medai-platform/
├── apps/
│   ├── web/                  # Frontend React
│   │   ├── src/
│   │   │   ├── pages/
│   │   │   ├── components/
│   │   │   ├── hooks/
│   │   │   ├── services/     # chamadas à API
│   │   │   ├── stores/       # estado global
│   │   │   └── types/
│   │   └── package.json
│   │
│   └── api/                  # Backend NestJS
│       ├── src/
│       │   ├── modules/
│       │   │   ├── auth/
│       │   │   ├── users/
│       │   │   ├── patients/
│       │   │   ├── doctors/
│       │   │   ├── appointments/
│       │   │   ├── exams/
│       │   │   ├── prescriptions/
│       │   │   └── ai/
│       │   ├── prisma/
│       │   └── common/
│       └── package.json
│
├── packages/
│   └── shared-types/         # tipos TypeScript compartilhados
│
├── docs/                     # esta documentação
├── prisma/
│   └── schema.prisma
└── package.json              # workspace root
```

---

## Camadas da Aplicação

### Frontend (apps/web)
- **Páginas**: rotas da aplicação por perfil de usuário
- **Componentes**: UI reutilizável (forms, cards, modais, tabelas)
- **Hooks**: lógica de estado e efeitos encapsulada
- **Services**: abstração das chamadas HTTP à API
- **Stores**: estado global com Zustand ou Context API

### Backend (apps/api)
- **Controllers**: recebem requisições HTTP e delegam para services
- **Services**: lógica de negócio
- **Repositories**: acesso ao banco via Prisma
- **Guards**: validação de autenticação e autorização por perfil
- **DTOs**: validação e tipagem de entrada de dados

### Banco de Dados
- PostgreSQL como banco principal
- Prisma como ORM (migrations, tipagem, query builder)

### Módulo de IA
- Integração com OpenAI API (GPT-4) para chatbot e triagem
- Integração com Claude API para análise clínica e resumos
- Prompts estruturados por contexto médico
- Histórico de conversação persistido por paciente

---

## Decisões de Arquitetura

| Decisão | Escolha | Justificativa |
|---|---|---|
| Framework backend | NestJS | Estrutura modular, TypeScript nativo, fácil escalabilidade |
| ORM | Prisma | Tipagem forte, migrations declarativas, DX excelente |
| Banco | PostgreSQL | Relacional, robusto, suporte a JSON para dados flexíveis |
| Frontend | React + TypeScript | Ecossistema maduro, componentes reutilizáveis |
| Estilo | Tailwind CSS | Utilitário, rápido de iterar, design system fácil |
| Monorepo | Simples com workspaces | Compartilhamento de tipos sem overhead de turborepo inicial |
| Auth | JWT + Refresh Tokens | Stateless, escalável, padrão de mercado |
| IA | OpenAI + Claude | Complementares: GPT para chat, Claude para análise longa |

---

## Fluxo de Dados

```
Paciente → Frontend → API (Guard: autenticado?) 
    → Service (lógica) → Prisma → PostgreSQL
    
Paciente → Chatbot → API /ai/chat 
    → AI Service → OpenAI API 
    → salva histórico → retorna resposta
```

---

## Segurança

- Todas as rotas autenticadas exigem JWT válido
- Controle de acesso por papel (PATIENT / DOCTOR / ADMIN)
- Dados sensíveis nunca retornam em respostas desnecessárias
- Validação de entrada em todos os endpoints com class-validator
- Rate limiting no módulo de IA para controlar custos e abuso
- HTTPS obrigatório em produção
