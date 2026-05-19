# Práctica 2º Trimestre - Servidor de Alojamiento Web

**ASIR - Administración de Sistemas Informáticos en Red**
**Curso 2025/2026**

---

## Índice

1. [Introducción y Objetivos](#1-introducción-y-objetivos)
2. [Requisitos del Sistema](#2-requisitos-del-sistema)
3. [Arquitectura de la Solución](#3-arquitectura-de-la-solución)
4. [Instalación de Servicios](#4-instalación-de-servicios)
   - [4.1. Apache2 + PHP](#41-apache2--php)
   - [4.2. MariaDB](#42-mariadb)
   - [4.3. phpMyAdmin](#43-phpmyadmin)
   - [4.4. ProFTPD + TLS](#44-proftpd--tls)
   - [4.5. Bind9 (DNS)](#45-bind9-dns)
   - [4.6. OpenSSH Server](#46-openssh-server)
   - [4.7. Python WSGI](#47-python-wsgi)
5. [Configuración de Servicios](#5-configuración-de-servicios)
   - [5.1. Apache](#51-apache)
   - [5.2. DNS (Bind9)](#52-dns-bind9)
   - [5.3. ProFTPD con TLS](#53-proftpd-con-tls)
   - [5.4. MariaDB](#54-mariadb)
   - [5.5. phpMyAdmin](#55-phpmyadmin)
6. [Scripts de Automatización](#6-scripts-de-automatización)
   - [6.1. crear_subdominio.sh](#61-crear_subdominiosh)
   - [6.2. crear_vhost.sh](#62-crear_vhostsh)
   - [6.3. crear_usuario.sh (Script Maestro)](#63-crear_usuariosh-script-maestro)
7. [Prueba de Funcionamiento](#7-prueba-de-funcionamiento)
8. [Docker (Opcional)](#8-docker-opcional)
9. [Conclusiones](#9-conclusiones)
10. [Referencias](#10-referencias)

---

## 1. Introducción y Objetivos

El objetivo de esta práctica es la instalación, configuración y puesta en marcha de un **servidor de alojamiento web** que permita dar servicio a múltiples clientes de forma automatizada.

### Servicios que ofrece el servidor

| Servicio | Descripción |
|----------|-------------|
| **Web** | Alojamiento de páginas estáticas y dinámicas con PHP |
| **Base de datos** | MySQL/MariaDB administrable con phpMyAdmin |
| **FTP** | Acceso con cifrado TLS para transferencia de archivos |
| **DNS** | Resolución de nombres con subdominios por cliente |
| **SSH/SFTP** | Acceso remoto seguro y transferencia de archivos |
| **Python** | Ejecución de aplicaciones Python mediante WSGI |

### Datos del servidor

| Parámetro | Valor |
|-----------|-------|
| **IP del servidor** | `192.168.8.167` |
| **Dominio principal** | `marisma.local` |
| **Sistema Operativo** | Ubuntu Server 26.04 LTS |
| **Usuario administrador** | `mario` |

---

## 2. Requisitos del Sistema

### Hardware
- Ubuntu Server 26.04 LTS
- Conexión a red con IP estática
- Usuario con privilegios `sudo`

### Paquetes necesarios

| Servicio | Paquete |
|----------|---------|
| Servidor Web | `apache2` |
| PHP | `php libapache2-mod-php` |
| Base de Datos | `mariadb-server` |
| Administración BD | `phpmyadmin php-mbstring php-zip php-gd php-json php-curl` |
| FTP | `proftpd` |
| DNS | `bind9` |
| SSH | `openssh-server` |
| Python | `python3 libapache2-mod-wsgi-py3` |

### Comprobación inicial del sistema

<img width="927" height="727" alt="Screenshot 2026-05-19 093506" src="https://github.com/user-attachments/assets/e26f9617-9c8e-49c0-82ba-ff3fe00efd74" />

---

## 3. Arquitectura de la Solución

```
                    +-------------------+
                    |   Servidor DNS    |
                    |   (Bind9)         |
                    |  marisma.local    |
                    +--------+----------+
                             |
                    +--------+----------+
                    |   Servidor Web    |
                    |   (Apache 2)      |
                    +--------+----------+
                   /         |           \
                  /          |            \
     +-----------+  +-----------+  +-----------+
     | Cliente 1  |  | Cliente 2  |  | Cliente N  |
     | cliente1   |  | cliente2   |  | clienteN   |
     | FTP/SSH    |  | FTP/SSH    |  | FTP/SSH    |
     | PHP/MySQL  |  | PHP/MySQL  |  | PHP/MySQL  |
     +-----------+  +-----------+  +-----------+
```

### Estructura de cada cliente

```
/var/www/html/<usuario>/
├── index.html       # Página principal
├── info.php         # Información de PHP
└── app.wsgi         # Aplicación Python WSGI

Base de datos: bd_<usuario>
Usuario DB:    user_<usuario>
Subdominio:    <usuario>.marisma.local
```

---

## 4. Instalación de Servicios

### 4.1. Apache2 + PHP

```bash
# Actualizar repositorios
sudo apt update && sudo apt upgrade -y

# Instalar Apache2 y PHP
sudo apt install -y apache2 php libapache2-mod-php

# Verificar estado
sudo systemctl status apache2
```

<img width="887" height="469" alt="Screenshot 2026-05-19 093314" src="https://github.com/user-attachments/assets/eb0c671c-c0b8-4ffb-b963-bda8334e8d95" />


### 4.2. MariaDB

```bash
sudo apt install -y mariadb-server
sudo systemctl enable --now mariadb
```

<img width="1702" height="499" alt="Screenshot 2026-05-19 093600" src="https://github.com/user-attachments/assets/066c7004-62d7-469a-872f-ce48e8c7758c" />

### 4.3. phpMyAdmin

```bash
sudo apt install -y phpmyadmin php-mbstring php-zip php-gd php-json php-curl
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

<img width="1718" height="1019" alt="Screenshot 2026-05-19 093727" src="https://github.com/user-attachments/assets/582b103a-0132-48fa-b1af-450b55465c6b" />

### 4.4. ProFTPD + TLS

```bash
sudo apt install -y proftpd
```

<img width="1104" height="363" alt="Screenshot 2026-05-19 093807" src="https://github.com/user-attachments/assets/8e5c6f5b-ab4b-43bb-9d97-ddea304e8570" />

### 4.5. Bind9 (DNS)

```bash
sudo apt install -y bind9
```

<img width="1324" height="497" alt="Screenshot 2026-05-19 093838" src="https://github.com/user-attachments/assets/a2c398a3-5126-4535-8448-c9137d3a884e" />

### 4.6. OpenSSH Server

```bash
# Normalmente viene preinstalado, si no:
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

<img width="1129" height="495" alt="Screenshot 2026-05-19 093911" src="https://github.com/user-attachments/assets/bca46682-74ca-4095-bff1-aadd24b5bbc9" />

### 4.7. Python WSGI

```bash
sudo apt install -y python3 libapache2-mod-wsgi-py3
```


> ```bash
> sudo apache2ctl -M 2>&1 | grep -E "(wsgi|rewrite|ssl)""
> ```
<img width="718" height="102" alt="Screenshot 2026-05-19 094137" src="https://github.com/user-attachments/assets/03958671-40c7-4ca4-9f85-ceab5ae5b4cc" />

---

## 5. Configuración de Servicios

### 5.1. Apache

#### Configuración de ServerName

```bash
echo "ServerName marisma.local" | sudo tee /etc/apache2/conf-available/servername.conf
sudo a2enconf servername
```

#### Habilitación de módulos

```bash
sudo a2enmod rewrite ssl wsgi
sudo systemctl restart apache2
```

#### VirtualHost

Archivo de configuración: `/etc/apache2/sites-available/<usuario>.marisma.conf`

```apache
<VirtualHost *:80>
    ServerAdmin admin@<usuario>.marisma.local
    ServerName www.<usuario>.marisma.local
    ServerAlias <usuario>.marisma.local
    DocumentRoot /var/www/html/<usuario>

    <Directory /var/www/html/<usuario>>
        DirectoryIndex index.html index.php
        Options Indexes FollowSymLinks MultiViews
        AllowOverride all
        Require all granted
    </Directory>

    ErrorLog  /var/log/apache2/<usuario>.marisma.local.error.log
    LogLevel error
    CustomLog /var/log/apache2/<usuario>.marisma.local.access.log combined
</VirtualHost>
```

<img width="982" height="440" alt="Screenshot 2026-05-19 094539" src="https://github.com/user-attachments/assets/02e8f85f-bca2-4fe8-be38-a1a85035ed63" />

### 5.2. DNS (Bind9)

#### Archivo de configuración de zonas: `/etc/bind/named.conf.local`

```bind
zone "marisma.local" {
    type master;
    file "/etc/bind/zones/db.marisma.local";
};

zone "8.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.8.168.192";
};
```

#### Zona directa: `/etc/bind/zones/db.marisma.local`

```bind
$TTL    604800
@       IN      SOA     ns1.marisma.local. admin.marisma.local. (
                    2026051901
                         604800
                          86400
                        2419200
                         604800 )

@       IN      NS      ns1.marisma.local.
ns1     IN      A       192.168.8.167
@       IN      A       192.168.8.167
www     IN      A       192.168.8.167

; cliente1
$ORIGIN cliente1.marisma.local.
@       IN      A       192.168.8.167
www     IN      A       192.168.8.167
```

#### Zona inversa: `/etc/bind/zones/db.8.168.192`

```bind
$TTL    604800
@       IN      SOA     marisma.local. admin.marisma.local. (
                    2026051901
                         604800
                          86400
                        2419200
                         604800 )

@       IN      NS      ns1.marisma.local.
167     IN      PTR     ns1.marisma.local.
167     IN      PTR     marisma.local.
; cliente1
167     IN      PTR     cliente1.marisma.local.
```

#### Verificación de la configuración DNS

```bash
sudo named-checkconf
sudo named-checkzone marisma.local /etc/bind/zones/db.marisma.local
sudo systemctl restart named
```

<img width="829" height="80" alt="Screenshot 2026-05-19 094617" src="https://github.com/user-attachments/assets/906ece5a-9c1f-4943-9284-47f879227228" />

<img width="519" height="146" alt="Screenshot 2026-05-19 095521" src="https://github.com/user-attachments/assets/c2c75f8c-94d4-42be-b4fe-4fb8a0f62dc9" />

### 5.3. ProFTPD con TLS

#### Generación de certificado SSL

```bash
sudo mkdir -p /etc/proftpd/ssl
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
    -keyout /etc/proftpd/ssl/proftpd.key \
    -out /etc/proftpd/ssl/proftpd.crt \
    -subj "/C=ES/ST=Madrid/L=Madrid/O=ASIR/OU=Admin/CN=marisma.local"
```

#### Configuración TLS: `/etc/proftpd/tls.conf`

```conf
<IfModule mod_tls.c>
    TLSEngine on
    TLSLog /var/log/proftpd/tls.log
    TLSProtocol TLSv1.2 TLSv1.3
    TLSRSACertificateFile /etc/proftpd/ssl/proftpd.crt
    TLSRSACertificateKeyFile /etc/proftpd/ssl/proftpd.key
    TLSRequired on
    TLSVerifyClient off
</IfModule>
```

Incluir en `/etc/proftpd/proftpd.conf`:
```conf
Include /etc/proftpd/tls.conf
```

<img width="1717" height="1016" alt="Screenshot 2026-05-19 100221" src="https://github.com/user-attachments/assets/d483792f-edf6-4911-a973-bb92d0fdfa17" />

### 5.4. MariaDB

#### Configuración de seguridad (opcional en entorno de pruebas)

```bash
sudo mysql_secure_installation
```

#### Verificación de bases de datos

```sql
SHOW DATABASES;
```

<img width="526" height="237" alt="Screenshot 2026-05-19 100317" src="https://github.com/user-attachments/assets/dc7fb8bd-454a-44ff-b68f-0f0d068f8682" />


### 5.5. phpMyAdmin

Habilitar en Apache:
```bash
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

**Acceso:** `http://192.168.8.167/phpmyadmin`

<img width="1691" height="885" alt="Screenshot 2026-05-19 101033" src="https://github.com/user-attachments/assets/70ace223-ba3a-4cb5-bf9c-1a4cb976cfb1" />

---

## 6. Scripts de Automatización

Los scripts se encuentran en `/usr/local/bin/` y deben tener permisos de ejecución (`chmod +x`).

### 6.1. crear_subdominio.sh

**Ubicación:** `/usr/local/bin/crear_subdominio.sh`

**Función:** Crea un subdominio en el servidor DNS Bind9, añadiendo registros A (directa) y PTR (inversa).

```bash
#!/bin/bash
# crear_subdominio.sh <nombre_usuario> <ip>

if [ $# -le 1 ]; then
    echo "Error! Uso: $0 <nombre_usuario> <ip>"
    exit 1
fi

USER=$1
IP=$2
SUB_DOMAIN="${USER}.marisma.local"
ZONE_FILE="/etc/bind/zones/db.marisma.local"
REV_ZONE_FILE="/etc/bind/zones/db.8.168.192"

echo "[DNS] Creando subdominio ${SUB_DOMAIN}"

# Añadir entrada en zona directa
echo "" >> $ZONE_FILE
echo "; ${USER}" >> $ZONE_FILE
echo "\$ORIGIN ${SUB_DOMAIN}." >> $ZONE_FILE
echo "@       IN      A       ${IP}" >> $ZONE_FILE
echo "www     IN      A       ${IP}" >> $ZONE_FILE

# Añadir entrada en zona inversa
OCTET=$(echo $IP | cut -d. -f4)
echo "" >> $REV_ZONE_FILE
echo "; ${USER}" >> $REV_ZONE_FILE
echo "${OCTET}     IN      PTR     ${SUB_DOMAIN}." >> $REV_ZONE_FILE

# Recargar DNS
systemctl reload named 2>/dev/null || systemctl reload bind9 2>/dev/null
echo "[DNS] Subdominio ${SUB_DOMAIN} creado correctamente"
```

<img width="718" height="675" alt="Screenshot 2026-05-19 101225" src="https://github.com/user-attachments/assets/0261e3cf-4a7b-4aa1-9bb9-b1c6f157c4d0" />


### 6.2. crear_vhost.sh

**Ubicación:** `/usr/local/bin/crear_vhost.sh`

**Función:** Crea un VirtualHost en Apache2, el directorio web y la página `index.html` por defecto.

```bash
#!/bin/bash
# crear_vhost.sh <nombre_usuario>

if [ $# -eq 0 ]; then
    echo "Error! Uso: $0 <nombre_usuario>"
    exit 1
fi

USER=$1
CONF="${USER}.marisma.conf"
PATH_AVAILABLE="/etc/apache2/sites-available/${CONF}"
SUB_DOMAIN="${USER}.marisma.local"
DOCUMENT_ROOT="/var/www/html/${USER}"
INDEX="${DOCUMENT_ROOT}/index.html"

echo "[Apache] Creando VirtualHost para ${SUB_DOMAIN}"

if [ ! -d "$DOCUMENT_ROOT" ]; then
    mkdir -p "$DOCUMENT_ROOT"
    chown -R "${USER}:${USER}" "$DOCUMENT_ROOT" 2>/dev/null
fi

cat > $PATH_AVAILABLE <<VHOSTEOF
<VirtualHost *:80>
    ServerAdmin admin@${SUB_DOMAIN}
    ServerName www.${SUB_DOMAIN}
    ServerAlias ${SUB_DOMAIN}
    DocumentRoot ${DOCUMENT_ROOT}
    <Directory ${DOCUMENT_ROOT}>
        DirectoryIndex index.html index.php
        Options Indexes FollowSymLinks MultiViews
        AllowOverride all
        Require all granted
    </Directory>
    ErrorLog  /var/log/apache2/${SUB_DOMAIN}.error.log
    LogLevel error
    CustomLog /var/log/apache2/${SUB_DOMAIN}.access.log combined
</VirtualHost>
VHOSTEOF

# Crear index.html
cat > $INDEX <<HTMLEOF
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Bienvenido - ${SUB_DOMAIN}</title>
</head>
<body>
    <h1>Subdominio: ${SUB_DOMAIN}</h1>
    <p>Usuario: ${USER}</p>
    <p>Bienvenido a tu espacio de alojamiento web.</p>
</body>
</html>
HTMLEOF

# Crear info.php
echo "<?php phpinfo(); ?>" > ${DOCUMENT_ROOT}/info.php

chown -R "${USER}:${USER}" "$DOCUMENT_ROOT" 2>/dev/null
a2ensite $CONF 2>/dev/null
systemctl reload apache2

echo "[Apache] VirtualHost ${SUB_DOMAIN} creado y habilitado"
```

<img width="1200" height="1376" alt="Screenshot 2026-05-19 101357" src="https://github.com/user-attachments/assets/a6ff236d-e684-45cf-9c05-04e0959cdf38" />


<img width="888" height="142" alt="Screenshot 2026-05-19 101404" src="https://github.com/user-attachments/assets/ac7f767d-5951-44de-be86-f184d203759a" />

### 6.3. crear_usuario.sh (Script Maestro)

**Ubicación:** `/usr/local/bin/crear_usuario.sh`

**Función:** Script completo que automatiza todo el proceso de alta de un nuevo cliente en **6 pasos**:

1. **Creación del usuario del sistema** (con home y contraseña aleatoria)
2. **Creación del directorio web** (`/var/www/html/<usuario>/`)
3. **Creación del VirtualHost** en Apache (llama a `crear_vhost.sh`)
4. **Creación del subdominio** en DNS (llama a `crear_subdominio.sh`)
5. **Creación de base de datos** y usuario MySQL con todos los privilegios
6. **Configuración de Python WSGI** (archivo `app.wsgi`)

```bash
#!/bin/bash
# crear_usuario.sh <nombre_usuario> <ip>

if [ $# -ne 2 ]; then
    echo "Uso: $0 <nombre_usuario> <ip>"
    exit 1
fi

USER=$1
IP=$2
PASS_USER=$(openssl rand -base64 12)
SUB_DOMAIN="${USER}.marisma.local"
DOCUMENT_ROOT="/var/www/html/${USER}"
DB_NAME="bd_${USER}"
DB_USER="user_${USER}"
DB_PASS=$(openssl rand -base64 12)

echo "=========================================="
echo "Creando cliente: ${USER}"
echo "=========================================="

# Paso 1: Crear usuario del sistema
echo "[1/6] Creando usuario del sistema..."
useradd -m -d "/home/${USER}" -s /bin/bash "${USER}" 2>/dev/null
echo "${USER}:${PASS_USER}" | chpasswd

# Paso 2: Crear directorio web
echo "[2/6] Creando directorio web..."
mkdir -p "${DOCUMENT_ROOT}"
chown -R "${USER}:${USER}" "${DOCUMENT_ROOT}"

# Paso 3: Crear VirtualHost
echo "[3/6] Creando VirtualHost en Apache..."
/usr/local/bin/crear_vhost.sh "${USER}"

# Paso 4: Crear subdominio DNS
echo "[4/6] Creando subdominio en DNS..."
/usr/local/bin/crear_subdominio.sh "${USER}" "${IP}"

# Paso 5: Crear base de datos MySQL
echo "[5/6] Creando base de datos MySQL..."
mysql -u root <<SQL
CREATE DATABASE IF NOT EXISTS ${DB_NAME};
CREATE USER IF NOT EXISTS '${DB_USER}'@'localhost' IDENTIFIED BY '${DB_PASS}';
GRANT ALL PRIVILEGES ON ${DB_NAME}.* TO '${DB_USER}'@'localhost';
FLUSH PRIVILEGES;
SQL

# Paso 6: Configurar Python WSGI
echo "[6/6] Configurando soporte Python WSGI..."
cat > "${DOCUMENT_ROOT}/app.wsgi" <<PYEOF
def application(environ, start_response):
    status = '200 OK'
    output = '<h1>Aplicacion Python WSGI funcionando</h1>'
    output += '<p>Subdominio: ${SUB_DOMAIN}</p>'
    output += '<p>Usuario: ${USER}</p>'
    response_headers = [('Content-type', 'text/html; charset=utf-8')]
    start_response(status, response_headers)
    return [output.encode('utf-8')]
PYEOF
chown "${USER}:${USER}" "${DOCUMENT_ROOT}/app.wsgi"

echo "=========================================="
echo "Cliente ${USER} creado correctamente"
echo "=========================================="
echo "Subdominio: http://${SUB_DOMAIN}"
echo "Directorio web: ${DOCUMENT_ROOT}"
echo "FTP/SSH usuario: ${USER} / ${PASS_USER}"
echo "Base de datos: ${DB_NAME} / ${DB_USER} / ${DB_PASS}"
echo "Python WSGI: http://${SUB_DOMAIN}/app.wsgi"
echo "phpMyAdmin: http://192.168.8.167/phpmyadmin"
```

<img width="855" height="1397" alt="Screenshot 2026-05-19 101637" src="https://github.com/user-attachments/assets/b19a8971-8e37-4d40-b1a8-6e37f098f8dd" />

<img width="827" height="454" alt="Screenshot 2026-05-19 101701" src="https://github.com/user-attachments/assets/7570dff3-b617-417e-a0fb-3da3cc10c225" />


---

## 7. Prueba de Funcionamiento

### 7.1. Creación de un cliente de prueba

Ejecución del script maestro para crear el cliente `cliente1`:

```bash
sudo /usr/local/bin/crear_usuario.sh cliente1 192.168.8.167
```

<img width="853" height="916" alt="Screenshot 2026-05-19 102310" src="https://github.com/user-attachments/assets/590b44b3-975d-4a9f-9947-75d75232d89f" />

### 7.2. Verificación de resultados

#### Usuario del sistema

```bash
id cliente1
```

<img width="558" height="137" alt="Screenshot 2026-05-19 102341" src="https://github.com/user-attachments/assets/a4f15349-180e-4724-9faa-aab6a77ca3e7" />

#### Directorio web

```bash
ls -la /var/www/html/cliente1/
```

<img width="559" height="425" alt="Screenshot 2026-05-19 102412" src="https://github.com/user-attachments/assets/395a38af-9f7b-4c68-bc37-1bca49128d90" />

#### VirtualHost de Apache

```bash
sudo apache2ctl -S | grep cliente1
```

<img width="1005" height="521" alt="Screenshot 2026-05-19 102436" src="https://github.com/user-attachments/assets/e6e07d34-eb10-4a03-9373-4b267441c07c" />

#### Registro DNS

```bash
grep cliente1 /etc/bind/zones/db.marisma.local
```

<img width="678" height="362" alt="Screenshot 2026-05-19 102641" src="https://github.com/user-attachments/assets/68344762-47b1-4c9f-8734-0f024e6d6a61" />

#### Base de datos MySQL

```bash
sudo mysql -e "SHOW DATABASES LIKE '%cliente1%';"
sudo mysql -e "SHOW GRANTS FOR 'user_cliente1'@'localhost';"
```

<img width="1106" height="274" alt="Screenshot 2026-05-19 102821" src="https://github.com/user-attachments/assets/03a67e81-63d1-44ae-bea0-d7a6151fb272" />

#### Acceso web

```bash
curl -s -H "Host: cliente1.marisma.local" http://192.168.8.167/
```

<img width="934" height="331" alt="Screenshot 2026-05-19 103702" src="https://github.com/user-attachments/assets/99baf267-bf06-4c2b-a069-3aa4de2ae0ea" />

#### PHP funcionando

```bash
curl -s -H "Host: cliente1.marisma.local" http://192.168.8.167/info.php | head -20
```

<img width="1717" height="1439" alt="Screenshot 2026-05-19 103933" src="https://github.com/user-attachments/assets/6d89d689-c58d-43e5-a1d4-1a95b32b005d" />

#### Python WSGI

```bash
curl -s -H "Host: cliente1.marisma.local" http://192.168.8.167/app.wsgi
```

<img width="1716" height="493" alt="Screenshot 2026-05-19 104352" src="https://github.com/user-attachments/assets/9e3092c6-c000-4e40-b55c-98876e82ba6d" />

#### Acceso FTP con TLS

<img width="1181" height="942" alt="Screenshot 2026-05-19 105211" src="https://github.com/user-attachments/assets/b30a2100-54fc-46d3-b9ca-2dcbf3b5e345" />

<img width="1180" height="936" alt="Screenshot 2026-05-19 105316" src="https://github.com/user-attachments/assets/f49e665d-d528-45cb-9c36-7365e6311341" />


#### Acceso SSH

```bash
ssh cliente1@192.168.8.167
```

<img width="1088" height="580" alt="Screenshot 2026-05-19 105526" src="https://github.com/user-attachments/assets/6c88a5e8-57ad-44db-99b8-27e38fad8c87" />

#### phpMyAdmin

<img width="1817" height="1018" alt="Screenshot 2026-05-19 111317" src="https://github.com/user-attachments/assets/8fd726fb-5d17-4396-a26a-f570c021a42e" />

### 7.3. Resumen de comprobaciones

| Comprobación | Comando | Resultado esperado |
|-------------|---------|-------------------|
| Apache | `systemctl status apache2` | `active (running)` |
| MariaDB | `systemctl status mariadb` | `active (running)` |
| ProFTPD | `systemctl status proftpd` | `active (running)` |
| Bind9 | `systemctl status named` | `active (running)` |
| SSH | `systemctl status ssh` | `active (running)` |
| Usuario creado | `id cliente1` | `uid=1001(cliente1)` |
| Web accesible | `curl http://cliente1.marisma.local` | Código HTML 200 |
| DNS resuelve | `nslookup cliente1.marisma.local` | IP `192.168.8.167` |
| Base de datos | `mysql -e "SHOW DATABASES"` | `bd_cliente1` |
| PHP | curl al `info.php` | `phpinfo()` |
| Python | curl al `app.wsgi` | `"Python WSGI funcionando"` |

<img width="1187" height="689" alt="Screenshot 2026-05-19 111432" src="https://github.com/user-attachments/assets/a60f15f1-e0b4-4370-a185-d802f61d27bc" />
---

<img width="1291" height="497" alt="Screenshot 2026-05-19 111516" src="https://github.com/user-attachments/assets/f45f7721-7279-4b85-9362-e9cc5ce32b5e" />

<img width="1098" height="367" alt="Screenshot 2026-05-19 111544" src="https://github.com/user-attachments/assets/66cc1f54-d8c5-4374-89c0-43f35dac475b" />

<img width="918" height="283" alt="Screenshot 2026-05-19 111606" src="https://github.com/user-attachments/assets/31c1149d-1464-4cb0-8b91-0a2b70611d6a" />


<img width="959" height="277" alt="Screenshot 2026-05-19 111627" src="https://github.com/user-attachments/assets/9f1c511a-9d2a-4f58-a4f8-f497260203f6" />

## 8. Docker (Opcional)

*Este apartado puede suponer hasta el 10% adicional de la nota.*

### Estructura propuesta

```
docker/
├── docker-compose.yml
├── dns/
│   ├── Dockerfile
│   └── named.conf
├── web/
│   ├── Dockerfile
│   └── sites/
├── db/
│   └── Dockerfile
└── scripts/
    └── deploy.sh
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  dns:
    build: ./dns
    container_name: dns-server
    ports:
      - "53:53/udp"
      - "53:53/tcp"
    volumes:
      - ./dns/zones:/etc/bind/zones
    networks:
      web-network:
        ipv4_address: 172.20.0.2

  web:
    build: ./web
    container_name: web-server
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./www:/var/www/html
    networks:
      web-network:
        ipv4_address: 172.20.0.3
    depends_on:
      - dns
      - db

  db:
    image: mariadb:latest
    container_name: db-server
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: phpmyadmin
    volumes:
      - db-data:/var/lib/mysql
    networks:
      web-network:
        ipv4_address: 172.20.0.4

volumes:
  db-data:

networks:
  web-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

> 📸 **CAPTURA 31:** `docker-compose ps` o `docker ps` — Contenedores en ejecución
>
> *(Insertar aquí captura de pantalla)*

---

## 9. Conclusiones

En esta práctica se ha conseguido:

1. ✅ **Instalar y configurar** un servidor Ubuntu con todos los servicios necesarios para alojamiento web.
2. ✅ **Automatizar** el proceso de alta de clientes mediante 3 scripts que crean el usuario, el VirtualHost de Apache, el subdominio DNS y la base de datos MySQL en **6 pasos**.
3. ✅ **Asegurar** las comunicaciones FTP mediante cifrado TLS.
4. ✅ **Proporcionar** a cada cliente:
   - Alojamiento de páginas web estáticas (HTML) y dinámicas (PHP)
   - Base de datos MySQL con phpMyAdmin
   - Subdominio propio con resolución DNS directa e inversa
   - Acceso FTP con TLS, SSH y SFTP
   - Capacidad de ejecutar aplicaciones Python

> 📸 **CAPTURA 32:** Esquema final del despliegue — Todo funcionando
>
> *(Insertar aquí captura de pantalla final)*

---

## 10. Referencias

- [Tutorial de Shell Scripting](https://www.shellscript.sh/index.html)
- [Creación de subdominios con script en Bind9](http://bash.cyberciti.biz/domain/create-bind9-domain-zone-configuration-file/)
- [Python para administradores de sistemas](https://python-for-system-administrators.readthedocs.io/en/latest/)
- [Documentación oficial de Apache HTTP Server](https://httpd.apache.org/docs/)
- [Documentación de ProFTPD](http://www.proftpd.org/docs/)
- [Documentación de Bind9](https://bind9.readthedocs.io/)
- [Documentación de MariaDB](https://mariadb.com/docs/)
- [Documentación de Docker Compose](https://docs.docker.com/compose/)

---

*Documentación generada para la Práctica 2º Trimestre - ASIR 2025/2026*
*Servidor: 192.168.8.167 | Usuario admin: mario*
