# Docker Activity#3 — Imágenes y contenedores

## Objetivo

Aprender a descargar imágenes Docker, ejecutar contenedores con nombre propio, detenerlos y eliminarlos.

## Recursos de referencia

- [Pull docker images & run docker containers (ServerMom)](http://www.servermom.org/pull-docker-images-run-docker-containers/3225/)
- [Borrar imágenes y contenedores Docker (Tecmint)](https://www.tecmint.com/remove-docker-images-containers-and-volumes/)
- [Dar nombre a contenedores docker (Tecmint)](https://www.tecmint.com/name-docker-containers/)
- Ejercicio recomendado: [Play With Docker — ops-s1-hello](https://training.play-with-docker.com/ops-s1-hello/)

---

## Tareas a realizar

### 1. Descarga la imagen de ubuntu

```bash
docker pull ubuntu
```

> *Captura de pantalla aquí*

---

### 2. Descarga la imagen de hello-world

```bash
docker pull hello-world
```

> *Captura de pantalla aquí*

---

### 3. Descarga la imagen nginx

```bash
docker pull nginx
```

> *Captura de pantalla aquí*

---

### 4. Muestra un listado de todas las imágenes

```bash
docker images
```

> *Captura de pantalla aquí*

---

### 5. Ejecuta un contenedor hello-world y dale nombre "myhello1"

```bash
docker run --name myhello1 hello-world
```

> *Captura de pantalla aquí*

---

### 6. Ejecuta un contenedor hello-world y dale nombre "myhello2"

```bash
docker run --name myhello2 hello-world
```

> *Captura de pantalla aquí*

---

### 7. Ejecuta un contenedor hello-world y dale nombre "myhello3"

```bash
docker run --name myhello3 hello-world
```

> *Captura de pantalla aquí*

---

### 8. Muestra los contenedores que se están ejecutando

```bash
docker ps
```

> *Captura de pantalla aquí*

> **Nota:** Los contenedores `hello-world` se ejecutan y se detienen automáticamente al finalizar. Es normal que `docker ps` (sin `-a`) no muestre ninguno. Si quieres ver todos (incluidos los detenidos), usa `docker ps -a`.

---

### 9. Para el contenedor "myhello1"

```bash
docker stop myhello1
```

> *Captura de pantalla aquí*

---

### 10. Para el contenedor "myhello2"

```bash
docker stop myhello2
```

> *Captura de pantalla aquí*

---

### 11. Borra el contenedor "myhello1"

```bash
docker rm myhello1
```

> *Captura de pantalla aquí*

---

### 12. Muestra los contenedores que se están ejecutando

```bash
docker ps
```

> *Captura de pantalla aquí*

> Puedes comprobar también con `docker ps -a` para ver que `myhello1` ya no aparece en la lista.

---

### 13. Borra todos los contenedores

```bash
docker rm $(docker ps -aq)
```

> O si hay contenedores en ejecución, páralos primero:
>
> ```bash
> docker stop $(docker ps -q)
> docker rm $(docker ps -aq)
> ```

> *Captura de pantalla aquí*

---

## Resumen de comandos utilizados

| Comando | Descripción |
|---|---|
| `docker pull <imagen>` | Descargar una imagen del registro |
| `docker images` | Listar imágenes locales |
| `docker run --name <nombre> <imagen>` | Crear y ejecutar un contenedor con nombre |
| `docker ps` | Listar contenedores en ejecución |
| `docker ps -a` | Listar todos los contenedores (activos y detenidos) |
| `docker stop <nombre>` | Detener un contenedor |
| `docker rm <nombre>` | Eliminar un contenedor |
| `docker rm $(docker ps -aq)` | Eliminar todos los contenedores |
