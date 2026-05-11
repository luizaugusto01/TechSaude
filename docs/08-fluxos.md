# 08 — Fluxos Principais do Sistema

## Fluxo 1: Cadastro e Primeiro Acesso do Paciente

```
1. Paciente acessa /cadastro
2. Preenche: nome, e-mail, senha, CPF, data de nascimento
3. Sistema cria User (role: PATIENT) + Patient
4. Redireciona para /dashboard/paciente
5. Onboarding: pergunta se tem histórico de doenças, alergias, plano de saúde
6. Dados são salvos no perfil do Patient
7. Tela principal exibe: próximas consultas, acesso rápido ao chatbot
```

---

## Fluxo 2: Triagem por IA e Agendamento

```
1. Paciente clica em "Sentindo algum sintoma?"
2. Chatbot inicia conversa
3. Paciente descreve sintomas em linguagem natural
4. IA faz perguntas de acompanhamento (2-3 rodadas)
5. IA retorna:
   - Nível de urgência (LOW/MEDIUM/HIGH/EMERGENCY)
   - Especialidade sugerida
   - Orientação geral
6. Se EMERGENCY → alerta vermelho + instruções de pronto-socorro
7. Se outros → botão "Agendar consulta de [Especialidade]"
8. Paciente clica → redireciona para busca de médicos da especialidade
9. Seleciona médico, data e horário disponível
10. Confirma agendamento
11. Notificação enviada para paciente e médico
```

---

## Fluxo 3: Consulta Médica (Dia da Consulta)

```
MÉDICO:
1. Acessa dashboard → vê agenda do dia
2. Clica na consulta → abre ficha do paciente
3. Sistema carrega resumo IA do histórico do paciente automaticamente
4. Médico inicia atendimento → status muda para IN_PROGRESS
5. Durante atendimento preenche prontuário:
   - Queixa principal
   - Histórico
   - Exame físico
   - Diagnóstico (CID)
   - Conduta
6. Se necessário: solicita exames (formulário inline)
7. Se necessário: emite receita (formulário inline)
8. Finaliza atendimento → status COMPLETED
9. Prontuário, exames e receitas são vinculados à consulta

PACIENTE (durante/após):
1. Recebe notificação quando médico inicia atendimento
2. Após consulta → vê prontuário disponível no histórico
3. Recebe notificação de receitas e exames solicitados
```

---

## Fluxo 4: Acesso a Exames

```
1. Médico solicita exame → status: REQUESTED
2. Clínica/laboratório realiza exame (fora do sistema por ora)
3. Admin ou médico faz upload do resultado (PDF/imagem)
4. Status muda para: COMPLETED → DELIVERED
5. Paciente recebe notificação: "Resultado disponível"
6. Paciente acessa /exames → vê resultado
7. [Fase 3] IA analisa resultado automaticamente e destaca valores alterados
```

---

## Fluxo 5: Dashboard do Médico

```
Ao acessar /dashboard/medico:

┌─────────────────────────────────────────────────┐
│  Bom dia, Dr. Carlos   |  Hoje: 12 consultas    │
├─────────────────────────────────────────────────┤
│  AGENDA DO DIA                                  │
│  09:00 - João Silva [CONFIRMADA] [ALTA PRIORIDADE]│
│  09:30 - Maria Souza [CONFIRMADA]               │
│  10:00 - Pedro Lima  [AGENDADA]                 │
│  ...                                            │
├─────────────────────────────────────────────────┤
│  MÉTRICAS DO MÊS                                │
│  Pacientes: 127  |  Consultas: 89  |  Taxa retorno: 42% │
└─────────────────────────────────────────────────┘
```

- **Alta prioridade** é marcada automaticamente quando a triagem IA classificou como HIGH
- Médico pode clicar em qualquer consulta para ver detalhes do paciente

---

## Fluxo 6: Painel Administrativo

```
Admin acessa /admin:

1. Dashboard com métricas da clínica em tempo real
2. Gestão de médicos: cadastro, especialidades, disponibilidade
3. Gestão de pacientes: busca, visualização, ativação/desativação
4. Relatórios: exporta dados por período
5. Configurações da clínica: nome, logo, especialidades, horários
```

---

## Estados das Entidades

### Consulta (Appointment)

```
SCHEDULED → CONFIRMED → IN_PROGRESS → COMPLETED
     ↓             ↓
 CANCELLED      CANCELLED

     (a qualquer momento antes de IN_PROGRESS)

NO_SHOW (marcado manualmente se paciente não apareceu)
```

### Exame (Exam)

```
REQUESTED → SCHEDULED → COMPLETED → DELIVERED
```

### Conversa IA (AIConversation)

```
Criada (sem urgência) → Com urgência atribuída → Vinculada a agendamento
```

---

## Navegação por Perfil

### Paciente
```
/                    → landing page
/login               → login
/cadastro            → cadastro
/dashboard           → home do paciente
/consultas           → lista e agendamento
/exames              → meus exames
/receitas            → minhas receitas
/historico           → prontuários
/chatbot             → triagem por IA
/perfil              → editar dados
```

### Médico
```
/medico/dashboard    → agenda + métricas
/medico/consultas    → gestão de consultas
/medico/pacientes    → lista de pacientes
/medico/pacientes/:id → ficha completa do paciente
/medico/perfil       → editar perfil
```

### Admin
```
/admin/dashboard     → métricas da clínica
/admin/usuarios      → gestão de usuários
/admin/medicos       → gestão de médicos
/admin/relatorios    → relatórios e exportações
/admin/configuracoes → configurações do sistema
```
