# DNS Activity #8 - Subdominios

## Introducción

Configurar un subdominio `informatica.marisma.intranet` dentro del dominio `marisma.intranet` ya configurado en la Activity #6, usando el método de **subdominio virtual** (todo en el mismo archivo de zona).

## 1. Añadir el subdominio al archivo de zona

Editamos el archivo de zona del dominio principal:

```bash
sudo nano /etc/bind/db.marisma.intranet
```

Quedaría así (lo que ya tenías de la Activity #6 más los registros del subdominio):

```dns
$TTL    604800
@       IN      SOA     ns1.marisma.intranet. admin.marisma.intranet. (
                        2024052001      ; Serial - ¡incrementa este número!
                        604800
                        86400
                        2419200
                        604800 )

@       IN      NS      ns1.marisma.intranet.

ns1     IN      A       192.168.8.190
www     IN      A       192.168.8.190
ftp1    IN      A       192.168.8.190
mail1   IN      A       192.168.8.190
mail2   IN      A       192.168.8.190
departamentos   IN      A       192.168.8.190

@       IN      MX      10 mail1.marisma.intranet.
@       IN      MX      20 mail2.marisma.intranet.

; --- Subdominio informatica (subdominio virtual) ---
www.informatica  IN      A       192.168.8.190
ftp.informatica  IN      A       192.168.8.190
smtp.informatica IN      A       192.168.8.190
```


<img width="1302" height="528" alt="image" src="https://github.com/user-attachments/assets/62c81233-ef13-4916-96da-3e8357ba1168" />


## 2. Comprobar y reiniciar

```bash
sudo named-checkconf
sudo named-checkzone marisma.intranet /etc/bind/db.marisma.intranet
sudo systemctl restart bind9
sudo systemctl status bind9 --no-pager -l | head -10
```

<img width="1040" height="395" alt="image" src="https://github.com/user-attachments/assets/50893276-95f0-4c3b-b1ae-67d25489e04d" />


## 3. Configuración del cliente

En el cliente, aseguramos que `/etc/resolv.conf` tenga:

```
nameserver 192.168.8.190
search marisma.intranet
```

## 4. Comprobaciones

### 4.1 Probar hosts del subdominio

```bash
dig www.informatica.marisma.intranet
dig ftp.informatica.marisma.intranet
dig smtp.informatica.marisma.intranet
```

<img width="1037" height="1280" alt="image" src="https://github.com/user-attachments/assets/2714d9e1-663f-471b-8ab5-2b8fcce3ccaf" />


### 4.2 Probar que el dominio principal sigue funcionando

```bash
dig www.marisma.intranet
dig marisma.intranet MX
```

<img width="729" height="953" alt="image" src="https://github.com/user-attachments/assets/4385241e-9aef-441f-b7fb-1bba48c340a2" />


## 5. Opcional: Script para crear subdominios con $INCLUDE

### 5.1 Script en Bash

Creamos el script:

```bash
#!/bin/bash


if [ -z "$1" ]; then
    echo "Uso: $0 nombre_subdominio"
    exit 1
fi

SUBDOMINIO=$1
DOMINIO="marisma.intranet"
IP="192.168.8.190"
ZONE_FILE="/etc/bind/db.${DOMINIO}"
SUB_FILE="/etc/bind/db.${SUBDOMINIO}.${DOMINIO}.hosts"

cat <<EOF | sudo tee "$SUB_FILE"
; Subdominio: ${SUBDOMINIO}.${DOMINIO}
\$ORIGIN ${SUBDOMINIO}.${DOMINIO}.
www     IN  A   $IP
ftp     IN  A   $IP
smtp    IN  A   $IP
EOF

if grep -q "$SUB_FILE" "$ZONE_FILE"; then
    echo "El subdominio ya estaba incluido."
else
    echo "\$INCLUDE $SUB_FILE" | sudo tee -a "$ZONE_FILE"
    echo "Subdominio ${SUBDOMINIO} añadido correctamente."
fi

sudo systemctl restart named
```

```bash
sudo chmod +x crear_subdominio.sh
sudo bash crear_subdominio.sh pruebas
```

<img width="1037" height="261" alt="image" src="https://github.com/user-attachments/assets/f4b953d9-7365-410f-9ce5-ee429a1cd184" />


### 5.2 Verificar el include

```bash
cat /etc/bind/db.pruebas.marisma.intranet.hosts
cat /etc/bind/db.marisma.intranet | grep INCLUDE
```

<img width="1007" height="239" alt="image" src="https://github.com/user-attachments/assets/97c94bd8-ae94-4bd5-9718-5ee014e83776" />


### 5.3 Comprobar desde el cliente

```bash
dig www.pruebas.marisma.intranet
```
<img width="685" height="510" alt="image" src="https://github.com/user-attachments/assets/41fdc477-b891-4e36-aabd-58ba474a1008" />

---

