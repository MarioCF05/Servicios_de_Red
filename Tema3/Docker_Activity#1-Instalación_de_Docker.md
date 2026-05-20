# Docker Activity#1 — Instalación de Docker en Ubuntu

## Objetivo

Instalar Docker Engine en Ubuntu y documentar el proceso en el repositorio de GitHub.

## Recursos de referencia

- [Documentación oficial — Docker CE en Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
- [How to install Docker on Ubuntu 16.04 (Medium)](https://medium.com/@Grigorkh/how-to-install-docker-on-ubuntu-16-04-3f509070d29c)
- [Install Docker and Run Docker Containers in Ubuntu (Tecmint)](https://www.tecmint.com/install-docker-and-run-docker-containers-in-ubuntu/)

---

## Tarea: Instalar Docker en Ubuntu

### Requisitos previos

- Sistema Ubuntu (16.04 o superior recomendado)
- Acceso a terminal con permisos de superusuario (`sudo`)
- Conexión a Internet

### Pasos de instalación

1. **Actualizar los paquetes del sistema**

   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

   > *Captura de pantalla aquí*

2. **Instalar dependencias necesarias**

   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
   ```

   > *Captura de pantalla aquí*

3. **Agregar la clave GPG oficial de Docker**

   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

   > *Captura de pantalla aquí*

4. **Agregar el repositorio oficial de Docker**

   ```bash
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

   > *Captura de pantalla aquí*

5. **Instalar Docker Engine**

   ```bash
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io -y
   ```

   > *Captura de pantalla aquí*

6. **Verificar que Docker está instalado correctamente**

   ```bash
   sudo systemctl status docker
   ```

   > *Captura de pantalla aquí*

7. **(Opcional) Ejecutar el contenedor de prueba "Hello World"**

   ```bash
   sudo docker run hello-world
   ```

   > *Captura de pantalla aquí*

8. **(Opcional) Agregar tu usuario al grupo `docker` para evitar usar `sudo`**

   ```bash
   sudo usermod -aG docker $USER
   ```

   > Tras ejecutarlo, cierra sesión y vuelve a iniciarla para que el cambio surta efecto.

   > *Captura de pantalla aquí*

---

## Verificación final

Comprueba que Docker funciona correctamente:

```bash
docker --version
docker info
```

> *Captura de pantalla aquí*

---

## Documentación en GitHub

- Crea o utiliza tu repositorio de prácticas.
- Sube este archivo (o un resumen) con las capturas de pantalla integradas.
- Asegúrate de incluir los comandos utilizados y una breve explicación de cada paso.

---

## Notas adicionales

- Si usas una máquina virtual en Windows/Mac, asegúrate de tener activada la virtualización anidada.
- Docker Desktop para Windows/Mac también es una alternativa, pero esta práctica se centra en Ubuntu nativo.
- En caso de errores de permisos, revisa que tu usuario pertenezca al grupo `docker`.
