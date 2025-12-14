## SSL Autofirmado & SSL con Let´s encrypt

# SSL Autofirmado

Para crear el ssl autofirmado ponemos el siguiente comando "sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt" 

<img width="935" height="597" alt="image" src="https://github.com/user-attachments/assets/4edea8d9-ae9a-4806-aa42-4c05256aa157" />

Modificamos el "default-ssl.conf" y añadimos las lineas que se van a ver en la imagen

