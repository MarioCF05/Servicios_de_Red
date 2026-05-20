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

<img width="1718" height="203" alt="image" src="https://github.com/user-attachments/assets/6ebd1949-6fe9-4fe3-97c5-51c0ef32ba5c" />


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

<img width="1717" height="719" alt="image" src="https://github.com/user-attachments/assets/21c97164-9f48-432d-ade8-ff10e6b9f366" />




### 2.3 Comprobar sintaxis y reiniciar

```bash
sudo named-checkconf
sudo systemctl restart bind9
sudo systemctl status bind9
```

<img width="1715" height="721" alt="image" src="https://github.com/user-attachments/assets/2c02b426-70f3-4a91-a120-2fe9e945d8e1" />


### 2.4 Verificar que escucha en puerto 53

```bash
sudo ss -tulpn | grep :53
```

<img width="1717" height="753" alt="image" src="https://github.com/user-attachments/assets/7f2664c9-9910-4310-a7bf-cf75d3d2ca0a" />


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

<img width="1718" height="717" alt="image" src="https://github.com/user-attachments/assets/1c2ee1b2-556f-476b-8381-40dc7c2e6a3e" />


```bash
sudo named-checkconf
sudo systemctl restart bind9
```

### 3.1 Abrir puerto en firewall

```bash
sudo ufw allow Bind9
sudo ufw reload
```

<img width="1717" height="185" alt="image" src="https://github.com/user-attachments/assets/8392bfa9-32ad-43be-b802-e0fcbb5a9602" />


## 4. Configuración del cliente

Editamos `/etc/resolv.conf` del cliente para que use nuestro servidor DNS:

```bash
sudo nano /etc/resolv.conf
```

Añadimos:

```
nameserver 192.168.X.XX
```

<img width="1211" height="726" alt="image" src="https://github.com/user-attachments/assets/a2a4651f-ea28-42eb-bd4e-b7e0179b4b37" />


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

<img width="1276" height="499" alt="image" src="https://github.com/user-attachments/assets/7a7cd937-1050-45bc-8bcd-710708765145" />


### 5.2 nslookup

```bash
nslookup google.com
nslookup 8.8.8.8
```

<img width="1281" height="495" alt="image" src="https://github.com/user-attachments/assets/bd390d65-a894-46d1-9c1a-d3f378ba7bde" />


### 5.3 dig

```bash
dig google.com
dig -x 8.8.8.8
```

<img width="946" height="638" alt="image" src="https://github.com/user-attachments/assets/ac76b84b-6927-4e25-9234-89422301ed13" />


### 5.4 Logs del servidor

```bash
sudo tail -f /var/log/syslog | grep named
```

<img width="1715" height="363" alt="image" src="https://github.com/user-attachments/assets/29fe977d-ab3a-438f-b116-ebf847ec9115" />


---
