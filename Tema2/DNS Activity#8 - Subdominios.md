# DNS Activity #8 - Subdominios

## Introducción

Configurar un DNS primario para el dominio `iesmarisma.intranet` con un subdominio `informatica.iesmarisma.intranet`.

Se pueden usar dos métodos:
1. **Subdominio virtual** (todo en el mismo archivo de zona)
2. **Delegación del subdominio** (zonas separadas con NS propio)

> En esta guía se explican ambos métodos.

---

## Método 1: Subdominio virtual

Este método consiste en definir los registros del subdominio directamente en el archivo de zona del dominio principal.

### 1.1 Crear zona del dominio principal

Editamos `/etc/bind/named.conf.local`:

```bash
sudo nano /etc/bind/named.conf.local
```

```
zone "iesmarisma.intranet" {
    type master;
    file "/etc/bind/db.iesmarisma.intranet";
};
```

### 1.2 Crear archivo de zona

```bash
sudo nano /etc/bind/db.iesmarisma.intranet
```

```dns
;
; Zona para iesmarisma.intranet con subdominio virtual
;
$TTL    604800
@       IN      SOA     ns1.iesmarisma.intranet. admin.iesmarisma.intranet. (
                        2024052001      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL

; NS
@       IN      NS      ns1.iesmarisma.intranet.

; Servidor DNS
ns1     IN      A       192.168.X.27

; Hosts del dominio principal
www     IN      A       192.168.X.27
ftp     IN      A       192.168.X.27
smtp    IN      A       192.168.X.27

; Hosts del subdominio informatica (subdominio virtual)
www.informatica  IN      A       192.168.X.27
ftp.informatica  IN      A       192.168.X.27
smtp.informatica IN      A       192.168.X.27
```

![Zona con subdominio virtual](capturas/actividad8/zona_virtual.png)

> **Nota:** Al escribir `www.informatica` sin punto al final, BIND lo completa como `www.informatica.iesmarisma.intranet`.

### 1.3 Comprobar y reiniciar

```bash
sudo named-checkconf
sudo named-checkzone iesmarisma.intranet /etc/bind/db.iesmarisma.intranet
sudo systemctl restart bind9
```

![Checkconf subdominio virtual](capturas/actividad8/checkconf_virtual.png)

---

## Método 2: Delegación del subdominio (recomendado para entornos reales)

Creamos una zona separada para el subdominio, permitiendo gestión independiente.

### 2.1 Configurar dominio padre

Editamos el archivo de zona del dominio principal `/etc/bind/db.iesmarisma.intranet`:

```dns
;
; Zona para iesmarisma.intranet
;
$TTL    604800
@       IN      SOA     ns1.iesmarisma.intranet. admin.iesmarisma.intranet. (
                        2024052001      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL

; NS del dominio principal
@       IN      NS      ns1.iesmarisma.intranet.

; Glue record para el subdominio
informatica     IN      NS      ns1.informatica.iesmarisma.intranet.
ns1.informatica IN      A       192.168.X.27

; Hosts del dominio principal
ns1     IN      A       192.168.X.27
www     IN      A       192.168.X.27
ftp     IN      A       192.168.X.27
smtp    IN      A       192.168.X.27
```

![Zona padre con delegación](capturas/actividad8/zona_padre_delegada.png)

### 2.2 Configurar la zona del subdominio

Añadimos la nueva zona en `/etc/bind/named.conf.local`:

```
zone "informatica.iesmarisma.intranet" {
    type master;
    file "/etc/bind/db.informatica.iesmarisma.intranet";
};
```

![named.conf.local subdominio](capturas/actividad8/named_conf_local_sub.png)

### 2.3 Crear archivo de zona del subdominio

```bash
sudo nano /etc/bind/db.informatica.iesmarisma.intranet
```

```dns
;
; Zona delegada para informatica.iesmarisma.intranet
;
$TTL    604800
@       IN      SOA     ns1.informatica.iesmarisma.intranet. admin.iesmarisma.intranet. (
                        2024052001      ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL

; NS
@       IN      NS      ns1.informatica.iesmarisma.intranet.

; Glue record (ya definido en el padre, pero se repite aquí)
ns1     IN      A       192.168.X.27

; Hosts del subdominio
www     IN      A       192.168.X.27
ftp     IN      A       192.168.X.27
smtp    IN      A       192.168.X.27
```

![Zona del subdominio](capturas/actividad8/zona_subdominio.png)

### 2.4 Comprobar sintaxis

```bash
sudo named-checkconf
sudo named-checkzone iesmarisma.intranet /etc/bind/db.iesmarisma.intranet
sudo named-checkzone informatica.iesmarisma.intranet /etc/bind/db.informatica.iesmarisma.intranet
```

![Checkconf delegación](capturas/actividad8/checkconf_delegacion.png)

> Posible warning: `ns1.informatica.iesmarisma.intranet has no addresses records` en la zona padre. Es normal porque es un glue record y named-checkzone no lo considera propio. No es un error real.

### 2.5 Reiniciar

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

---

## 3. Configuración del cliente

```bash
sudo nano /etc/resolv.conf
```

```
search iesmarisma.intranet
nameserver 192.168.X.27
```

## 4. Comprobaciones

### 4.1 Probar NS del subdominio

```bash
dig informatica.iesmarisma.intranet NS
```

![NS subdominio](capturas/actividad8/dig_ns_sub.png)

### 4.2 Probar hosts del subdominio

```bash
dig www.informatica.iesmarisma.intranet
dig ftp.informatica.iesmarisma.intranet
dig smtp.informatica.iesmarisma.intranet
```

![Hosts subdominio](capturas/actividad8/dig_hosts_sub.png)

### 4.3 Probar que el dominio principal sigue funcionando

```bash
dig www.iesmarisma.intranet
dig smtp.iesmarisma.intranet
```

![Dominio principal](capturas/actividad8/dig_principal.png)

---

## 5. Opcional: Script para crear subdominios con $INCLUDE

### 5.1 Script en Bash

Creamos el script:

```bash
#!/bin/bash

# crear_subdominio.sh - Crea un subdominio virtual usando $INCLUDE

# Validar parámetro
if [ -z "$1" ]; then
    echo "Uso: $0 nombre_subdominio"
    exit 1
fi

SUBDOMINIO=$1
DOMINIO="iesmarisma.intranet"
IP="192.168.X.27"
ZONE_FILE="/etc/bind/db.${DOMINIO}"
SUB_FILE="/etc/bind/db.${SUBDOMINIO}.${DOMINIO}.hosts"

# Crear archivo include del subdominio
cat <<EOF | sudo tee "$SUB_FILE"
; Subdominio: ${SUBDOMINIO}.${DOMINIO}
\$ORIGIN ${SUBDOMINIO}.${DOMINIO}.
www     IN  A   $IP
ftp     IN  A   $IP
smtp    IN  A   $IP
EOF

# Añadir $INCLUDE si no existe ya
if grep -q "$SUB_FILE" "$ZONE_FILE"; then
    echo "El subdominio ya estaba incluido."
else
    echo "\$INCLUDE $SUB_FILE" | sudo tee -a "$ZONE_FILE"
    echo "Subdominio ${SUBDOMINIO} añadido correctamente."
fi

# Incrementar serial (opcional, habría que hacerlo manualmente)
# Reiniciar BIND
sudo systemctl restart named
```

```bash
sudo chmod +x crear_subdominio.sh
sudo bash crear_subdominio.sh pruebas
```

![Script bash](capturas/actividad8/script_bash.png)

### 5.2 Verificar el include

```bash
cat /etc/bind/db.pruebas.iesmarisma.intranet.hosts
cat /etc/bind/db.iesmarisma.intranet | grep INCLUDE
```

![Verificar include](capturas/actividad8/verificar_include.png)

### 5.3 Comprobar desde el cliente

```bash
dig www.pruebas.iesmarisma.intranet
dig ftp.pruebas.iesmarisma.intranet
```

---
