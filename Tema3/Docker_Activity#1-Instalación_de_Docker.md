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

<img width="1272" height="458" alt="image" src="https://github.com/user-attachments/assets/e69eee52-49fa-40d1-9203-e6e2a4982fab" />


2. **Instalar dependencias necesarias**

   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
   ```

 <img width="1268" height="475" alt="image" src="https://github.com/user-attachments/assets/d9f0d40a-2c55-449b-a8f4-bfd2e067b3da" />


3. **Agregar la clave GPG oficial de Docker**

   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

<img width="1717" height="104" alt="image" src="https://github.com/user-attachments/assets/33493411-ef32-4a7f-9b47-98a412955706" />


4. **Agregar el repositorio oficial de Docker**

   ```bash
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

<img width="1718" height="105" alt="image" src="https://github.com/user-attachments/assets/cd4de57e-89a0-480b-9eae-699bf44aedfe" />


5. **Instalar Docker Engine**

   ```bash
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io -y
   ```

<img width="1425" height="641" alt="image" src="https://github.com/user-attachments/assets/6f241d65-d47d-477f-8e73-f5d2e523ff61" />


6. **Verificar que Docker está instalado correctamente**

   ```bash
   sudo systemctl status docker
   ```

<img width="1716" height="499" alt="image" src="https://github.com/user-attachments/assets/8aa5d608-1ff6-4e7c-85ce-80920726a905" />


## Verificación final

Comprueba que Docker funciona correctamente:

```bash
docker --version
docker info
```

<img width="1066" height="318" alt="image" src="https://github.com/user-attachments/assets/182b051b-e96a-487c-990b-bc1ddc39a83d" />


---

