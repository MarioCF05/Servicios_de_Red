# Docker Activity#2 — Introducción a los contenedores

## Objetivo

Llevar a cabo los ejemplos prácticos del módulo de introducción a contenedores Docker y documentarlos en el repositorio.

## Recurso de referencia

- [Curso Docker IES — Módulo 3: Introducción a los contenedores Docker](https://github.com/josedom24/curso_docker_ies)

---

## Tareas a realizar

### 1. Instalación de Docker

*Ya lo hicimos en la Activity#1 para mas informacion)*

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
sudo systemctl status docker
```

<img width="1718" height="663" alt="image" src="https://github.com/user-attachments/assets/d26adb60-5441-494c-acfc-2f4f28dd1dca" />


---

### 2. El "Hola Mundo" de Docker

Ejecuta el contenedor clásico de prueba:

```bash
docker run hello-world
```

Este comando descarga la imagen `hello-world` (si no la tienes), crea un contenedor, ejecuta el mensaje de bienvenida y se detiene.

<img width="1718" height="547" alt="image" src="https://github.com/user-attachments/assets/3f69e671-9da8-4c81-a982-c64873964cad" />


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

<img width="818" height="837" alt="image" src="https://github.com/user-attachments/assets/9de95eee-1c89-4d95-b6f5-94eddafeda29" />


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

<img width="1010" height="220" alt="image" src="https://github.com/user-attachments/assets/a883093d-5590-4def-bdcb-d3c1940c24d0" />


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

<img width="1096" height="283" alt="image" src="https://github.com/user-attachments/assets/6b9dec73-f3e6-4897-8004-5e646676d3a4" />


---

### 6. Creando un contenedor con un servidor web

Ejecuta Nginx en un contenedor y expón el puerto 80 del contenedor en tu máquina:

```bash
docker run -d --name mynginx -p 8080:80 nginx
```

<img width="1206" height="189" alt="image" src="https://github.com/user-attachments/assets/007f559b-a25e-4ec3-907e-a418c00c7e47" />


Abre el navegador y visita `http://localhost:8080`. Deberías ver la página de bienvenida de Nginx.

<img width="1716" height="510" alt="image" src="https://github.com/user-attachments/assets/40f68b4e-5e51-4ea4-8a63-c1e06d65fd84" />


Para ver los logs del servidor:

```bash
docker logs mynginx
```

<img width="1715" height="656" alt="image" src="https://github.com/user-attachments/assets/1bed4f1d-2634-4df0-b8f3-bf7ec8ade675" />


Para detener y eliminar:

```bash
docker stop mynginx
docker rm mynginx
```

<img width="1718" height="196" alt="image" src="https://github.com/user-attachments/assets/50b9aa0e-f5f3-4044-9d02-770bb3c365ab" />


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

<img width="1716" height="185" alt="image" src="https://github.com/user-attachments/assets/90950d05-4f35-46d1-b786-ffac2beab9f3" />


También puedes probar con la imagen oficial de MySQL, que usa variables de entorno para configurarla:

```bash
docker run -d --name mydb -e MYSQL_ROOT_PASSWORD=1234 mysql:5.7
```

Verifica que el contenedor está corriendo:

```bash
docker ps
```

<img width="1718" height="514" alt="image" src="https://github.com/user-attachments/assets/42452ca5-8598-46d5-8d80-fda4bc071dc3" />


---

## Limpieza final

Para borrar todos los contenedores creados en esta práctica:

```bash
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
```

<img width="1717" height="414" alt="image" src="https://github.com/user-attachments/assets/b0a124f5-5a24-4607-84de-e032502d8d89" />



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
