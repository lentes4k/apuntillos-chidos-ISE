# Lección 3 - P2 ISE

Instalamos las componentes de la pila de software LAMP (linux,apache, mariaDB, php). Esto es la combinación de software usada para definir la infraestrutura de un servidor web. Usado por: bajo coste, ubicuidad

## *CentOS*

### Instalamos apache y activamos:

Con `yum search` podemos buscar los paquetes que contienen lo que queremos.

`yum install httpd`

El comando `systemctl` se encarga de administar nuestro sistema y sus servicios.

`systemctl status httpd` para ver el estado de este servicio.

Lo activamos con `systemctl start httpd` y posteriormnete `systemctl enable httpd`

Lo relacionado con su confi lo encotramos dentro de `/etc/httpd/`

Como curiosidad `curl` el cual es un servicio orientado a transferecia de archivos entre servidor y cliente. Si le pasamos la web nos devuelve codigo html con bastante info.

Al hacer `curl` desde otra máquina nos va a decir que no tenemos abierto el puerto 80, lo abrimos con `firewall-cmd --zone=public --permanent -add-service=http` con `--permanent` nos aseguramos que al reiniciar esto siga funcionando.

`firewall-cmd --state` para ver el estado de nuestro firewall

Como curisoidad: con `firewall-cmd --list-all` podremos ver la confi por defecto del firewall. Con `firewall-cmd --list-all-zones` podemos ver las zonas del firewalp  

`firewall-cmd --reload` y ya funciona

En `/var/log/httpd` encontramos dos archivos `access_log` y `error_logo` los cuales muestran los accesos y los errores, serán tremendamente largos y en la practica se suelen borrar

Las páginas se cargar por defecto en `/var/www` pero mediante `DocumenRoot "directorio"` podremos cambiarlo en el archivo de confi. La página que se carga por defecto se llarama `index.html` pero esto se puede cambiar en el archivo de confi  

### Instalación y configuración de PHP

PHP es un lenguaje de programación.
`yum install php`

reiniciamos apache con `apachectl restart` para que cargue en memoria

Véase que `apachectl restart` es lo mismo que `systemctl httpd restart` 

Con `apachectl configtest` se examina en busca de errores sintacticos

Ahoa podemos por ejemplo crear un documento acabado en `.php` para ejecutar código en dicho lenguaje, al ponerle nombre tendremos que redireccionarlo desde el navegador (`ip/documento.php`)

### Instalamos mysql(maria db) y configuramos

MariaDB es un sistema de gestión de bases de datos derivado de MySQL 

`yum install mariadb-server`

no esta habilitado →`systemctl enable mariadb` 

`systemctl list-unit-files` pa ver los servicios de linux

mariadb usa comandos mysql

`mysql` estamos dentro

`create database ise2020;` →creamos una base de datos

`grant all privileges on ise2020.* to 'alumno'@'localhost' identified by 'ise2020';` → para tener un usuario en mySQL

`use nombrebase` para entrar en nuestra base de datos

`exit` para salir

descargamos un interprete de mysql en phpq (`yum install php-mysql`)

reinicio apaxe `apachectl restart`

Creamos una tabla, en este caso la llamaremos 'alumnos' e insertamos algo

    #mysql
    > USE ise2020
    > CREATE TABLE alumnos (id INT, nombre VARCHAR(10), apellidos VARCHAR(10), email VARCHAR(50));
    > INSERT INTO alumnos VALUES ('···', '···', '···', '···')

 Ahora modificamos el archivo que nos da el profe para poder mostrar en la web esta base de datos.

    <?php
    	$mysql = new mysqli('localhost', 'alumno', 'ise2020','ise2020'); //aqui -> (donde nos conectamos, usuario, password, nombre base de datos)
    	if ($mysql->connect_error) {
    		die('No pudo conectarse: ' . $mysql->connected_error);
    	 }
    	echo '<h1>Conectado satisfactoriamente</h1>';
    ?>
    
    <h1>Contenido de la tabla Alumnos</h1>
    <table border="1">
    <tr><td>Id</td><td>Nombre</td><td>Email</td></tr>
    
    <?php
    	$sql = "SELECT id, nombre, apellidos, email FROM alumnos";
    	$result = $mysql->query($sql);
    	if ($result->num_rows > 0) {
    		while($row = $result->fetch_assoc()) {
    			echo "<tr><td>".$row["id"]."</td><td>".$row["nombre"]." ".$row["apellidos"]."</td>";
    			echo "<td>".$row["email"]."</td></tr>\n";
    		}
    	}
    
    	mysql_close($enlace);
    	phpinfo();
    ?>

Después de esto veremos la tabla en la web.

## *Ubuntu*

### Instalacion de Apache y su confi

`apt install apache2` 

Activamos con `systemctl start apache2`

Ponemos en enable con `systemctl enable apache2`

Creo que por defecto al instalarlo se nos activa y se pone enable

Activamos nuestro firewall para poder abrir los puertos →`ufw enable` si lo queremos de forma fija → `systemctl enable ufw`

`ufw status` para ver el estado 

Vemos mediante `ufw app info 'Apache'` que nos está escuchando en el puerto 80, por lo tanto le damos permisos al firewall para poder abrir ese puerte mediante `ufw allow Apache`

`ufw reload`

[Podemos hacer todo tipo de comprobaciones como las que se habló en el apartado de CentOS](https://www.notion.so/Lecci-n-3-P2-ISE-c714b0ced50a487bb1b6d6553769e6ce#29721507a360442d9f1dbeeffc2896a8)

### Instalamos PHP

`apt install php`

`apt install libapache2-mod-php` sin esto nuestro php no se puede comunicar con el servidor

`apt install php-mysql` → con esto podemos interpretar mysql en php

Recargamos el servidor

### Vamos a instalar MariaDB

`apt install mariadb-server`

[Todo igual que hicimos en CentOS](https://www.notion.so/Lecci-n-3-P2-ISE-c714b0ced50a487bb1b6d6553769e6ce#134c7e116e254b72b420a50746ce9cd8)

En las distros basadas en RedHat los archivos de confi y gestores de paquetes decidieron llamar al servidor apache como httpd