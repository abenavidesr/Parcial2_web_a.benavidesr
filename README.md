# Proyecto Base — Parcial Práctico Backend (NestJS + MongoDB)

API REST en NestJS y Mongoose para la gestión de **Usuarios (`/users`)** y **Tareas (`/tasks`)**.

---

## Requisitos Previos

- **Node.js** 18+ y **npm**
- **Docker Desktop** (o instancia local de MongoDB en el puerto `27017`)

---

## Instalación y Puesta en Marcha

1. **Levantar la base de datos MongoDB:**
   ```bash
   docker compose up -d
   ```
   *(Si utilizas una instancia local o MongoDB Atlas diferente a `mongodb://localhost:27017/nest-parcial`, configura la variable `MONGODB_URI` en un archivo `.env` tomando como base `.env.example`.)*

2. **Instalar dependencias del proyecto:**
   ```bash
   npm install
   ```

3. **Iniciar la aplicación en modo desarrollo:**
   ```bash
   npm run start:dev
   ```
   El servidor quedará disponible en `http://localhost:3000`.

---

## Pruebas y Validación

- **Ejecutar pruebas unitarias:**
  ```bash
  npm test
  ```
- **Ejecutar pruebas end-to-end:**
  ```bash
  npm run test:e2e
  ```
- **Verificar compilación y linter:**
  ```bash
  npm run build
  npm run lint
  ```

---

## Endpoints del Sistema

| Método | Ruta | Acceso | Descripción |
| :--- | :--- | :--- | :--- |
| `GET` | `/` | Público | Verificación básica del servidor |
| `POST` | `/users` | Público | Crear usuario (`name`, `email`) |
| `GET` | `/users` | Público | Listar usuarios registrados |
| `GET` | `/users/:id` | Público | Consultar usuario por ID |
| `GET` | `/tasks` | Público | Listar tareas registradas |
| `GET` | `/tasks/:id` | Público | Consultar tarea por ID |
| `POST` | `/tasks` | Restringido (`Authorization: admin-secret-token`) | Crear una tarea asociada a un usuario |

También puedes importar `nest-parcial.postman_collection.json` en Postman o utilizar `requests.http` con la extensión REST Client de VS Code para probar manualmente los endpoints.


## Evidencia de funcionamiento

Para el caso 1, la solución fue lo que planteé en el parcial teorico de crear un DTO con ValidationPipe para validar los datos y pues que no se acepten, aunque en el teorico tambien hable sobre la single responsability del Controller, el cual si es importante pero no necesariamente con este. Hice validaciones para title, description, completed y userID. Configuré el ValidationPipe en main.ts utilizando whitelist para solo aceptar los campos autorizados y forbidNonWhiteListed para rechazar las peticiones adicionales que no estan permitidas. Puse en Schema strict: true para que no se almacenen atributos por fuera del modelo.

Para el caso 2, la solucion fue la que propuse en el parcial teorico de importar UsersModule en TasksModule y exportar el provider desde UsersModule, eliminando el acceso directo al model de users. Esto va de la mano con lo que puse de la inversión de dependencias ya que tasks dependia directamente de la implementación de users, lo cual no debería ser así.

Para el caso 3, es en la que más note cambio con lo que dije en el parcial teorico ya que identifique que el problema era en el middleware pero mi solución no fue la de poner el guard, sino que dije que debia solucionarse en el servicio de tasks al ser logica de negocio. La solución que hice fue eliminar el middleware de la implementación, aunque deje el archivo ahí aunque no se use. Cree AuthGuard para validar la cabecera de autorización y mandar el Forbidden Exception cuando no sea valida. Luego aplique el guard unicamente en Post /tasks, dejando GET /tasks y GET /tasks/:id como rutas de consulta publicas. 

Para el caso 4, la solución si fue lo que dije en el parcial teorico de single responsability del controller, ya que este conectaba directo con el model, por lo que lo cambie para que ahora se conecte con service, eliminando las inyecciones de task model y user model. Cambie la logica de creación y persistencia en TasksService quitandole esa responsabilidad al controller y puse que verificara que si exista el usuario. 

Probé que los cambios funcionaran con npm run build, corregi un import en AppModule para que me corrieran las pruebas lint y tambien corrieron las pruebas end to end con los comandos que decian el readme. Importe la colección a postman y funciono bien. 