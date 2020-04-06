# Lección 1 - P2 ISE

## SSH

Protocolo que te permite el acceso a una máquina remoto por un canal seguro (cifrado)

La principal diferencia de `ssh` y `telnet` es que este segundo no cifra los usuarios ni las contraseñas, con lo que cualquiera que intercepte nuestra comunicación nos podrá robar los datos

Vamos a instalarlo en Ubuntu `apt install openssh-server` , en CentOS viene por defecto.

`systemctl status ssh` para ver el estado de `ssh` .

Con `systemctl list-unit-files` podemos ver toooooooodos los servicios que hay

**Nosotros nos vamos a conectar desde CentOS a Ubuntu, pero se puede hacer a la inversa**

Primero vamos a ver cosas importantes del archivo de configuración `/etc/ssh/sshd_config`

- `Port` → aqui podemos cambiar el puerto de escucha de nuestro servidor, a partir de ahora, cuando lo cambiemos tendremos que acceder con el comando `ssh -p "puerto" usuario@ipdestino`
- `PermirRootLogin` → si se pone a `yes` cuando accedan a nuestra máquina podremos ser usuarios root, cosa increíblemente peligrosa, por lo tanto lo mantrendemos con `prohibit-password` o `no`

Cabe decir que despues de cualquier modificación de `sshd_config`  deberemos de ejecutar el comando `systemctl restart ssh` para reniciar el servicio `ssh`

### Conectarnos a SSH sin contraseña

Como vimos anteriormente este proceso lo vamos a realizar con CentOS como cliente y Ubuntu como servidor.

En CentOS (cliente) vamos a generar nuestras claves, pues vamos a usar un protocolo de encriptación de clave asimetrica. Las generamos con el comando `ssh-keygen.`

Posteriormente ejecutamos el comando `ssh-copy-id usuario@ipdestino`

Lo que hemos hecho es, crear en nuestro cliente dos claves, la pública y la privada, la pública, con este último comando se la hemos enviado a nuestro servidor, así cuando vayamos a conectarnos, el servidor y el cliente se darán cuenta de que se "conocen" y podremos acceder sin tener que teclear la contraseña.

**Carpeta .ssh**: contiene archivos importantes para el cliente. Esta es una carpeta oculta que encontramos en nuestro directorio de usuario. Documentos importantes en el interior: ·`id_rsa` → clave privada ·`id_rsa.pub` → clave pública ·`authorized_keys` → lista de claves públicas autorizadas para servidores. Es decir, aquí comprobamos si la clave pública enviada por algún cliente se encuentra acá ·`known_hosts` → algo parecido a lo anterior, pero aquí comprobamos si conocemos a un server al que nos vamos a conectar.