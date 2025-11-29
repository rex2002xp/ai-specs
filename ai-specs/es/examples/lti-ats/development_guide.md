# Guía de Desarrollo (Arquitectura Unificada con Next.js)

Esta guía proporciona instrucciones paso a paso para configurar el entorno de desarrollo y ejecutar la aplicación y las pruebas para el sistema LTI ATS.

## 🚀 Instrucciones de Configuración

### Prerrequisitos

Asegúrate de tener instalado lo siguiente:
- **Node.js** (v18 o superior, recomendado v20+)
- **npm** (v9 o superior) o **pnpm/yarn**
- **Docker** y **Docker Compose**
- **Git**
- **Next.js 16+** para soporte de MCP (Model Context Protocol)

### 1. Clonar el Repositorio

```bash
git clone git@github.com:LIDR-academy/AI4Devs-LTI-extended.git
cd AI4Devs-LTI-extended
```

### 2. Configuración de la Base de Datos con Docker

La base de datos PostgreSQL se gestiona con Docker para simplificar la configuración.

```bash
# Iniciar el contenedor de la base de datos en segundo plano
docker-compose up -d

# Verificar que el contenedor esté corriendo
docker ps
```

La base de datos estará disponible localmente con las credenciales definidas en el archivo `docker-compose.yml`.

### 3. Configuración del Entorno

Crea un archivo de entorno en la raíz del proyecto. Este único archivo contendrá todas las variables necesarias.

**Crear el archivo:**
```bash
touch .env.local
```

**Añadir el siguiente contenido a `.env.local`:**

```env
# URL de Conexión a la Base de Datos para Prisma
# Asegúrate de que las credenciales coincidan con las de tu docker-compose.yml
DATABASE_URL="postgresql://LTIdbUser:D1ymf8wyQEGthFR1E9xhCq@localhost:5432/LTIdb"

# URL pública de la aplicación (usada por NextAuth, etc.)
NEXT_PUBLIC_APP_URL="http://localhost:3000"

# Otras variables de entorno (ej. para autenticación)
# NEXTAUTH_URL="http://localhost:3000"
# NEXTAUTH_SECRET="tu_secreto_aqui"
```

### 4. Instalación y Ejecución de la Aplicación

Con la base de datos corriendo y el entorno configurado, instala las dependencias y ejecuta la aplicación.

```bash
# 1. Instalar dependencias del proyecto
npm install

# 2. Aplicar las migraciones de la base de datos para crear las tablas
npx prisma migrate deploy

# 3. (Opcional) Poblar la base de datos con datos de prueba
npx prisma db seed

# 4. Iniciar el servidor de desarrollo
npm run dev
```

La aplicación Next.js estará disponible en `http://localhost:3000`. Incluirá tanto el frontend como las rutas de API.

### 5. (Opcional) Configurar MCP para Desarrollo Asistido por IA

Si deseas utilizar agentes de IA con acceso en tiempo real a tu aplicación Next.js, configura MCP:

```bash
# Crear archivo de configuración MCP en la raíz
cat > .mcp.json << 'EOF'
{
  "mcpServers": {
    "next-devtools": {
      "command": "npx",
      "args": ["-y", "next-devtools-mcp@latest"]
    }
  }
}
EOF
```

Con el servidor de desarrollo corriendo, los agentes de IA podrán acceder a errores, logs, metadata de páginas y más.

**Nota**: Para más información sobre MCP, consulta [mcp-integration.mdc](./mcp-integration.mdc).

## 🧪 Pruebas

Las pruebas se han unificado en un solo conjunto de comandos en la raíz del proyecto.

### Pruebas Unitarias y de Integración (Jest)

```bash
# Ejecutar todas las pruebas una vez
npm test

# Ejecutar pruebas en modo "watch" para desarrollo
npm run test:watch
```

### Pruebas End-to-End (Cypress)

```bash
# Abrir el lanzador de pruebas de Cypress en modo interactivo
npm run cypress:open

# Ejecutar todas las pruebas E2E en modo "headless" (sin UI)
npm run cypress:run
```
