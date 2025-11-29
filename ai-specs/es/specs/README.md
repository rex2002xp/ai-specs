# Especificaciones para Desarrollo de Aplicaciones Next.js con IA

Este directorio contiene especificaciones, estándares y plantillas **genéricas y reutilizables** para construir aplicaciones full stack con Next.js 16+ utilizando asistentes de IA.

---

## 📋 Propósito

Estas especificaciones están diseñadas para ser utilizadas por **agentes de IA** (Claude, Cursor, GitHub Copilot, etc.) como contexto y guía durante el desarrollo de proyectos Next.js. Son **independientes del proyecto** y pueden aplicarse a cualquier aplicación Next.js.

---

## 📁 Estructura de Archivos

### 🎯 Estándares Core (Siempre Aplican)

#### **`base-standards.mdc`**
Principios fundamentales que aplican a TODOS los proyectos:
- Idioma: Español para documentación, Inglés para código
- Filosofía de desarrollo: tareas pequeñas, TDD, seguridad de tipos
- Referencias a documentación específica

**Cuándo usar**: Siempre. Este es el punto de entrada principal.

---

#### **`nextjs-standards.mdc`**
Estándares específicos de Next.js 16+:
- Arquitectura unificada (frontend + backend)
- App Router patterns
- Arquitectura en capas (Route → Service → Repository)
- Patrón Repositorio
- Gestión de estado
- Testing

**Cuándo usar**: En todos los proyectos Next.js.

---

#### **`mcp-integration.mdc`**
Guía completa de MCP (Model Context Protocol):
- Configuración de MCP para Next.js 16+
- Herramientas disponibles para agentes de IA
- Mejores prácticas
- Casos de uso

**Cuándo usar**: Si utilizas agentes de IA con acceso en tiempo real al proyecto.

---

#### **`documentation-standards.mdc`**
Estándares para mantener documentación:
- Reglas de lenguaje (Español/Inglés)
- Proceso de actualización de documentación
- Proceso de aprendizaje de IA

**Cuándo usar**: Siempre. Guía sobre cómo mantener la documentación actualizada.

---

### 📝 Plantillas (Para Personalizar en Cada Proyecto)

#### **`data-model.template.md`**
Plantilla para documentar el modelo de datos:
- Descripción de entidades y relaciones
- Campos y validaciones
- Diagramas ER
- Principios de diseño

**Cómo usar**:
1. Copia como `data-model.md` en tu proyecto
2. Reemplaza los placeholders `[...]` con tu información
3. Documenta tus entidades específicas
4. Mantén sincronizado con `prisma/schema.prisma`

---

#### **`api-spec.template.yml`**
Plantilla OpenAPI 3.0 para documentar la API:
- Endpoints CRUD genéricos
- Schemas reutilizables
- Respuestas de error estandarizadas
- Parámetros comunes

**Cómo usar**:
1. Copia como `api-spec.yml` en tu proyecto
2. Reemplaza `[Recurso1]`, `[Recurso2]` con tus recursos
3. Agrega endpoints específicos de tu negocio
4. Actualiza schemas según tu modelo de datos

---

#### **`development_guide.template.md`**
Plantilla de guía de desarrollo:
- Instrucciones de configuración
- Variables de entorno
- Scripts disponibles
- Solución de problemas

**Cómo usar**:
1. Copia como `development_guide.md` o `README.md`
2. Personaliza según tu stack tecnológico
3. Actualiza URLs, nombres de proyecto, etc.
4. Agrega instrucciones específicas de tu proyecto

---

### 📂 Legacy (Archivados)

Contiene especificaciones de arquitecturas anteriores que ya no se utilizan:
- `frontend-standards.mdc` - React standalone + CRA
- `backend-standards.mdc` - Express.js separado

**Ver**: `legacy/README.md` para más detalles.

---

## 🚀 Flujo de Trabajo Recomendado

### Para un Nuevo Proyecto

1. **Configura el agente de IA** con `base-standards.mdc`
2. **Referencia** `nextjs-standards.mdc` para arquitectura
3. **Opcional**: Configura MCP usando `mcp-integration.mdc`
4. **Personaliza las plantillas**:
   - Copia `data-model.template.md` → `data-model.md`
   - Copia `api-spec.template.yml` → `api-spec.yml`
   - Copia `development_guide.template.md` → `README.md`
5. **Desarrolla** siguiendo los estándares

### Durante el Desarrollo

- **Consulta** `nextjs-standards.mdc` para patrones de código
- **Actualiza** `data-model.md` cuando cambies el schema de Prisma
- **Actualiza** `api-spec.yml` cuando agregues/modifiques endpoints
- **Usa MCP** si está configurado para debugging asistido

---

## 🎓 Conceptos Clave

### Arquitectura Unificada de Next.js

```
Next.js App
├── Frontend (Server/Client Components)
│   └── Componentes React en src/app/
└── Backend (API Routes)
    └── Rutas de API en src/app/api/
```

**Regla fundamental**: El frontend NUNCA accede directamente a la base de datos, siempre a través de API Routes.

### Arquitectura en Capas

```
Route (Controller) → Service → Repository → Prisma → Database
```

- **Route**: Maneja HTTP (request/response)
- **Service**: Lógica de negocio pura
- **Repository**: Acceso a datos (abstrae Prisma)

### Patrón Repositorio

Centraliza el acceso a datos por entidad:

```typescript
export const userRepository = {
  findById: (id) => prisma.user.findUnique({ where: { id } }),
  create: (data) => prisma.user.create({ data }),
  // ...
}
```

### MCP (Model Context Protocol)

Permite a agentes de IA acceder en tiempo real a:
- Errores de compilación, runtime y tipos
- Metadata de páginas y rutas
- Logs del servidor
- Estado de la aplicación

---

## 📖 Ejemplos de Referencia

Para ver cómo aplicar estas especificaciones en un proyecto real, consulta:

**[Sistema LTI ATS](../examples/lti-ats/)** - Ejemplo completo de sistema de seguimiento de candidatos (ATS) construido siguiendo estos estándares.

---

## 🤖 Uso con Agentes de IA

### Claude Code

```bash
# Claude Code lee automáticamente archivos .mdc en el proyecto
# Asegúrate de tener base-standards.mdc en tu raíz
```

### Cursor

```json
// .cursorrules
{
  "rules": [
    "Seguir estándares en ai-specs/es/specs/base-standards.mdc",
    "Aplicar arquitectura de ai-specs/es/specs/nextjs-standards.mdc"
  ]
}
```

### GitHub Copilot

Abre los archivos `.mdc` relevantes en tu editor para que Copilot tenga contexto.

---

## 🔄 Mantenimiento

### Cuándo Actualizar los Estándares

Actualiza los estándares cuando:
- Adoptas una nueva versión de Next.js con cambios significativos
- Identificas patrones repetitivos que deberían estandarizarse
- Descubres mejores prácticas que quieres documentar
- Hay cambios en MCP o herramientas de IA

### Versionado

Estos estándares siguen el proyecto y evolucionan con él. Para mantener historial:
- Usa Git para trackear cambios
- Documenta cambios significativos en commits
- Considera tags para versiones mayores

---

## 📞 Soporte

Si tienes dudas sobre cómo aplicar estos estándares:

1. Consulta los ejemplos en `../examples/lti-ats/`
2. Revisa la documentación oficial de Next.js
3. Pregunta al agente de IA proporcionando contexto de los estándares

---

## 📄 Licencia

Estos estándares son parte del proyecto [nombre del proyecto] y están bajo la misma licencia.

---

**Última Actualización**: 2025-11-29
**Versión de Next.js**: 16+
**Compatibilidad**: Next.js 16+ con App Router
