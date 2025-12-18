# Creamos el entorno VPC
Para este caso crearemos dos subreders publicas y dos privadas. Las dos publicas para balanceador de carga mientras el uso de las privadas se deriba a sostener la seguridad de la data base evitando que sea accesible para los usuarios

Nos dirigimos al buscador y buscamos "VPC" y pulsamos ahi
<img width="957" height="528" alt="image" src="https://github.com/user-attachments/assets/4c540d09-b40e-4523-b857-fd02743f4197" />

Pulsamos sobre "Crear VPC"
<img width="955" height="310" alt="image" src="https://github.com/user-attachments/assets/e6b1750c-8a4a-48fb-a081-ac737493f030" />

En la configuracion tenemos que marcar la casilla "VPC y más", le asignamos un nombre y nos aseguramos de que en "Número de zonas de disponibilidad (AZ)" sean 2 al igual que en "Cantidad de subredes públicas" y "Cantidada de subredes privadas
" y en la zona de "Puertas de enlace NAT ($): actualizado" lo dejamos en "Ninguna" y en "Puntos de enlace de la VPC" tambien marcamos "Ninguna" y le pulsamos a "Crear VPC"

<img width="954" height="956" alt="image" src="https://github.com/user-attachments/assets/59997f0c-9a4a-4e87-b98b-477a67d01d98" />

<img width="383" height="259" alt="image" src="https://github.com/user-attachments/assets/6aed1b5c-8b89-4ef3-8181-5e88eafe0e78" />

<img width="953" height="452" alt="image" src="https://github.com/user-attachments/assets/cf12e912-d66e-4cd5-b61a-1e027ed3fcd5" />

Aqui tendriamos el mapa de recursos

<img width="1112" height="458" alt="image" src="https://github.com/user-attachments/assets/26c3c59f-d115-4b30-b8b8-ae45be2dca11" />

# Creacion del entorno de instancias y conexion ssh

Al igual que antes nos dirigimos a las instancias para crear una 
<img width="957" height="242" alt="image" src="https://github.com/user-attachments/assets/f3872814-b66a-49d7-8f72-8b05ea0d9547" />

Una vez dentro le damos a "Lanzar instancias"

<img width="955" height="367" alt="image" src="https://github.com/user-attachments/assets/57dcab54-6668-4aa7-9bbe-27ebc7f44ea5" />

Le damos un nombre y le asignamos el tipo de imagen en este caso usaremos un debian (El favorito de Roddy aunque sea mas complicado pal ssh ajajajaja)
<img width="954" height="429" alt="image" src="https://github.com/user-attachments/assets/cd5eff0a-4877-420e-ad59-55c9a978c426" />

Nos vamos a "Configuraciones de red" y modificamos el VPC y seleccionamos el que creamos con anterioridad y le habilitamos "Asignar automáticamente la IP pública"
<img width="722" height="224" alt="image" src="https://github.com/user-attachments/assets/d0ceacc0-c67b-461f-b362-b2449b607038" />

Habilitamos la conexion por ssh y por http (Puerto 22 y 80)
<img width="724" height="545" alt="image" src="https://github.com/user-attachments/assets/182577e7-1b2c-44ec-8cc1-b1bea6600b95" />

Creamos y lanzamos la instancia 
<img width="887" height="303" alt="image" src="https://github.com/user-attachments/assets/6bac02b8-3def-4e81-bc1a-c8968058e542" />

<img width="1665" height="811" alt="image" src="https://github.com/user-attachments/assets/582e5f3e-3e11-4269-85c9-db7a56a2b297" />

Nos conectamos por ssh, abrimos el powershell y ejecutamos el siguiente comando

<img width="1055" height="619" alt="image" src="https://github.com/user-attachments/assets/52cc14b0-ce6f-4ed0-9d80-d1fbb681340d" />

Lo ponemos en el powershell pero teniendo en cuenta que en "KeysMarioWordPress.pem" tenemos que poner la ubicacion del archivo pem de nuestra instancia y darle los permisos correspodientes al archivo (400)

<img width="1080" height="351" alt="image" src="https://github.com/user-attachments/assets/e6fd3375-5eba-4f60-b42a-4afaa94bbe88" />

# Instalacion de Apache / PHP / MySQL

Como siempre antes de anda update a las listas de paquetes y upgrademaos los paquetes actuales

<img width="735" height="284" alt="image" src="https://github.com/user-attachments/assets/c2769454-38fe-4099-80a4-42a1d1dded14" />

<img width="1031" height="238" alt="image" src="https://github.com/user-attachments/assets/e15a9004-796c-49e4-b1cb-2c38c4102e9a" />

Instalamos el paquete Apache2 
<img width="945" height="260" alt="image" src="https://github.com/user-attachments/assets/1ee76046-5485-47fd-b42b-a0a07acd13d1" />

Habilitamos el paquete con un systemctl enable y comprobamos que funcione con la IP publica de la instancia

<img width="1039" height="320" alt="image" src="https://github.com/user-attachments/assets/acfb55ce-8be7-4b69-91a1-e3dd2d413962" />

<img width="1056" height="1079" alt="image" src="https://github.com/user-attachments/assets/03377ac4-2ab9-4ed6-b17a-2f59475f9cba" />

Intalamos PHP para Apache (con sus respectivas dependencias)

<img width="1063" height="245" alt="image" src="https://github.com/user-attachments/assets/58f15b93-29bc-44d9-bc42-2dd231c8829c" />

Ahora instalamos MySQL para PHP

<img width="809" height="192" alt="image" src="https://github.com/user-attachments/assets/a21d19bb-02a6-4cfc-b5af-556739eb2c1f" />

Y ya estaria funcionando Apache junto con PHP y MySQL
<img width="932" height="248" alt="image" src="https://github.com/user-attachments/assets/51ae4053-f8ad-4c08-971c-6843e040a246" />

# Creacion de la DB relacionada
Al igual que en creaciones anteriores nos vamos al buscador y buscamos "RDS" en este caso y pulsamos sobre "Aurora and RDS"
<img width="1058" height="300" alt="image" src="https://github.com/user-attachments/assets/821a2c16-8a1a-4983-9df8-bd084e972d97" />

Le damos sobre "Crear una base de datos"

<img width="1048" height="817" alt="image" src="https://github.com/user-attachments/assets/7e9a8e94-f600-457e-825f-adafa22eddd5" />

Dejamos en la opcion "Configuracion completa" y marcamos la casilla "MySQL"
<img width="1004" height="596" alt="image" src="https://github.com/user-attachments/assets/eb4036cb-3764-4181-a537-a41e735af797" />

Le damos un identificador para encontrarlo favil y la dejamos en "Autoadministrado" y le damos una contraseña

<img width="1011" height="743" alt="image" src="https://github.com/user-attachments/assets/54e25b79-2064-41d9-81b6-aff7fbde6891" />

En configuracion de la instancia la dejaremos en esta
<img width="998" height="472" alt="image" src="https://github.com/user-attachments/assets/1dbfa080-657e-452a-9eda-6698b8c2fa9a" />

En almacenamineto dejamos lo mas basico (pq el presupuesto no es infinito jajaja) 
<img width="1002" height="412" alt="image" src="https://github.com/user-attachments/assets/3efeafc3-ad1d-481d-8411-a097f5eecfef" />

Importante en "Conectividad" marcar la VPC que creamos con anterioridad
<img width="1001" height="344" alt="image" src="https://github.com/user-attachments/assets/a552b17d-96d7-42ef-a70f-11f6638f1b2a" />

Creamos un nuevo grupo de seguridad para el VPC
<img width="996" height="210" alt="image" src="https://github.com/user-attachments/assets/cda4e9e0-baf0-4894-aea0-d0da6445da43" />

Abrimos el menu de "Configuracion adicional" (Abajo del todo) y le damos un nombre

<img width="746" height="372" alt="image" src="https://github.com/user-attachments/assets/e35b2b92-b21f-4455-ba72-0cae796ce3cc" />

Creamos la DB
<img width="1007" height="578" alt="image" src="https://github.com/user-attachments/assets/e152bd93-ea66-4483-9df7-08f4fa48abd7" />

Ahora conectamos la DB con la instancia que creamos con anterioridad, click derecho sobre la DB y le damos a "Configurar la conexion de EC2"

<img width="1056" height="998" alt="image" src="https://github.com/user-attachments/assets/54e5c7de-f882-4e8a-b48a-1eb8b0f577f6" />

Marcamos la instancia con la que queremos que se conecte y continuamos y confirmamos 
<img width="753" height="431" alt="image" src="https://github.com/user-attachments/assets/fd38b83d-c698-4cb0-b969-4beed3857c8f" />

<img width="1249" height="784" alt="image" src="https://github.com/user-attachments/assets/500b8d0b-8940-4d06-962a-15286318aa07" />

<img width="796" height="50" alt="image" src="https://github.com/user-attachments/assets/6df34664-5ccb-4a72-ac0b-1e5e579f69fc" />

# Creacion del Elastic File System

Volvemos a buscar en este caso "EFS"

<img width="1057" height="295" alt="image" src="https://github.com/user-attachments/assets/a0b7ff51-1495-4098-8d22-68b509e80e19" />

 Pulsamos sobre "Crear un sistema de archivos"

 <img width="1055" height="282" alt="image" src="https://github.com/user-attachments/assets/8562430b-5055-4da5-97b8-341ee71925fe" />

Le damos un nombre y le asignamos nuestro VPC y pulsamos sobre "Crear un sistema de archivos"
<img width="605" height="637" alt="image" src="https://github.com/user-attachments/assets/6c013810-8645-4d38-84d7-024eb4f90887" />

<img width="786" height="267" alt="image" src="https://github.com/user-attachments/assets/a68d206e-b336-4806-81f2-5b6d2ee3e6d9" />

Nos volvmeos a la instancia EC2 para habilitar un nuevo puerto (para permitir acceso de la instancia EFS)
<img width="1058" height="458" alt="image" src="https://github.com/user-attachments/assets/05218c30-a4e0-45b5-8d32-57ff4170a972" />

Volvemos al EFS y entramos en el que acabamos de crear y pulsamos "Asociar" y lo dejamos en "Montaje a traves de DNS" y seguimos las instrucciones

<img width="1054" height="998" alt="image" src="https://github.com/user-attachments/assets/d93ec1fb-f6a6-4422-940a-04ec9eefd180" />

Nos vamos al SSH con la instancia y creamos una carpeta y montamos el efs con el comando que nos sale en "Asociar"

<img width="1100" height="557" alt="image" src="https://github.com/user-attachments/assets/ab61f9d6-b875-48da-b5b5-807d29af118f" />

<img width="636" height="283" alt="image" src="https://github.com/user-attachments/assets/b304e162-4eb2-436e-9cec-6442c71cf36f" />

# Descargar WordPress

Dentro de nuestra carpeta de paginas de apache descargamos wordpress "wget http://wordpress.org/latest.tar.gz"
 y lo descomprimimos con "tar -xf latest.tar.gz"

<img width="930" height="311" alt="image" src="https://github.com/user-attachments/assets/3dd841c6-57fd-404f-bc72-c0f41e9d8686" />

Descargamos el cliente de MySQL
<img width="838" height="239" alt="image" src="https://github.com/user-attachments/assets/5364a682-9e82-4846-90f5-c81d293d7fde" />

Conectamos el la base de datos con el cliente para ello he tenido que instalar el certificado raíz de Amazon RDS con el comando "wget https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem -O rds-combined-ca-bundle.pem" y ya usamos el comando "mysql -u admin -h dbmysqlmario.ceklv5whsaaf.us-east-1.rds.amazonaws.com --ssl-ca=rds-combined-ca-bundle.pem -p" y ponemos la contraseña

<img width="940" height="309" alt="image" src="https://github.com/user-attachments/assets/b16beaab-37ee-4a78-aa8f-2d797728c777" />

Creamos la base de datos, el suuario y la contraseña "CREATE DATABASE wordpress_mario; CREATE USER 'wordpress_usuario'@'%' IDENTIFIED BY 'mario2005'; GRANT ALL PRIVILEGES ON wordpress_mario.* TO 'wordpress_usuario'@'%'; FLUSH PRIVILEGES;
"
<img width="1542" height="175" alt="image" src="https://github.com/user-attachments/assets/b0cd4cab-57c1-418a-ac3e-18b55d5c9f69" />

Nos metemos al navegador y buscamos nuestro wordpress

<img width="958" height="629" alt="image" src="https://github.com/user-attachments/assets/a13e66cb-6f37-46db-b1dc-46cd2dec8e7b" />

Rellenamos los campos con los datos de nuestra Base de datos

<img width="953" height="742" alt="image" src="https://github.com/user-attachments/assets/66c5cfe6-9e7a-4681-a7d5-9480fba702c3" />

Creamos el archivo wp-config.php y pulsamos sobre "Run the installation"

<img width="957" height="486" alt="image" src="https://github.com/user-attachments/assets/37fd4a88-6ae3-4b69-ab28-2b367f27024d" />

<img width="942" height="813" alt="image" src="https://github.com/user-attachments/assets/b007edbf-b715-4421-9f14-b84689174bec" />

Ahora le damos un nombre al sitio, un usuario (con sus credenciales) y pulsamos "Install WordPress"
<img width="829" height="895" alt="image" src="https://github.com/user-attachments/assets/0fcb49fb-5c38-41bf-9a89-c28daf85ddd1" />

Y ya tendriamos creado el wordpress

<img width="859" height="456" alt="image" src="https://github.com/user-attachments/assets/86910728-1b59-4a87-a8d7-9717b3f54448" />

Prueba de login y vista de wordpress

<img width="917" height="486" alt="image" src="https://github.com/user-attachments/assets/4de648f3-046b-40eb-84ee-83ea9b2c7efc" />

<img width="1919" height="1044" alt="image" src="https://github.com/user-attachments/assets/c9629656-9f56-44b4-8765-87ef7c7b7fb9" />

# Conexion de EFS con WP-Content

Nos vamos a la carpeta de wordpress (/var/www/wordpress) y hacemos una backup de lo que ya tenemos "mv wp-content wp-content-backup"

<img width="623" height="19" alt="image" src="https://github.com/user-attachments/assets/2b4389ad-621a-40ea-9b20-7f0363680071" />

Creamos un punto de mount vacio "mkdir wp-content"
<img width="451" height="36" alt="image" src="https://github.com/user-attachments/assets/489c1085-bada-4ea2-91f1-11c0dee1cde7" />

Y montamos el EFS como con anterioridad pero en wp-content
