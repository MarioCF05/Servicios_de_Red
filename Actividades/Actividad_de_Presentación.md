# Conceptos básicos de servidores y HTTP

## ¿Quién, dónde y cuándo se crea el primer servidor web?
- **Quién:** Tim Berners-Lee  
- **Dónde:** CERN (Suiza)  
- **Cuándo:** 1990  
- **Dato curioso:** La primera página web estaba en `http://info.cern.ch/`

---

## Pila de protocolos usados por HTTP
HTTP funciona sobre la pila **TCP/IP**:
- **Aplicación:** HTTP  
- **Transporte:** TCP (asegura entrega de datos)  
- **Red:** IP (direccionamiento)  
- **Enlace:** Ethernet/Wi-Fi (transmisión física)

---

## Componentes de una URL
Ejemplo: `https://www.ejemplo.com:443/ruta/pagina?user=1#seccion`
- **Protocolo:** `https`  
- **Dominio:** `www.ejemplo.com`  
- **Puerto:** `443`  
- **Ruta:** `/ruta/pagina`  
- **Parámetros:** `?user=1`  
- **Fragmento:** `#seccion`

---

## Pasos en la recuperación de una página web mediante HTTP
1. El usuario escribe la URL en el navegador.  
2. Se resuelve el **DNS** para obtener la IP del servidor.  
3. Se establece conexión **TCP** con el servidor.  
4. El navegador envía una **request HTTP**.  
5. El servidor procesa y envía una **response HTTP**.  
6. El navegador interpreta y muestra la página.

---

## Diferencia entre páginas dinámicas y estáticas
- **Estáticas:** Contenido fijo, igual para todos (ej. HTML simple).  
- **Dinámicas:** Generadas en tiempo real según usuario o datos (ej. PHP, Node.js).

---

## Cómo usar Telnet para acceder a un servidor web
Ejemplo (actividad 0.3):  
```bash
telnet www.ejemplo.com 80
GET / HTTP/1.1
Host: www.ejemplo.com
