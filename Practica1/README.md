# Practica 1. Presentación de las prácticas y preparación de las herramientas #
 
## Universidad de Granada - ETSIIT ##
### Servidores Web de Altas Prestaciones ###

### Participantes ###

- Raul Del Pozo Moreno
- Juan Carlos Hermoso Quesada

### Indice ###

1. [Descripcion](#id1)
2. [Configuracion de red](#id2)
3. [Conexion por ssh entre maquinas](#id3)
4. [Creacion y prueba de fichero en HTML](#id4)
5. [Conexion por curl entre maquinas](#id5)

### Descripcion <a name="id1"></a>

En esta practica se realizara la instalacion de dos maquinas virtuales con Ubuntu Server 16.04.3. Durante la instalacion se instalaran los servicios siguientes, tal y como indica la practica:

- OpenSSH server
- LAMP server

Como el proceso de alguna configuraciones es exactamente el mismo en ambas maquinas, en algunos pasos solo se mostrara la configuracion de una de ellas, indicando que hay que repetir para la otra maquina.

### Configuracion de red <a name="id2"></a>

Para permitir la conexion entre ambas maquinas virtuales, se ha creado una red Solo-Anfitrion en VirtualBox con las siguientes caracteristicas:

**Nombre Red Solo-Anfitrion**
 
![Imagen Red-Anfitrion Nombre](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/RedSoloAnfitrion.png "Imagen Red-Anfitrion Nombre")

**Configuracion Adaptador**

![Imagen Adaptador Solo-Afitrion](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/RedSoloAnfitrionAdaptador.png "Imagen Configuracion Adaptador")

**Configuracion DHCP**

![Imagen DHCP Solo-Anfitrion](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/RedSoloAnfitrionDHCP.png "Imagen Configuracion DHCP")

**Configuracion Red Maquina Virtual**

Una vez configurado Virtual Box, hay que crear el adaptador en las maquinas virtuales, este proceso es el mismo para las dos maquinas asi que solo se mostrara una de ellas, para ello, vamos a la **Configuracion** de la maquina virtual y en la opcion de **Red**, vamos a la pestaña **Adaptador 2**, aqui lo configuramos de la siguiente manera:

![Imagen Configuracion Red Maquina Virtual](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/Configuracion%20Red%20Maquina%20Virtual.png "Imagen Configuracion Red Maquina Virtual")

El siguiente paso es crear la interfaz de red nueva en Ubuntu Server, para ello, iniciamos la maquina virtual y una vez logueados en el sistema escribimos el siguiente comando:

- sudo cp /etc/network/interfaces /etc/network/interfaces.old (para tener un respaldo de seguridad)

Despues de crear la copia de seguridad, editamos el archivo para crear la interfaz:

- sudo vim /etc/network/interfaces

![Imagen Interfaz Maquina 1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/Configuracion%20interfaces%201.png "Imagen Interfaz 1")

![Imagen Interfaz Maquina 2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/Configuracion%20interfaces%202.png "Imagen Interfaz 2")

Como podemos ver, se le ha asignado la direccion ip 192.168.56.105 a la maquina ubuntu1, y 192.168.56.115 a la maquina ubuntu2, mediante estas direcciones las maquinas seran capaces de conectarse entre si como veremos mas adelante.

Para ver si funciona esto, hacemos ping a cada maquina

**Ping maquina 1 (ubuntu1) a maquina 2 (ubuntu2)**

- ping 192.168.56.115
- ping 192.168.56.105

![Imagen ping maquina 1 a 2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/Ping%20maquina%201.png "Imagen Ping maquina 1")

**Ping maquina 2 (ubuntu2) a maquina 1 (ubuntu1)** 

![Imagen ping maquina 2 a 1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/Ping%20maquina%202.png "Imagen Ping maquina 2")

Como vemos, hay conexion entre ellas, lo siguiente es comprobar si hay conexion hacia fuera, hacia internet, para ello ejecutamos el siguiente comando:

**Ping a google**

- ping 8.8.8.8 

![Ping fallido](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ping%20google%20fallido.png "Ping fallido")

En algunas ocasiones, es posible que de error y no haya salida (se quedara pensando durante mucho tiempo), para arreglar esto, ejecutamos los siguiente comandos:

- sudo ifconfig enp0s8 down
- sudo ip addr
- sudo ifconfig enp0s8 up
- sudo dhclient -v

![Reset de red](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/reset%20red.png "Reset red")

Con esto, hemos reseteado la red, y como vemos, ya funciona:

![Ping exito](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ping%20google%20exito.png "Ping exito")

Este proceso se realiza para ambas maquinas.

### Conexion por ssh entre maquinas <a name="id3"></a>

Para la conexion mediante ssh, tenemos que seguir una serie de pasos, los cuales consisten en:

1. Crear clave ssh
2. Importar clave a la otra maquina

Para la creacion de la clave ssh tenemos que acudir al siguiente comando, el cual nos creara una clave a la cual podremos configurar con un nombre (opcional) y darle una contraseña para su uso. 

- shh-keygen

**Creacion de clave ssh en maquina 1 (ubuntu1)**

![Clave ssh ubuntu1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-keygen1.png "Clave ssh en maquina 1")

**Creacion de clave ssh en maquina 2 (ubuntu2)**

![Clave ssh ubuntu2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-keygen2.png "Clave ssh en maquina 2")

Una vez creadas las claves, las importamos a la otra maquina con el siguiente comando.

- ssh-copy-id rauldpm@192.168.56.115 (en maquina 1)
- ssh-copy-id rauldpm@192.168.56.105 (en maquina 2)

**Envio de clave ssh de maquina 1 (ubuntu1) a maquina 2 (ubuntu2)**

![Envio clave ssh ubuntu1 a ubuntu2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-copy1.png "Envio clave ssh ubuntu1 a ubuntu2")

**Envio de clave ssh de maquina 2 (ubuntu2) a maquina 1 (ubuntu1)**

![Envio clave ssh ubuntu2 a ubuntu1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-copy2.png "Envio clave ssh ubuntu2 a ubuntu1")

Una vez que ambas maquinas tienen la clave de la otra, es hora de comprobar que se puede realizar la conexion entre ellas, esto se hara con el comando:

- ssh rauldpm@192.168.56.115 (en maquina 1, para conectar con la maquina 2)
- ssh rauldpm@192.168.56.105 (en maquina 2, para conectar con la maquina 1)

**Conexion de maquina 1 (ubuntu1) a maquina2 (ubuntu2)**

![Conexion ssh ubuntu1 a ubuntu2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-connect1.png "Conexion ssh ubuntu1 a ubuntu2")

**Conexion de maquina 2 (ubuntu2) a maquina1 (ubuntu1)**

![Conexion ssh ubuntu2 a ubuntu1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/ssh-connect2.png "Conexion ssh ubuntu2 a ubuntu1")

Como podemos ver, antes de cada conexion se ha realizado un ifconfig de la interfaz **enp0s8**, y si nos fijamos, una vez realizada la conexion, la direccion ip de la interfaz **enp0s8** cambia de valor, coincidiendo con la ip a la cual se le ha realizado la conexion.

Tambien podemos saber que ha sido un exito porque el nombre de la maquina cambia.


### Creacion y prueba de fichero en HTML <a name="id4"></a>

Ahora vamos a comprobar que el Apache2 esta en funcionamiento, para ello se va a crear un archivo HTML llamado hola.html en la ruta "/var/www/html/", el cual contendra el siguiente codigo:

<HTML>
  <BODY>
    Esto funciona  :)
  </BODY>
</HTML>

Esto lo vamos a mostrar solo en una maquina ya que es el mismo proceso en ambas maquinas.

- sudo vim /var/www/html/hola.html

Y para ver el contenido mas facilmente:

- sudo less /var/www/html/hola.html

![Archivo HTML](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/holaMaquina1.png "Archivo HTML")

Y en el navegador de la maquina anfitrion comprobamos que se interpreta el archivo.

![Comprobacion HTML](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/HTML.png "Comprobacion HTML")


### Conexion por curl entre maquinas <a name="id5"></a>

Para comprobar que tenemos conectividad mediante curl, realizaremos una peticion del archivo hola.html de la otra maquina.

- curl 192.168.56.115/hola.html (desde ubuntu1 a ubuntu2)
- curl 192.168.56.105/hola.html (desde ubuntu2 a ubuntu1)

**Curl desde la maquina 1 (ubuntu1) a la maquina 2 (ubuntu2)**

![Curl ubuntu1 a ubuntu2](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/curl1.png "Curl ubuntu1 a ubuntu2")

**Curl desde la maquina 2 (ubuntu2) a la maquina 1 (ubuntu1)**

![Curl ubuntu2 a ubuntu1](https://github.com/rauldpm/SWAP1718/blob/master/Practica1/Imagenes/curl2.png "Curl ubuntu2 a ubuntu1")
















