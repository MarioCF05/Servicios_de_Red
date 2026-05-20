# Docker Activity#0 — Introducción a Docker

## Objetivos

### 1. Conceptos básicos sobre Docker

Docker es una herramienta de contenerización que permite empaquetar aplicaciones con todo lo que necesitan para ejecutarse (código, librerías, configuraciones) en unidades llamadas **contenedores**.

**¿Por qué Docker y no una máquina virtual?**  
Una MV se lleva su propio sistema operativo invitado —ocupa GB y tarda minutos en arrancar—. Un contenedor comparte el kernel del host y arranca en segundos, ocupando MB. Es como la diferencia entre alquilar un piso entero (MV) o usar una taquilla en un gimnasio (contenedor): haces lo que necesitas sin pagar espacio muerto.

**Arquitectura básica:**
- **Docker daemon (`dockerd`)**: el cerebro que gestiona contenedores, imágenes y redes.
- **Docker client (`docker`)**: la terminal que le da órdenes al daemon.
- **Docker registries** (como Docker Hub): el "App Store" de imágenes, de donde descargas y subes imágenes.

**Ciclo de vida:** `pull` → `run` → `stop` → `start` → `rm`. O bien: "bajarse la receta, cocinar, apagar el fuego, volver a encender, y fregar los platos".

---

### 2. Imágenes Docker y contenedores

**Imagen** = plantilla de solo lectura con todo lo necesario para ejecutar una app (como un ISO o un instalador).  
**Contenedor** = una instancia en ejecución de esa imagen (como el proceso ya corriendo en tu máquina).

**Comandos esenciales:**
- `docker pull <imagen>` — descargarse la imagen sin ejecutarla.
- `docker images` — ver qué imágenes tienes localmente.
- `docker run <imagen>` — crear y arrancar un contenedor.
- `docker ps` — ver contenedores activos (con `-a` ves también los muertos).
- `docker rmi <imagen>` — borrar una imagen ("meterla en la papelera").

**Analogía informal:**  
Una imagen es como una **receta de cocina**. El contenedor es el **plato cocinado**. Puedes tener la receta guardada (imagen) y cocinarla mil veces (crear mil contenedores). Si modificas el plato, la receta original sigue intacta. Para cambiar la receta necesitas un `Dockerfile` —algo así como anotar los cambios en los márgenes y luego publicar una nueva versión de la receta.

**Capas (layers):**  
Cada instrucción en un `Dockerfile` crea una capa. Docker reutiliza capas de otras imágenes, así que si dos imágenes comparten la misma base Ubuntu, no se duplica el espacio. Es como si Lego compartiera piezas entre sets: ahorras almacenamiento porque usas los mismos ladrillos.

---

### 3. Volúmenes de almacenamiento

Los contenedores son efímeros por naturaleza: los datos que genera un contenedor **desaparecen cuando el contenedor se elimina**. Como escribir en una pizarra de agua: en cuanto pasas el borrador, se va todo.

**Soluciones para persistir datos:**

- **Volúmenes (volumes):** almacenamiento gestionado por Docker en el host. Es la opción recomendada. Se crean con `docker volume create` y se montan con `-v` o `--mount`. Docker los guarda en una ubicación especial del sistema, lejos de tus archivos personales (como un trastero que solo Docker sabe dónde está).

- **Bind mounts:** montas directamente una carpeta del host dentro del contenedor. Le dices al contenedor: "mira, ahí en `C:\Users\mario\datos` está tu carpeta, úsala". Sirve para desarrollo, porque editas en el host y el contenedor lo ve al instante.

**Analogía informal:**  
Un contenedor sin volumen es como un **táper de un solo uso** —tiras el táper con la comida. Un volumen es un **táper reutilizable**: tiras la comida vieja, pero el táper se queda para la próxima. Un bind mount es como tener la cocina abierta: el chef (contenedor) cocina directamente en tu encimera (host) y tú ves los cambios en tiempo real.

**Diferencia clave:**

| Almacenamiento | Persiste al eliminar contenedor | Uso típico |
|---|---|---|
| Container layer (efímero) | ❌ Se borra todo | Logs temporales, archivos de trabajo |
| Volumes | ✅ Docker lo gestiona | Bases de datos, configuraciones |
| Bind mounts | ✅ Tú controlas la ruta | Desarrollo, hot-reload |

## Tareas a realizar online

- [ops-stage1](https://training.play-with-docker.com/ops-stage1/) — Primeros pasos interactivos con Docker.
- [ops-s1-hello](https://training.play-with-docker.com/ops-s1-hello/) — Ejecutar tu primer contenedor "Hello World".
- [ops-s1-images](https://training.play-with-docker.com/ops-s1-images/) — Gestionar imágenes y contenedores básicos.

> Play-With-Docker te da una terminal online lista para usar, sin necesidad de instalar nada en tu máquina.

## Enlaces de interés

- [Documentación oficial de Docker](https://docs.docker.com/get-started/)
- [Docker Curriculum — tutorial completo](https://docker-curriculum.com/)
- [Contenedores vs VMs explicado para mortales (Medium)](https://medium.freecodecamp.org/a-beginner-friendly-introduction-to-containers-vms-and-docker-79a9e3e119b)
- [Awesome Docker — recursos curados](https://github.com/veggiemonk/awesome-docker)
- [Labs oficiales para principiantes](https://github.com/docker/labs/tree/master/beginner/)

## CheatSheets

- [CheatSheet oficial de Docker (PDF)](https://www.docker.com/sites/default/files/Docker_CheatSheet_08.09.2016_0.pdf)
- [wsargent/docker-cheat-sheet (GitHub)](https://github.com/wsargent/docker-cheat-sheet)
