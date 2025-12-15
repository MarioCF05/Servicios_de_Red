# Practica Primer Trimestre  
**Alumno:** Mario  
**Módulo:** Servicios en Red / Aplicaciones Web  
**Sistema operativo:** Ubuntu Server 25.10  

---

## 1. Introducción

En esta actividad he realizado la instalación y configuración de un servidor web interno para un instituto.  
El objetivo es disponer de varios servicios web accesibles mediante dominios internos, usando diferentes tecnologías como Apache, WordPress, Python (WSGI), MySQL, AWStats y un segundo servidor web (Nginx).

Al final del proyecto se obtiene una infraestructura funcional y similar a la que se puede encontrar en un entorno real.

---

## 2. Preparación del sistema

Antes de comenzar, actualicé el sistema para asegurarme de tener los últimos paquetes:
```bash
apt update && sudo apt upgrade -y
```

<img width="627" height="331" alt="image" src="https://github.com/user-attachments/assets/1a75a1b8-1ec0-4e9a-8053-da3c5c307d48" />

Tambien instalare herramientas que pobrablemente vaya a usar (curl, wget, unzip)
```bash
apt install curl wget unzip -y
```

cur<img width="639" height="370" alt="image" src="https://github.com/user-attachments/assets/630e445f-b660-42ec-9274-90fc91911f3e" />

## 3. Instalacion del servidor apache
Instalamos apache y compruebo que funcione
```bash
apt install apache2 -y
```
<img width="644" height="310" alt="image" src="https://github.com/user-attachments/assets/1e937675-3f50-4ecb-8ea6-7de81e095ba6" />


<img width="1066" height="725" alt="image" src="https://github.com/user-attachments/assets/674f842c-35e1-4d13-8de0-5708614519b2" />

Habilitamos apache2 para que se ejecute automaticamente al iniciar el sistema
```bash
systemctl enable apache2
```
<img width="657" height="111" alt="image" src="https://github.com/user-attachments/assets/66cfde98-b432-46c1-8c2f-47a31dfffd91" />

## 4. Configuracion de dominios internos
Para modificar los dominios internos editamos el archivo /etc/hosts
```bash
nano /etc/hosts
```
<img width="620" height="65" alt="image" src="https://github.com/user-attachments/assets/82b249db-abb3-4966-9edf-6130e58ffc58" />


y añadimos las siguientes lineas
```text
127.0.0.1   centro.intranet
127.0.0.1   departamentos.centro.intranet
127.0.0.1   servidor2.centro.intranet
```
<img width="647" height="249" alt="image" src="https://github.com/user-attachments/assets/d30ccc19-82dd-4fe4-b8db-3188087a2564" />

## 5. Configuracion de Virtual Host en Apache
Creamos los directorios donde se guardaran los distintos servicios
```bash
sudo mkdir -p /var/www/centro
sudo mkdir -p /var/www/departamentos
```
<img width="829" height="70" alt="image" src="https://github.com/user-attachments/assets/d2812d00-eb4c-486b-ba18-30761f7ac592" />

 y le asignamos los permisos adecuados
 ```bash
chown -R www-data:www-data /var/www
```
<img width="763" height="58" alt="image" src="https://github.com/user-attachments/assets/41f213b4-814e-4c2c-a3a1-e673b36e6652" />

### Creacion de VirtualHost

**centro.intranet**
Creamos archivo .conf
```bash
nano /etc/apache2/sites-available/centro.intranet.conf
```
<img width="840" height="55" alt="image" src="https://github.com/user-attachments/assets/c6f2ec58-e3ac-46f4-91eb-310f6d1308fc" />

Le metemos el siguiente contenido:
```text
<VirtualHost *:80>
    ServerName centro.intranet
    DocumentRoot /var/www/centro

    <Directory /var/www/centro>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/centro_error.log
    CustomLog ${APACHE_LOG_DIR}/centro_access.log combined
</VirtualHost>
```
<img width="850" height="256" alt="image" src="https://github.com/user-attachments/assets/36f535a9-3d46-4f61-9e58-4a9ed269c5d7" />


**departamentos.centro.intranet (Python)**
Creamos archivo .conf
```bash
nano /etc/apache2/sites-available/departamentos.centro.intranet.conf
```
<img width="845" height="44" alt="image" src="https://github.com/user-attachments/assets/058715fb-50e9-43f0-8080-28111660341b" />

Le metemos el siguiente contenido:
```text
<VirtualHost *:80>
    ServerName departamentos.centro.intranet

    WSGIDaemonProcess departamentos python-path=/var/www/departamentos
    WSGIProcessGroup departamentos
    WSGIScriptAlias / /var/www/departamentos/app.wsgi

    <Directory /var/www/departamentos>
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/departamentos_error.log
    CustomLog ${APACHE_LOG_DIR}/departamentos_access.log combined
</VirtualHost>
```
<img width="759" height="272" alt="image" src="https://github.com/user-attachments/assets/70fda4b5-2696-4368-bbbc-d705fcfdb97b" />

Activamos los sitios y recargamos apache
```bash
a2ensite centro.intranet.conf
a2ensite departamentos.centro.intranet.conf
systemctl reload apache2
```
<img width="857" height="132" alt="image" src="https://github.com/user-attachments/assets/b84417f7-ee30-4334-928d-ec466e4e5b7f" />

## 6. Instalacion PHP y MySQL
Instalamos PHP y sus modulos necesarios para WordPress
```bash
apt install php php-mysql libapache2-mod-php -y
```
<img width="1146" height="86" alt="image" src="https://github.com/user-attachments/assets/1faf1260-3158-4d7a-87e2-83c0fb15459e" />

Instalamos MySQL
```bash
apt install mysql-server -y
```
<img width="1183" height="288" alt="image" src="https://github.com/user-attachments/assets/37ce72f3-27c7-4cc9-8806-578138fc306a" />

Ejecutamos la configuracion de seguridad basica
```bash
mysql_secure_installation
```
<img width="792" height="202" alt="image" src="https://github.com/user-attachments/assets/10e93886-6443-4692-8d51-4384de90810f" />

## Instalacion y configuracion de WordPress
### Accedemos a MySQL y creamos la base de datos
```bash
mysql
```
<img width="1079" height="280" alt="image" src="https://github.com/user-attachments/assets/c922b96c-6bd9-465e-9b45-167f72e35f76" />

```sql
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
<img width="1204" height="226" alt="image" src="https://github.com/user-attachments/assets/2fafa6d8-c443-4397-aeda-6edefecf4f31" />

### Descargamos e instalamos
Descargamos WordPress y lo movemos al directorio correspondiente
```bash
cd /tmp
curl -O https://wordpress.org/latest.zip
unzip latest.zip
sudo mv wordpress/* /var/www/centro
sudo chown -R www-data:www-data /var/www/centro
```
<img width="459" height="117" alt="image" src="https://github.com/user-attachments/assets/a516017b-7292-45d6-8a81-dfbc8b56f46e" />

## 8. Activacion del modulo WSGI para Python
Instalamos modulo WSGI para Apache (esto lo tuve que hacer con anterioridad cuando puse el departamentos.centro.intranet.conf)
```bash
apt install libapache2-mod-wsgi-py3 -y
a2enmod wsgi
systemctl restart apache2
```

<img width="1148" height="225" alt="image" src="https://github.com/user-attachments/assets/1124df84-2fc5-4dea-93f7-bd36a8e04660" />

## 9. Aplicacion Python de prueba
Creamos una aplicacion sencilla para comprobar el funcionamiento
``` bash
nano /var/www/departamentos/app.wsgi
```
<img width="970" height="41" alt="image" src="https://github.com/user-attachments/assets/c6532a23-fc38-414a-9111-613a394df59c" />

```python
def application(environ, start_response):
    status = '200 OK'
    output = b'Aplicacion Python funcionando correctamente'

    response_headers = [('Content-type', 'text/plain'),
                         ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
```
<img width="837" height="204" alt="image" src="https://github.com/user-attachments/assets/9b17d9e2-401f-4202-b596-17a66d167e44" />


Aplicacion funcionando
<img width="1213" height="175" alt="image" src="https://github.com/user-attachments/assets/122108fb-f986-4c5a-9265-b809d18d9fc7" />


## 10. Proteccion con autenticacion 
Para proteger el acceso a la aplicacion python
Configuramos la autentificacion
```bash
htpasswd -c /etc/apache2/.htpasswd profesor
```
<img width="754" height="111" alt="image" src="https://github.com/user-attachments/assets/3c54e62d-6340-4a3a-87c9-7fd5358af160" />

Modificamos el virtualhost y añadimos las siguientes lineas
```text
    AuthType Basic
    AuthName "Acceso restringido"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
```
<img width="911" height="346" alt="image" src="https://github.com/user-attachments/assets/afa52352-e845-4663-a47e-b3ab35c60a00" />

## 11. Instalacion y configuracion de AWStats

Instalamos AWStats
```bash
apt install awstats -y
```
<img width="1154" height="242" alt="image" src="https://github.com/user-attachments/assets/666026a6-5141-4183-8c06-e0ff6e1f92f2" />

Modificamos el "awstats.centro.intranet.conf" y añadimos las siguiente lineas
```bash
nano /etc/awstats/awstats.centro.intranet.conf
```
<img width="860" height="37" alt="image" src="https://github.com/user-attachments/assets/453ff73d-0444-4b70-9575-063262163c6a" />

<img width="809" height="314" alt="image" src="https://github.com/user-attachments/assets/c957937a-cb17-4b4b-83bd-e58b58f0eeb5" />

Activamos el modulo CGI
<img width="537" height="95" alt="image" src="https://github.com/user-attachments/assets/506be704-3a50-4cc2-8b8f-46280700ab1d" />

## 12. Segundo servidor Nginx
Instalamos Ngnix
```bash
apt install nginx -y
```
<img width="732" height="80" alt="image" src="https://github.com/user-attachments/assets/d8b1f625-8435-4c29-93ed-5e6b84fdbc84" />

Creamos un virtualhost para Nginx
```bash
nano /etc/nginx/sites-available/servidor2
```
```text
server {
    listen 8080;
    server_name servidor2.centro.intranet;

    root /var/www/servidor2;
    index index.php index.html index.htm;

    # PHP handling
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    }

    # Evitar mostrar archivos ocultos
    location ~ /\. {
        deny all;
    }

    # Logs
    access_log /var/log/nginx/servidor2_access.log;
    error_log /var/log/nginx/servidor2_error.log;
}
```
<img width="1183" height="384" alt="image" src="https://github.com/user-attachments/assets/37c74968-199c-4110-9087-11626eb7905e" />

Activamos el sitio y reiniciamos nginx

```bash
ln -s /etc/nginx/sites-available/servidor2 /etc/nginx/sites-enabled/
```
<img width="983" height="74" alt="image" src="https://github.com/user-attachments/assets/5509164a-c8cd-4ab5-bc59-47c2213dc38c" />

## 13. Instalacion de phpMyAdmin
Instalamos
```bash
apt install phpmyadmin -y
```
<img width="1164" height="701" alt="image" src="https://github.com/user-attachments/assets/12f77a3d-d965-4506-a689-a515132f47ad" />

