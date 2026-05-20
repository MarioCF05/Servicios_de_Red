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

<img width="1128" height="517" alt="image" src="https://github.com/user-attachments/assets/6842ec51-37eb-44ea-932e-d218cc3caef7" />


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

<img width="1718" height="352" alt="image" src="https://github.com/user-attachments/assets/e60ddd06-0c00-4647-b0be-19978a48e48f" />


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
ns1     IN      A       192.168.8.190

; Servidores de correo
mail1   IN      A       192.168.8.190
mail2   IN      A       192.168.8.190
@       IN      MX      10 mail1.marisma.intranet.
@       IN      MX      20 mail2.marisma.intranet.

; Servidores web
www     IN      A       192.168.8.190
departamentos IN    A   192.168.8.190

; Servidor FTP
ftp1    IN      A       192.168.8.190
```

<img width="1145" height="701" alt="image" src="https://github.com/user-attachments/assets/fb6c2c50-1a49-492e-b746-726124fb55f0" />


### FQDN definidos en la zona:

| FQDN | Tipo | IP |
|------|------|----|
| `ns1.marisma.intranet` | A | 192.168.8.190 |
| `ftp1.marisma.intranet` | A | 192.168.8.190 |
| `mail1.marisma.intranet` | A | 192.168.8.190 |
| `mail2.marisma.intranet` | A | 192.168.8.190 |
| `www.marisma.intranet` | A | 192.168.8.190 |
| `departamentos.marisma.intranet` | A | 192.168.8.190 |

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

<img width="1160" height="424" alt="image" src="https://github.com/user-attachments/assets/d98c2d00-84fb-44cf-a0bd-4433f6e93e9a" />


## 5. Comprobar sintaxis

```bash
sudo named-checkconf
sudo named-checkzone marisma.intranet /etc/bind/db.marisma.intranet
sudo named-checkzone X.168.192.in-addr.arpa /etc/bind/db.192.168.X
```

<img width="1717" height="349" alt="image" src="https://github.com/user-attachments/assets/8ac4077c-f67a-4711-8440-086ca5727ee9" />


## 6. Reiniciar servicio

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

<img width="1089" height="558" alt="image" src="https://github.com/user-attachments/assets/116c2500-cab5-41a7-8709-59fec44ef393" />


## 7. Configurar el cliente

En la máquina cliente, editamos `/etc/resolv.conf`:

```bash
sudo nano /etc/resolv.conf
```

```
search marisma.intranet
nameserver 192.168.8.190
```

<img width="1099" height="519" alt="image" src="https://github.com/user-attachments/assets/c72ac800-9b90-4a5e-aa66-39779957ac6d" />


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

<img width="1420" height="652" alt="image" src="https://github.com/user-attachments/assets/46438944-7171-4d4d-8851-a6951a80f86c" />


### 8.2 Consulta NS

```bash
dig marisma.intranet NS
```

<img width="945" height="510" alt="image" src="https://github.com/user-attachments/assets/883205d6-99e0-4c1c-b900-71f6252c8cc2" />


### 8.3 Consulta MX

```bash
dig marisma.intranet MX
```

<img width="1069" height="583" alt="image" src="https://github.com/user-attachments/assets/7c7ee11f-c34a-4dd8-a74d-b79812614cf6" />


### 8.4 Consultas A (directas)

```bash
dig www.marisma.intranet
dig ftp1.marisma.intranet
dig mail1.marisma.intranet
dig ns1.marisma.intranet
```

<img width="762" height="1242" alt="image" src="https://github.com/user-attachments/assets/f4abc70d-36d3-4aab-801c-1aa35ebc6b61" />
<img width="736" height="464" alt="image" src="https://github.com/user-attachments/assets/7a04c87e-cc09-4fd8-bab3-f91bd9110684" />


### 8.5 Resolución inversa

```bash
dig -x 192.168.8.190
```

<img width="733" height="542" alt="image" src="https://github.com/user-attachments/assets/12455c50-e604-4c5f-ab59-2f7c8aa80f4b" />


### 8.6 Ping y nslookup

```bash
ping -c 2 www.marisma.intranet
nslookup www.marisma.intranet
```

<img width="835" height="309" alt="image" src="https://github.com/user-attachments/assets/5be3fbd6-6518-4302-8ff2-3c83129a9e2f" />


---
