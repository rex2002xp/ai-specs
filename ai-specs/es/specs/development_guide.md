# Guía de Desarrollo

Esta guía proporciona instrucciones paso a paso para configurar el entorno de desarrollo y ejecutar pruebas para el sistema LTI ATS.

## 🚀 Instrucciones de Configuración

### Prerrequisitos

Asegúrate de tener instalado lo siguiente:
- **Node.js** (v16 o superior)
- **npm** (v8 o superior)
- **Docker** y **Docker Compose**
- **Git**

### 1. Clonar el Repositorio

```bash
git clone git@github.com:LIDR-academy/AI4Devs-LTI-extended.git
cd AI4Devs-LTI-extended
```

### 2. Configuración del Entorno

Crear archivos de entorno tanto para backend como para frontend:

**Entorno del Backend** (`backend/.env`):
```env
# Configuración de Base de Datos
DB_HOST=localhost
DB_PORT=5432
DB_USER=LTIdbUser
DB_PASSWORD=D1ymf8wyQEGthFR1E9xhCq
DB_NAME=LTIdb

# Configuración de la Aplicación
PORT=3000
NODE_ENV=development

# URL de Base de Datos Prisma
DATABASE_URL="postgresql://LTIdbUser:D1ymf8wyQEGthFR1E9xhCq@localhost:5432/LTIdb"
```

**Entorno del Frontend** (`frontend/.env`):
```env
REACT_APP_API_URL=http://localhost:3000
```

### 3. Configuración de Base de Datos (PostgreSQL con Docker)

Iniciar la base de datos PostgreSQL usando Docker Compose:

```bash
# Iniciar contenedor de PostgreSQL
docker-compose up -d

# Verificar que la base de datos esté corriendo
docker-compose ps
```

La base de datos PostgreSQL estará disponible en:
- **Host**: `localhost`
- **Puerto**: `5432`
- **Base de datos**: `LTIdb`
- **Usuario**: `LTIdbUser`
- **Contraseña**: `D1ymf8wyQEGthFR1E9xhCq`

### 4. Configuración del Backend

```bash
# Navegar al directorio del backend
cd backend

# Instalar dependencias
npm install

# Generar cliente de Prisma
npm run prisma:generate

# Ejecutar migraciones de base de datos
npx prisma migrate deploy

# (Opcional) Poblar la base de datos con datos de ejemplo
npx prisma db seed

# Iniciar el servidor de desarrollo
npm run dev
```

La API del backend estará disponible en `http://localhost:3000`

### 5. Configuración del Frontend

```bash
# Navegar al directorio del frontend (desde la raíz del proyecto)
cd frontend

# Instalar dependencias
npm install

# Iniciar el servidor de desarrollo
npm start
```

La aplicación frontend estará disponible en `http://localhost:3001`

### 6. Configuración de Suite de Pruebas Cypress

```bash
# Desde el directorio frontend
cd frontend

# Instalar Cypress (si no está instalado)
npm install

# Abrir Cypress Test Runner (Interactivo)
npm run cypress:open

# O ejecutar pruebas en modo headless
npm run cypress:run
```

## 🧪 Pruebas

### Pruebas de Backend

```bash
cd backend

# Ejecutar todas las pruebas
npm test

# Ejecutar pruebas en modo watch
npm run test:watch

# Ejecutar pruebas con cobertura
npm run test:coverage
```

### Pruebas de Frontend

```bash
cd frontend

# Ejecutar pruebas unitarias
npm test

# Ejecutar pruebas E2E con Cypress
npm run cypress:run

# Abrir Cypress Test Runner
npm run cypress:open
```
