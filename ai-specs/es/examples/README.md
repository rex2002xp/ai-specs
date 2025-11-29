# Ejemplos de Aplicaciones Next.js

Este directorio contiene **ejemplos completos de proyectos** construidos siguiendo las especificaciones genéricas definidas en `../specs/`.

---

## 📋 Propósito

Los ejemplos sirven para:
1. **Demostrar** cómo aplicar las especificaciones en proyectos reales
2. **Proveer referencia** para casos de uso comunes
3. **Mostrar** la estructura completa de documentación personalizada
4. **Inspirar** decisiones arquitectónicas

⚠️ **Importante**: Estos son ejemplos específicos de proyectos, NO plantillas genéricas. Para plantillas, ve a `../specs/`.

---

## 🗂️ Ejemplos Disponibles

### [LTI ATS - Sistema de Seguimiento de Candidatos](./lti-ats/)

**Descripción**: Sistema completo de Application Tracking System (ATS) para procesos de reclutamiento.

**Dominio**: Recursos Humanos / Reclutamiento

**Características**:
- Gestión de candidatos
- Posiciones laborales
- Proceso de entrevistas (multi-step)
- Aplicaciones y tracking
- Empresas y empleados (entrevistadores)

**Tecnologías Demostradas**:
- Next.js 16+ App Router
- Prisma ORM con PostgreSQL
- Arquitectura en capas (Route → Service → Repository)
- TypeScript modo estricto
- Testing con Jest y Cypress

**Qué puedes aprender**:
- Modelo de datos complejo con múltiples relaciones
- API REST bien estructurada
- Flujos de negocio con múltiples pasos
- Gestión de estados de aplicaciones

**Ver**:
- [`lti-ats/README.md`](./lti-ats/README.md) - Descripción detallada
- [`lti-ats/data-model.md`](./lti-ats/data-model.md) - Modelo de datos completo
- [`lti-ats/api-spec.yml`](./lti-ats/api-spec.yml) - Especificación OpenAPI
- [`lti-ats/development_guide.md`](./lti-ats/development_guide.md) - Guía de desarrollo
- [`lti-ats/ARCHITECTURAL_DISCUSSION.md`](./lti-ats/ARCHITECTURAL_DISCUSSION.md) - Discusión sobre DDD + Event-Driven

---

## 🚀 Cómo Usar Estos Ejemplos

### 1. Como Referencia

```bash
# Navega al ejemplo
cd lti-ats/

# Revisa la estructura de archivos
ls -la

# Lee la documentación
cat README.md
cat data-model.md
```

### 2. Como Punto de Partida

Si quieres construir algo similar:

```bash
# 1. Copia las plantillas genéricas desde ../specs/
cp ../specs/data-model.template.md ./mi-proyecto/data-model.md
cp ../specs/api-spec.template.yml ./mi-proyecto/api-spec.yml

# 2. Usa el ejemplo LTI como referencia para personalizar
# Compara lti-ats/data-model.md con tu data-model.md
```

### 3. Para Entrenamiento de IA

Proporciona el ejemplo completo al agente de IA:

```
"Revisa el ejemplo en ai-specs/es/examples/lti-ats/ para entender cómo
aplicar los estándares de ../specs/ en un proyecto real"
```

---

## 🎯 Comparación: Specs vs Examples

| Aspecto | `../specs/` | `./examples/` |
|---------|-------------|---------------|
| **Propósito** | Estándares genéricos reutilizables | Casos de uso específicos |
| **Contenido** | Plantillas con placeholders `[...]` | Documentación completa y específica |
| **Uso** | Copiar y personalizar | Referenciar y estudiar |
| **Aplicabilidad** | Todos los proyectos Next.js | Proyectos similares al ejemplo |
| **Mantenimiento** | Actualizar cuando cambien estándares | Actualizar cuando evolucione el ejemplo |

---

## 📚 Estructura de un Ejemplo Completo

Cada ejemplo debe incluir:

```
example-name/
├── README.md                      # Descripción del proyecto
├── data-model.md                  # Modelo de datos específico
├── api-spec.yml                   # OpenAPI spec completa
├── development_guide.md           # Guía de setup
└── ARCHITECTURAL_DISCUSSION.md    # (Opcional) Decisiones arquitectónicas
```

---

## 🤝 Contribuir con Nuevos Ejemplos

¿Construiste un proyecto interesante siguiendo estas especificaciones?

### Criterios para un Buen Ejemplo

1. **Completo**: Incluye toda la documentación (data-model, api-spec, guide)
2. **Real**: Basado en un proyecto funcional, no teórico
3. **Didáctico**: Demuestra patrones o casos de uso interesantes
4. **Documentado**: Explica decisiones y trade-offs

### Proceso de Contribución

1. Crea un nuevo directorio en `examples/`
2. Incluye los 4 archivos mínimos (ver estructura arriba)
3. Actualiza este README agregando tu ejemplo
4. Asegúrate que tu ejemplo siga los estándares de `../specs/`

---

## 💡 Ideas para Futuros Ejemplos

Ejemplos que serían valiosos:

- **E-commerce**: Productos, carrito, checkout, órdenes
- **Blog/CMS**: Posts, autores, categorías, comentarios
- **SaaS Multi-tenant**: Organizaciones, usuarios, subscripciones
- **Dashboard Analítico**: Métricas, reportes, visualizaciones
- **Marketplace**: Vendors, listings, transacciones
- **Learning Management System**: Cursos, estudiantes, lecciones
- **Project Management**: Proyectos, tareas, equipos
- **Healthcare**: Pacientes, citas, historia clínica

---

## 📖 Recursos Relacionados

- **[Especificaciones Genéricas](../specs/)** - Estándares base y plantillas
- **[Documentación de Next.js](https://nextjs.org/docs)** - Docs oficiales
- **[Prisma Docs](https://www.prisma.io/docs)** - ORM y migraciones

---

## ⚖️ Licencia

Los ejemplos son proporcionados con fines educativos y de referencia. Consulta cada ejemplo para detalles de licencia específicos.

---

**Última Actualización**: 2025-11-29
**Ejemplos Disponibles**: 1 (LTI ATS)
