# DNS Activity #6 - Master DNS Server

## 1. Configuración previa

Partimos de un servidor con BIND9 instalado (desde la Activity #5). Vamos a configurarlo como **servidor maestro autoritativo** para el dominio `marisma.intranet` y su zona de resolución inversa.

### 1.1 Deshabilitar recursión (servidor autoritativo)

Editamos `/etc/bind/named.conf.options` y dejamos la recursión desactivada:

```
options {
    directory "/var/cache/bind";
    recursion no;
    allow-query { any; };

    dnssec-validation auto;
    listen-on { any; };
    listen-on-v6 { none; };
};
```

![named.conf.options](capturas/actividad6/options.png)

## 2. Definir las zonas en named.conf.local

Editamos el archivo de configuración de zonas locales:

```bash
sudo nano /etc/bind/named.conf.local
```

Añadimos las zonas directa e inversa:

```
zone "marisma.intranet" {
    type master;
    file "/etc/bind/db.marisma.intranet";
};

zone "X.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.X";
};
```

> **Importante:** Sustituye `X` por el tercer octeto de tu red (ej: 195, 206, 197, etc.)

![named.conf.local](capturas/actividad6/named_conf_local.png)

## 3. Crear archivo de zona directa

Creamos el archivo de zona para `marisma.intranet`:

```bash
sudo cp /etc/bind/db.local /etc/bind/db.marisma.intranet
sudo nano /etc/bind/db.marisma.intranet
```

Contenido del archivo:

```dns
;
; Archivo de zona directa para marisma.intranet
;
$TTL    604800
@       IN      SOA     ns1.marisma.intranet. admin.marisma.intranet. (
                        2024052001      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL

; Servidores de nombres
@       IN      NS      ns1.marisma.intranet.

; Servidor DNS
ns1     IN      A       192.168.X.27

; Servidores de correo
mail1   IN      A       192.168.X.27
mail2   IN      A       192.168.X.27
@       IN      MX      10 mail1.marisma.intranet.
@       IN      MX      20 mail2.marisma.intranet.

; Servidores web
www     IN      A       192.168.X.27
departamentos IN    A   192.168.X.27

; Servidor FTP
ftp1    IN      A       192.168.X.27
```

![Zona directa](capturas/actividad6/zona_directa.png)

### FQDN definidos en la zona:

| FQDN | Tipo | IP |
|------|------|----|
| `ns1.marisma.intranet` | A | 192.168.X.27 |
| `ftp1.marisma.intranet` | A | 192.168.X.27 |
| `mail1.marisma.intranet` | A | 192.168.X.27 |
| `mail2.marisma.intranet` | A | 192.168.X.27 |
| `www.marisma.intranet` | A | 192.168.X.27 |
| `departamentos.marisma.intranet` | A | 192.168.X.27 |

## 4. Crear archivo de zona inversa

```bash
sudo cp /etc/bind/db.127 /etc/bind/db.192.168.X
sudo nano /etc/bind/db.192.168.X
```

```dns
;
; Archivo de zona inversa para 192.168.X.0/24
;
$TTL    604800
@       IN      SOA     ns1.marisma.intranet. admin.marisma.intranet. (
                        2024052001      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL

; Servidores de nombres
@       IN      NS      ns1.marisma.intranet.

; Registros PTR
27      IN      PTR     ns1.marisma.intranet.
27      IN      PTR     www.marisma.intranet.
27      IN      PTR     mail1.marisma.intranet.
27      IN      PTR     mail2.marisma.intranet.
27      IN      PTR     ftp1.marisma.intranet.
27      IN      PTR     departamentos.marisma.intranet.
```

![Zona inversa](capturas/actividad6/zona_inversa.png)

## 5. Comprobar sintaxis

```bash
sudo named-checkconf
sudo named-checkzone marisma.intranet /etc/bind/db.marisma.intranet
sudo named-checkzone X.168.192.in-addr.arpa /etc/bind/db.192.168.X
```

![Comprobación sintaxis](capturas/actividad6/checkconf.png)

## 6. Reiniciar servicio

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

![Restart bind9](capturas/actividad6/restart_bind9.png)

## 7. Configurar el cliente

En la máquina cliente, editamos `/etc/resolv.conf`:

```bash
sudo nano /etc/resolv.conf
```

```
search marisma.intranet
nameserver 192.168.X.27
```

![resolv.conf cliente](capturas/actividad6/resolv_conf_cliente.png)

### 7.1 Si ping no funciona (nsswitch.conf)

Si `dig` resuelve pero `ping` no, editar `/etc/nsswitch.conf`:

```
hosts: files dns
```

(O eliminar `mdns4` de la línea)

```bash
sudo apt-get remove libnss-mdns -y
```

## 8. Comprobaciones

### 8.1 Consulta SOA

```bash
dig marisma.intranet SOA
```

![Consulta SOA](capturas/actividad6/dig_soa.png)

### 8.2 Consulta NS

```bash
dig marisma.intranet NS
```

![Consulta NS](capturas/actividad6/dig_ns.png)

### 8.3 Consulta MX

```bash
dig marisma.intranet MX
```

![Consulta MX](capturas/actividad6/dig_mx.png)

### 8.4 Consultas A (directas)

```bash
dig www.marisma.intranet
dig ftp1.marisma.intranet
dig mail1.marisma.intranet
dig ns1.marisma.intranet
```

![Consultas A](capturas/actividad6/dig_a.png)

### 8.5 Resolución inversa

```bash
dig -x 192.168.X.27
```

![Resolución inversa](capturas/actividad6/dig_inversa.png)

### 8.6 Ping y nslookup

```bash
ping -c 2 www.marisma.intranet
nslookup www.marisma.intranet
```

![Ping y nslookup](capturas/actividad6/ping_nslookup.png)

---
