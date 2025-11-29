# Archivos Legacy (Archivados)

Esta carpeta contiene especificaciones y estándares de arquitecturas anteriores del proyecto que ya no son relevantes para la arquitectura actual.

## Archivos Archivados

### `frontend-standards.mdc`
**Fecha de Archivo**: 2025-11-29
**Razón**: Describe una arquitectura de frontend separado con Create React App, React Router y React Bootstrap, que fue reemplazada por la arquitectura unificada de Next.js 16+.

**Tecnologías que Describía**:
- Create React App 5.0.1
- React Router DOM 6.23.1
- React Bootstrap 2.10.2
- Frontend separado en carpeta `frontend/`

### `backend-standards.mdc`
**Fecha de Archivo**: 2025-11-29
**Razón**: Describe una arquitectura de backend separado con Express/Node.js, que fue reemplazada por las API Routes de Next.js en la arquitectura unificada.

**Tecnologías que Describía**:
- Express.js
- Node.js/TypeScript backend standalone
- Backend separado en carpeta `backend/`
- Arquitectura DDD con Express

## Arquitectura Actual

El proyecto ahora utiliza una **arquitectura unificada con Next.js 16+** donde:
- Frontend y backend están integrados en la misma aplicación Next.js
- Se utiliza el App Router de Next.js
- Las API Routes manejan el backend en `src/app/api/`
- Se utiliza Tailwind CSS y shadcn/ui para estilos
- Soporte nativo para MCP (Model Context Protocol)

## Referencias a Documentación Actual

Para consultar los estándares actuales del proyecto, revisa:
- **[nextjs-standards.mdc](../nextjs-standards.mdc)**: Estándares principales de Next.js 16+
- **[mcp-integration.mdc](../mcp-integration.mdc)**: Configuración de MCP para desarrollo asistido por IA
- **[base-standards.mdc](../base-standards.mdc)**: Principios fundamentales del proyecto
- **[development_guide.md](../development_guide.md)**: Guía de configuración y desarrollo

---

**Nota**: Estos archivos se mantienen por razones históricas y de referencia, pero **NO deben ser utilizados** para desarrollo actual.
