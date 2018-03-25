# Práctica 2. Clonar la información de un sitio web

## Instalar la herramienta rsync

Una vez instalada la herramienta rsync con el comando **sudo apt-get install rsync** y nombrado como propietario de la carpeta **/var/www** al usuario sin privilegios mediante **sudo chown jchermoso:jchermoso -R /var/www** pasamos a probar el funcionamiento de rsync.

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2012-33-07.png)

## Clonación con rsync
Realizamos la clonación de la carpeta **/var/www** de la máquina 1 (**192.168.56.101**) en la máquina 2, mediante la contraseña de la máquina 1. Para comprobar que la clonación ha concluido de forma correcta, hacemos **ls -la /var/www**, donde podemos corroborar que la clonación se ha realizado correctamente debido a que ambas carpetas tienen los mismos permisos y el mismo dueño.

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2012-48-36.png)

## Acceso sin contraseña para ssh
Para eso, ejecutamos el comando **ssh-keygen -b 4096 -t rsa**

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2012-54-43.png)

La **passphrase** la dejamos en blanco, ya que queremos acceder a ambas máquinas sin contraseña.

Después, la clave pública generada en la máquina secundaria la copiamos en la máquina principal por lo que primero tenemos que darle los permisos 600 mediante **chmod 600 ~/.ssh/authorized_keys** y luego la copiaremos a la máquina principal mediante **ssh-copy-id 192.168.56.101**.

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2012-58-36.png)

Y ahora, comprobamos ya se puede acceder a la máquina principal con ssh y sin contraseña:

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2013-00-52.png)

Como se puede ver, tras hacer **ssh 192.168.56.101**, al hacer ifconfig y fijarnos en **enp0s8** vemos que la IP es la correspondiente a la máquina principal, por lo que la conexión remota ha funcionado.

También funciona el ejecutar comando de forma remota:

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2012-59-12.png)

## Programar tareas con crontab

Mediante **crontab** podemos programar que la clonación de la máquina principal a la máquina secundaria se realice cada período de tiempo en segundo plano.

En este caso, queremos que se actualice el contenido de la carpeta **/var/www** cada hora en punto, por lo cual abriremos el crontab y pondremos esta línea **0 \* * * * * jchermoso rsync -avz -e ssh 192.168.56.101:/var/www /var/www**

![imagen](https://github.com/jchermoso/swap_jchermoso/blob/master/practica2/Captura%20de%20pantalla%20de%202018-03-14%2013-12-05.png)



