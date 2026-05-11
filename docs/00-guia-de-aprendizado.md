# 00 — Guia de Aprendizado: Como Usar Esta Documentação

Este documento explica o que cada arquivo de documentação contém e como você pode usá-lo para aprender desenvolvimento de software do zero, construindo este projeto do começo ao fim.

---

## A Ideia Central

Você não vai aprender a programar lendo. Você vai aprender **construindo** — e a documentação existe para que você nunca fique perdido sobre o que construir, por que aquela decisão foi tomada, ou como as partes se conectam.

Pense nela como um mapa. O mapa não substitui a viagem, mas sem ele você anda em círculos.

---

## O que Foi Criado e Por Que Existe

### [01 — Visão Geral](01-visao-geral.md)

**O que é:** Uma descrição do problema real que o sistema resolve e para quem ele foi construído.

**Por que existe em todo projeto real:** Antes de escrever uma linha de código, engenheiros de software precisam entender o problema. Código sem contexto vira código sem propósito — você não sabe se está construindo a coisa certa.

**Como usar para aprender:**
- Leia antes de qualquer outra coisa
- Ao longo do desenvolvimento, volte aqui sempre que ficar em dúvida sobre uma decisão: *"esta funcionalidade resolve o problema do usuário?"*
- Exercício: tente reescrever este documento com suas próprias palavras. Se conseguir explicar o problema sem consultar, você entendeu o domínio.

---

### [02 — Arquitetura](02-arquitetura.md)

**O que é:** Um diagrama e uma descrição de como as partes do sistema se conectam — frontend, backend, banco de dados e IA.

**Por que existe em todo projeto real:** Arquitetura é a estrutura que decide onde cada pedaço de código vai morar. Sem ela, o projeto vira uma pasta com arquivos jogados sem organização — funciona no início, impossível de manter depois.

**Conceitos que você vai aprender usando este documento:**
- O que é uma **API REST** (o "contrato" entre frontend e backend)
- O que é uma **arquitetura em camadas** (cada camada tem uma responsabilidade)
- O que é um **monorepo** (uma pasta com múltiplos projetos organizados)
- Por que frontend e backend são separados (podem evoluir independentemente)

**Como usar para aprender:**
- Quando criar um arquivo novo, pergunte: *"Em qual pasta deste diagrama este arquivo pertence?"*
- Se a resposta não for óbvia, o arquivo provavelmente está mal posicionado
- A estrutura de pastas documentada aqui é o esqueleto que você vai criar no primeiro dia de código

---

### [03 — Funcionalidades](03-funcionalidades.md)

**O que é:** Uma lista de tudo que o sistema deve fazer, organizado por perfil de usuário (paciente, médico, admin).

**Por que existe em todo projeto real:** Engenheiros usam este tipo de documento como **backlog** — a lista de trabalho a ser feito. Cada item com `[ ]` é uma tarefa que ainda não foi construída. Cada `[x]` é uma funcionalidade entregue.

**Conceitos que você vai aprender:**
- Como **quebrar um sistema grande** em partes menores e construíveis
- O que é **MVP** (produto mínimo viável): construir apenas o essencial primeiro
- Como pensar em funcionalidades do ponto de vista do **usuário**, não do código

**Como usar para aprendizado:**
- Use como guia do que implementar em cada fase
- Marque `[x]` conforme for completando — é motivador ver o progresso
- Ao implementar cada funcionalidade, pense: *"Qual rota da API preciso criar? Qual tela no frontend?"*

---

### [04 — Modelos de Dados](04-modelos-de-dados.md)

**O que é:** A definição de todas as entidades do banco de dados e como elas se relacionam entre si. Escrito em **Prisma Schema**, que é a linguagem usada para descrever o banco neste projeto.

**Por que existe em todo projeto real:** O banco de dados é a memória permanente do sistema. Decisões ruins aqui geram problemas que são difíceis de corrigir depois porque os dados já existem. Projetar o modelo antes de codar evita retrabalho enorme.

**Conceitos fundamentais que você vai aprender:**
- O que são **entidades** e **atributos** (tabelas e colunas)
- O que são **relações** entre entidades (1:1, 1:N, N:N)
- O que são **enums** (valores fixos e controlados)
- O que é um **ORM** (Prisma) e por que ele existe (abstrai SQL)
- O que são **migrations** (versões do banco, como um git para a estrutura dos dados)

**Como usar para aprender:**
- Leia o diagrama no topo antes de olhar o código do schema
- Para cada modelo, pergunte: *"Quais informações esta entidade guarda? Com quem ela se relaciona?"*
- A relação `User → Patient / Doctor / Admin` é um padrão clássico chamado **herança de tabela** — vale pesquisar
- Exercício: tente desenhar o diagrama de entidades no papel a partir do schema, sem olhar o diagrama pronto

---

### [05 — API REST](05-api.md)

**O que é:** A documentação de todas as rotas do backend — o que cada endpoint recebe, o que retorna, e quem tem permissão de acessá-lo.

**Por que existe em todo projeto real:** A API é o contrato entre frontend e backend. Com esta documentação, o desenvolvedor frontend sabe exatamente como chamar o backend sem precisar perguntar. Em times, esta documentação é essencial para trabalhar em paralelo.

**Conceitos que você vai aprender:**
- O que são os **verbos HTTP**: GET (buscar), POST (criar), PATCH (editar), DELETE (remover)
- O que são **rotas** e **parâmetros** (`/patients/:id`)
- O que é um **corpo de requisição** (body) e uma **resposta** (response)
- O que são **códigos de status HTTP**: 200 (ok), 201 (criado), 400 (erro do cliente), 401 (não autenticado), 403 (sem permissão), 404 (não encontrado)
- O que é **autenticação por Bearer Token**

**Como usar para aprender:**
- Antes de implementar qualquer rota no NestJS, leia a especificação aqui
- Use o **Insomnia** ou **Postman** para testar cada rota que você criar, usando os exemplos de body deste documento
- Exercício: ao terminar uma rota, tente documentá-la você mesmo antes de consultar o que está escrito aqui — o que você produziu está correto?

---

### [06 — Inteligência Artificial](06-ia.md)

**O que é:** A especificação de como a IA está integrada ao sistema — quais modelos são usados, como os prompts são estruturados, e como o serviço de IA é implementado no backend.

**Por que existe em todo projeto real:** IA em sistemas reais não é mágica — é uma chamada de API como qualquer outra. O que faz diferença é a **engenharia de prompt** (como você instrui o modelo) e a **integração** (como os dados do sistema enriquecem o contexto da IA).

**Conceitos que você vai aprender:**
- O que é uma **chamada à API da OpenAI** e como funciona o formato de mensagens
- O que é **engenharia de prompt** (construir instruções claras para o modelo)
- Como **injetar contexto do usuário** no prompt (dados reais do paciente)
- O que é **histórico de conversa** e por que ele precisa ser enviado a cada requisição
- Diferença de uso entre **GPT-4o** (respostas rápidas e interativas) e **Claude** (análise de textos longos)

**Como usar para aprender:**
- Implemente primeiro o chatbot mais simples possível: um endpoint que recebe uma mensagem e retorna a resposta da OpenAI, sem contexto
- Depois, adicione o histórico de conversa
- Por último, injete os dados do paciente no prompt do sistema
- Cada etapa ensina algo diferente sobre como APIs de IA funcionam

---

### [07 — Autenticação](07-autenticacao.md)

**O que é:** A especificação completa do sistema de login — como os tokens funcionam, como o acesso é controlado por perfil, e como a segurança é implementada.

**Por que existe em todo projeto real:** Autenticação errada é a causa de boa parte das vulnerabilidades de segurança em sistemas web. Entender este módulo profundamente é um dos conhecimentos mais valiosos que um desenvolvedor pode ter.

**Conceitos que você vai aprender:**
- O que é **JWT** (JSON Web Token) e como ele funciona
- Por que existem dois tokens (access e refresh) e qual o papel de cada um
- O que é **hashing de senha** com bcrypt e por que nunca se guarda senha em texto puro
- O que é **RBAC** (controle de acesso baseado em papéis)
- O que são **Guards** no NestJS e como eles protegem rotas

**Como usar para aprender:**
- Este módulo deve ser o **primeiro que você implementa** no backend — sem autenticação, nenhum outro módulo faz sentido
- Leia o fluxo de autenticação em texto antes de escrever código
- Exercício de segurança: após implementar, tente acessar uma rota protegida sem token. Depois com token expirado. Depois com token de PATIENT numa rota de ADMIN. O sistema deve bloquear os três casos.

---

### [08 — Fluxos](08-fluxos.md)

**O que é:** A descrição passo a passo dos principais fluxos de uso do sistema — como um paciente agenda uma consulta, como um médico realiza um atendimento, etc.

**Por que existe em todo projeto real:** Fluxos (ou *user flows*) são a perspectiva do **usuário** sobre o sistema. Eles conectam telas do frontend com rotas do backend e mostram como tudo funciona junto — algo que nenhum documento técnico isolado consegue mostrar.

**Conceitos que você vai aprender:**
- Como pensar em **máquinas de estado** (uma consulta passa por estados: SCHEDULED → CONFIRMED → IN_PROGRESS → COMPLETED)
- Como o frontend e o backend **se comunicam em sequência** para completar uma ação
- O que é **UX flow** e por que ele importa no desenvolvimento

**Como usar para aprender:**
- Antes de implementar qualquer funcionalidade, leia o fluxo correspondente aqui
- Use os fluxos para saber **quantas rotas de API** são necessárias para uma funcionalidade funcionar de ponta a ponta
- Os estados das entidades (seção final do documento) são exatamente os enums do schema Prisma — perceba como eles se conectam

---

### [09 — Roadmap](09-roadmap.md)

**O que é:** Um plano de desenvolvimento dividido em 4 fases, com checklists de tarefas por fase.

**Por que existe em todo projeto real:** Projetos grandes precisam de priorização. Construir tudo ao mesmo tempo é impossível. O roadmap define o que é essencial agora (fase 1) e o que pode esperar (fase 3 e 4).

**Como usar para aprender:**
- Este é o seu **guia do dia a dia** — abra antes de cada sessão de código para saber o que fazer
- Nunca pule fases: cada fase constrói sobre a anterior
- A ordem sugerida no final do documento (`1. Setup → 2. Auth → 3. CRUD → ...`) é pedagogicamente correta: você aprende os conceitos na sequência certa

---

### [10 — Stack Tecnológica](10-stack.md)

**O que é:** A lista de todas as tecnologias usadas e a justificativa de por que cada uma foi escolhida.

**Por que existe em todo projeto real:** Escolher tecnologias sem critério é um erro comum. Engenheiros experientes documentam o **raciocínio** por trás das escolhas — assim, quando alguém questionar, há uma resposta.

**Como usar para aprender:**
- Leia as justificativas, não apenas os nomes das tecnologias
- Quando encontrar uma tecnologia desconhecida, pesquise especificamente: *"o que é [tecnologia] e para que serve"* — não tente aprender tudo de uma vez
- As decisões documentadas no final (NestJS vs Express, Prisma vs TypeORM) ensinam a pensar criticamente sobre escolhas técnicas

---

## Ordem Recomendada de Estudo e Implementação

```
SEMANA 1 — Fundação conceitual
  Ler: 01, 02, 10
  Fazer: configurar o ambiente (Node, pnpm, PostgreSQL, VS Code)
  Criar: estrutura de pastas do monorepo

SEMANA 2 — Banco de dados
  Ler: 04
  Fazer: instalar Prisma, escrever o schema, rodar a primeira migration
  Aprender: o que são tabelas, relações, migrations

SEMANA 3 — Backend: autenticação
  Ler: 07, 05 (somente seção /auth)
  Fazer: criar módulo auth no NestJS (register, login, JWT)
  Aprender: HTTP, REST, JWT, bcrypt

SEMANA 4 — Backend: módulos de domínio
  Ler: 03, 05, 08
  Fazer: módulos de patients, doctors, appointments
  Aprender: CRUD, validação, guards, roles

SEMANA 5 — Frontend: base
  Ler: 08 (fluxos de tela)
  Fazer: criar projeto React, configurar rotas, telas de login e cadastro
  Aprender: componentes, props, estado, chamadas HTTP

SEMANA 6 — Frontend: funcionalidades
  Fazer: dashboard paciente, agendamento, histórico
  Aprender: React Query, formulários, navegação protegida

SEMANA 7 — IA
  Ler: 06
  Fazer: integrar OpenAI, criar chatbot de triagem
  Aprender: APIs externas, prompts, histórico de conversa

SEMANA 8+ — Refinamento
  Completar funcionalidades do checklist do Roadmap (fase 1)
  Corrigir bugs, melhorar UX, adicionar notificações
```

---

## Perguntas que Esta Documentação Responde

Ao longo do desenvolvimento, você vai ter dúvidas. Antes de pesquisar no Google, consulte aqui:

| Dúvida | Onde encontrar |
|---|---|
| *"Que campos o usuário tem no banco?"* | [04 — Modelos de Dados](04-modelos-de-dados.md) |
| *"Qual rota usar para agendar uma consulta?"* | [05 — API REST](05-api.md) |
| *"Quem pode acessar esta rota?"* | [07 — Autenticação](07-autenticacao.md) |
| *"Qual a sequência de passos para um paciente agendar?"* | [08 — Fluxos](08-fluxos.md) |
| *"O que construir agora?"* | [09 — Roadmap](09-roadmap.md) |
| *"Por que escolhemos NestJS?"* | [10 — Stack](10-stack.md) |
| *"Como a IA de triagem funciona?"* | [06 — IA](06-ia.md) |

---

## Uma Observação Final

Esta documentação foi projetada para um sistema real. Ela não simplifica os conceitos — ela os usa como seriam usados em uma empresa de tecnologia.

Isso significa que você vai encontrar partes que não entende. Isso é esperado e é bom. O processo é:

1. Tente entender pelo contexto
2. Pesquise o conceito específico que não conhece
3. Volte e releia
4. Implemente — a implementação concreta sempre esclarece o que a leitura deixou vago

O projeto inteiro foi desenhado para que você, ao terminá-lo, tenha passado pelos principais conceitos de desenvolvimento fullstack moderno: banco de dados relacional, API REST, autenticação, frontend reativo, integração com IA e arquitetura em camadas.
