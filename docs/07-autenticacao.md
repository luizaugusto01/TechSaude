# 07 — Autenticação e Controle de Acesso

## Estratégia de Autenticação

O sistema utiliza **JWT (JSON Web Tokens)** com dois tokens:

| Token | TTL | Uso |
|---|---|---|
| `accessToken` | 15 minutos | Autenticar requisições à API |
| `refreshToken` | 7 dias | Renovar o accessToken sem novo login |

O refreshToken é armazenado no banco (tabela `RefreshToken`) permitindo invalidação explícita no logout.

---

## Fluxo de Autenticação

```
Login
  → POST /auth/login
  → Valida credenciais
  → Gera accessToken (15min) + refreshToken (7d)
  → Salva refreshToken no banco
  → Retorna ambos ao cliente

Requisição autenticada
  → Header: Authorization: Bearer <accessToken>
  → Guard valida JWT
  → Extrai userId e role do payload
  → Requisição prossegue

Token expirado
  → POST /auth/refresh com refreshToken
  → Valida token no banco (existe e não expirou)
  → Gera novo accessToken
  → Retorna novo accessToken

Logout
  → POST /auth/logout com refreshToken
  → Remove token do banco
  → Cliente descarta ambos os tokens
```

---

## Perfis de Acesso (Roles)

```typescript
enum Role {
  PATIENT  // paciente cadastrado
  DOCTOR   // médico cadastrado
  ADMIN    // administrador da clínica
}
```

---

## Matriz de Permissões por Rota

| Recurso | PATIENT | DOCTOR | ADMIN |
|---|---|---|---|
| Perfil próprio | leitura/escrita | leitura/escrita | leitura |
| Dados de outros pacientes | — | leitura | leitura/escrita |
| Consultas próprias | leitura/agendamento/cancelamento | leitura/gestão | leitura/gestão |
| Prontuários (criar) | — | escrita | — |
| Prontuários (ler) | próprios | próprios + pacientes | todos |
| Exames (solicitar) | — | escrita | — |
| Exames (ler) | próprios | próprios + pacientes | todos |
| Receitas (emitir) | — | escrita | — |
| Receitas (ler) | próprias | próprias + pacientes | todas |
| Chatbot de IA | sim | — | — |
| Resumo IA de prontuário | — | sim | — |
| Métricas da clínica | — | parcial | completo |
| Gestão de usuários | — | — | sim |

---

## Implementação NestJS

### JWT Guard (global)

```typescript
// src/common/guards/jwt-auth.guard.ts
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

Aplicado globalmente via `APP_GUARD` — todas as rotas exigem autenticação por padrão. Rotas públicas usam o decorator `@Public()`.

### Roles Guard

```typescript
// src/common/guards/roles.guard.ts
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<Role[]>('roles', [
      context.getHandler(),
      context.getClass(),
    ]);
    if (!requiredRoles) return true;

    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.includes(user.role);
  }
}
```

### Decorator de Roles

```typescript
// uso nos controllers:
@Roles(Role.DOCTOR, Role.ADMIN)
@Get(':patientId/records')
getMedicalRecords(@Param('patientId') id: string) { ... }
```

### Decorator Public

```typescript
// rotas sem autenticação:
@Public()
@Post('login')
login(@Body() dto: LoginDto) { ... }
```

---

## Payload do JWT

```typescript
interface JwtPayload {
  sub: string;    // userId
  email: string;
  role: Role;
  iat: number;    // issued at
  exp: number;    // expiration
}
```

---

## Segurança Adicional

- **Senhas:** armazenadas com bcrypt (salt rounds: 12)
- **Refresh tokens:** armazenados como hash no banco, nunca em plain text
- **CORS:** configurado para aceitar apenas origens autorizadas
- **Rate limiting:** `@nestjs/throttler` aplicado globalmente (100 req/min por IP)
- **Helmet:** headers de segurança HTTP configurados
- **Validação:** todos os DTOs validados com `class-validator` antes de chegar nos services

---

## Recuperação de Senha

```
POST /auth/forgot-password { email }
  → Gera token de reset (UUID, expira em 1h)
  → Salva hash do token no banco
  → Envia e-mail com link: /reset-password?token=...

POST /auth/reset-password { token, newPassword }
  → Valida token no banco (existe, não expirou, não foi usado)
  → Atualiza senha com novo hash bcrypt
  → Invalida token (marca como usado)
  → Invalida todos os refreshTokens do usuário
```
