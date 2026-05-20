# Docker Activity#6 — Creación de imágenes Docker

## Objetivo

Aprender a construir imágenes Docker personalizadas mediante `Dockerfile` para distintos tipos de aplicaciones.

## Recurso de referencia

- [Curso Docker IES — Módulo 5: Creación de imágenes Docker](https://github.com/josedom24/curso_docker_ies)
- [Ejemplos del módulo 5](https://github.com/josedom24/curso_docker_ies/tree/main/ejemplos/modulo5)

---

## Ejemplo 1: Construcción de imágenes con una página estática

### Versión 1 — Desde Debian con Apache2

#### Estructura del proyecto

```
ejemplo1/
├── Dockerfile
└── public_html/
    └── index.html
```

#### Crear la página HTML

Crea `public_html/index.html`:

```html
<!DOCTYPE html>
<html>
<head><title>Mi página estática</title></head>
<body>
  <h1>Hola desde Docker</h1>
  <p>Imagen construida desde Debian + Apache2</p>
</body>
</html>
```

> *Captura de pantalla aquí*

#### Crear el Dockerfile

```dockerfile
FROM debian:stable-slim
RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www/html/
COPY public_html .
EXPOSE 80
CMD apache2ctl -D FOREGROUND
```

> *Captura de pantalla aquí*

#### Construir la imagen

```bash
docker build -t mario/ejemplo1:v1 .
```

> *Captura de pantalla aquí*

#### Ejecutar el contenedor

```bash
docker run -d -p 80:80 --name ejemplo1 mario/ejemplo1:v1
```

> *Captura de pantalla aquí*

#### Acceder

Abre el navegador en `http://localhost`.

> *Captura de pantalla aquí*

---

### Versión 2 — Desde imagen httpd

```dockerfile
FROM httpd:2.4
COPY public_html /usr/local/apache2/htdocs/
EXPOSE 80
```

```bash
docker build -t mario/ejemplo1:v2 .
docker run -d -p 8080:80 --name ejemplo1-v2 mario/ejemplo1:v2
```

> *Captura de pantalla aquí (build y navegador)*

---

### Versión 3 — Desde imagen nginx

```dockerfile
FROM nginx:1.24
COPY public_html /usr/share/nginx/html
EXPOSE 80
```

```bash
docker build -t mario/ejemplo1:v3 .
docker run -d -p 8081:80 --name ejemplo1-v3 mario/ejemplo1:v3
```

> *Captura de pantalla aquí (build y navegador)*

---

## Ejemplo 2: Construcción de imágenes con una aplicación PHP

### Versión 1 — Desde Debian con Apache2 y PHP

#### Estructura del proyecto

```
ejemplo2/
├── Dockerfile
└── app/
    ├── index.php
    └── info.php
```

#### Crear la aplicación PHP

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

> *Captura de pantalla aquí*

#### Crear el Dockerfile

```dockerfile
FROM debian:stable-slim
RUN apt-get update && apt-get install -y apache2 libapache2-mod-php7.4 php7.4 && apt-get clean && rm -rf /var/lib/apt/lists/* && rm /var/www/html/index.html
COPY app /var/www/html/
EXPOSE 80
CMD apache2ctl -D FOREGROUND
```

> *Captura de pantalla aquí*

#### Construir y ejecutar

```bash
docker build -t mario/ejemplo2:v1 .
docker run -d -p 80:80 --name ejemplo2 mario/ejemplo2:v1
```

> *Captura de pantalla aquí*

#### Acceder

Abre `http://localhost` y `http://localhost/info.php`.

> *Captura de pantalla aquí (navegador)*

---

### Versión 2 — Desde imagen oficial php:apache

```dockerfile
FROM php:7.4-apache
COPY app /var/www/html/
EXPOSE 80
```

```bash
docker build -t mario/ejemplo2:v2 .
docker run -d -p 8082:80 --name ejemplo2-v2 mario/ejemplo2:v2
```

> *Captura de pantalla aquí (build y navegador con info.php)*

---

## Ejemplo 3: Construcción de imágenes con una aplicación Python (Flask)

### Versión 1 — Desde Debian con Python

#### Estructura del proyecto

```
ejemplo3/
├── Dockerfile
└── app/
    ├── app.py
    └── requirements.txt
```

#### Crear la aplicación Flask

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

> *Captura de pantalla aquí*

#### Crear el Dockerfile

```dockerfile
FROM debian:12
RUN apt-get update && apt-get install -y python3-pip && apt-get clean && rm -rf /var/lib/apt/lists/*
WORKDIR /usr/share/app
COPY app .
RUN pip3 install --no-cache-dir --break-system-packages -r requirements.txt
EXPOSE 3000
CMD python3 app.py
```

> *Captura de pantalla aquí*

#### Construir y ejecutar

```bash
docker build -t mario/ejemplo3:v1 .
docker run -d -p 80:3000 --name ejemplo3-v1 mario/ejemplo3:v1
```

> *Captura de pantalla aquí*

#### Acceder

Abre `http://localhost`.

> *Captura de pantalla aquí (navegador)*

---

### Versión 2 — Desde imagen oficial python

```dockerfile
FROM python:3.12.1-bookworm
WORKDIR /usr/share/app
COPY app .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 3000
CMD python app.py
```

```bash
docker build -t mario/ejemplo3:v2 .
docker run -d -p 8083:3000 --name ejemplo3-v2 mario/ejemplo3:v2
```

> *Captura de pantalla aquí (build y navegador)*

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
docker rmi mario/ejemplo1:v1 mario/ejemplo1:v2 mario/ejemplo1:v3 mario/ejemplo2:v1 mario/ejemplo2:v2 mario/ejemplo3:v1 mario/ejemplo3:v2
```

> *Captura de pantalla aquí*
