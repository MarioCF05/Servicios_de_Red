# Activity#1 - Instalación de Apache
<br>

## Instalar Apache

Actualizamos el sistema

<img width="800" height="575" alt="image" src="https://github.com/user-attachments/assets/3c085abe-552d-4b09-bd20-9e924c596d26" />

<img width="707" height="364" alt="image" src="https://github.com/user-attachments/assets/f695aa06-cf56-4f71-a1bf-f4d811545400" />

Instalamos Apache

<img width="731" height="475" alt="image" src="https://github.com/user-attachments/assets/f68a4a40-755a-4eea-aeda-18549e6d6ca3" />

Ajustamos configuración de su firewall para permitir el trafico HTTP

<img width="548" height="169" alt="image" src="https://github.com/user-attachments/assets/657d1de5-4d81-4882-83c4-d23b855c0dca" />

Le damos permisos de trafico del puerto 80

<img width="640" height="60" alt="image" src="https://github.com/user-attachments/assets/b804d244-3e53-4960-904f-6b5c92ed1057" />

<img width="558" height="162" alt="image" src="https://github.com/user-attachments/assets/2ef6308e-c65d-46e6-a2c5-49ca434e5e92" />

Vamos a comprobar que Apache este funcionando http://127.0.0.1

<img width="734" height="572" alt="image" src="https://github.com/user-attachments/assets/e2f70176-7158-497c-bb6a-cf04d3bb7eca" />

## Instalar MySQL

Instalamos MySQL

<img width="723" height="381" alt="image" src="https://github.com/user-attachments/assets/b98049ca-18cf-4850-aa88-87bb21eef203" />

Modificamos las configuraciones de seguridad

<img width="697" height="229" alt="image" src="https://github.com/user-attachments/assets/6261284d-9cf6-4402-bde3-3a18f5a298ec" />

Seleccionamos el nivel de seguridad de la contraseña, en mi caso un punto medio

<img width="724" height="181" alt="image" src="https://github.com/user-attachments/assets/fc49816f-82a8-46ad-bdd0-89d2aa8949f7" />

Dejamos los usuarios anonimos y deshabilitamos la posibilidad del acceso remoto root 

<img width="671" height="320" alt="image" src="https://github.com/user-attachments/assets/df01943b-67d5-4827-9acb-36554af3402e" />

<img width="727" height="53" alt="image" src="https://github.com/user-attachments/assets/45eefb31-979e-4642-b3b6-b650db1f30d8" />

Entramos en MySQL para asegurarnos que esta activo

<img width="689" height="274" alt="image" src="https://github.com/user-attachments/assets/fb9ed42b-4498-445d-9273-84ffad7d0951" />

## Instalar PHP

Instalamos PHP y librerias necesarias y comprovamos que se haya instalado de forma correcta

<img width="737" height="345" alt="image" src="https://github.com/user-attachments/assets/5b21f6b6-400a-4b85-adee-3aa4742701c3" />

<img width="580" height="114" alt="image" src="https://github.com/user-attachments/assets/e56e998a-943d-44dc-9dc7-6e84dd6710ad" />


## Creamos un Host Virtual

Creamos el directorio para el dominio y se le da la prioridad del directorio

<img width="734" height="98" alt="image" src="https://github.com/user-attachments/assets/51858520-d798-4bd8-a84f-d8f05c02f8d3" />

Abrimos un nuevo archivo de configuracion en "sites-available"

<img width="728" height="64" alt="image" src="https://github.com/user-attachments/assets/f3471166-d5af-4846-a28b-50d375dc7730" />

Dejamos la proxima configuracion

<img width="729" height="178" alt="image" src="https://github.com/user-attachments/assets/daa11b17-d9fc-42b8-ac15-e78c138d363a" />

Habilitamos el sitio online nuestro y deshabilitamos el de apache predeterminado

#### HABILITAR

<img width="740" height="147" alt="image" src="https://github.com/user-attachments/assets/9c8cf025-9678-4d12-b88a-75bd518c69b6" />

#### DESHABILITAR

<img width="740" height="121" alt="image" src="https://github.com/user-attachments/assets/09a7f72c-26cf-47ff-a6b3-94749ca799fa" />

Reinicimoa el servicio de apache

<img width="724" height="235" alt="image" src="https://github.com/user-attachments/assets/217ecce5-3371-4d1f-8c26-b52dfae33f78" />

Creamos un archivo html para la pagina

<img width="685" height="63" alt="image" src="https://github.com/user-attachments/assets/d63c6bc3-769b-40ce-9c56-77d90a385749" />

<img width="725" height="377" alt="image" src="https://github.com/user-attachments/assets/f81f802e-2f20-4115-935b-f8f5aa128c85" />

# Pagina final

<img width="307" height="209" alt="image" src="https://github.com/user-attachments/assets/c3a18d89-f054-4ee1-91d0-cebaccd3c021" />

