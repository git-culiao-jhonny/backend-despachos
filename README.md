# Backend Despachos

API REST Spring Boot para administrar despachos de productos.

## Que hace

Este servicio permite:

- crear un despacho
- listar despachos
- consultar un despacho por ID
- actualizar un despacho
- eliminar un despacho

Se conecta con el frontend para cerrar el flujo compra -> despacho -> cierre del despacho.

## Estructura principal

- `src/main/java/com/citt/controller/DespachoController.java`: endpoints REST
- `src/main/java/com/citt/persistence/entity/Despacho.java`: entidad JPA
- `src/main/java/com/citt/persistence/repository/DespachoRepository.java`: acceso a datos
- `src/main/java/com/citt/persistence/services/DespachoServiceImpl.java`: logica de negocio
- `src/main/resources/application.properties`: configuracion de Spring
- `Dockerfile`: imagen multi-stage
- `docker-compose.yml`: stack local del servicio

## Modelo de datos

La entidad `Despacho` contiene:

- `idDespacho`
- `fechaDespacho`
- `patenteCamion`
- `intento`
- `idCompra`
- `direccionCompra`
- `valorCompra`
- `despachado`

## Endpoints

Base path:

```text
/api/v1/despachos
```

Endpoints:

- `POST /api/v1/despachos`
- `GET /api/v1/despachos`
- `GET /api/v1/despachos/{idDespacho}`
- `PUT /api/v1/despachos/{idDespacho}`
- `DELETE /api/v1/despachos/{idDespacho}`

## Requisitos

- Java 17
- Maven Wrapper incluido en el repositorio
- Docker y Docker Compose, si se ejecuta con contenedores

## Ejecucion local sin Docker

```powershell
./mvnw spring-boot:run
```

En Windows PowerShell:

```powershell
.\mvnw.cmd spring-boot:run
```

## Base de datos

El servicio usa MySQL mediante variables de entorno:

- `DB_ENDPOINT`
- `DB_PORT`
- `DB_NAME`
- `DB_USERNAME`
- `DB_PASSWORD`

En Docker Compose, la base de datos se llama `db-despachos`.

## Ejecucion con Docker

Construccion y prueba local:

```powershell
docker compose up --build
```

Servicios:

- API despachos: `http://localhost:8081`
- Swagger: `http://localhost:8081/swagger-ui.html`
- MySQL despachos: `localhost:3308`

## Logica de negocio

La actualizacion de un despacho conserva los campos que no llegan en la solicitud, para evitar borrar informacion ya guardada.

Eso se implementa en `DespachoServiceImpl`.

## Dockerfile

El `Dockerfile` usa multi-stage:

1. Build stage con Maven para generar el JAR.
2. Runtime stage con una imagen JRE liviana.
3. Usuario no root para ejecutar la app.

## Despliegue

En CI/CD, este repo usa GitHub Actions para:

1. construir la imagen
2. publicarla en Docker Hub
3. conectarse por SSH a la EC2 del backend
4. ejecutar `docker compose pull` y `docker compose up -d`

Los secrets esperados estan documentados en [deploy/README.md](../../deploy/README.md).
