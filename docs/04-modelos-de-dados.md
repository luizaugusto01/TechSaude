# 04 — Modelos de Dados

## Diagrama de Entidades

```
User (base)
 ├── Patient (1:1)
 ├── Doctor (1:1)
 └── Admin (1:1)

Doctor ──< Appointment >── Patient
Appointment ──< MedicalRecord
Appointment ──< Exam
Appointment ──< Prescription
Prescription ──< PrescriptionItem >── Medication
Patient ──< AIConversation ──< AIMessage
```

---

## Schema Prisma

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ─── Enums ──────────────────────────────────────────────

enum Role {
  PATIENT
  DOCTOR
  ADMIN
}

enum AppointmentStatus {
  SCHEDULED
  CONFIRMED
  IN_PROGRESS
  COMPLETED
  CANCELLED
  NO_SHOW
}

enum ExamStatus {
  REQUESTED
  SCHEDULED
  COMPLETED
  DELIVERED
}

enum UrgencyLevel {
  LOW
  MEDIUM
  HIGH
  EMERGENCY
}

// ─── Usuário Base ────────────────────────────────────────

model User {
  id             String    @id @default(cuid())
  email          String    @unique
  passwordHash   String
  name           String
  role           Role
  avatarUrl      String?
  phone          String?
  isActive       Boolean   @default(true)
  createdAt      DateTime  @default(now())
  updatedAt      DateTime  @updatedAt

  patient        Patient?
  doctor         Doctor?
  admin          Admin?
  notifications  Notification[]
  refreshTokens  RefreshToken[]
}

// ─── Paciente ────────────────────────────────────────────

model Patient {
  id              String    @id @default(cuid())
  userId          String    @unique
  user            User      @relation(fields: [userId], references: [id])

  cpf             String    @unique
  dateOfBirth     DateTime
  bloodType       String?
  allergies       String[]
  healthInsurance String?
  insuranceNumber String?
  address         Json?     // { street, city, state, zip }
  emergencyContact Json?    // { name, phone, relationship }

  appointments    Appointment[]
  medicalRecords  MedicalRecord[]
  exams           Exam[]
  prescriptions   Prescription[]
  aiConversations AIConversation[]

  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
}

// ─── Médico ──────────────────────────────────────────────

model Doctor {
  id            String    @id @default(cuid())
  userId        String    @unique
  user          User      @relation(fields: [userId], references: [id])

  crm           String    @unique
  specialties   String[]
  bio           String?
  consultationDurationMin Int @default(30)
  availableSlots Json?    // { weekdays: [...], times: [...] }

  appointments  Appointment[]
  medicalRecords MedicalRecord[]
  prescriptions  Prescription[]

  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

// ─── Admin ───────────────────────────────────────────────

model Admin {
  id        String   @id @default(cuid())
  userId    String   @unique
  user      User     @relation(fields: [userId], references: [id])
  createdAt DateTime @default(now())
}

// ─── Consulta ────────────────────────────────────────────

model Appointment {
  id          String            @id @default(cuid())
  patientId   String
  patient     Patient           @relation(fields: [patientId], references: [id])
  doctorId    String
  doctor      Doctor            @relation(fields: [doctorId], references: [id])

  scheduledAt DateTime
  durationMin Int               @default(30)
  status      AppointmentStatus @default(SCHEDULED)
  specialty   String
  notes       String?           // observações do agendamento
  cancelReason String?

  medicalRecord MedicalRecord?
  exams         Exam[]
  prescriptions Prescription[]

  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

// ─── Prontuário ──────────────────────────────────────────

model MedicalRecord {
  id              String   @id @default(cuid())
  appointmentId   String   @unique
  appointment     Appointment @relation(fields: [appointmentId], references: [id])
  patientId       String
  patient         Patient  @relation(fields: [patientId], references: [id])
  doctorId        String
  doctor          Doctor   @relation(fields: [doctorId], references: [id])

  chiefComplaint  String   // queixa principal
  history         String?  // histórico da doença atual
  physicalExam    String?  // exame físico
  diagnosis       String?  // hipótese diagnóstica / CID
  conduct         String?  // conduta
  observations    String?
  aiSummary       String?  // resumo gerado por IA

  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
}

// ─── Exame ───────────────────────────────────────────────

model Exam {
  id            String     @id @default(cuid())
  patientId     String
  patient       Patient    @relation(fields: [patientId], references: [id])
  appointmentId String?
  appointment   Appointment? @relation(fields: [appointmentId], references: [id])

  name          String
  type          String     // laboratorial, imagem, etc.
  status        ExamStatus @default(REQUESTED)
  requestedAt   DateTime   @default(now())
  scheduledAt   DateTime?
  completedAt   DateTime?

  resultUrl     String?    // URL do arquivo de resultado
  resultSummary String?    // resumo do resultado
  aiAnalysis    String?    // análise gerada por IA (futuro)

  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt
}

// ─── Receita ─────────────────────────────────────────────

model Prescription {
  id            String   @id @default(cuid())
  patientId     String
  patient       Patient  @relation(fields: [patientId], references: [id])
  doctorId      String
  doctor        Doctor   @relation(fields: [doctorId], references: [id])
  appointmentId String?
  appointment   Appointment? @relation(fields: [appointmentId], references: [id])

  issuedAt      DateTime @default(now())
  validUntil    DateTime?
  pdfUrl        String?

  items         PrescriptionItem[]

  createdAt     DateTime @default(now())
}

model PrescriptionItem {
  id             String       @id @default(cuid())
  prescriptionId String
  prescription   Prescription @relation(fields: [prescriptionId], references: [id])

  medicationName String
  dosage         String       // ex: "500mg"
  frequency      String       // ex: "2x ao dia"
  duration       String?      // ex: "7 dias"
  instructions   String?      // ex: "tomar após as refeições"
}

// ─── IA — Conversa ───────────────────────────────────────

model AIConversation {
  id          String      @id @default(cuid())
  patientId   String
  patient     Patient     @relation(fields: [patientId], references: [id])

  title       String?     // título gerado automaticamente
  urgency     UrgencyLevel?
  suggestion  String?     // especialidade sugerida pela IA

  messages    AIMessage[]

  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model AIMessage {
  id             String         @id @default(cuid())
  conversationId String
  conversation   AIConversation @relation(fields: [conversationId], references: [id])

  role           String         // "user" | "assistant"
  content        String
  createdAt      DateTime @default(now())
}

// ─── Notificação ─────────────────────────────────────────

model Notification {
  id        String   @id @default(cuid())
  userId    String
  user      User     @relation(fields: [userId], references: [id])

  title     String
  body      String
  type      String   // appointment, exam, prescription, system
  isRead    Boolean  @default(false)
  link      String?  // rota interna

  createdAt DateTime @default(now())
}

// ─── Refresh Token ───────────────────────────────────────

model RefreshToken {
  id        String   @id @default(cuid())
  userId    String
  user      User     @relation(fields: [userId], references: [id])

  token     String   @unique
  expiresAt DateTime
  createdAt DateTime @default(now())

  @@index([userId])
}
```

---

## Notas sobre o Schema

- **User** é a entidade base de autenticação. Cada usuário tem exatamente um perfil (Patient, Doctor ou Admin).
- **Appointment** é o eixo central: conecta paciente, médico, e origina prontuários, exames e receitas.
- **MedicalRecord** é sempre 1:1 com Appointment — um prontuário por consulta.
- **AIConversation** é persistida por paciente para manter histórico de triagens.
- O campo `address` e `emergencyContact` usam `Json` para flexibilidade sem tabelas extras.
- `availableSlots` do médico também usa `Json` por ser um dado de configuração variável.
