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

> **Nota:** Al escribir `www.informatica` sin punto al final, BIND lo completa automáticamente como `www.informatica.marisma.intranet`.
>
> **Importante:** Incrementa el Serial antes de guardar (ej: de `2024052001` a `2024052002`).

![Zona con subdominio virtual](capturas/actividad8/zona_virtual.png)

## 2. Comprobar y reiniciar

```bash
sudo named-checkconf
sudo named-checkzone marisma.intranet /etc/bind/db.marisma.intranet
sudo systemctl restart bind9
sudo systemctl status bind9 --no-pager -l | head -10
```

![Checkconf subdominio virtual](capturas/actividad8/checkconf_virtual.png)

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

![Hosts subdominio](capturas/actividad8/dig_hosts_sub.png)

### 4.2 Probar que el dominio principal sigue funcionando

```bash
dig www.marisma.intranet
dig marisma.intranet MX
```

![Dominio principal](capturas/actividad8/dig_principal.png)

## 5. Opcional: Script para crear subdominios con $INCLUDE

### 5.1 Script en Bash

Creamos el script:

```bash
#!/bin/bash

# crear_subdominio.sh - Crea un subdominio virtual usando $INCLUDE

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

![Script bash](capturas/actividad8/script_bash.png)

### 5.2 Verificar el include

```bash
cat /etc/bind/db.pruebas.marisma.intranet.hosts
cat /etc/bind/db.marisma.intranet | grep INCLUDE
```

![Verificar include](capturas/actividad8/verificar_include.png)

### 5.3 Comprobar desde el cliente

```bash
dig www.pruebas.marisma.intranet
```

---

**Comandos resumen para capturas:**

| Captura | Comando |
|---------|---------|
| Editar zona principal | `sudo nano /etc/bind/db.marisma.intranet` |
| Checkconf | `sudo named-checkconf` |
| Check zona | `sudo named-checkzone marisma.intranet /etc/bind/db.marisma.intranet` |
| Consulta www subdominio | `dig www.informatica.marisma.intranet` |
| Consulta ftp subdominio | `dig ftp.informatica.marisma.intranet` |
| Consulta smtp subdominio | `dig smtp.informatica.marisma.intranet` |
| Script bash | `sudo bash crear_subdominio.sh nombre` |
| Verificar include | `cat /etc/bind/db.marisma.intranet \| grep INCLUDE` |
