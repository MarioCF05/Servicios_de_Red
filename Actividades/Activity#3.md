# Ejercicios Actividad 5

## 1. Crea un directorio llamado "dir1" y otro llamado "dir2"

Creo los directorios 

<img width="725" height="147" alt="image" src="https://github.com/user-attachments/assets/dd6eb46c-9b03-40fc-9ab4-372af9412d0a" />

## 2. Explica qué diferencia existe entre ambos y muestra su equivalencia con la directiva Require:

La diferencia entre ambos bloques de configuración radica en el orden en que se aplican las reglas de acceso. En el primer bloque Order Deny,Allow se niega el acceso por defecto a todos (Deny from All) y luego se permite únicamente a la IP 192.168.1.100 (Allow from 192.168.1.100), mientras que en el segundo bloque Order Allow,Deny se permite todo por defecto y luego se niega el acceso a todos (Deny from All), lo que termina bloqueando también la IP 192.168.1.100. Su equivalencia usando la directiva moderna Require sería: para el primer bloque <Directory /var/www/example1> Require ip 192.168.1.100</Directory> y para el segundo <Directory /var/www/example1> Require all denied</Directory>.

## 3. Para dir1

### a. Permite el acceso de las peticiones provenientes de 10.3.0.100

### b. Permite el acceso desde "marisma.intranet"

### c. Permite el acceso desde cualquier subdominio de "marisma.intranet"

### d. Permite el acceso de las peticiones provenientes de "10.3.0.100" con máscara "255.255.0.0"

Nos metemos en "/etc/apache2/sites-available/dir1.conf" y ponemos el siguiente codigo
<img width="715" height="212" alt="image" src="https://github.com/user-attachments/assets/7981e933-412a-4f06-b446-7a1cad414c5f" />

## 4. Modifica la configuración de forma que el acceso a dir1 se permita a "marisma.intranet" y no se permita desde 10.3.0.101"

<img width="632" height="232" alt="image" src="https://github.com/user-attachments/assets/0129d911-0ff6-42de-af67-866a86f458c7" />



## 5. Modifica la configuración de forma que el acceso a dir2 se permita a "10.3.0.100/8" y no a "marisma.intranet"


<img width="635" height="189" alt="image" src="https://github.com/user-attachments/assets/2cd201be-40b3-46b3-b002-f73a8bf4238a" />

