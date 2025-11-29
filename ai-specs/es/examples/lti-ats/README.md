# LTI ATS - Sistema de Seguimiento de Candidatos

**Ejemplo de Aplicación**: Sistema completo de Application Tracking System (ATS) construido con Next.js 16+ siguiendo las especificaciones definidas en `../../specs/`.

---

## 📋 Descripción del Proyecto

LTI ATS (Learning Technology Initiative - Applicant Tracking System) es un sistema completo para gestionar procesos de reclutamiento, desde la publicación de posiciones laborales hasta la contratación final.

### Caso de Uso

**Actores**:
- **Reclutadores**: Publican posiciones, revisan aplicaciones, programan entrevistas
- **Candidatos**: Aplican a posiciones, suben CVs, participan en entrevistas
- **Entrevistadores (Empleados)**: Conducen entrevistas, evalúan candidatos

**Flujo Principal**:
1. Empresa publica una posición laboral
2. Candidato aplica a la posición
3. Candidato pasa por múltiples pasos de entrevista (configurables)
4. Cada entrevista es evaluada por un empleado
5. Candidato es contratado o rechazado

---

## 🗂️ Archivos de Documentación

### `data-model.md`
Modelo de datos completo con 12 entidades:

**Entidades Principales**:
- `Candidate` - Candidatos con educación, experiencia, CVs
- `Position` - Posiciones laborales
- `Application` - Aplicaciones de candidatos a posiciones
- `Interview` - Sesiones de entrevista individuales
- `InterviewFlow` - Flujo configurable de pasos de entrevista
- `Company` - Empresas que publican posiciones
- `Employee` - Empleados/entrevistadores

**Características**:
- Relaciones complejas (1:1, 1:N, N:M)
- Validaciones de negocio
- Diagrama ER en Mermaid
- Índices para performance

### `api-spec.yml`
Especificación OpenAPI 3.0 completa con:

**Endpoints**:
- CRUD completo para Candidates, Positions, Applications
- Endpoints especializados (ej: aplicar a posición)
- Paginación, filtrado, búsqueda
- Validaciones de datos

**Componentes**:
- Schemas reutilizables
- Respuestas de error estandarizadas
- Parámetros comunes

### `development_guide.md`
Guía específica para este proyecto:

**Contenido**:
- Setup de PostgreSQL con Docker
- Variables de entorno específicas
- Seeds para datos de prueba
- Scripts del proyecto

### `ARCHITECTURAL_DISCUSSION.md`
Discusión arquitectónica avanzada:

**Temas**:
- DDD + Event-Driven Architecture
- Arquitectura modular en Next.js
- Feature Modules vs Feature Flags
- Implementación de Event Bus
- Trade-offs y decisiones de diseño

---

## 🏗️ Arquitectura Aplicada

### Patrón en Capas

```
API Route → Service → Repository → Prisma → PostgreSQL
```

**Ejemplo del Flujo "Aplicar a Posición"**:

```typescript
// 1. Route (Controller)
// src/app/api/applications/route.ts
export async function POST(request: Request) {
  const data = await request.json();
  const application = await applicationService.create(data);
  return NextResponse.json(application, { status: 201 });
}

// 2. Service (Business Logic)
// src/services/application-service.ts
export async function create(data: ApplicationCreateInput) {
  // Validar que la posición esté abierta
  const position = await positionRepository.findById(data.positionId);
  if (position.status !== 'Open') {
    throw new Error('Position is not open for applications');
  }

  // Verificar que el candidato no haya aplicado antes
  const existing = await applicationRepository.findByPositionAndCandidate(
    data.positionId,
    data.candidateId
  );
  if (existing) {
    throw new Error('Candidate already applied to this position');
  }

  // Crear aplicación
  return applicationRepository.create(data);
}

// 3. Repository (Data Access)
// src/repositories/application-repository.ts
export const applicationRepository = {
  create: (data) => prisma.application.create({ data }),
  findByPositionAndCandidate: (positionId, candidateId) =>
    prisma.application.findFirst({
      where: { positionId, candidateId }
    })
};
```

### Patrones Aplicados

✅ **Single Responsibility Principle (SRP)**: Cada capa tiene una responsabilidad única
✅ **Dependency Inversion (DIP)**: Services dependen de interfaces, no de implementaciones
✅ **Repository Pattern**: Abstracción de acceso a datos
✅ **Domain Events**: Para desacoplamiento entre módulos (ver ARCHITECTURAL_DISCUSSION.md)

---

## 🎯 Características Destacadas

### 1. Modelo de Datos Complejo

**Relaciones Múltiples**:
- Candidate → Education (1:N)
- Candidate → WorkExperience (1:N)
- Candidate → Resume (1:N)
- Position → Application (1:N)
- Application → Interview (1:N)
- InterviewFlow → InterviewStep (1:N)

**Validaciones de Negocio**:
- Máximo 3 registros de educación por candidato
- Rango salarial (salaryMax >= salaryMin)
- Emails únicos
- Fechas de deadline futuras

### 2. Flujo de Entrevistas Configurable

```
Position → InterviewFlow → InterviewStep[]

Ejemplo:
Position: "Senior Developer"
└─ InterviewFlow: "Tech Hiring Process"
   ├─ Step 1: HR Interview
   ├─ Step 2: Technical Interview
   ├─ Step 3: System Design Interview
   └─ Step 4: Cultural Fit Interview
```

### 3. Estados de Posición

```typescript
enum PositionStatus {
  Draft    // Borrador, no visible
  Open     // Abierta para aplicaciones
  Hired    // Alguien fue contratado
  Closed   // Cerrada sin contratación
}
```

### 4. Tracking de Aplicaciones

Cada `Application` tiene:
- `currentInterviewStep`: En qué paso del proceso está
- `interviewStepId`: Referencia al paso actual
- `notes`: Notas del reclutador

---

## 📊 Diagrama de Dominio

```
┌─────────────┐     aplica a      ┌──────────┐
│  Candidate  │───────────────────>│ Position │
│             │                    │          │
│ - firstName │                    │ - title  │
│ - lastName  │                    │ - status │
│ - email     │                    └──────────┘
└─────────────┘                         │
      │                                 │ tiene
      │ tiene                           ▼
      │                          ┌──────────────┐
      ▼                          │InterviewFlow │
┌─────────────┐                  │              │
│  Education  │                  │- description │
│             │                  └──────────────┘
│ - title     │                         │
│ - institution│                        │ contiene
└─────────────┘                         ▼
                                 ┌──────────────┐
┌─────────────┐                  │InterviewStep │
│WorkExperience│                 │              │
│             │                  │ - name       │
│ - company   │                  │ - orderIndex │
│ - position  │                  └──────────────┘
└─────────────┘                         │
                                        │ define
                                        ▼
┌─────────────┐                  ┌──────────────┐
│   Resume    │                  │  Interview   │
│             │                  │              │
│ - filePath  │                  │ - date       │
│ - fileType  │                  │ - result     │
└─────────────┘                  │ - score      │
                                 └──────────────┘
```

---

## 🧪 Testing

### Unit Tests (Jest)

```bash
# Tests de servicios
__tests__/services/application-service.test.ts
__tests__/services/candidate-service.test.ts

# Tests de repositorios (con mocks de Prisma)
__tests__/repositories/application-repository.test.ts
```

### E2E Tests (Cypress)

```bash
# Flujos completos
cypress/e2e/application-flow.cy.ts
cypress/e2e/interview-process.cy.ts
```

---

## 🚀 Casos de Uso Completos

### Caso 1: Publicar Posición

```typescript
// 1. Crear Interview Flow
const flow = await interviewFlowService.create({
  description: "Standard Tech Hiring"
});

// 2. Agregar Steps al Flow
await interviewStepService.create({
  name: "HR Screening",
  orderIndex: 1,
  interviewFlowId: flow.id,
  interviewTypeId: hrTypeId
});

// 3. Crear Position
await positionService.create({
  title: "Senior Developer",
  companyId: company.id,
  interviewFlowId: flow.id,
  status: "Open",
  // ...otros campos
});
```

### Caso 2: Candidato Aplica

```typescript
// 1. Crear Application
const application = await applicationService.create({
  candidateId: candidate.id,
  positionId: position.id,
  currentInterviewStep: 1
});

// 2. Event (si usas arquitectura por eventos)
eventBus.publish('CandidateAppliedToPosition', {
  candidateId,
  positionId,
  applicationDate: new Date()
});

// 3. Handlers de evento
// - Enviar email de confirmación
// - Notificar a reclutador
// - Registrar en analytics
```

### Caso 3: Conducir Entrevista

```typescript
// 1. Programar Interview
const interview = await interviewService.schedule({
  applicationId: application.id,
  interviewStepId: step.id,
  employeeId: interviewer.id,
  interviewDate: new Date('2024-02-15T10:00:00Z')
});

// 2. Evaluar Interview
await interviewService.evaluate(interview.id, {
  result: "PASS",
  score: 85,
  notes: "Strong technical skills, good communication"
});

// 3. Avanzar Application al siguiente step
await applicationService.advanceToNextStep(application.id);
```

---

## 📈 Escalabilidad y Extensiones

### Extensiones Posibles

1. **Módulo de Notificaciones**
   - Email notifications
   - SMS reminders
   - Push notifications

2. **Módulo de Analytics**
   - Time-to-hire metrics
   - Conversion rates
   - Interview success rates

3. **Módulo de Calendario**
   - Disponibilidad de entrevistadores
   - Integración con Google Calendar
   - Recordatorios automáticos

4. **Módulo de Evaluaciones**
   - Tests técnicos online
   - Assignments de código
   - Scoring automático

### Migración a Microservicios

Si el sistema crece, módulos pueden extraerse:

```
Monolito Next.js
├── Core: Candidates, Positions, Applications
├── Interview Service (microservicio)
├── Notification Service (microservicio)
└── Analytics Service (microservicio)
```

---

## 🔗 Referencias

- **[Especificaciones Base](../../specs/base-standards.mdc)** - Estándares aplicados
- **[Next.js Standards](../../specs/nextjs-standards.mdc)** - Patrones de arquitectura
- **[MCP Integration](../../specs/mcp-integration.mdc)** - Desarrollo asistido por IA

---

## 📝 Licencia

Este ejemplo es proporcionado con fines educativos y de referencia.

---

**Última Actualización**: 2025-11-29
**Versión**: 1.0.0
**Estado**: Ejemplo Completo
