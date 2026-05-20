# DNS Activity #5 - Caching & Forwarding DNS Server

## 1. Instalación de BIND9

Actualizamos los paquetes e instalamos bind9 junto con sus utilidades:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install bind9 bind9utils bind9-doc -y
```
<img width="1718" height="194" alt="image" src="https://github.com/user-attachments/assets/d27fd1a3-5363-4abd-ab08-d5a29484a306" />

<img width="1718" height="503" alt="image" src="https://github.com/user-attachments/assets/493ef66c-36bf-462e-8553-269ce23fea5e" />


## 2. Configuración como servidor caché

Editamos el archivo de configuración principal:

```bash
sudo nano /etc/bind/named.conf.options
```

### 2.1 Crear ACL de clientes permitidos

Encima del bloque `options{}`, añadimos una ACL con las redes que pueden consultar nuestro DNS:

```
acl goodclients {
    192.168.X.0/24;
    localhost;
    localnets;
};
```

![ACL en named.conf.options](capturas/actividad5/acl.png)

### 2.2 Configurar opciones de caché

Dentro de `options{}`, configuramos la recursión y permitimos consultas desde la ACL:

```
options {
    directory "/var/cache/bind";
    recursion yes;
    allow-query { goodclients; };
    allow-query-cache { goodclients; };

    dnssec-validation auto;
    listen-on { any; };
    listen-on-v6 { none; };
};
```

![Configuración caché](capturas/actividad5/config_cache.png)

### 2.3 Comprobar sintaxis y reiniciar

```bash
sudo named-checkconf
sudo systemctl restart bind9
sudo systemctl status bind9
```

![Comprobación sintaxis caché](capturas/actividad5/checkconf_cache.png)

### 2.4 Verificar que escucha en puerto 53

```bash
sudo ss -tulpn | grep :53
```

![Puerto 53 escuchando](capturas/actividad5/ss_port53.png)

## 3. Configuración como servidor forwarding

Sobre el mismo archivo `named.conf.options`, añadimos los reenviadores dentro de `options{}`:

```
options {
    directory "/var/cache/bind";
    recursion yes;
    allow-query { goodclients; };
    allow-query-cache { goodclients; };

    forwarders {
        8.8.8.8;
        1.1.1.1;
    };
    forward only;

    dnssec-validation auto;
    listen-on { any; };
    listen-on-v6 { none; };
};
```

> **Nota:** Si `named-checkconf` da error con `dnssec-enable`, elimina esa línea porque es obsoleta en versiones recientes de BIND.

![Configuración forwarding](capturas/actividad5/config_forward.png)

```bash
sudo named-checkconf
sudo systemctl restart bind9
```

### 3.1 Abrir puerto en firewall

```bash
sudo ufw allow Bind9
sudo ufw reload
```

![Firewall allow Bind9](capturas/actividad5/ufw_allow.png)

## 4. Configuración del cliente

Editamos `/etc/resolv.conf` del cliente para que use nuestro servidor DNS:

```bash
sudo nano /etc/resolv.conf
```

Añadimos:

```
nameserver 192.168.X.XX
```

![resolv.conf cliente](capturas/actividad5/resolv_conf.png)

> **Nota persistente:** Si el sistema sobreescribe resolv.conf, instala `resolvconf`:
> ```bash
> sudo apt install resolvconf -y
> ```
> O edita `/etc/netplan/` correspondiente.

## 5. Comprobaciones

### 5.1 Ping

```bash
ping -c 4 google.com
ping -c 4 8.8.8.8
```

![Ping a google.com](capturas/actividad5/ping_google.png)

### 5.2 nslookup

```bash
nslookup google.com
nslookup 8.8.8.8
```

![nslookup google](capturas/actividad5/nslookup_google.png)

### 5.3 dig

```bash
dig google.com
dig -x 8.8.8.8
```

![dig google](capturas/actividad5/dig_google.png)

### 5.4 Logs del servidor

```bash
sudo tail -f /var/log/syslog | grep named
```

![Logs del servidor DNS](capturas/actividad5/logs_named.png)

---
