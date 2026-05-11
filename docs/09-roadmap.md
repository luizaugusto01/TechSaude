# 09 — Roadmap de Desenvolvimento

## Visão Geral das Fases

```
FASE 1          FASE 2          FASE 3          FASE 4
Fundação    →   IA Médica   →   Análise     →   Escala
(MVP)           (Avançado)      de Exames       (Produção)

~2-3 meses      ~2 meses        ~2-3 meses      contínuo
```

---

## Fase 1 — MVP (Fundação)

**Objetivo:** sistema funcional com os fluxos principais operando de ponta a ponta.

### Backend
- [ ] Setup do projeto NestJS + Prisma + PostgreSQL
- [ ] Módulo de autenticação (registro, login, JWT, refresh token)
- [ ] Módulo de usuários (User, Patient, Doctor, Admin)
- [ ] Módulo de consultas (CRUD, status, disponibilidade)
- [ ] Módulo de prontuários (criação e leitura)
- [ ] Módulo de exames (solicitação e upload de resultado)
- [ ] Módulo de receitas (emissão e listagem)
- [ ] Módulo de notificações (in-app)
- [ ] Módulo de IA — chatbot básico de triagem (GPT-4o)
- [ ] Validação global (class-validator, pipes)
- [ ] Rate limiting e segurança básica

### Frontend
- [ ] Setup do projeto React + TypeScript + Tailwind
- [ ] Sistema de rotas com proteção por perfil
- [ ] Telas de login e cadastro
- [ ] Dashboard do paciente
- [ ] Tela de agendamento de consultas
- [ ] Tela de exames e receitas
- [ ] Tela de histórico de prontuários
- [ ] Chatbot de sintomas (interface de chat)
- [ ] Dashboard básico do médico (agenda do dia)
- [ ] Formulário de prontuário e receita
- [ ] Painel admin simples (listagem de usuários e métricas)

### Infraestrutura
- [ ] Variáveis de ambiente configuradas
- [ ] Docker Compose para desenvolvimento local
- [ ] Migrations do banco em ordem

---

## Fase 2 — IA Médica Avançada

**Objetivo:** expandir o uso de IA para apoiar médicos com resumos e priorização.

### Backend
- [ ] Integração com Claude API (Anthropic)
- [ ] Endpoint de resumo automático de histórico do paciente
- [ ] Priorização de consultas por urgência via IA
- [ ] Histórico de conversas com IA persistido e acessível
- [ ] Alertas inteligentes baseados nos dados do paciente
- [ ] E-mail transacional (consulta agendada, resultado disponível)

### Frontend
- [ ] Resumo IA carregado automaticamente na ficha do paciente (médico)
- [ ] Badge de prioridade na agenda do médico
- [ ] Histórico de conversas com o chatbot para o paciente
- [ ] Centro de notificações completo
- [ ] Métricas avançadas no dashboard do médico
- [ ] Relatórios exportáveis (CSV/PDF) no painel admin

### Melhorias
- [ ] Busca avançada de médicos (filtros: localização, disponibilidade, avaliação)
- [ ] Sistema de avaliação de consultas pelo paciente
- [ ] Confirmação automática de consultas por e-mail

---

## Fase 3 — Análise de Exames

**Objetivo:** IA lê e interpreta resultados de exames laboratoriais.

### Backend (Python microservice ou endpoint separado)
- [ ] Upload e parsing de PDF de exames (PyMuPDF)
- [ ] Extração de valores de exames laboratoriais
- [ ] Análise via IA com destaque de valores fora do padrão
- [ ] Armazenamento estruturado dos resultados
- [ ] Histórico de resultados por item (ex: hemoglobina ao longo do tempo)

### Frontend
- [ ] Visualização de resultados de exames com highlights
- [ ] Gráficos de evolução de indicadores ao longo do tempo
- [ ] Comparação com valores de referência
- [ ] Exportação do laudo analisado

### Infraestrutura
- [ ] Armazenamento de arquivos (S3 ou equivalente)
- [ ] Filas de processamento para análise assíncrona

---

## Fase 4 — Escala e Produção

**Objetivo:** preparar para múltiplas clínicas e uso em produção.

### Infraestrutura
- [ ] Deploy em nuvem (VPS ou serviço gerenciado)
- [ ] CI/CD configurado (GitHub Actions)
- [ ] Monitoramento de erros (Sentry)
- [ ] Logs estruturados
- [ ] Backup automático do banco
- [ ] HTTPS e domínio próprio

### Produto
- [ ] Multi-tenant: suporte a múltiplas clínicas no mesmo sistema
- [ ] Telemedicina: consulta por videochamada integrada
- [ ] App mobile (React Native)
- [ ] Integração com sistemas de laboratório (HL7/FHIR)
- [ ] Visão computacional para imagens radiológicas

### Segurança e Compliance
- [ ] Conformidade com LGPD
- [ ] Auditoria de acesso a dados sensíveis
- [ ] Política de retenção de dados
- [ ] Termos de uso e privacidade

---

## Prioridade para Começar

Para o MVP funcionar, a ordem recomendada de implementação é:

```
1. Setup monorepo + banco
2. Auth (login/cadastro)
3. CRUD de pacientes e médicos
4. Agendamento de consultas
5. Prontuários
6. Chatbot de triagem (IA)
7. Exames e receitas
8. Dashboard por perfil
9. Notificações
```
