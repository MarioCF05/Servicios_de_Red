# DNS Activity #1 - Introducción (Resuelta)

## 1. ¿Qué es TLD? ¿Cómo se clasifican los dominios de nivel superior? Pon algunos ejemplos.

**TLD** son las siglas de **Top-Level Domain**, vamos, el dominio de nivel superior. Es lo último que ves en una URL después del último punto. Por ejemplo, en `www.wikipedia.org` el TLD es `.org`. Básicamente es la categoría más alta en el tinglado del DNS.

### Clasificación de los TLD

La ICANN (los que mandan en esto) los clasifica así:

| Tipo | Qué son | Ejemplos |
|------|---------|----------|
| **ccTLD** | De países, con 2 letras. Cada pais tiene el suyo. | `.es` (España), `.mx` (México), `.ar` (Argentina), `.fr` (Francia), `.jp` (Japón), `.pt` (Portugal) |
| **gTLD** | Los genéricos, de 3 letras o más. Pueden ser: | |
| - *uTLD* | Sin restricciones, los usa todo cristo | `.com`, `.net`, `.org`, `.info` |
| - *sTLD* | Restringidos a según qué sectores | `.aero` (aviones), `.cat` (cultura catalana), `.museum` (museos), `.coop` (cooperativas) |
| **Infraestructura** | Pa la infraestructura de Internet | `.arpa` (el abuelo de todos) |
| **Descentralizados** | Los que van con blockchain, fuera del control de la ICANN | `.bit`, `.crypto` |

También hay **pseudodominios** raros como `.onion` (de la red Tor, modo hacker) o `.bitnet`.

---

## 2. ¿Qué es FQDN? Pon algún ejemplo de FQDN.

**FQDN** significa **Fully Qualified Domain Name**, o sea, el nombre de dominio completo del tirón, sin dejarse nada. Es la dirección entera de un equipo en Internet, incluyendo el host, los subdominios, el dominio y el TLD. Y ojo, que un FQDN de verdad termina con un **punto al final** (`.`), que representa la raíz del DNS, aunque muchas veces nos lo saltemos por vagos.

### Características:
- Máximo 255 caracteres en total, y cada trocito (etiqueta) no puede pasar de 63.
- Solo acepta letras, números y guiones. Nada de ñes ni acentos (bueno, desde 2004 sí se pueden, pero vamos).

### Ejemplos:

| FQDN (con su puntito) | Qué significa |
|------------------------|---------------|
| `mail.google.com.` | Host `mail`, dominio `google`, TLD `.com`, raíz `.` |
| `www.wikipedia.org.` | Host `www`, dominio `wikipedia`, TLD `.org`, raíz `.` |
| `serv1.bar.com.` | Host `serv1`, dominio `bar`, TLD `.com`, raíz `.` |
| `smtp.oficina.empresa.es.` | Host `smtp`, subdominio `oficina`, dominio `empresa`, TLD `.es` |


---

## 3. ¿Qué son los root servers? ¿Cuántos root servers hay? ¿Cuántos servidores raíz físicos existen y dónde se encuentran? ¿Qué es anycast?

### ¿Qué son los root servers?

Los **root servers** (servidores raíz) son los servidores DNS que se encargan de la **zona raíz** (`.`), o sea, la que está arriba del todo. Son el primer paso cuando tu ordenador pregunta "oye, ¿dónde está tal página?". Ellos no saben la respuesta, pero te dicen "pregúntale a los servidores del `.com`" y así sucesivamente hasta que das con la IP.

### ¿Cuántos root servers hay?

Hay **13 servidores raíz lógicos**, con nombres de la **A a la M** (`a.root-servers.net`, `b.root-servers.net`, ..., `m.root-servers.net`). Son 13 porque en su día el tamaño máximo de un paquete UDP daba pa eso y no pa más.

### ¿Cuántos servidores raíz físicos existen y dónde se encuentran?

Aquí está el truco: aunque solo haya 13 nombres, **hay cientos de servidores físicos** repartidos por todo el mundo gracias al **anycast**. Originalmente 10 estaban en EE. UU. y 3 fuera (Estocolmo, Ámsterdam y Tokio), pero hoy están por tos laos.

| Letra | Operador | Sitios |
|-------|----------|--------|
| A | Verisign | Por tos laos (anycast) |
| B | USC-ISI | Marina del Rey, California (EE. UU.) |
| C | Cogent Communications | Anycast |
| D | Universidad de Maryland | College Park, Maryland (EE. UU.) |
| E | NASA | Mountain View, California (EE. UU.) |
| F | ISC | Anycast |
| G | DISA (Ejército USA) | Anycast |
| H | U.S. Army Research Lab | Aberdeen, Maryland (EE. UU.) |
| I | Netnod | Anycast |
| J | Verisign | Anycast |
| K | RIPE NCC | Anycast |
| L | ICANN | Anycast |
| M | Proyecto WIDE (Japón) | Anycast |


### ¿Qué es anycast?

**Anycast** es una movida de redes que permite que **varios servidores físicos** repartidos por el mundo usen la **misma IP**. Cuando tú preguntas, el tráfico se va automáticamente al servidor que te pille más cerca (el que tenga menor latencia). Es como si llamaras al 112 y te respondiera el de tu ciudad en vez del de Madrid.

Ventajas:
- **Va más rápido** (menos latencia)
- **Si uno falla, otro lo cubre** (tolerancia a fallos)
- **Pueden poner máquinas nuevas sin cambiar la IP** (escalabilidad)

---

## 4. ¿Qué es un archivo de zona (zone file)? Indica para qué sirven los registros de un archivo de zona. Pon un ejemplo de un archivo de zona e interpreta la información almacenada.

### ¿Qué es un archivo de zona?

Un **zone file** es un archivo de texto normal y corriente donde se define una **zona DNS**. Es como la "carta" de presentación de un dominio: dice qué IP tiene cada servicio, quién es el servidor de correo, etc. Este formato viene de los tiempos del BIND y se usa en tos los servidores DNS serios.

### ¿Para qué sirven los registros?

Cada tipo de registro hace una cosa distinta:

| Registro | Pa qué sirve |
|----------|--------------|
| **SOA** | El más importante. Es el "jefe" de la zona: dice quién manda aquí, el email del administrador, y los tiempos de caché y sincronización. |
| **NS** | Dice qué servidores DNS tienen info autorizada sobre este dominio. |
| **A** | La típica: traduce un nombre (ej: `google.com`) a una dirección **IPv4**. |
| **AAAA** | Lo mismo pero pa **IPv6** (porque el IPv4 se nos quedó pequeño). |
| **CNAME** | Hace que un nombre sea un **alias** de otro. Ej: `www` apunta a `dominio.com`. |
| **MX** | Dice cuál es el servidor de **correo** del dominio, con prioridad (a menor número, más prioridad). |
| **PTR** | La inversa del A: de IP a nombre. |
| **TXT** | Para meter texto suelto. Se usa pa verificaciones (SPF, DKIM, etc.). |

### Ejemplo de archivo de zona (comentado)

```dns
; Archivo de zona pa ejemplo.com
$ORIGIN ejemplo.com.     ; Lo que va después de esto si no ponemos punto, se autocompleta
$TTL 3600                ; Las respuestas se cacheduran 1 hora por defecto

; --- Registro SOA (Start of Authority) - el que manda ---
ejemplo.com.  IN  SOA  ns1.ejemplo.com. admin.ejemplo.com. (
                2024052001  ; Serial: la versión del archivo (formato: AAAAMMDD + número)
                7200        ; Refresh: cada 2h los esclavos preguntan si hay cambios
                3600        ; Retry: si no pueden contactar, esperan 1h pa reintentar
                1209600     ; Expire: si en 14 días no contactan, los esclavos se rinden
                3600        ; TTL mínimo pa respuestas negativas (1h)
                )

; --- Servidores de nombres (NS) ---
ejemplo.com.  IN  NS  ns1.ejemplo.com.
ejemplo.com.  IN  NS  ns2.ejemplo.com.

; --- Servidor de correo (MX) ---
ejemplo.com.  IN  MX  10 mail.ejemplo.com.    ; Prioridad 10, el principal
ejemplo.com.  IN  MX  20 mail2.ejemplo.com.   ; Prioridad 20, el de respaldo

; --- Registros A (IPv4) ---
ejemplo.com.  IN  A   192.0.2.1               ; IP del dominio principal
ns1           IN  A   192.0.2.2               ; ns1.ejemplo.com
ns2           IN  A   192.0.2.3               ; ns2.ejemplo.com
mail          IN  A   192.0.2.10              ; El servidor de correo
www           IN  A   192.0.2.20              ; El servidor web

; --- Registros AAAA (IPv6) ---
ejemplo.com.  IN  AAAA  2001:db8::1           ; IPv6 del dominio principal
www           IN  AAAA  2001:db8::20          ; IPv6 del www

; --- Registros CNAME (Alias) ---
ftp           IN  CNAME  www.ejemplo.com.     ; ftp.ejemplo.com es un apaño, apunta al www
blog          IN  CNAME  www.ejemplo.com.     ; blog.ejemplo.com igual
```

### Interpretación (traducción al cristiano):

- **`$ORIGIN ejemplo.com.`**: Todo nombre que no termine en punto se considera que es `loquesea.ejemplo.com`. Por ejemplo, `www` = `www.ejemplo.com`.
- **`$TTL 3600`**: 1 hora de caché pa los registros que no digan otra cosa. Ni más ni menos.
- **SOA**: El servidor principal es `ns1.ejemplo.com`, el admin es `admin@ejemplo.com` (el punto reemplaza a la arroba). El serial `2024052001` es como la versión: cada vez que cambies algo, lo tienes que subir.
- **NS**: Hay dos DNS autoritativos: `ns1` y `ns2`. Si uno falla, que conteste el otro.
- **MX**: El correo se manda primero a `mail.ejemplo.com` (prioridad 10). Si ese no responde, se va a `mail2` (prioridad 20).
- **A/AAAA**: Asocian nombres con IPs (IPv4 e IPv6 respectivamente). Lo básico del DNS vamos.
- **CNAME**: `ftp` y `blog` son "apodos" de `www.ejemplo.com`. No tienen IP propia, redirigen al www.

---

> **Referencias (por si el profe pregunta):**
> - [How DNS Works (YouTube)](https://www.youtube.com/watch?v=mpQZVYPuDGU)
> - [An Introduction to DNS Terminology (DigitalOcean)](https://www.digitalocean.com/community/tutorials/an-introduction-to-dns-terminology-components-and-concepts) 
> - [Wikipedia: Top-level domain](https://es.wikipedia.org/wiki/Dominio_de_nivel_superior)
> - [Wikipedia: FQDN](https://es.wikipedia.org/wiki/FQDN)
> - [Wikipedia: Root name server](https://en.wikipedia.org/wiki/Root_name_server)
> - [Wikipedia: Zone file](https://en.wikipedia.org/wiki/Zone_file)

---


