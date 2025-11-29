# Discusión Arquitectónica: DDD + Event-Driven + Modularización en Next.js

**Fecha**: 2025-11-29
**Contexto**: Sistema LTI ATS con Next.js 16+

---

## 1. Compatibilidad entre DDD y Arquitectura por Eventos

### Respuesta Corta: **¡Absolutamente compatibles!**

De hecho, **Event-Driven Architecture (EDA)** es uno de los patrones más naturales para implementar Domain-Driven Design, especialmente cuando se combina con:

- **CQRS** (Command Query Responsibility Segregation)
- **Event Sourcing**
- **Domain Events**

### 1.1 Cómo se Complementan

#### DDD proporciona:
- **Agregados**: Unidades de consistencia transaccional
- **Entidades**: Objetos con identidad
- **Value Objects**: Objetos inmutables sin identidad
- **Domain Events**: Eventos que ocurren en el dominio

#### Event-Driven Architecture proporciona:
- **Desacoplamiento**: Los módulos no se conocen directamente
- **Escalabilidad**: Procesamiento asíncrono
- **Auditoría**: Historia completa de cambios
- **Extensibilidad**: Nuevos módulos pueden suscribirse a eventos existentes

### 1.2 Ejemplo Práctico en tu Sistema LTI

```typescript
// Domain Event: CandidateAppliedToPosition
interface CandidateAppliedToPosition {
  eventId: string;
  timestamp: Date;
  aggregateId: string; // applicationId
  data: {
    candidateId: number;
    positionId: number;
    applicationDate: Date;
  };
}

// Cuando se crea una Application, emitir evento
class ApplicationService {
  async applyToPosition(candidateId: number, positionId: number) {
    // 1. Crear Application (Agregado)
    const application = await applicationRepository.create({
      candidateId,
      positionId,
      applicationDate: new Date(),
      currentInterviewStep: 1
    });

    // 2. Emitir Domain Event
    await eventBus.publish({
      type: 'CandidateAppliedToPosition',
      eventId: generateId(),
      timestamp: new Date(),
      aggregateId: application.id.toString(),
      data: {
        candidateId,
        positionId,
        applicationDate: application.applicationDate
      }
    });

    return application;
  }
}

// Módulos que escuchan el evento
// 1. Notification Module
eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
  await notificationService.notifyRecruiter(event.data.positionId);
});

// 2. Analytics Module
eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
  await analyticsService.trackApplication(event.data);
});

// 3. Email Module
eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
  await emailService.sendConfirmationToCandidate(event.data.candidateId);
});
```

### 1.3 Beneficios para tu Sistema

1. **Desacoplamiento de Módulos**: El módulo de Applications no necesita conocer Notifications, Analytics o Email
2. **Fácil Extensión**: Agregar un nuevo módulo de "AuditLog" solo requiere suscribirse a eventos existentes
3. **Historia Completa**: Event Store mantiene registro de todo lo que ha pasado
4. **Rollback y Replay**: Puedes reconstruir el estado desde eventos

---

## 2. Arquitectura Modular en Next.js

### 2.1 Concepto: Feature Modules (Módulos por Funcionalidad)

En lugar de organizar por tipo técnico (controllers, services, repositories), organizamos por **dominios de negocio**:

```
src/
├── modules/                    # Todos los módulos del sistema
│   ├── candidates/            # Módulo de Candidatos
│   │   ├── domain/           # Lógica de dominio
│   │   │   ├── entities/
│   │   │   │   └── candidate.entity.ts
│   │   │   ├── events/
│   │   │   │   └── candidate-registered.event.ts
│   │   │   └── value-objects/
│   │   │       └── email.vo.ts
│   │   ├── application/      # Casos de uso
│   │   │   ├── commands/
│   │   │   │   └── register-candidate.command.ts
│   │   │   ├── queries/
│   │   │   │   └── get-candidate.query.ts
│   │   │   └── services/
│   │   │       └── candidate.service.ts
│   │   ├── infrastructure/   # Acceso a datos y servicios externos
│   │   │   ├── repositories/
│   │   │   │   └── candidate.repository.ts
│   │   │   └── prisma/
│   │   │       └── candidate.prisma.ts
│   │   ├── presentation/     # UI y API
│   │   │   ├── api/
│   │   │   │   └── route.ts  # /api/candidates
│   │   │   ├── components/
│   │   │   │   ├── CandidateCard.tsx
│   │   │   │   └── CandidateForm.tsx
│   │   │   └── pages/
│   │   │       └── page.tsx  # /candidates
│   │   └── module.config.ts  # Configuración del módulo
│   │
│   ├── positions/             # Módulo de Posiciones
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   ├── presentation/
│   │   └── module.config.ts
│   │
│   ├── interviews/            # Módulo de Entrevistas
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   ├── presentation/
│   │   └── module.config.ts
│   │
│   ├── notifications/         # Módulo de Notificaciones
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   ├── presentation/
│   │   └── module.config.ts
│   │
│   └── analytics/             # Módulo de Analytics (opcional/desactivable)
│       ├── domain/
│       ├── application/
│       ├── infrastructure/
│       ├── presentation/
│       └── module.config.ts
│
├── shared/                    # Código compartido entre módulos
│   ├── domain/
│   │   ├── events/
│   │   │   └── event-bus.interface.ts
│   │   └── repositories/
│   │       └── base.repository.ts
│   ├── infrastructure/
│   │   ├── events/
│   │   │   └── event-bus.implementation.ts
│   │   ├── prisma/
│   │   │   └── prisma.client.ts
│   │   └── validation/
│   │       └── zod-schemas.ts
│   └── presentation/
│       ├── components/
│       │   └── ui/           # shadcn/ui components
│       └── layouts/
│
├── app/                       # Next.js App Router (routing only)
│   ├── api/
│   │   ├── candidates/
│   │   │   └── route.ts     # Re-exporta desde módulo
│   │   └── positions/
│   │       └── route.ts
│   ├── candidates/
│   │   └── page.tsx         # Re-exporta desde módulo
│   └── positions/
│       └── page.tsx
│
└── config/
    └── modules.config.ts    # Registro de módulos activos
```

### 2.2 Ventajas de esta Arquitectura

#### ✅ **Alta Cohesión**
Todo lo relacionado con Candidates está en `modules/candidates/`

#### ✅ **Bajo Acoplamiento**
Módulos se comunican solo vía:
- Domain Events (event bus)
- Interfaces bien definidas
- Shared abstractions

#### ✅ **Fácil de Activar/Desactivar Módulos**
```typescript
// config/modules.config.ts
export const moduleRegistry = {
  candidates: { enabled: true, required: true },
  positions: { enabled: true, required: true },
  interviews: { enabled: true, required: true },
  notifications: { enabled: true, required: false },
  analytics: { enabled: false, required: false },  // Desactivado
  reporting: { enabled: false, required: false }   // No implementado aún
};
```

#### ✅ **Testing Aislado**
Cada módulo tiene sus propios tests sin depender de otros

#### ✅ **Escalabilidad**
Módulos pueden extraerse a microservicios en el futuro

---

## 3. Feature Flags vs Feature Folders

### 3.1 Feature Folders (Organización del Código)

**Definición**: Organizar el código por funcionalidades/módulos en lugar de por tipo técnico.

```
❌ Organización por Tipo Técnico:
src/
├── controllers/
├── services/
├── repositories/
└── models/

✅ Organización por Features (Módulos):
src/modules/
├── candidates/
├── positions/
└── interviews/
```

**Cuándo usar**: Siempre en proyectos medianos a grandes.

### 3.2 Feature Flags (Toggles de Funcionalidad)

**Definición**: Mecanismo para activar/desactivar funcionalidades en **runtime** sin cambiar código.

```typescript
// Implementación simple de Feature Flags
class FeatureFlags {
  private flags: Map<string, boolean>;

  async isEnabled(flagName: string): Promise<boolean> {
    // Puede leer de base de datos, variable de entorno, servicio remoto
    return process.env[`FEATURE_${flagName}`] === 'true';
  }
}

// Uso en código
if (await featureFlags.isEnabled('ADVANCED_ANALYTICS')) {
  // Mostrar analytics avanzados
}

// En API Route
export async function GET(request: Request) {
  const candidates = await candidateService.getAll();

  if (await featureFlags.isEnabled('ANALYTICS_MODULE')) {
    await analyticsService.trackCandidateViews();
  }

  return NextResponse.json(candidates);
}
```

**Cuándo usar**:
- Despliegues graduales (canary releases)
- A/B testing
- Desactivar funcionalidades con problemas sin re-deploy
- Beta features para usuarios específicos

### 3.3 ¿Necesitas ambos?

**Para módulos completos**: Usa **Feature Folders** + **Module Configuration**

```typescript
// modules/analytics/module.config.ts
export const analyticsModuleConfig = {
  name: 'analytics',
  enabled: process.env.ENABLE_ANALYTICS_MODULE === 'true',
  dependencies: ['candidates', 'positions'],
  eventSubscriptions: [
    'CandidateAppliedToPosition',
    'InterviewScheduled'
  ]
};
```

**Para features pequeñas dentro de módulos**: Usa **Feature Flags**

```typescript
// Dentro del módulo de candidates
if (await featureFlags.isEnabled('CANDIDATE_SOCIAL_LOGIN')) {
  // Mostrar botones de login social
}
```

---

## 4. Implementación Práctica: Event Bus en Next.js

### 4.1 Event Bus Simple

```typescript
// shared/infrastructure/events/event-bus.ts
type EventHandler<T = any> = (event: T) => Promise<void> | void;

class EventBus {
  private handlers: Map<string, EventHandler[]> = new Map();

  subscribe<T>(eventType: string, handler: EventHandler<T>) {
    if (!this.handlers.has(eventType)) {
      this.handlers.set(eventType, []);
    }
    this.handlers.get(eventType)!.push(handler);
  }

  async publish<T>(eventType: string, event: T) {
    const handlers = this.handlers.get(eventType) || [];

    // Ejecutar handlers en paralelo (o secuencialmente según necesidad)
    await Promise.all(
      handlers.map(handler =>
        handler(event).catch(err => {
          console.error(`Error handling ${eventType}:`, err);
          // Aquí podrías implementar retry logic, dead letter queue, etc.
        })
      )
    );
  }
}

export const eventBus = new EventBus();
```

### 4.2 Registro de Módulos con Eventos

```typescript
// modules/notifications/module.config.ts
import { eventBus } from '@/shared/infrastructure/events/event-bus';
import { sendApplicationNotification } from './application/services/notification.service';

export function registerNotificationsModule() {
  // Suscribirse a eventos del dominio
  eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
    await sendApplicationNotification(event.data);
  });

  eventBus.subscribe('InterviewScheduled', async (event) => {
    await sendInterviewReminder(event.data);
  });
}

// En app/layout.tsx o similar
if (moduleRegistry.notifications.enabled) {
  registerNotificationsModule();
}
```

### 4.3 Event Store (Opcional pero Recomendado)

```typescript
// shared/infrastructure/events/event-store.ts
interface StoredEvent {
  id: string;
  type: string;
  aggregateId: string;
  data: any;
  timestamp: Date;
  metadata?: any;
}

class EventStore {
  async save(event: StoredEvent) {
    await prisma.eventStore.create({
      data: event
    });
  }

  async getEventsForAggregate(aggregateId: string): Promise<StoredEvent[]> {
    return prisma.eventStore.findMany({
      where: { aggregateId },
      orderBy: { timestamp: 'asc' }
    });
  }

  async getAllEvents(): Promise<StoredEvent[]> {
    return prisma.eventStore.findMany({
      orderBy: { timestamp: 'asc' }
    });
  }
}
```

---

## 5. Propuesta de Arquitectura para tu Sistema LTI

### 5.1 Módulos Principales

#### Módulos Core (Siempre Activos)
1. **Candidates Module**: Gestión de candidatos
2. **Positions Module**: Gestión de posiciones
3. **Applications Module**: Proceso de aplicación
4. **Interviews Module**: Sistema de entrevistas

#### Módulos Opcionales (Activables)
5. **Notifications Module**: Email, SMS, push notifications
6. **Analytics Module**: Métricas y reportes
7. **Reporting Module**: Reportes avanzados
8. **Integration Module**: Integraciones con servicios externos (LinkedIn, etc.)

### 5.2 Comunicación entre Módulos

```typescript
// Ejemplo: Flujo de Aplicación a Posición

// 1. Applications Module emite evento
const application = await applicationService.create(data);
await eventBus.publish('CandidateAppliedToPosition', {
  eventId: generateId(),
  aggregateId: application.id,
  data: { candidateId, positionId, applicationDate }
});

// 2. Notifications Module (si está activado) escucha y envía email
if (moduleRegistry.notifications.enabled) {
  eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
    await emailService.sendConfirmation(event.data.candidateId);
    await emailService.notifyRecruiter(event.data.positionId);
  });
}

// 3. Analytics Module (si está activado) registra métrica
if (moduleRegistry.analytics.enabled) {
  eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
    await analyticsService.trackApplicationConversion(event.data);
  });
}

// 4. Audit Module (siempre activo) registra en log
eventBus.subscribe('CandidateAppliedToPosition', async (event) => {
  await auditLog.record(event);
});
```

### 5.3 Estructura de Archivo Propuesta

```
src/
├── modules/
│   ├── candidates/
│   │   ├── domain/
│   │   │   ├── entities/candidate.entity.ts
│   │   │   ├── events/candidate-registered.event.ts
│   │   │   └── repositories/candidate-repository.interface.ts
│   │   ├── application/
│   │   │   ├── commands/register-candidate.command.ts
│   │   │   ├── queries/get-candidate.query.ts
│   │   │   └── services/candidate.service.ts
│   │   ├── infrastructure/
│   │   │   └── repositories/candidate.repository.impl.ts
│   │   ├── presentation/
│   │   │   ├── components/CandidateForm.tsx
│   │   │   └── api/candidates.route.ts
│   │   └── index.ts  # Public API del módulo
│   │
│   ├── positions/
│   ├── applications/
│   ├── interviews/
│   └── notifications/  # Módulo opcional
│
├── shared/
│   ├── domain/
│   │   └── events/event-bus.interface.ts
│   ├── infrastructure/
│   │   ├── events/event-bus.impl.ts
│   │   ├── prisma/client.ts
│   │   └── config/module-registry.ts
│   └── presentation/
│       └── components/ui/
│
└── app/  # Next.js routing (thin layer)
    ├── api/
    │   └── [...moduleroute]/route.ts
    └── (pages)/
        └── [...modulepage]/page.tsx
```

---

## 6. Consideraciones y Trade-offs

### 6.1 Ventajas de DDD + Event-Driven + Modular

✅ **Mantenibilidad**: Cambios aislados por módulo
✅ **Escalabilidad**: Módulos pueden extraerse a servicios
✅ **Testing**: Tests aislados y rápidos
✅ **Extensibilidad**: Nuevos módulos sin tocar existentes
✅ **Flexibilidad**: Activar/desactivar features
✅ **Auditoría**: Event Store completo

### 6.2 Desafíos

⚠️ **Complejidad Inicial**: Más boilerplate al inicio
⚠️ **Curva de Aprendizaje**: El equipo debe entender DDD/Events
⚠️ **Eventual Consistency**: Los eventos son asíncronos
⚠️ **Debugging**: Flujos distribuidos son más difíciles de depurar
⚠️ **Over-engineering**: Para proyectos muy pequeños puede ser excesivo

### 6.3 ¿Cuándo aplicar esta arquitectura?

✅ **Aplicar si**:
- El proyecto crecerá significativamente
- Múltiples desarrolladores trabajando en paralelo
- Necesitas activar/desactivar funcionalidades
- Planeas escalar a microservicios
- Necesitas auditoría completa

❌ **No aplicar si**:
- Proyecto muy pequeño (< 5 páginas)
- Equipo sin experiencia en DDD
- Deadline muy ajustado
- MVP rápido sin planes de crecimiento

---

## 7. Próximos Pasos Sugeridos

### Fase 1: Fundación (Semana 1-2)
1. Implementar Event Bus básico
2. Definir estructura de módulos
3. Migrar un módulo como prueba (ej: Candidates)

### Fase 2: Migración Gradual (Semana 3-6)
4. Migrar módulos restantes uno por uno
5. Implementar Event Store
6. Documentar eventos del dominio

### Fase 3: Optimización (Semana 7+)
7. Implementar retry logic para eventos
8. Agregar monitoreo de eventos
9. Implementar dead letter queue
10. Performance tuning

---

## 8. Preguntas para Discusión

1. **¿Cuál es el tamaño esperado del proyecto?** (pequeño, mediano, grande)
2. **¿Cuántos desarrolladores trabajarán en paralelo?**
3. **¿Necesitas activar/desactivar funcionalidades según cliente/tenant?**
4. **¿Planeas escalar a microservicios en el futuro?**
5. **¿Qué tan importante es la auditoría completa de cambios?**
6. **¿Prefieres empezar simple y evolucionar, o diseñar todo desde el inicio?**

---

## Referencias y Recursos

- [Domain-Driven Design by Eric Evans](https://www.domainlanguage.com/ddd/)
- [Event-Driven Architecture Patterns](https://martinfowler.com/articles/201701-event-driven.html)
- [Modular Monolith Architecture](https://www.kamilgrzybek.com/design/modular-monolith-primer/)
- [Next.js Project Structure Best Practices](https://nextjs.org/docs/app/building-your-application/routing)
- [CQRS Pattern](https://martinfowler.com/bliki/CQRS.html)
