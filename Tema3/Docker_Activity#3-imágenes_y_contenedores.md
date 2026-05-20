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

<img width="1715" height="268" alt="image" src="https://github.com/user-attachments/assets/bbb27f80-d982-4671-bb99-45a356b7634e" />


---

### 2. Descarga la imagen de hello-world

```bash
docker pull hello-world
```

<img width="1716" height="242" alt="image" src="https://github.com/user-attachments/assets/99e81791-6aa1-4644-a33f-4366845ef659" />


---

### 3. Descarga la imagen nginx

```bash
docker pull nginx
```

<img width="1718" height="361" alt="image" src="https://github.com/user-attachments/assets/b4632081-d289-4439-906b-c0711a3b0155" />


---

### 4. Muestra un listado de todas las imágenes

```bash
docker images
```

<img width="1720" height="197" alt="image" src="https://github.com/user-attachments/assets/f65e1aad-1e98-4e30-8064-6f233ef6630c" />


---

### 5. Ejecuta un contenedor hello-world y dale nombre "myhello1"

```bash
docker run --name myhello1 hello-world
```

<img width="1715" height="526" alt="image" src="https://github.com/user-attachments/assets/b15e126b-059d-4082-8f89-29defa8e63dd" />


---

### 6. Ejecuta un contenedor hello-world y dale nombre "myhello2"

```bash
docker run --name myhello2 hello-world
```

<img width="1717" height="531" alt="image" src="https://github.com/user-attachments/assets/1c6571bc-6940-4027-836f-58efddaac49f" />


---

### 7. Ejecuta un contenedor hello-world y dale nombre "myhello3"

```bash
docker run --name myhello3 hello-world
```

<img width="1718" height="538" alt="image" src="https://github.com/user-attachments/assets/94af5ac5-126e-4ce2-ade3-ab7aa25d0834" />


---

### 8. Muestra los contenedores que se están ejecutando

```bash
docker ps -a
```

<img width="1718" height="185" alt="image" src="https://github.com/user-attachments/assets/f172e1ad-4383-43d1-8728-26338f169e60" />

---

### 9. Para el contenedor "myhello1"

```bash
docker stop myhello1
```

<img width="1721" height="198" alt="image" src="https://github.com/user-attachments/assets/f75d7d66-d709-4ce8-8bd4-e9b6c14a9dd9" />


---

### 10. Para el contenedor "myhello2"

```bash
docker stop myhello2
```

<img width="1718" height="164" alt="image" src="https://github.com/user-attachments/assets/8fec034e-48ae-4fc9-9f8e-96f650d9e639" />


---

### 11. Borra el contenedor "myhello1"

```bash
docker rm myhello1
```

<img width="1721" height="161" alt="image" src="https://github.com/user-attachments/assets/f45b3309-20fc-4848-9175-0924666a5828" />


---

### 12. Muestra los contenedores que se están ejecutando

```bash
docker ps -a
```

<img width="1719" height="178" alt="image" src="https://github.com/user-attachments/assets/8a83d088-c693-4873-9cbf-2e87df6a35fe" />


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

<img width="1719" height="178" alt="image" src="https://github.com/user-attachments/assets/6a00f18b-c03a-4489-ad7b-c992a8b98963" />

<img width="1718" height="150" alt="image" src="https://github.com/user-attachments/assets/929b8745-a316-4ed5-bd2a-1285754d4cc4" />

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
