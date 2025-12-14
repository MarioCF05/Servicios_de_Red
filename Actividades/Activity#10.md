## SSL Autofirmado & SSL con Let´s encrypt

# SSL Autofirmado

Para crear el ssl autofirmado ponemos el siguiente comando "sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt" 

<img width="935" height="597" alt="image" src="https://github.com/user-attachments/assets/4edea8d9-ae9a-4806-aa42-4c05256aa157" />

Modificamos el "default-ssl.conf" y el "000-default.conf", añadiendo las lineas que se van a ver en la imagen mas cambiar el puerto al "443"

# default-ssl.conf

<img width="830" height="162" alt="image" src="https://github.com/user-attachments/assets/e1385fc4-1cb6-407e-bb0d-b8b9d31513aa" />
/>

# 000-default.conf

<img width="927" height="342" alt="image" src="https://github.com/user-attachments/assets/843df391-73fc-440d-a495-0507985e4fe0" />

Habilitamos el sitio "default-ssl.conf" y el modulo "ssl" y el modulo "rewrite"

<img width="932" height="453" alt="image" src="https://github.com/user-attachments/assets/8005b10f-6898-4ad4-a3b8-a9a37bf6fb59" />

Como vemos nos avisa de que no es seguro porque es una conexion ssl autofirmada

<img width="938" height="697" alt="image" src="https://github.com/user-attachments/assets/9984a33c-7a67-4827-b7c8-9de739c2de2f" />

<img width="944" height="829" alt="image" src="https://github.com/user-attachments/assets/bf102548-b307-46b2-bf72-d3db5600c7a7" />

# SSL con Let´s Encrypt

Abrimos los puertos "80" y "443"

<img width="643" height="225" alt="image" src="https://github.com/user-attachments/assets/ba8ebbae-03f4-4385-a004-b4b696bd6c82" />

Nos vamos a la pagina "https://www.noip.com/es-MX" nos registramos y creamos un hostname con la IP publica de nuestra instancia AWS
<img width="946" height="1079" alt="image" src="https://github.com/user-attachments/assets/f5eb23d8-9028-4160-89b1-43c275f5df26" />

En nuestra instancia instalamos "certbot"

<img width="904" height="108" alt="image" src="https://github.com/user-attachments/assets/74c73482-fda1-47e5-a71d-dc8c330c3c80" />

Hacemos que certbot detecte Apache automáticamente y configurare HTTPS y ya lo tendriamos
<img width="790" height="420" alt="image" src="https://github.com/user-attachments/assets/faf6bc6d-9387-4081-9ce7-a40cd57055a4" />


