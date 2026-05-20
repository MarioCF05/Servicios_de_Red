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
<img width="1719" height="172" alt="image" src="https://github.com/user-attachments/assets/1aa36b0d-25d9-45bb-a525-c25666c66a34" />


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

<img width="1716" height="713" alt="image" src="https://github.com/user-attachments/assets/22d7fcb5-1173-4882-b2bb-4a1f3bea37eb" />


### Levantar el escenario

```bash
docker compose up -d
```

<img width="1715" height="274" alt="image" src="https://github.com/user-attachments/assets/39b6390c-167b-4ff6-b99f-b053735638e9" />


### Listar los contenedores

```bash
docker compose ps
```

<img width="1717" height="194" alt="image" src="https://github.com/user-attachments/assets/d0c69c14-92f0-4d6a-9e87-fa40dd368c87" />


### Acceder a la aplicación

Abre el navegador en `http://localhost:8080`. Deberías ver la aplicación Guestbook.

<img width="1719" height="642" alt="image" src="https://github.com/user-attachments/assets/10459faa-803c-4e97-bbce-b8b540706609" />


### Parar y eliminar el escenario

```bash
docker compose stop
docker compose down
```

Para eliminar también el volumen:
```bash
docker compose down -v
```

<img width="1716" height="580" alt="image" src="https://github.com/user-attachments/assets/d7c0226e-c205-4627-a918-7e3a93f4ce87" />


---

## Ejemplo 2: Despliegue de Temperaturas con Docker Compose

### Crear el fichero `docker-compose.yaml`

<img width="1716" height="141" alt="image" src="https://github.com/user-attachments/assets/b57e65d5-0e07-45ca-a42b-19305f2e466c" />


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

<img width="1716" height="706" alt="image" src="https://github.com/user-attachments/assets/c9a58957-4027-493d-a995-2f4b3fbf9b40" />


### Levantar el escenario

```bash
docker compose up -d
```

<img width="1716" height="220" alt="image" src="https://github.com/user-attachments/assets/68abee49-7af8-4ef1-ae6c-441652ccd38c" />


### Listar los contenedores

```bash
docker compose ps
```

<img width="1718" height="189" alt="image" src="https://github.com/user-attachments/assets/e7670a5d-73b8-4ceb-9e47-182bc97a5802" />


### Acceder a la aplicación

Abre el navegador en `http://localhost:8081`. Deberías ver el buscador de municipios.

<img width="1717" height="663" alt="image" src="https://github.com/user-attachments/assets/48315991-f881-41bb-838e-34b903b64706" />


### Eliminar el escenario

```bash
docker compose down
```

<img width="1717" height="194" alt="image" src="https://github.com/user-attachments/assets/f5b1d5df-3d28-4f1b-bb67-585ebbb02247" />


---

## Ejemplo 3: Despliegue de WordPress + MariaDB con Docker Compose
<img width="1716" height="159" alt="image" src="https://github.com/user-attachments/assets/87e90df7-67d5-412a-a020-b765368780c1" />


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

<img width="1718" height="715" alt="image" src="https://github.com/user-attachments/assets/55287c58-1f0f-4d14-a9e8-6edec4234e47" />


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

<img width="1718" height="717" alt="image" src="https://github.com/user-attachments/assets/0007d8e8-4e6d-41a1-9656-656c28242bda" />


### Levantar el escenario

```bash
docker compose up -d
```

<img width="1719" height="250" alt="image" src="https://github.com/user-attachments/assets/794609c7-3ece-4423-8890-34299a6d5e92" />


### Listar los contenedores

```bash
docker compose ps
```

<img width="1718" height="194" alt="image" src="https://github.com/user-attachments/assets/4e3f6994-92e5-422a-aeff-1a2f6cea94dc" />


### Acceder a WordPress

Abre el navegador en `http://localhost`. Deberías ver la instalación de WordPress (ya configurada con la BD gracias a las variables de entorno).

<img width="1715" height="794" alt="image" src="https://github.com/user-attachments/assets/5ed4c11f-e426-4f99-86ee-d8f00f0031b1" />


### Verificar la base de datos

```bash
docker exec -it servidor_mysql mariadb -u root -p -e "SHOW DATABASES;"
```
(Contraseña: `asdasd`)

<img width="1719" height="288" alt="image" src="https://github.com/user-attachments/assets/dd3e34c5-9dec-4072-a587-48c80599acd9" />


### Eliminar el escenario (incluyendo volúmenes)

```bash
docker compose down -v
```

<img width="1716" height="226" alt="image" src="https://github.com/user-attachments/assets/23ab0296-5fee-468d-bba5-97c5caa322b4" />


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
