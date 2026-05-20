# Docker Activity#5 — Docker Compose

## Objetivo

Aprender a desplegar escenarios multicontenedor con Docker Compose usando un fichero `docker-compose.yaml`.

## Recurso de referencia

- [Curso Docker IES — Módulo 4: Docker Compose](https://github.com/josedom24/curso_docker_ies)
- [Ejemplos del módulo 4](https://github.com/josedom24/curso_docker_ies/tree/main/ejemplos/modulo4)

---

## Ejemplo 1: Despliegue de Guestbook con Docker Compose

### Crear el fichero `docker-compose.yaml`

Crea un directorio para el ejemplo y dentro un fichero `docker-compose.yaml`:

```yaml
version: '3.1'
services:
  app:
    container_name: guestbook
    image: iesgn/guestbook
    restart: always
    environment:
      REDIS_SERVER: redis
    ports:
      - 8080:5000
  db:
    container_name: redis
    image: redis
    restart: always
    command: redis-server --appendonly yes
    volumes:
      - redis:/data
volumes:
  redis:
```

> *Captura de pantalla aquí (contenido del fichero)*

### Levantar el escenario

```bash
docker compose up -d
```

> *Captura de pantalla aquí*

### Listar los contenedores

```bash
docker compose ps
```

> *Captura de pantalla aquí*

### Acceder a la aplicación

Abre el navegador en `http://localhost:8080`. Deberías ver la aplicación Guestbook.

> *Captura de pantalla aquí (navegador)*

### Parar y eliminar el escenario

```bash
docker compose stop
docker compose down
```

Para eliminar también el volumen:
```bash
docker compose down -v
```

> *Captura de pantalla aquí*

---

## Ejemplo 2: Despliegue de Temperaturas con Docker Compose

### Crear el fichero `docker-compose.yaml`

```yaml
version: '3.1'
services:
  frontend:
    container_name: temperaturas-frontend
    image: iesgn/temperaturas_frontend
    restart: always
    ports:
      - 8081:3000
    environment:
      TEMP_SERVER: temperaturas-backend:5000
    depends_on:
      - backend
  backend:
    container_name: temperaturas-backend
    image: iesgn/temperaturas_backend
    restart: always
```

> *Captura de pantalla aquí (contenido del fichero)*

### Levantar el escenario

```bash
docker compose up -d
```

> *Captura de pantalla aquí*

### Listar los contenedores

```bash
docker compose ps
```

> *Captura de pantalla aquí*

### Acceder a la aplicación

Abre el navegador en `http://localhost:8081`. Deberías ver el buscador de municipios.

> *Captura de pantalla aquí (navegador)*

### Eliminar el escenario

```bash
docker compose down
```

> *Captura de pantalla aquí*

> **Nota:** Esta aplicación no necesita volúmenes porque es **stateless**. Observa también el uso de `depends_on` para indicar que el frontend depende del backend.

---

## Ejemplo 3: Despliegue de WordPress + MariaDB con Docker Compose

### Opción A: Con volúmenes Docker

```yaml
version: '3.1'
services:
  wordpress:
    container_name: servidor_wp
    image: wordpress
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: user_wp
      WORDPRESS_DB_PASSWORD: asdasd
      WORDPRESS_DB_NAME: bd_wp
    ports:
      - 80:80
    volumes:
      - wordpress_data:/var/www/html/wp-content
  db:
    container_name: servidor_mysql
    image: mariadb
    restart: always
    environment:
      MYSQL_DATABASE: bd_wp
      MYSQL_USER: user_wp
      MYSQL_PASSWORD: asdasd
      MYSQL_ROOT_PASSWORD: asdasd
    volumes:
      - mariadb_data:/var/lib/mysql
volumes:
  wordpress_data:
  mariadb_data:
```

> *Captura de pantalla aquí (contenido del fichero)*

### Opción B: Con bind mount

```yaml
version: '3.1'
services:
  wordpress:
    container_name: servidor_wp
    image: wordpress
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: user_wp
      WORDPRESS_DB_PASSWORD: asdasd
      WORDPRESS_DB_NAME: bd_wp
    ports:
      - 80:80
    volumes:
      - ./wordpress:/var/www/html/wp-content
  db:
    container_name: servidor_mysql
    image: mariadb
    restart: always
    environment:
      MYSQL_DATABASE: bd_wp
      MYSQL_USER: user_wp
      MYSQL_PASSWORD: asdasd
      MYSQL_ROOT_PASSWORD: asdasd
    volumes:
      - ./mysql:/var/lib/mysql
```

> *Captura de pantalla aquí (contenido del fichero)*

### Levantar el escenario

```bash
docker compose up -d
```

> *Captura de pantalla aquí*

### Listar los contenedores

```bash
docker compose ps
```

> *Captura de pantalla aquí*

### Acceder a WordPress

Abre el navegador en `http://localhost`. Deberías ver la instalación de WordPress (ya configurada con la BD gracias a las variables de entorno).

> *Captura de pantalla aquí (navegador)*

### Verificar la base de datos

```bash
docker exec servidor_mysql mysql -u root -p -e "SHOW DATABASES;"
```
(Contraseña: `asdasd`)

> *Captura de pantalla aquí*

### Eliminar el escenario (incluyendo volúmenes)

```bash
docker compose down -v
```

> *Captura de pantalla aquí*

---

## Resumen de comandos de Docker Compose

| Comando | Descripción |
|---|---|
| `docker compose up -d` | Crear y arrancar todos los servicios en segundo plano |
| `docker compose ps` | Listar los contenedores del escenario |
| `docker compose logs` | Ver los logs de todos los servicios |
| `docker compose stop` | Parar los contenedores sin eliminarlos |
| `docker compose start` | Reanudar los contenedores detenidos |
| `docker compose down` | Eliminar contenedores y red |
| `docker compose down -v` | Eliminar también los volúmenes |
