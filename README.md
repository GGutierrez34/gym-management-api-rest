# Gym Management API

Aplicación web full-stack y API REST diseñada para facilitar la gestión de un gimnasio, permitiendo administrar **usuarios/socios**, **entrenadores** y la **reserva de turnos/clases** con sus horarios y actividades.

## Funcionalidades

- **Usuarios (Socios):** registro, consulta general e individual por DNI, edición y baja (con eliminación de turnos asociados).
- **Entrenadores:** gestión CRUD completa por DNI, actividad asignada y franja horaria, con validaciones mediante `express-validator`.
- **Turnos / Sesiones:** reserva, consulta global y por usuario, modificación y cancelación de clases (con asignación automática de entrenador disponible según actividad y franja horaria desde el frontend).
- **Colección de pruebas:** colección de [Bruno](https://www.usebruno.com/) incluida en `backend/Bruno_trainers_gym/` con todos los endpoints listos para probar.

## Tecnologías

- **Backend:** Node.js, Express, Prisma ORM, `express-validator`, CORS, Nodemon
- **Base de datos:** PostgreSQL 17 (Docker / Docker Compose)
- **Frontend:** HTML5, CSS3, Bulma CSS, Font Awesome, JavaScript (Vanilla + Fetch API)
- **Testing manual de API:** Bruno

## Estructura del proyecto

```text
.
├── backend/
│   ├── Bruno_trainers_gym/   # Colección de requests para Bruno
│   ├── src/
│   │   └── app.js            # Servidor Express y endpoints REST
│   └── compose.yml           # Docker Compose para PostgreSQL 17
├── frontend/
│   ├── IMG/                  # Imágenes y capturas
│   ├── trainers/             # Vistas y scripts de entrenadores
│   ├── turns/                # Vistas y scripts de turnos
│   ├── users/                # Vistas y scripts de usuarios
│   ├── package.json          # Servidor estático opcional (serve)
│   └── pag.html              # Página principal
├── prisma/
│   ├── migrations/           # Migraciones SQL
│   └── schema.prisma         # Esquema de base de datos (Users, Trainer, Turn)
├── .example.env              # Plantilla de variables de entorno
└── package.json              # Dependencias y scripts del backend
```

## Requisitos previos

- [Node.js](https://nodejs.org/) (v18.18 o superior)
- [Docker](https://www.docker.com/) (con Docker Compose)

## Instalación y configuración

### 1. Clonar el repositorio

```bash
git clone <URL_DEL_REPOSITORIO>
cd <CARPETA_DEL_REPOSITORIO>
```

### 2. Instalar dependencias del backend

```bash
npm install
```

### 3. Levantar la base de datos y configurar `.env`

Primero creá el archivo `.env` en la raíz del proyecto a partir del ejemplo:

```bash
cp .example.env .env
```

Luego elegí **una** de las dos opciones para levantar PostgreSQL:

#### Opción A: Con Docker Compose (recomendada)

Usa el archivo `backend/compose.yml` incluido en el proyecto (persiste los datos en `backend/psql_data/`):

```bash
docker compose -f backend/compose.yml up -d
```

Configurá `.env` con las credenciales definidas en `compose.yml`:

```env
DATABASE_URL="postgresql://tp:123@localhost:5432/gym"
```

#### Opción B: Con `docker run` directo

```bash
docker run --name jvgg-db \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=jvgg \
  -p 5432:5432 \
  -d postgres:17
```

Configurá `.env` con:

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/jvgg"
```

### 4. Aplicar migraciones y generar Prisma Client

Esperá unos segundos a que PostgreSQL termine de inicializar y ejecutá:

```bash
npx prisma migrate deploy
npx prisma generate
```

## Cómo ejecutar el proyecto

### Backend

Desde la raíz del proyecto:

```bash
npm run dev
```

La API quedará disponible en `http://localhost:3000`.

### Frontend

**Opción A (servidor local recomendado):**

```bash
cd frontend
npm install
npm start
```

Luego abrí en tu navegador: `http://localhost:8000/pag.html`

**Opción B (archivo local):**

Abrí directamente `frontend/pag.html` en tu navegador.

> **Nota:** Si reiniciás la computadora y ya habías creado el contenedor de la base de datos, solo necesitás volver a iniciarlo antes de correr `npm run dev`:
> - Con Docker Compose: `docker compose -f backend/compose.yml start`
> - Con `docker run`: `docker start jvgg-db`

## Modelo de datos

Definido en `prisma/schema.prisma`:

- **`Users`**: `dni` (PK), `name`, `email`, `gender`, `age`, `cellphone`
- **`Trainer`**: `dni` (PK), `name`, `activity`, `timeSlot`, `age`, `gender`
- **`Turn`**: `id` (PK autoincremental), `userDni` (FK → `Users`), `trainerDni` (FK → `Trainer`), `activity`, `timeSlot`, `timesPerWeek`

## Endpoints de la API

Base URL: `http://localhost:3000`

### General

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `GET` | `/` | Comprobación básica del servidor (`Hello World!`) |

### Usuarios

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `POST` | `/register` | Registrar un nuevo usuario |
| `GET` | `/users` | Listar todos los usuarios |
| `GET` | `/user/:dni` | Obtener un usuario por DNI |
| `PUT` | `/user/:dni` | Actualizar datos de un usuario |
| `DELETE` | `/user/:dni` | Eliminar un usuario y sus turnos asociados |

### Entrenadores

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `GET` | `/api/v1/trainers` | Listar todos los entrenadores (`204` si está vacío) |
| `GET` | `/api/v1/trainer/:dni` | Obtener un entrenador por DNI (mín. 7 dígitos) |
| `POST` | `/api/v1/trainer` | Crear un nuevo entrenador |
| `PUT` | `/api/v1/trainer/:dni` | Actualizar un entrenador (elimina sus turnos previos) |
| `DELETE` | `/api/v1/trainer/:dni` | Eliminar un entrenador y sus turnos asociados |

### Turnos / Sesiones

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `POST` | `/api/v1/turn` | Crear un nuevo turno |
| `PUT` | `/api/v1/turns/:id` | Actualizar un turno existente |
| `GET` | `/turns` | Listar todos los turnos |
| `GET` | `/users/:dni/turns` | Listar los turnos de un usuario específico |
| `DELETE` | `/turns/:id` | Eliminar un turno por ID |

## Pruebas con Bruno

Para probar los endpoints sin usar el frontend:

1. Abrí [Bruno](https://www.usebruno.com/).
2. Seleccioná **Open Collection** y elegí la carpeta `backend/Bruno_trainers_gym/`.
3. Asegurate de tener el backend corriendo en `http://localhost:3000`.

## Capturas

### Menú de navegación
![Con nuestro menú podrás desplazarte mediante toda la página](frontend/IMG/menu.png)

### Carga de datos mediante formularios
![Podrás ingresar información mediante formularios](frontend/IMG/formulario.png)

### Visualización en tablas
![Donde se guardarán en forma de tabla](frontend/IMG/lista.png)

### Edición y eliminación de registros
![Teniendo la posibilidad de eliminar y actualizar información](frontend/IMG/botones.png)
