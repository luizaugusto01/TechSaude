# 03 — Funcionalidades

## Área do Paciente

### Autenticação e Perfil
- [ ] Cadastro com nome, e-mail, senha, data de nascimento, CPF
- [ ] Login com e-mail e senha
- [ ] Recuperação de senha por e-mail
- [ ] Edição de dados pessoais e foto de perfil
- [ ] Visualização de plano de saúde cadastrado

### Histórico Médico
- [ ] Visualização de prontuários anteriores
- [ ] Linha do tempo de consultas
- [ ] Filtros por data, especialidade e médico
- [ ] Download de prontuário em PDF

### Exames
- [ ] Listagem de exames solicitados e realizados
- [ ] Visualização e download de resultados
- [ ] Status do exame (solicitado / realizado / entregue)
- [ ] Notificação quando resultado estiver disponível

### Receitas
- [ ] Listagem de receitas emitidas
- [ ] Visualização e download de receitas
- [ ] Informações sobre medicamentos prescritos
- [ ] Alertas de receita próxima ao vencimento

### Agendamento
- [ ] Busca de médicos por especialidade
- [ ] Visualização de disponibilidade de agenda
- [ ] Agendamento de consulta
- [ ] Cancelamento e reagendamento
- [ ] Confirmação por e-mail/notificação

### Acompanhamento de Consultas
- [ ] Lista de consultas agendadas e passadas
- [ ] Status da consulta (agendada / confirmada / realizada / cancelada)
- [ ] Detalhes da consulta (médico, local, horário)
- [ ] Notificações de lembrete

### Chatbot de IA
- [ ] Chat para descrição de sintomas
- [ ] Triagem inicial com sugestão de urgência
- [ ] Sugestão de especialidade médica adequada
- [ ] Histórico de conversas salvo por sessão
- [ ] Encaminhamento para agendamento a partir da triagem

---

## Área do Médico

### Dashboard
- [ ] Visão geral do dia (consultas, pacientes, alertas)
- [ ] Próximas consultas do dia
- [ ] Indicadores: total de pacientes, consultas realizadas, pendências
- [ ] Alertas de casos prioritários

### Gerenciamento de Consultas
- [ ] Agenda semanal e diária
- [ ] Confirmação e cancelamento de consultas
- [ ] Anotações por consulta
- [ ] Registro de início e fim de atendimento

### Prontuários
- [ ] Criação e edição de prontuário por consulta
- [ ] Campos: queixa principal, histórico, exame físico, hipótese diagnóstica, conduta
- [ ] Busca de prontuários anteriores do paciente
- [ ] Histórico clínico consolidado do paciente

### Solicitação de Exames
- [ ] Solicitação de exames vinculada à consulta
- [ ] Tipos de exames (laboratorial, imagem, etc.)
- [ ] Visualização dos resultados entregues

### Emissão de Receitas
- [ ] Criação de receita com medicamentos e posologia
- [ ] Busca de medicamentos por nome
- [ ] Geração de receita em PDF
- [ ] Histórico de receitas emitidas por paciente

### Métricas e Relatórios
- [ ] Total de atendimentos por período
- [ ] Distribuição de diagnósticos
- [ ] Taxa de retorno de pacientes
- [ ] Exportação de relatórios

### IA para Médicos
- [ ] Resumo automático do histórico do paciente antes da consulta
- [ ] Sugestão de conduta baseada nos dados registrados
- [ ] Priorização automática de casos por urgência
- [ ] Alertas de interações medicamentosas (futuramente)

---

## Área Administrativa

### Gestão de Usuários
- [ ] Cadastro e gestão de médicos
- [ ] Cadastro e gestão de pacientes
- [ ] Ativação e desativação de contas
- [ ] Definição de especialidades por médico

### Gestão de Agenda
- [ ] Configuração de horários disponíveis por médico
- [ ] Bloqueio de datas/horários
- [ ] Visão macro da agenda da clínica

### Métricas Operacionais
- [ ] Dashboard com indicadores da clínica
- [ ] Total de consultas por período
- [ ] Ocupação de agenda por médico
- [ ] Taxa de cancelamentos e faltas
- [ ] Receita estimada por período

### Relatórios
- [ ] Exportação de dados por período
- [ ] Relatórios de atendimento por especialidade
- [ ] Relatórios financeiros (futuramente)

### Configurações do Sistema
- [ ] Dados da clínica (nome, endereço, logo)
- [ ] Configuração de especialidades disponíveis
- [ ] Gerenciamento de notificações automáticas

---

## Funcionalidades de IA (Detalhadas)

Ver [06-ia.md](06-ia.md) para especificação completa.

| Funcionalidade | Usuário | Modelo | Status |
|---|---|---|---|
| Chatbot de sintomas | Paciente | GPT-4 | Fase 1 |
| Triagem e urgência | Paciente | GPT-4 | Fase 1 |
| Sugestão de especialidade | Paciente | GPT-4 | Fase 1 |
| Resumo de histórico | Médico | Claude | Fase 2 |
| Priorização de casos | Médico | Claude | Fase 2 |
| OCR de exames | Todos | Python/Vision | Fase 3 |
| Análise de imagens | Médico | Vision API | Fase 3 |

---

## Notificações

O sistema envia notificações nos seguintes eventos:

| Evento | Canal | Destinatário |
|---|---|---|
| Consulta agendada | E-mail + In-app | Paciente |
| Lembrete 24h antes | E-mail + In-app | Paciente |
| Resultado de exame disponível | In-app | Paciente |
| Nova receita emitida | In-app | Paciente |
| Consulta cancelada | E-mail + In-app | Paciente e Médico |
| Novo agendamento | In-app | Médico |
