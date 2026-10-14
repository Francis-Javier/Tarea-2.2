# Tarea-2.2  
# Práctica: instalar tres SGBD  

Francisco Javier Díaz Garrón  
14/10/2026  
ASIR 2º  

Para esta tarea vamos a instalar los SGBD de Oracle MariaDB y PostgreSQL  

## 1. Desplegar Oracle Database 23ai Free con Podman  

### Preparar Podman y descargar la imagen  

Para empezar realizamos un sudo apt update para actualizar toda la lista de paquetes de la terminal y así poder descargar la última versión de cada gestor.  
![Actualizar paquetes](imagenes/Captura1.png)  

Y luego con sudo apt install podman instalamos el gestor de oracle para poder usarlo.  
![Instalación de Podman](imagenes/Captura2.png)  

Con el comando sudo podman pull ruta, lo que estoy haciendo es descargarme la imagen de oracle desde sus servidores para ya tener la imagen antes de crear el contenedor.  
![Descarga de imagen Oracle](imagenes/Captura3.png)  

Y en la siguiente captura se puede observar que ha acabado la descarga de la imagen de oracle.  
![Finalización descarga de la imagen Oracle](imagenes/Captura4.png)  

Y con el comando sudo podman images podemos observar que ya tenemos en el sistema la imagen de Oracle  
![Visualización imagen de Oracle](imagenes/Captura5.png)  

### Crear y comprobar el contenedor  

Ahora usamos el comando sudo podman volume create nombre, para crear un volumen por si se borrase o detuviese el contenedor por error, no se perdieran los datos.  
![Crear volumen](imagenes/Captura6.png)  

Con el siguiente comando mostrado en la captura lo que se está haciendo es arrancar el contenedor y abriendo el puerto 1521 para que pueda haber comunicación con la base de datos.  
![Arranque contenedor](imagenes/Captura7.png)  

con el comando sudo podman ps, podemos ver que está el contenedor ya creado y vemos algunos de sus datos  
![Observar contenedor](imagenes/Captura8.png)  

Con sudo podman -a podemos ver también datos del contenedor creado, y podemos ver que ya está en healthy por lo que significa que ya funciona.  
![Observar el contenedor en healthy](imagenes/Captura9.png)  

Y con el comando sudo podman logs nombre, podemos ver los logs que se han realizado del contenedor, es decir los logs internos del contenedor.  
![Ver logs del contenedor](imagenes/Captura10.png)  

### Entrar en Oracle y crear el usuario de trabajo  

Con el comando sudo podman exec -it cont-oracle /bin/bash lo que estamos haciendo es entrar a la terminal interactiva dentro del contenedor.  
![Entrar terminal interactiva dentro del contenedor](imagenes/Captura11.png)  

Con el comando sudo podman exec -it cont-oracle sqlplus / as sysdba, lo que estoy haciendo es entrar directamente dentro del contenedor como administrador para tener permisos para crear el usuario.  
![Entrar dentro del contenedor](imagenes/Captura12.png)  

Luego ya dentro del contenedor primero ejecutamos SHOW PDBS, para poder ver cuantas bases de datos existen.  
![Ver las bases de datos del contenedor](imagenes/Captura13.png)  

Con el comando ALTER SESSION SET CONTAINER = FREEPDB1; lo que estamos haciendo es mover toda la configuración que vamos a hacer a la base de datos FREEPDB1  
![Mover la configuración a FREEPDB1](imagenes/Captura14.png)  

Ahora con CREATE USER orauser IDENTIFIED BY "TU_CLAVE";  estoy creando un nuevo usuario que se llama orauser.  
![Crear usuario](imagenes/Captura15.png)  

Con el comando GRANT CREATE SESSION, CREATE TABLE TO orauser; lo que estoy haciendo es darle permisos al usuario orauser para que pueda entrar a la base de datos y crear tablas en ella.  
![Dando permisos al usuario](imagenes/Captura16.png)  

Y con ALTER USER orauser QUOTA 100M ON USERS; lo que estamos haciendo es limitar el espacio del usuario orauser a solamente 100 megabytes en la base de datos.  
![Añadir cuota al usuario](imagenes/Captura17.png)  

Y con un EXIT salimos del contenedor y volvemos a la terminal normal.  
![Salir del contenedor](imagenes/Captura18.png)  

Y para acabar con el comando sudo podman exec -it cont-oracle sqlplus orauser@//localhost:1521/FREEPDB1, lo que vamos a hacer es entrar a el contenedor que creamos con el puerto 1521 para conectarse a la base de datos FREEPDB1 con el usuario orauser, y podiendo la contraseña de orauser podremos ver que hemos accedido a la base de datos del contenedor sin problemas.  
![Acceder a la base de datos con el usuario creado](imagenes/Captura19.png)  

## 2. Desplegar PostgreSQL  

Ahora que ya hemos acabado con Oracle lo siguiente es PostgreSQL, para ello lo primero que debemos hacer es usar el comando sudo apt install postgresql postgresql-contrib php-pgsql, para instalar el gestor PostgreSQL en la máquina.  
![Instalando PostgreSQL](imagenes/Captura20.png)  

Ahora con los comandos sudo systemctl start postgresql y sudo systemctl enable postgresql, lo que estamos haciendo es iniciar el gestor arrancandolo, y con el enable estamos configurando el servicio para que se inicie automáticamente cada vez que se inicie sesión en la máquina y no tener que hacer el start siempre.  
![Iniciando el servicio de PostgreSQL](imagenes/Captura21.png)  

Y si ejecutamos el comando sudo systemctl status postgresql, podremos observar que el servicio está activado y funcionando sin ningún error por el enable se puede saber.  
![Comprobando que funciona el servicio de PostgreSQL](imagenes/Captura22.png)  

Ahora con el comando sudo -i -u postgres psql, lo que estoy haciendo es cambiar momentáneamente al usuario interno de PostgreSQL para poder acceder al servicio sin problemas y con permisos para poder crear el usuario.  
![Cambiando al usuario interno del servicio de PostgreSQL](imagenes/Captura23.png)  

Ya dentro usamos el comando CREATE DATABASE dbpg; para crear la base de datos dbpg que usara el usuario.  
![Creando la base de datos de PostgreSQL](imagenes/Captura24.png)  

Con el comando CREATE USER pguser WITH PASSWORD 'TU_CLAVE'; estamos creando el usuario pguser que es el que usaremos en la práctica.  
![Creando el usuario de PostgreSQL](imagenes/Captura25.png)  

Y con GRANT ALL PRIVILEGES ON DATABASE dbpg TO pguser; para darle todos los permisos al usuario pguser que creamos dentro de la base de datos dbpg que creamos antes.  
![Dando todos los permisos al usuario de PostgreSQL](imagenes/Captura26.png)  

y con \q salimos de la consola interna de postgreSQ.  
![Salir de la consola interna de PostgreSQL](imagenes/Captura27.png)  

y haciendo un exit cerramos sesión en el gestor de base de datos de PostgreSQL y volvemos a la terminal normal.  
![Saliendo de PostgreSQL](imagenes/Captura28.png)  

Y para acabar usando el comando psql -h localhost -U pguser -d dbpg -W, vamos a iniciar sesión en la base de datos dbpg con el usuario pguser, y poniendo la contraseña del usuario ya estaremos dentro de la base de datos de dbpg con el usuario pguser, por lo que ya habríamos acabado con PostgreSQL.  
![Comprobación de acceso a la base de datos con el usuario que cremos PostgreSQL](imagenes/Captura29.png)  

## 3. Desplegar MariaDB  

Ahora el último gestor de base de datos que queda es MariaDB y lo primero que tendremos que hacer es usar el comando sudo apt install mariadb-server mariadb-client, para instalar el gestor de base de datos de mariaDB.  
![Instalando MariaDB](imagenes/Captura30.png)  

Con el comando sudo systemctl enable --now mariadb lo que estamos haciendo es iniciar el servicio y configurando el inicio automático al iniciar sesión de MariaDB.  
![Iniciando el servicio de MariaDB](imagenes/Captura31.png)  

Ahora con el comando sudo mariadb, estamos entrando al gestor para poder configurarlo y crear el usuario.  
![Entrando al gestor MariaDB](imagenes/Captura32.png)  

Con el comando CREATE DATABASE dbm CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci; lo que estamos haciendo es crear la base de datos dbm que será la que usará el usuario que creemos, aparte le estamos diciendo que usará caracteres utf8  
![Creando la base de datos MariaDB](imagenes/Captura33.png)  

Con el comando CREATE USER 'muser'@'localhost' IDENTIFIED BY 'TU_CLAVE'; lo que estamos haciendo es crear el usuario muser el cual sera local.  
![Creando el usuario de MariaDB](imagenes/Captura34.png)  

Con GRANT ALL PRIVILEGES ON dbm.* TO 'muser'@'localhost'; lo que estamos haciendo es darle todos los permisos posibles al usuario muser a la base de datos dbm que creamos.  
![Dando todos los permisos al usuario que creamos dentro de la base de datos que creamos MariaDB](imagenes/Captura35.png)  

Y para acabar usamos el comando FLUSH PRIVILEGES; para recargar la tabla de privilegios del gestor para actualizar los permisos del usuario muser.  
![Actualizando los permisos de MariaDB](imagenes/Captura36.png)  

Y ejecutando EXIT salimos del gestor y volvemos a la terminal normal.  
![Saliendo del gestor MariaDB](imagenes/Captura37.png)  

Y para finalizar usamos el comando mariadb -u muser -p dbm para acceder a la base de datos de dbm con el usuario muser y al poner su contraseña entraremos dentro de la base de datos y podremos comprobar que realmente funciona lo que hemos configurado.  
![Comrpobación de funcionamiento y acceso de MariaDB](imagenes/Captura38.png)  

## 4. Conexiones desde otra máquina  

### PostgreSQL  

Ahora lo último que tenemos que hacer es acceder a los gestores desde otra máquina cliente, para eso primero debemos configurar una IP al servidor por lo que entramos al archivo mostrado en la captura.  
![Entrando al archivo de configuración de red](imagenes/Captura39.png)  

Y tendremos que poner una estructura parecida a la de la imagen para otorgarle una IP estática a la máquina, en este caso será la 192.168.100.1.  
![Asignando dirección IP a la máquina](imagenes/Captura40.png)  

En esta captura se puede ver que dio problemas el aplicarle la IP a la máquina por lo que tuve que hacer, sudo systemctl unmask systemd-networkd.service para desbloquear el servicio de red de systemd, sudo systemctl enable --now systemd-networkd.service para habilitar y arrancar inmediatamente el servicio encargado de gestionar las configuraciones de red de forma nativa.  
![Arreglando problema al asignar IP](imagenes/Captura41.png)  

Y lo último que se hizo fue un sudo chmos 600 al fichero de red del equipo para que dejara actualizar los datos de red y un sudo netplan try para actualizar los cambios de red y ya tener la IP estática.  
![Actualizando los cambios de red](imagenes/Captura42.png)  

Luego entramos al fichero que se muestra en la captura, y cambiamos listen_addresses = 'IP_DE_LA_VM' por la IP que le asignamos a la máquina  
![Entrando al archivo de postgresql](imagenes/Captura43.png)  
![Mostrando el archivo y que hay que cambiar](imagenes/Captura44.png)  
![Cambiando el dato por la IP del equipo](imagenes/Captura45.png)  

Y luego podemos ver la dirección IP que tendra el cliente, la cual la tenemos que añadir de manera manual  
![Mostrando configuración de red del cliente](imagenes/Captura46.png)  

Luego con nano entramos al archivo pg_hba.conf  para añadir una línea al final del archivo  
![Entrando al segundo archivo de postgre](imagenes/Captura47.png)  

Añadimos la linea host dbpg pguser 192.168.1.0/24 scram-sha-256  para indicar en que red se encuentra el usuario que creamos y guardamos.  
![Añadiendo la linea](imagenes/Captura48.png)  

Hacemos sudo systemctl restart postgresql para reiniciar el servicio y que utilice la configuración que hemos añadido.  
![Reiniciando el servicio de PostgreSQL](imagenes/Captura49.png)  

Y con sudo systemctl status postgresql podemos ver si el servicio está iniciado y funcionando sin problemas después de las modificaciones.  
![Comprobando el servicio de PostgreSQL](imagenes/Captura50.png)  

Luego si hacemos un sudo ufw status nos mostrara si el cortafuegos esta activado o no, como yo lo tengo desactivado me salto el comando para configurar el que no de problemas en la configuración.  
![Comprobar si el cortafuegos está activado o no](imagenes/Captura51.png)  

después ya en el cliente ejecutamos sudo apt install -y postgresql-client-common postgresql-client, para descargar en la máquina cliente el cliente de PostgreSQL ya que si no no nos dejará conectarnos a la base de datos.  
![Instalando cliente de PostgreSQL en la máquina cliente](imagenes/Captura52.png)  

Y ejecutamos psql -h IP_DE_LA_VM -U pguser -d dbpg -W parecido a cuando lo hicimos en la otra máquina, pero esta vez poniendo la IP de la máquina con el gestor, y así podremos acceder a la base de datos con el usuario pguser desde otra máquina virtual con un cliente.  
![Accediendo a la base de datos desde la máquina cliente](imagenes/Captura53.png)  

### MariaDB  

Luego entramos con nano al archivo mostrado en la captura de pantalla para modificar bind-address = IP_DE_LA_VM y cambiar el dato por la IP de la máquina virtual.  
![Entrando al archivo de configuración de mariadb](imagenes/Captura54.png)  
![Cambiando la información por la IP de la máquina virtual](imagenes/Captura55.png)  

Luego hacemos sudo mariadb para acceder al gestor para crea una cuenta para la IP concreta del cliente y concédele permisos solo sobre dbm.  
![Entrando al gestor de mariaDB](imagenes/Captura56.png)  

Ejecutamos CREATE USER 'muser'@'IP_DEL_CLIENTE' IDENTIFIED BY 'TU_CLAVE'; para asignarle al usuario que creamos la dirección IP de la máquina cliente.  
![Asignando la dirección IP del usuario con la de la máquina cliente](imagenes/Captura57.png)  

Luego le volvemos a dar todos los permisos al cliente dentro de la base de datos de dbm con GRANT ALL PRIVILEGES ON dbm.* TO 'muser'@'IP_DEL_CLIENTE';  
![Dando todos los permisos al usuario dentro de la tabla dbm](imagenes/Captura58.png)  

Y para acabar ejecutamos FLUSH PRIVILEGES;  y EXIT; para actualizar la tabla de permisos del gestor y salir después del gestor para volver a la terminal normal.  
![Actualizando la tabla de permisos y saliendo del gestor](imagenes/Captura59.png)  

Y para acabar ejecutamos sudo systemctl restart mariadb para reiniciar el servicio y por último un sudo systemctl status mariadb para ver el estado del servicio si funciona y está iniciado.  
![Reiniciando y comprobando el servicio de mariadb](imagenes/Captura60.png)  

Ya para acabar ejecutamos en el cliente sudo apt update && sudo apt install -y mariadb-client, para descargar el cliente de mariadb en la máquina cliente ya que si no no podremos entrar a la base de datos.  
![Instalando el cliente del gestor de mariadb en la máquina cliente](imagenes/Captura61.png)  

Y ya lo último ejecutamos mariadb -h IP_DE_LA_VM -u muser -p dbm para poder acceder con el usuario muser a la base de datos dbm que creamos en el gestor de mariaDB, es decir que nos conectamos a la base de datos desde la máquina cliente sin problemas.  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura62.png)  

### 1. Características de la VM y versiones instaladas de Oracle 23ai Free, PostgreSQL y MariaDB.  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura63.png)  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura64.png)  
para saber las versiones de los gestores es tan simple como usar --version "nombre del gestor" y para oracle podman images  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura65.png)  

### 2. Cómo comprobaste el estado de los tres gestores y la conexión con orauser, pguser y muser.  
Lo comprobe usando sudo systemctl status, ya que este comando nos permite ver el estado del servicio si esta encendido funcionando, etc  
y tambien viendo si estan activos los puertos con el comando nc -zv IP PORT  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura66.png)  

### 3. Problemas encontrados, mensajes de error relevantes y cómo los resolviste; si no hubo incidencias, indícalo.

Y aqui algunos fallos que he tenido, como un fallo con la contraseña de un usuario la cual la puse con "" en vez de '', o el de la red de la maquina la cual se explico antes el porque hice esos comandos, o por no realizar un update por lo que me fallaba la descraga de los clientes  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura67.png)  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura68.png)  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura69.png)  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura70.png)  
![Accediendo a la base de datos con el usuario creado desde la máquina cleinte](imagenes/Captura71.png)  

### 4. Fuentes consultadas  
Archivo de la tarea: file:///C:/Users/ASUS/Desktop/Mis%20Documentos%20JAVIER/Music/Downloads/Practica_Instalar_3_SGBD.pdf  


