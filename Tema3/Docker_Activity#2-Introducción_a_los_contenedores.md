# Docker Activity#2 — Introducción a los contenedores

## Objetivo

Llevar a cabo los ejemplos prácticos del módulo de introducción a contenedores Docker y documentarlos en el repositorio.

## Recurso de referencia

- [Curso Docker IES — Módulo 3: Introducción a los contenedores Docker](https://github.com/josedom24/curso_docker_ies)

---

## Tareas a realizar

### 1. Instalación de Docker

*(Si no lo hiciste ya en la Activity#1)*

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
sudo systemctl status docker
```

> *Captura de pantalla aquí*

---

### 2. El "Hola Mundo" de Docker

Ejecuta el contenedor clásico de prueba:

```bash
docker run hello-world
```

Este comando descarga la imagen `hello-world` (si no la tienes), crea un contenedor, ejecuta el mensaje de bienvenida y se detiene.

> *Captura de pantalla aquí*

---

### 3. Ejecución simple de contenedores

Ejecuta un contenedor con Ubuntu que ejecute un comando y termine:

```bash
docker run ubuntu cat /etc/os-release
```

```bash
docker run ubuntu echo "Hola desde Docker"
```

```bash
docker run ubuntu ls -la
```

> *Captura de pantalla aquí*

---

### 4. Ejecutando un contenedor interactivo

Abre una terminal interactiva dentro de un contenedor Ubuntu:

```bash
docker run -it ubuntu bash
```

Dentro del contenedor puedes ejecutar comandos como si fuese una máquina Ubuntu mínima. Para salir:

```bash
exit
```

> *Captura de pantalla aquí (dentro y fuera del contenedor)*

---

### 5. Creando un contenedor demonio

Ejecuta un contenedor en segundo plano (modo detach) que se mantenga corriendo:

```bash
docker run -d --name mydaemon ubuntu sleep infinity
```

Verifica que está en ejecución:

```bash
docker ps
```

Para el contenedor demonio:

```bash
docker stop mydaemon
```

> *Captura de pantalla aquí*

---

### 6. Creando un contenedor con un servidor web

Ejecuta Nginx en un contenedor y expón el puerto 80 del contenedor en tu máquina:

```bash
docker run -d --name mynginx -p 8080:80 nginx
```

Abre el navegador y visita `http://localhost:8080`. Deberías ver la página de bienvenida de Nginx.

Para ver los logs del servidor:

```bash
docker logs mynginx
```

Para detener y eliminar:

```bash
docker stop mynginx
docker rm mynginx
```

> *Captura de pantalla aquí (navegador mostrando localhost:8080 y terminal)*

---

### 7. Configuración de contenedores con variables de entorno

Ejecuta un contenedor pasándole variables de entorno:

```bash
docker run -it --name myenv -e NOMBRE=Mario ubuntu bash
```

Dentro del contenedor, comprueba que la variable existe:

```bash
echo $NOMBRE
```

También puedes probar con la imagen oficial de MySQL, que usa variables de entorno para configurarla:

```bash
docker run -d --name mydb -e MYSQL_ROOT_PASSWORD=1234 mysql:5.7
```

Verifica que el contenedor está corriendo:

```bash
docker ps
```

> *Captura de pantalla aquí*

---

## Limpieza final

Para borrar todos los contenedores creados en esta práctica:

```bash
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
```

> *Captura de pantalla aquí*

---

## Resumen de comandos nuevos

| Comando | Descripción |
|---|---|
| `docker run -it <imagen> bash` | Ejecutar contenedor interactivo con terminal |
| `docker run -d <imagen>` | Ejecutar contenedor en segundo plano (detach) |
| `docker run -p <host>:<container> <imagen>` | Mapear puerto del host al contenedor |
| `docker run -e VAR=valor <imagen>` | Pasar variable de entorno al contenedor |
| `docker logs <contenedor>` | Ver los logs de un contenedor |
| `docker stop <contenedor>` | Detener un contenedor en ejecución |
| `docker rm <contenedor>` | Eliminar un contenedor detenido |
