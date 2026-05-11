# 05 — API REST

## Convenções

- Base URL: `/api/v1`
- Autenticação: `Authorization: Bearer <jwt>`
- Respostas de erro: `{ "statusCode": 400, "message": "...", "error": "Bad Request" }`
- Paginação: `?page=1&limit=20` onde aplicável
- Datas: ISO 8601 (`2025-06-15T10:00:00Z`)

---

## Autenticação

### POST `/auth/register`
Cadastro de novo usuário.

**Body:**
```json
{
  "name": "João Silva",
  "email": "joao@email.com",
  "password": "senhaSegura123",
  "role": "PATIENT",
  "cpf": "123.456.789-00",
  "dateOfBirth": "1990-05-15"
}
```

**Response 201:**
```json
{
  "accessToken": "eyJ...",
  "refreshToken": "...",
  "user": { "id": "...", "name": "João Silva", "role": "PATIENT" }
}
```

---

### POST `/auth/login`
Login com e-mail e senha.

**Body:** `{ "email": "...", "password": "..." }`

**Response 200:** mesmo formato do register

---

### POST `/auth/refresh`
Renova o access token.

**Body:** `{ "refreshToken": "..." }`

**Response 200:** `{ "accessToken": "..." }`

---

### POST `/auth/logout`
Invalida o refresh token.

**Body:** `{ "refreshToken": "..." }`

---

### POST `/auth/forgot-password`
Envia e-mail de recuperação.

**Body:** `{ "email": "..." }`

---

### POST `/auth/reset-password`
Redefine senha com token do e-mail.

**Body:** `{ "token": "...", "newPassword": "..." }`

---

## Pacientes

### GET `/patients/me`
Retorna perfil do paciente autenticado.

**Auth:** PATIENT

**Response 200:**
```json
{
  "id": "...",
  "name": "...",
  "dateOfBirth": "1990-05-15",
  "bloodType": "A+",
  "allergies": ["Penicilina"],
  "healthInsurance": "Unimed",
  "address": { "city": "São Paulo", "state": "SP" }
}
```

---

### PATCH `/patients/me`
Atualiza dados do paciente.

**Auth:** PATIENT

---

### GET `/patients` *(admin)*
Lista todos os pacientes com paginação.

**Auth:** ADMIN

**Query:** `?page=1&limit=20&search=joão`

---

### GET `/patients/:id` *(admin/doctor)*
Retorna dados completos de um paciente.

**Auth:** ADMIN | DOCTOR

---

## Médicos

### GET `/doctors`
Lista médicos disponíveis (público com filtros).

**Query:** `?specialty=Cardiologia&page=1&limit=20`

**Response 200:**
```json
{
  "data": [
    {
      "id": "...",
      "name": "Dr. Carlos Mendes",
      "specialties": ["Cardiologia"],
      "crm": "CRM/SP 12345",
      "bio": "..."
    }
  ],
  "total": 42,
  "page": 1
}
```

---

### GET `/doctors/:id`
Detalhes de um médico e disponibilidade.

---

### GET `/doctors/:id/availability`
Horários disponíveis do médico para agendamento.

**Query:** `?date=2025-07-01` (retorna slots da semana)

**Response 200:**
```json
{
  "slots": [
    { "date": "2025-07-01", "time": "09:00", "available": true },
    { "date": "2025-07-01", "time": "09:30", "available": false }
  ]
}
```

---

### GET `/doctors/me` *(doctor)*
Perfil do médico autenticado.

**Auth:** DOCTOR

---

### PATCH `/doctors/me` *(doctor)*
Atualiza perfil do médico.

**Auth:** DOCTOR

---

## Consultas

### POST `/appointments`
Agenda nova consulta.

**Auth:** PATIENT

**Body:**
```json
{
  "doctorId": "...",
  "scheduledAt": "2025-07-01T09:00:00Z",
  "specialty": "Cardiologia",
  "notes": "Dor no peito há 3 dias"
}
```

---

### GET `/appointments`
Lista consultas do usuário autenticado.

**Auth:** PATIENT | DOCTOR

**Query:** `?status=SCHEDULED&page=1&limit=10`

---

### GET `/appointments/:id`
Detalhes de uma consulta.

**Auth:** PATIENT (própria) | DOCTOR (própria) | ADMIN

---

### PATCH `/appointments/:id/status`
Atualiza status da consulta.

**Auth:** DOCTOR | ADMIN

**Body:** `{ "status": "CONFIRMED" }`

---

### DELETE `/appointments/:id`
Cancela uma consulta.

**Auth:** PATIENT (própria) | DOCTOR | ADMIN

**Body:** `{ "cancelReason": "Conflito de agenda" }`

---

## Prontuários

### POST `/medical-records`
Cria prontuário de uma consulta.

**Auth:** DOCTOR

**Body:**
```json
{
  "appointmentId": "...",
  "chiefComplaint": "Dor no peito",
  "history": "Paciente relata...",
  "physicalExam": "PA 120/80...",
  "diagnosis": "I20.9 - Angina pectoris",
  "conduct": "Solicitado ECG e troponina"
}
```

---

### GET `/medical-records/patient/:patientId`
Histórico de prontuários de um paciente.

**Auth:** DOCTOR | ADMIN | PATIENT (próprio)

---

### GET `/medical-records/:id`
Detalhe de um prontuário.

---

### PATCH `/medical-records/:id`
Edita prontuário.

**Auth:** DOCTOR (autor)

---

## Exames

### POST `/exams`
Solicita exame vinculado a uma consulta.

**Auth:** DOCTOR

**Body:**
```json
{
  "patientId": "...",
  "appointmentId": "...",
  "name": "Hemograma Completo",
  "type": "laboratorial"
}
```

---

### GET `/exams/patient/:patientId`
Lista exames de um paciente.

**Auth:** PATIENT (próprio) | DOCTOR | ADMIN

---

### PATCH `/exams/:id`
Atualiza status ou adiciona resultado.

**Auth:** DOCTOR | ADMIN

**Body:** `{ "status": "COMPLETED", "resultUrl": "https://..." }`

---

## Receitas

### POST `/prescriptions`
Emite receita.

**Auth:** DOCTOR

**Body:**
```json
{
  "patientId": "...",
  "appointmentId": "...",
  "validUntil": "2025-08-01",
  "items": [
    {
      "medicationName": "Atenolol",
      "dosage": "25mg",
      "frequency": "1x ao dia",
      "duration": "30 dias",
      "instructions": "Tomar pela manhã"
    }
  ]
}
```

---

### GET `/prescriptions/patient/:patientId`
Lista receitas de um paciente.

**Auth:** PATIENT (próprio) | DOCTOR | ADMIN

---

## IA

### POST `/ai/chat`
Envia mensagem ao chatbot de sintomas.

**Auth:** PATIENT

**Body:**
```json
{
  "message": "Tenho dor de cabeça e febre há 2 dias",
  "conversationId": "..." // opcional, para continuar conversa
}
```

**Response 200:**
```json
{
  "conversationId": "...",
  "reply": "Entendo. Você está sentindo alguma rigidez no pescoço?",
  "urgency": "MEDIUM",
  "suggestedSpecialty": null
}
```

---

### GET `/ai/conversations`
Lista conversas do paciente com a IA.

**Auth:** PATIENT

---

### GET `/ai/conversations/:id`
Mensagens de uma conversa.

**Auth:** PATIENT (própria)

---

### POST `/ai/summarize-record/:recordId`
Gera resumo de prontuário por IA.

**Auth:** DOCTOR

**Response 200:**
```json
{
  "summary": "Paciente de 45 anos com queixa de dor torácica..."
}
```

---

## Notificações

### GET `/notifications`
Lista notificações do usuário.

**Auth:** Qualquer

**Query:** `?isRead=false&limit=10`

---

### PATCH `/notifications/:id/read`
Marca notificação como lida.

---

### PATCH `/notifications/read-all`
Marca todas como lidas.

---

## Admin

### GET `/admin/metrics`
Métricas gerais da clínica.

**Auth:** ADMIN

**Response 200:**
```json
{
  "totalPatients": 342,
  "appointmentsToday": 28,
  "appointmentsThisMonth": 415,
  "cancellationRate": 0.08,
  "activeDoctors": 12
}
```

---

### GET `/admin/users`
Lista todos os usuários.

**Auth:** ADMIN

---

### PATCH `/admin/users/:id/status`
Ativa ou desativa usuário.

**Auth:** ADMIN

**Body:** `{ "isActive": false }`
