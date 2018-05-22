# Mario Chaves Caballero
# Juan Carlos Hermoso Quesada
# Práctica 5:  Replicación de bases de datos MySQL

## Crear una BD e insertar datos

Para poder crear una base de datos necesitamos tener MySQL, que está incluido en el paquete de servidor LAMP.

Para poder iniciarlo solo tenemos que ejecutar la orden `mysql -u root -p` y poner la contraseña creada en la instalación del paquete LAMP.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/mysql.PNG)

Una vez iniciado MySQL, ya podemos crear una base de datos. El comando para crear una base de datos es `create database nombreDB;`.

Una vez creada debemos señalar que la vamos a utilizar con el comando `use nombreDB;`, y a partir de aquí empezaremos a crear las tablas de la base de datos.

Para crear una tabla se utiliza el comando `create table nombreTabla(atr1 tipo, atr2 tipo...);` donde atrX son el tipo de dato que habrá en las columnas.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/crearTabla.PNG)

A continuación vamos a insertar datos en la tabla con el comando `insert into nombreTabla(atr1, atr2, atr3) values (value1, value2, value3);`.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/InsertarEnTablas.PNG)

Y por último para mostrar las tablas se utiliza el comando `show tables;` y para hacer una consulta de toda la tabla `select * from integrantes;`.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/mostrarTablas.PNG)

## Replicar una BD MySQL con mysqldump

`mysqldump` es una herramienta que ofrece mysql para clonar bases de datos en la misma máquina.

Antes de replicar la BD, tenemos que restringir la entrada ya que puede que los datos de la BD pueden estar actualizandose, para ello debemos entrar en MySQL y poner el comando `FLUSH TABLES WITH READ LOCK;`.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-15.jpg)

Ahora es más seguro duplicar la BD con la herramienta `mysqldump` cuya sintaxis es `mysqldump nombreBD -u root -p > /root/nombreDB.sql`

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-08.jpg)

Una vez duplicada la BD podemos desbloquear las tablas entrando en MySQL y utilizando el comando `UNLOCK TABLES;`.

Una vez duplicada la BD, llevamos a esta a la otra máquina mediante `scp máquina1:/tmp/nombreBD.sql /tmp/` desde la máquina 2.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-21.jpg)

El archivo que hemos introducido en la máquina 2 tiene como formato texto plano, pero con las sentencias SQL para restaurar los datos de la BD de maquina 1. Sin embargo `mysqldump` no incluye la sentencia para crear la BD. Por este motivo, para terminar de importar la BD debemos crear una base de datos y por último, ejecutar la orden `mysql -u root -p nombreBD < /tmp/nombreBD.sql`.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-24.jpg)
![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-27.jpg)

## Replicación de BD mediante configuración maestro-esclavo

La opción anterior funciona perfectamente, pero es manual. Para poder hacerlo automáticamente podemos configurar el demonio para que la replicación se haga sobre un esclavo con los datos de un maestro.

Para ello las versiones de MySQL deben ser la misma.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-35.jpg)

Para empezar a configurar el demonio, en la máquina maestra (máquina1), tenemos que editar como root el fichero `/etc/mysql/mysql.conf.d/mysql.cnf` cambiando los siguientes apartados:
    · Comentar `bind-address 127.0.0.1` , que sirve para que escuche a un servidor.
    · Añadir `log_error = /var/log/mysql/error.log` , por si ocurre un problema tener información detallada
    · Establecer id del servidor a 1: `server-id = 1`
    · Seleccionar fichero para el registro binario que contiene toda la información del registro de actualizaciones de forma más eficiente y segura: `log_bin =/var/log/mysql/bin.log`

Después de hacer estos cambios reiniciamos el servicio con `/etc/init.d/mysql restart`.

A continuación pasamos a la configuración de la máquina 2. La configuración es la misma, excepto por el server-id que en este caso será igual a 2.

Al tener una versión de MySQL superior a la 5.5 , la configuración del maestro debemos hacerla desde el servicio.

Estando ya en MySQL, ejecutamos las siguientes sentencias:
    · `CREATE USER esclavo IDNTIFIED BY 'esclavo';`
    · `GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';`
    · `FLUSH PRIVILEGES;`
    · `FLUSH TABLES;`
    · `FLUSH TABLES WITH READ LOCK;`

Para terminar con la configuración del maestro obtenemos los datos de la BD que vamos a replicar para utilizarlos en la configuración del esclavo:

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-29.jpg)

Ahora en la máquina esclava entramos en MySQL e introducimos los datos mediante la sentencia:

`CHANGE MASTER TO MASTER_HOST:'192.168.56.101', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS='980', MASTER_PORT=3306;`

A continuación arrancamos el esclavo y desbloqueamos las tablas en el maestro:

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-32.jpg)

Y por último comprobamos el estado del esclavo  con `SHOW SLAVE STATUS\G` y comprobamos que `Slave_IO_Runnig`y `Slave_SQL_Running` tengan valor Yes, y que `seconds_Behind_Master` tenga un valor diferente a NULL.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-41.jpg)
![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-50.jpg)

Y por último una comprobación de que los datos introducidos en la máquina maestra se duplican en la máquina esclava:

![img](https://github.com/Mchc97/SAWP2018/blob/master/P5/photo_2018-05-22_11-51-38.jpg)


