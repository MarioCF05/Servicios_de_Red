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

<img width="1718" height="126" alt="image" src="https://github.com/user-attachments/assets/8270d8cf-6bbe-46ab-b981-262f8f78e90f" />


### Ejecutar el contenedor de Redis con volumen persistente

Redis guarda los datos en `/data`. Activamos `--appendonly yes` para que persista la información.

```bash
docker run -d --name redis \
  --network red_guestbook \
  -v /opt/redis:/data \
  redis redis-server --appendonly yes
```

<img width="1714" height="403" alt="image" src="https://github.com/user-attachments/assets/ade3df2b-1024-4177-9b59-6249877d9570" />


### Ejecutar el contenedor de Guestbook

La aplicación espera conectarse a un host llamado `redis` (por defecto). Mapeamos el puerto 80 del host al 5000 del contenedor.

```bash
docker run -d -p 80:5000 \
  --name guestbook \
  --network red_guestbook \
  iesgn/guestbook
```

<img width="1719" height="471" alt="image" src="https://github.com/user-attachments/assets/056fad59-857d-4ad2-b17b-8a90884dd38d" />


### Acceder a la aplicación

Abre el navegador y visita `http://localhost`. Deberías ver la aplicación Guestbook. Prueba a añadir algún mensaje.

<img width="1717" height="667" alt="image" src="https://github.com/user-attachments/assets/cb847381-226c-44f5-bb7c-948618999c39" />


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

<img width="1717" height="988" alt="image" src="https://github.com/user-attachments/assets/2a4cb1a8-d60b-4be2-b732-4ab482155739" />


---

## Ejemplo 2: Despliegue de la aplicación Temperaturas

Aplicación sin estado (*stateless*) para consultar temperaturas mínimas y máximas de municipios de España. Consta de un `frontend` (Python, puerto 3000) y un `backend` (API REST, puerto 5000).

### Crear la red

```bash
docker network create red_temperaturas
```

<img width="1720" height="117" alt="image" src="https://github.com/user-attachments/assets/15ba9323-595a-47e7-8b1e-a634ae0846f9" />


### Ejecutar el contenedor backend

Debe llamarse `temperaturas-backend` porque el frontend lo busca por ese nombre.

```bash
docker run -d --name temperaturas-backend \
  --network red_temperaturas \
  iesgn/temperaturas_backend
```

<img width="1715" height="307" alt="image" src="https://github.com/user-attachments/assets/4bc10c1e-90d8-41bb-b136-112cb2a24676" />


### Ejecutar el contenedor frontend

```bash
docker run -d -p 80:3000 \
  --name temperaturas-frontend \
  --network red_temperaturas \
  iesgn/temperaturas_frontend
```

<img width="1717" height="318" alt="image" src="https://github.com/user-attachments/assets/fcfc2ddd-6af7-45bc-b525-7d4f8c6e6d39" />


### Acceder a la aplicación

Abre el navegador y visita `http://localhost`. Deberías ver el buscador de municipios.

<img width="1718" height="712" alt="image" src="https://github.com/user-attachments/assets/bb54b0bb-83de-4fb4-afb7-431ab39fdeaa" />


> **Nota:** Esta aplicación no necesita volúmenes porque es **sin estado** — no guarda información. Toda la configuración se pasa por variables de entorno o nombres DNS.

---

## Ejemplo 3: Despliegue de Wordpress + MariaDB

WordPress con base de datos MariaDB, usando volúmenes para persistir tanto los archivos como la base de datos.

### Crear la red

```bash
docker network create red_wp
```

<img width="1716" height="133" alt="image" src="https://github.com/user-attachments/assets/f29de7e1-8f85-4009-ab18-401445133c06" />


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

<img width="1716" height="516" alt="image" src="https://github.com/user-attachments/assets/85f56d3d-315c-424f-818d-fd8bb7e1b1cc" />


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

<img width="1713" height="828" alt="image" src="https://github.com/user-attachments/assets/ff638e93-1931-4dc0-9e2c-1c554c403c14" />


### Verificar los contenedores en ejecución

```bash
docker ps
```

<img width="1714" height="152" alt="image" src="https://github.com/user-attachments/assets/a0db565d-cd9a-418c-bae6-fec1bfd0b249" />


### Acceder a WordPress

Abre el navegador y visita `http://localhost`. Deberías ver el instalador de WordPress. Como ya pasamos las variables de entorno, la configuración de la base de datos ya está hecha automáticamente.

<img width="1716" height="872" alt="image" src="https://github.com/user-attachments/assets/d50668e3-b29e-4f08-84b0-e249d3c048d9" />


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
