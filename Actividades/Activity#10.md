# Creamos 5 usuarios 

Creamos un directorio para los usuarios nuevos (mkdir -p /etc/apache2/Usus)
<img width="840" height="30" alt="image" src="https://github.com/user-attachments/assets/fe1afb8c-86f2-49c1-9484-fa32f85a947a" />

Creamos los usuarios
<img width="943" height="416" alt="image" src="https://github.com/user-attachments/assets/b7740cde-ba6a-4831-82c7-441282da4bbc" />

# Crear dos grupos grupo1(usuario1, usuario2) grupo2(usuario3, usuario4, usuario5)

Creamos un archivo en la misma carpeta de los usuarios llamada ".htgroups" y añadimos la linea ("grupo1: usuario1 usuario2") y la linea ("grupo2: usuario3 usuario4 usuario5")
<img width="788" height="67" alt="image" src="https://github.com/user-attachments/assets/fd40380f-a852-4d09-8c7f-516a457a1882" />


<img width="783" height="105" alt="image" src="https://github.com/user-attachments/assets/7cd7ea5c-2022-40d9-94a7-fc2890387e5d" />

# Crear un directorio y permitir acceso a todos los usuarios

Ahora vamos a crear un directorio, para ello creamos un directorio en "/var/www/html/" y para darle acceso a todos los usuarios creamos un archivo ".htaccess"


Insertamos las siguientes lineas


