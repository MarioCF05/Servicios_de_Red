# Docker Activity#6 — Creación de imágenes Docker

## Objetivo

Aprender a construir imágenes Docker personalizadas mediante `Dockerfile` para distintos tipos de aplicaciones.

## Recurso de referencia

- [Curso Docker IES — Módulo 5: Creación de imágenes Docker](https://github.com/josedom24/curso_docker_ies)
- [Ejemplos del módulo 5](https://github.com/josedom24/curso_docker_ies/tree/main/ejemplos/modulo5)

---

## Ejemplo 1: Construcción de imágenes con una página estática

### Versión 1 — Desde Ubuntu con Apache2

#### Estructura del proyecto

```
~/docker/ejemplo1/
├── Dockerfile
└── public_html/
    └── index.html
```

#### Crear la página HTML

```bash
mkdir -p ~/docker/ejemplo1/public_html
```

Crea `public_html/index.html`:

```html
<!DOCTYPE html>
<html>
<head><title>Mi página estática</title></head>
<body>
  <h1>Hola desde Docker</h1>
  <p>Imagen construida desde Ubuntu + Apache2</p>
</body>
</html>
```

<img width="1716" height="111" alt="image" src="https://github.com/user-attachments/assets/c0633395-077f-4c8f-8e93-26953c690426" />


<img width="1716" height="707" alt="image" src="https://github.com/user-attachments/assets/cec5c1b2-ea23-4b83-929a-498fc34dfd3a" />


#### Crear el Dockerfile

```dockerfile
FROM ubuntu:22.04
RUN apt update && apt install -y apache2 && apt clean && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www/html/
COPY public_html .
EXPOSE 80
CMD apache2ctl -D FOREGROUND
```

<img width="1721" height="132" alt="image" src="https://github.com/user-attachments/assets/e60005ac-30d8-4771-b05e-9920997a6b0f" />


<img width="1714" height="711" alt="image" src="https://github.com/user-attachments/assets/8eae80f6-c1eb-4839-b798-c71c7c3a908b" />


#### Construir la imagen

```bash
cd ~/docker/ejemplo1
docker build -t mario/ejemplo1:v1 .
```

<img width="1714" height="558" alt="image" src="https://github.com/user-attachments/assets/9c1e4358-061a-4912-a809-256ffa6f1a8c" />


#### Ejecutar el contenedor

```bash
docker run -d -p 8085:80 --name ejemplo1 mario/ejemplo1:v1
```

<img width="1715" height="63" alt="image" src="https://github.com/user-attachments/assets/50ca3f87-85da-47ff-9028-0faf3e874ac4" />


#### Acceder

Desde cualquier navegador en tu red: `http://192.168.8.190:8085/`

<img width="1717" height="187" alt="image" src="https://github.com/user-attachments/assets/4ba625b4-1b06-4dea-8160-29a699b52a6e" />


---

### Versión 2 — Desde imagen httpd

```dockerfile
FROM httpd:2.4
COPY public_html /usr/local/apache2/htdocs/
EXPOSE 80
```

<img width="1717" height="711" alt="image" src="https://github.com/user-attachments/assets/fcad18d7-8c93-4355-baa2-c520d9fd913b" />


```bash
cd ~/docker/ejemplo1
docker build -t mario/ejemplo1:v2 .
docker run -d -p 8081:80 --name ejemplo1-v2 mario/ejemplo1:v2
```

<img width="1718" height="711" alt="image" src="https://github.com/user-attachments/assets/d073f3a7-8096-4515-b743-e10e94e55e31" />


Accede en: `http://192.168.8.190:8081`

<img width="1719" height="246" alt="image" src="https://github.com/user-attachments/assets/9906d935-ee72-43bc-9486-7b548bb82f0c" />


---

### Versión 3 — Desde imagen nginx

```dockerfile
FROM nginx:1.24
COPY public_html /usr/share/nginx/html
EXPOSE 80
```

<img width="1717" height="709" alt="image" src="https://github.com/user-attachments/assets/e31f20a5-bc32-491f-a983-92bc55cb8be9" />


```bash
cd ~/docker/ejemplo1
docker build -t mario/ejemplo1:v3 .
docker run -d -p 8082:80 --name ejemplo1-v3 mario/ejemplo1:v3
```

<img width="1712" height="789" alt="image" src="https://github.com/user-attachments/assets/6e1fd5ea-7532-42bd-8526-55d3200cba88" />


Accede en: `http://192.168.8.190:8082/`

<img width="1719" height="336" alt="image" src="https://github.com/user-attachments/assets/2ab995ac-980f-4bfb-8bfa-303bb81d7d53" />


---

## Ejemplo 2: Construcción de imágenes con una aplicación PHP

### Versión 1 — Desde Ubuntu con Apache2 y PHP

#### Estructura del proyecto

```
~/docker/ejemplo2/
├── Dockerfile
└── app/
    ├── index.php
    └── info.php
```

#### Crear la aplicación PHP

```bash
mkdir -p ~/docker/ejemplo2/app
```

`app/index.php`:

```php
<?php
echo "<h1>Hola desde Docker (PHP)</h1>";
echo "<p>Versión de PHP: " . phpversion() . "</p>";
?>
```

`app/info.php`:

```php
<?php phpinfo(); ?>
```

<img width="1718" height="309" alt="image" src="https://github.com/user-attachments/assets/811306be-ab9f-4239-aacc-bf0cc844c6ed" />


#### Crear el Dockerfile

```dockerfile
FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Madrid
RUN apt update && apt install -y apache2 libapache2-mod-php php && apt clean && rm -rf /var/lib/apt/lists/* && rm /var/www/html/index.html
COPY app /var/www/html/
EXPOSE 80
CMD apache2ctl -D FOREGROUND
```

<img width="1716" height="164" alt="image" src="https://github.com/user-attachments/assets/810893c8-f62f-4333-860c-98c2b00f914e" />


#### Construir y ejecutar

```bash
cd ~/docker/ejemplo2
docker build -t mario/ejemplo2:v1 .
docker run -d -p 8083:80 --name ejemplo2 mario/ejemplo2:v1
```

<img width="1721" height="576" alt="image" src="https://github.com/user-attachments/assets/ae76fcf8-7bbf-4146-b85e-7a3557e4c170" />


#### Acceder

`http://192.168.8.190:8083` y `http://192.168.8.190:8083/info.php`

<img width="1717" height="1391" alt="image" src="https://github.com/user-attachments/assets/e7f2ffbc-3417-485a-9458-4db08d6d6e7c" />


---

### Versión 2 — Desde imagen oficial php:apache

```dockerfile
FROM php:7.4-apache
COPY app /var/www/html/
EXPOSE 80
```

```bash
cd ~/docker/ejemplo2
docker build -t mario/ejemplo2:v2 .
docker run -d -p 8084:80 --name ejemplo2-v2 mario/ejemplo2:v2
```

<img width="1716" height="1071" alt="image" src="https://github.com/user-attachments/assets/3d7ff63a-6e24-47c5-b2df-4c6a053b12c4" />


Accede en: `http://192.168.8.190:8084/info.php`

<img width="1719" height="1391" alt="image" src="https://github.com/user-attachments/assets/3726fa1e-0642-4f68-8b6f-044a739a0e91" />


---

## Ejemplo 3: Construcción de imágenes con una aplicación Python (Flask)

### Versión 1 — Desde Ubuntu con Python

#### Estructura del proyecto

```
~/docker/ejemplo3/
├── Dockerfile
└── app/
    ├── app.py
    └── requirements.txt
```

#### Crear la aplicación Flask

```bash
mkdir -p ~/docker/ejemplo3/app
```

`app/app.py`:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "<h1>Hola desde Docker (Python Flask)</h1>"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=3000)
```

`app/requirements.txt`:

```
flask
```

<img width="1718" height="370" alt="image" src="https://github.com/user-attachments/assets/bd195979-d05d-4960-b133-834c200f2589" />


#### Crear el Dockerfile

```dockerfile
FROM ubuntu:22.04
RUN apt update && apt install -y python3-pip && apt clean && rm -rf /var/lib/apt/lists/*
WORKDIR /usr/share/app
COPY app .
RUN pip3 install --no-cache-dir -r requirements.txt
EXPOSE 3000
CMD python3 app.py
```

<img width="1716" height="250" alt="image" src="https://github.com/user-attachments/assets/59ddbcde-221b-4f75-9dff-729049a5a492" />



#### Construir y ejecutar

```bash
cd ~/docker/ejemplo3
docker build -t mario/ejemplo3:v1 .
docker run -d -p 8086:3000 --name ejemplo3-v1 mario/ejemplo3:v1
```

<img width="1716" height="628" alt="image" src="https://github.com/user-attachments/assets/96faf3b5-1e22-4439-8b6f-5e2bdd2259d1" />


#### Acceder

`http://192.168.8.190:8086`

<img width="1714" height="277" alt="image" src="https://github.com/user-attachments/assets/3fac3fe1-0c69-4c00-bace-ff640f10a7cd" />


---

### Versión 2 — Desde imagen oficial python

```dockerfile
FROM python:3.12-slim-bookworm
WORKDIR /usr/share/app
COPY app .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 3000
CMD python app.py
```
<img width="1716" height="259" alt="image" src="https://github.com/user-attachments/assets/6343ad2a-bc5a-4462-8efa-ef48d7d3e6bf" />


```bash
cd ~/docker/ejemplo3
docker build -t mario/ejemplo3:v2 .
docker run -d -p 8087:3000 --name ejemplo3-v2 mario/ejemplo3:v2
```

<img width="1717" height="765" alt="image" src="https://github.com/user-attachments/assets/c368d652-384e-4d04-8988-7550affcd67b" />


Accede en: `http://192.168.8.190:8087`

<img width="1718" height="252" alt="image" src="https://github.com/user-attachments/assets/2f83a618-fb3d-4052-9c6c-a6348dac2cb0" />



---

## Resumen de comandos

| Comando | Descripción |
|---|---|
| `docker build -t nombre:tag .` | Construir imagen desde un Dockerfile |
| `docker images` | Listar imágenes locales |
| `docker rmi <imagen>` | Eliminar una imagen |
| `docker history <imagen>` | Ver las capas de una imagen |

## Limpieza

```bash
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
docker rmi mario/ejemplo1:v1 mario/ejemplo1:v2 mario/ejemplo1:v3 \
          mario/ejemplo2:v1 mario/ejemplo2:v2 \
          mario/ejemplo3:v1 mario/ejemplo3:v2
```

<img width="1715" height="706" alt="image" src="https://github.com/user-attachments/assets/3bf178b1-dc02-4218-8cb2-46bd8169bc62" />


---

## Mapa de puertos usado

| Contenedor | Puerto host |
|---|---|
| ejemplo1 (Apache) | 8080 |
| ejemplo1-v2 (httpd) | 8081 |
| ejemplo1-v3 (nginx) | 8082 |
| ejemplo2 (PHP Ubuntu) | 8083 |
| ejemplo2-v2 (PHP oficial) | 8084 |
| ejemplo3-v1 (Flask Ubuntu) | 8085 |
| ejemplo3-v2 (Flask oficial) | 8086 |
