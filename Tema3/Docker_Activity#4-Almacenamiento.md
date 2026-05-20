# Docker Activity#4 — Almacenamiento y redes

## Objetivo

Desplegar aplicaciones multicontenedor usando volúmenes, redes y variables de entorno.

## Recurso de referencia

- [Curso Docker IES — Módulo 3: Almacenamiento y redes Docker](https://github.com/josedom24/curso_docker_ies)

---

## Ejemplo 1: Despliegue de la aplicación Guestbook

Aplicación web que guarda mensajes en una base de datos Redis. Consta de dos servicios: `guestbook` (Python, puerto 5000) y `redis` (base de datos no relacional, puerto 6379).

### Crear la red

```bash
docker network create red_guestbook
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor de Redis con volumen persistente

Redis guarda los datos en `/data`. Activamos `--appendonly yes` para que persista la información.

```bash
docker run -d --name redis \
  --network red_guestbook \
  -v /opt/redis:/data \
  redis redis-server --appendonly yes
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor de Guestbook

La aplicación espera conectarse a un host llamado `redis` (por defecto). Mapeamos el puerto 80 del host al 5000 del contenedor.

```bash
docker run -d -p 80:5000 \
  --name guestbook \
  --network red_guestbook \
  iesgn/guestbook
```

> *Captura de pantalla aquí*

### Acceder a la aplicación

Abre el navegador y visita `http://localhost`. Deberías ver la aplicación Guestbook. Prueba a añadir algún mensaje.

> *Captura de pantalla aquí (navegador mostrando la aplicación)*

### Verificar la persistencia

Para comprobar que los datos persisten aunque eliminemos el contenedor de Redis:

```bash
docker stop redis
docker rm redis
docker run -d --name redis \
  --network red_guestbook \
  -v /opt/redis:/data \
  redis redis-server --appendonly yes
```

> *Captura de pantalla aquí*

---

## Ejemplo 2: Despliegue de la aplicación Temperaturas

Aplicación sin estado (*stateless*) para consultar temperaturas mínimas y máximas de municipios de España. Consta de un `frontend` (Python, puerto 3000) y un `backend` (API REST, puerto 5000).

### Crear la red

```bash
docker network create red_temperaturas
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor backend

Debe llamarse `temperaturas-backend` porque el frontend lo busca por ese nombre.

```bash
docker run -d --name temperaturas-backend \
  --network red_temperaturas \
  iesgn/temperaturas_backend
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor frontend

```bash
docker run -d -p 80:3000 \
  --name temperaturas-frontend \
  --network red_temperaturas \
  iesgn/temperaturas_frontend
```

> *Captura de pantalla aquí*

### Acceder a la aplicación

Abre el navegador y visita `http://localhost`. Deberías ver el buscador de municipios.

> *Captura de pantalla aquí (navegador mostrando la aplicación)*

> **Nota:** Esta aplicación no necesita volúmenes porque es **sin estado** — no guarda información. Toda la configuración se pasa por variables de entorno o nombres DNS.

### (Opcional) Configurar con nombre personalizado

Si el backend se llama distinto, se pasa la variable `TEMP_SERVER`:

```bash
docker run -d --name temperaturas-api \
  --network red_temperaturas \
  iesgn/temperaturas_backend

docker run -d -p 80:3000 \
  --name temperaturas-frontend \
  -e TEMP_SERVER=temperaturas-api:5000 \
  --network red_temperaturas \
  iesgn/temperaturas_frontend
```

> *Captura de pantalla aquí*

---

## Ejemplo 3: Despliegue de Wordpress + MariaDB

WordPress con base de datos MariaDB, usando volúmenes para persistir tanto los archivos como la base de datos.

### Crear la red

```bash
docker network create red_wp
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor de MariaDB

```bash
docker run -d --name servidor_mysql \
  --network red_wp \
  -v /opt/mysql_wp:/var/lib/mysql \
  -e MYSQL_DATABASE=bd_wp \
  -e MYSQL_USER=user_wp \
  -e MYSQL_PASSWORD=asdasd \
  -e MYSQL_ROOT_PASSWORD=asdasd \
  mariadb
```

> *Captura de pantalla aquí*

### Ejecutar el contenedor de WordPress

```bash
docker run -d --name servidor_wp \
  --network red_wp \
  -v /opt/wordpress:/var/www/html/wp-content \
  -e WORDPRESS_DB_HOST=servidor_mysql \
  -e WORDPRESS_DB_USER=user_wp \
  -e WORDPRESS_DB_PASSWORD=asdasd \
  -e WORDPRESS_DB_NAME=bd_wp \
  -p 80:80 \
  wordpress
```

> *Captura de pantalla aquí*

### Verificar los contenedores en ejecución

```bash
docker ps
```

> *Captura de pantalla aquí*

### Acceder a WordPress

Abre el navegador y visita `http://localhost`. Deberías ver el instalador de WordPress. Como ya pasamos las variables de entorno, la configuración de la base de datos ya está hecha automáticamente.

> *Captura de pantalla aquí (navegador mostrando WordPress)*

### Probar la persistencia

Para comprobar que los datos sobreviven a la eliminación de contenedores:

```bash
docker stop servidor_mysql servidor_wp
docker rm servidor_mysql servidor_wp

# Vuelve a crear los contenedores con los mismos volúmenes
docker run -d --name servidor_mysql \
  --network red_wp \
  -v /opt/mysql_wp:/var/lib/mysql \
  -e MYSQL_DATABASE=bd_wp \
  -e MYSQL_USER=user_wp \
  -e MYSQL_PASSWORD=asdasd \
  -e MYSQL_ROOT_PASSWORD=asdasd \
  mariadb

docker run -d --name servidor_wp \
  --network red_wp \
  -v /opt/wordpress:/var/www/html/wp-content \
  -e WORDPRESS_DB_HOST=servidor_mysql \
  -e WORDPRESS_DB_USER=user_wp \
  -e WORDPRESS_DB_PASSWORD=asdasd \
  -e WORDPRESS_DB_NAME=bd_wp \
  -p 80:80 \
  wordpress
```

Los datos deberían seguir ahí porque los volúmenes `/opt/mysql_wp` y `/opt/wordpress` están en el host.

> *Captura de pantalla aquí*

---

## Limpieza final

Para detener y eliminar todos los contenedores y redes creados:

```bash
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
docker network rm red_guestbook red_temperaturas red_wp
```

> *Captura de pantalla aquí*

---

## Resumen de conceptos aplicados

| Concepto | Descripción |
|---|---|
| **Volumen (bind mount)** | Directorio del host montado dentro del contenedor (`-v /host:/container`) |
| **Red definida por el usuario** | Red propia que permite resolución DNS por nombre de contenedor |
| **Variables de entorno** | Configuración pasada al contenedor con `-e VAR=valor` |
| **Aplicación con estado** | Necesita almacenamiento persistente (Guestbook, WordPress) |
| **Aplicación sin estado** | No guarda datos, todo es efímero (Temperaturas) |
