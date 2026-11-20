# JVGG - Gym Management API

El objetivo de esta API es mejorar la organización de los propietarios de gimnasios, logrando un mejor seguimiento de sus entrenadores, atletas y clases que se lleven a cabo.

## Requisitos

- [Docker](https://www.docker.com/)
- [Node.js](https://nodejs.org/) (v18 o superior)

## Configuración inicial

### 1. Clonar el repositorio

```bash
git clone https://github.com/jvisca/jvgg.git
cd jvgg
```

### 2. Instalar dependencias

```bash
npm install
```

### 3. Configurar variables de entorno

Crear un archivo `.env` en la raíz del proyecto a partir del ejemplo:

```bash
cp .example.env .env
```

Editar el `.env` con los datos de tu base de datos:

```
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/jvgg"
```

### 4. Levantar la base de datos con Docker

```bash
docker run --name jvgg-db -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=jvgg -p 5432:5432 -d postgres
```

### 5. Ejecutar las migraciones de Prisma

```bash
npx prisma migrate deploy
npx prisma generate
```

## Cómo correr el proyecto

### Backend

```bash
npm run dev
```

El servidor se levanta en `http://localhost:3000`.

### Frontend

Abrir el archivo `frontend/pag.html` en el navegador.

> **Nota:** Si ya levantaste la base de datos anteriormente y reiniciaste la PC, solo necesitás correr `docker start jvgg-db` antes de `npm run dev`.

## Capturas

![Con nuestro menú podrás desplazarte mediante toda la página](frontend/IMG/menu.png)

*Con nuestro menú podrás desplazarte mediante toda la página*

![Podrás ingresar información mediante formularios](frontend/IMG/formulario.png)

*Podrás ingresar información mediante formularios*

![Donde se guardarán en forma de tabla](frontend/IMG/lista.png)

*Donde se guardarán en forma de tabla*

![Teniendo la posibilidad de eliminar y actualizar información](frontend/IMG/botones.png)

*Teniendo la posibilidad de eliminar y actualizar información*
