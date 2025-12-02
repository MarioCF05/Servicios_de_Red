# Configuracion Apache

## Apache utilizará el puerto 81 además del 80

No dirigimos al archivo ports.conf
<img width="682" height="48" alt="image" src="https://github.com/user-attachments/assets/9937c42d-8206-4f71-936d-78c634a66a4e" />

Añadimos una linea con "Listen 81" y guardamos 

<img width="732" height="494" alt="image" src="https://github.com/user-attachments/assets/25d70d18-b49f-4baf-9d66-fce51bdc16ff" />

## Añadir el dominio “marisma.intranet” en el fichero “hosts”

Nos vamos a "/etc/hosts" y añadimos "127.0.1.1 marisma.intranet" para que tambien nos lleve a nuestra pagina con ese nombre 

<img width="734" height="478" alt="image" src="https://github.com/user-attachments/assets/2a252b47-2a1f-4964-9fdf-b3d56c8b20ca" />

Y ya podriamos usarlo

<img width="736" height="405" alt="image" src="https://github.com/user-attachments/assets/c133bb78-adcf-40e1-bf0b-48cf8132ea3a" />

## Cambia la directiva “ServerTokens” para mostrar el nombre del producto.

Nos vamos al fichero /etc/apache2/conf-available/security.conf

<img width="731" height="74" alt="image" src="https://github.com/user-attachments/assets/d426f675-8db6-4de7-9682-7dfb380cac60" />

Y dentro modificamos la linea "ServerTokens OS" y lo modificamos a "ServerTokens ProductOnly" 

<img width="745" height="449" alt="image" src="https://github.com/user-attachments/assets/32f005b3-f75f-401d-a5e9-d3fb228940ee" />

Instalamos "curl" (apt install curl) y lo usamos para comprobar que solo se vea el nombre importante reiniciar el servicio sino no se aplican los cambios

<img width="725" height="414" alt="image" src="https://github.com/user-attachments/assets/0e2f6ef0-c0e7-41af-90d3-5c7244dc7128" />


	
## Comprueba si se visualiza el pie de página en las páginas generadas por Apache (por ejemplo, en las páginas de error). Cambia el valor de la directiva “ServerSignature” y comprueba que funciona correctamente. 

Probamos la pagina de error

<img width="730" height="258" alt="image" src="https://github.com/user-attachments/assets/7c86de30-27fc-47b0-b630-dd870810935c" />

Modificamos la directiva "ServerSignature" y la Desactivamos

<img width="733" height="469" alt="image" src="https://github.com/user-attachments/assets/f34909c4-a0de-409a-8c32-ab376c49cd4c" />

Y ya no nos sale el pie de pagina

<img width="733" height="281" alt="image" src="https://github.com/user-attachments/assets/8c8ee7cd-6f32-4672-9c5f-8f16125dfdd8" />


## Crea un directorio “prueba” y otro directorio “prueba2”. Incluye un par de páginas en cada una de ellas.

Creamos las carpetas

<img width="737" height="128" alt="image" src="https://github.com/user-attachments/assets/dff18f90-c576-480b-9c27-4e3e7e0b786e" />

Y creamos los archivos index.html

<img width="725" height="152" alt="image" src="https://github.com/user-attachments/assets/403a087b-6056-4e77-b49b-7c3ea8baa7b7" />

Y ya los tendriamos creado

<img width="729" height="199" alt="image" src="https://github.com/user-attachments/assets/89987dd2-7d6e-4071-ac65-e3fe69ff487b" />
	
## Redirecciona el contenido de la carpeta “prueba” hacia “prueba2”

Nos vamos a /etc/apache2/apache2.conf y añadimos la siguiente linea

<img width="486" height="62" alt="image" src="https://github.com/user-attachments/assets/84726afd-b0cd-4599-92d6-14d0bc61ea3a" />

Y ya nos redirige a prueba2

## Es posible redireccionar tan solo una página en lugar de toda la carpeta. Pruébalo.

Si, completamente simnplemente en el Redirect especificar los archivos como tal, voy a crear un archivo de prueba, modificamos el apache2.conf y reiniciamos el apache

<img width="486" height="71" alt="image" src="https://github.com/user-attachments/assets/a3692ccb-56b2-4abd-bf5e-0e260a587b62" />

<br>

<img width="706" height="548" alt="image" src="https://github.com/user-attachments/assets/7b5d4e43-0f31-448c-8dd7-50891668f7d5" />

<img width="720" height="561" alt="image" src="https://github.com/user-attachments/assets/a2c78799-3a2c-464a-b05e-87832a5bf970" />

	
## Usa la directiva userdir

Habilitamos el modulo userdir

<img width="655" height="132" alt="image" src="https://github.com/user-attachments/assets/d6a1d025-4b85-4579-82a3-9f5b8678245a" />

Creamos una carpeta en el directorio del usuario en el que estemos y le añadimos un html

<img width="638" height="42" alt="image" src="https://github.com/user-attachments/assets/9b5db86a-68ae-4c3d-b419-84255f7b77c9" />

Le damos permisos a la carpeta (chmod 755) y ya lo tendriamos

<img width="558" height="113" alt="image" src="https://github.com/user-attachments/assets/cb0cb70e-9b07-40cb-93db-175d642e7327" />
	
## Usa la directiva alias para redireccionar a una carpeta dentro del directorio de usuario.

Modificamos el archivo /etc/apache2/mods-available/alias.conf añadiendo un alias

<img width="337" height="178" alt="image" src="https://github.com/user-attachments/assets/ff422548-a789-41da-a428-5d15ee5b8c22" />

Y ya lo tendriamos 

<img width="700" height="534" alt="image" src="https://github.com/user-attachments/assets/320472b7-4950-45ce-9abe-208818223162" />

	
## ¿Para qué sirve la directiva Options y dónde aparece. Comprueba si apache indexa los directorios. Si es así, ¿cómo lo desactivamos?

Define qué características están permitidas dentro de un directorio en Apache, esta en /etc/apache2/apache2.conf

<img width="709" height="37" alt="image" src="https://github.com/user-attachments/assets/faceca11-74f9-4c89-9a1c-1137abd293e9" />

<img width="713" height="312" alt="image" src="https://github.com/user-attachments/assets/176e93e2-a305-483b-981d-9dc94a3b67d1" />

Para desactivarlos comentamos esa zona y descomentamos la de abajo

<img width="735" height="227" alt="image" src="https://github.com/user-attachments/assets/637f2364-d5dd-438d-8ab4-c7f8077b9292" />



# Creacion de scripts 

## Crea un script que añada un puerto de escucha en el fichero de configuración de Apache. El puerto se recibirá como parámetro en la llamada y se comprobará que no esté ya presente en el fichero de configuración.

```bash
#!/bin/bash

APACHE_CONFIG_FILE="/etc/apache2/ports.conf"

if [[ -z "$1" ]]; then
    echo "Uso: $0 <puerto>"
    exit 1
fi

PORT="$1"

# Validar puerto
if ! [[ "$PORT" =~ ^[0-9]+$ ]] || (( PORT < 1 || PORT > 65535 )); then
    echo "Puerto inválido. Debe estar entre 1 y 65535."
    exit 1
fi

# Comprobar si el puerto ya existe
if grep -q "Listen $PORT" "$APACHE_CONFIG_FILE"; then
    echo "El puerto $PORT ya existe."
    exit 0
fi

# Añadir puerto
echo "Listen $PORT" | sudo tee -a "$APACHE_CONFIG_FILE" >/dev/null
echo "Puerto $PORT añadido. Reinicia Apache para aplicar los cambios."
```

## Crea un script que añada un nombre de dominio y una ip al fichero hosts. Debemos comprobar que no existe dicho dominio en el fichero hosts

```bash
#!/bin/bash

if [[ $# -ne 2 ]]; then
    echo "Uso: sudo $0 <IP> <DOMINIO>"
    exit 1
fi

IP="$1"
DOMAIN="$2"
HOSTS_FILE="/etc/hosts"

if grep -qE "\s$DOMAIN(\s|$)" "$HOSTS_FILE"; then
    echo "El dominio $DOMAIN ya existe."
    exit 0
fi

echo -e "$IP\t$DOMAIN" | sudo tee -a "$HOSTS_FILE" >/dev/null
echo "Dominio $DOMAIN añadido a $HOSTS_FILE."

```

## Crea un script que nos permita crear una página web con un título, una cabecera y un mensaje

```bash
#!/bin/bash

if [[ $# -ne 3 ]]; then
    echo "Uso: $0 \"<titulo>\" \"<cabecera>\" \"<mensaje>\""
    exit 1
fi

TITLE="$1"
HEADER="$2"
MESSAGE="$3"
OUTPUT="index.html"

cat << EOF > "$OUTPUT"
<!DOCTYPE html>
<html lang='es'>
<head>
<meta charset='UTF-8'>
<title>$TITLE</title>
</head>
<body>
<h1>$HEADER</h1>
<p>$MESSAGE</p>
</body>
</html>
EOF

echo "Página web creada: $OUTPUT"

```
