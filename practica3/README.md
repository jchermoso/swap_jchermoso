
# Mario Chaves Caballero
# Juan Carlos Hermoso Quesada
# Práctica 3. Balanceo de carga
Leer nota antes de empezar: para una mayor agilización del trabajo, nos lo hemos repartido mediante la creación de ocho máquinas: cuatro servidores (dos para cada uno), dos balanceadores (uno para cada uno, uno con nginx y otro con haproxy) y dos clientes.

## Crear máquina balanceadora

Se instala **Ubuntu-Server16.08** con todos los valores por defecto en una nueva máquina para que esta sea nuestro balanceador. No se instala ningún paquete adicional y así ahorramos memoria, y nos aseguramos que no hay ningún software que se apodere del puerto 80 ya que es el pueto por el que se recibirán las peticiones **HTTP**.

Además, hemos creado una tarjeta de red host-only con la dirección IP estática `192.168.56.103`.

## Instalar nginx

Los comandos recomendados para la instalación de **nginx** por parte del guión son:

`sudo apt-get update && sudo apt-get dist-upgrade && sudo apt-get autoremove`
    
La cual sirve para actualizar el sistema y eliminar archivos sin utilidades.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Limpieza.png)

`sudo apt-get install nginx`

Con este comando se instala el servidor web **nginx**.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Instalacion.png)

`sudo systemctl start nginx`

Con este comando se inicia el servidor **nginx**.

![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Iniciar.png)


## Configurar nginx como balanceador

Para confifurar nginx como balanceador tenemos que cambiar el contenido del fichero `/etc/nginx/conf.d/default.conf` o crearlo. Si el fichero ya existe tenemos que eliminar todo su contenido y cambiarlo por:


![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Confnginx.png)

En nuestro caso no hemos puesto peso a ninguna máquina ya que las dos tienen las mismas capacidades (son clonadas), hemos hecho que la carga que viene desde la misma dirección IP la gestione el mismo servidor final con `ip_hash` y hemos puesto que durate 5 segundos solo se tenga que hacer una sola conexión por la que llegarán las peticiones **HTTP** con `keepalive 5`, en vez de crear una conexión por petición.

A continuación reiniciamos **nginx** para que la configuración se guarde.

Luego hemos comprobado si la configuración funciona correctamente conectando con las máquinas finales mediante cURL:

![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Comprobacionesnginx.png)



## Benchmark en nginx

Primero tenemos que crear otra máquina de la misma forma como hemos creado la máquina balanceador, solo que ésta vez hemos instalado un servidor LAMP para poder utilizar un Apache Benchmark (ab).

La orden que hemos utilizado ha sido `ab -n 20000 -c 50 http://192.168.56.103/index.html`, y como resultado ha sido este:

![img](https://github.com/Mchc97/SAWP2018/blob/master/P3/Benchmark.png)


## Instalación y configuración de haproxy

Mediante el comando `sudo apt-get install haproxy` instalamos el software de balanceo haproxy
 ![enter image description here](https://raw.githubusercontent.com/jchermoso/swap_jchermoso/master/practica3/Captura%20de%20pantalla%20de%202018-04-19%2012-24-54.png)

Una vez instalado, procedemos a su configuración. Para ello, comprobamos que la IP del balanceador es la correcta, que no es la misma que alguna de los servidores

![enter image description here](https://raw.githubusercontent.com/jchermoso/swap_jchermoso/master/practica3/Captura%20de%20pantalla%20de%202018-04-19%2013-03-30.png)

Seguidamente, entramos en el archivo `/etc/haproxy/haproxy.cfg` y lo editamos, de manera que le indicamos que escuche en el puerto 80 y las IPs de los servidores que son `192.168.56.101` y `192.168.56.102`

![enter image description here](https://raw.githubusercontent.com/jchermoso/swap_jchermoso/master/practica3/Captura%20de%20pantalla%20de%202018-04-19%2013-23-54.png)

Probamos que ha ido bien la instalación con el comando `sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg`

![enter image description here](https://raw.githubusercontent.com/jchermoso/swap_jchermoso/master/practica3/Captura%20de%20pantalla%20de%202018-04-19%2013-16-47.png)

Como no ha dado avisos y errores, es que ha ido bien.

## Benchmark con haproxy

Ahora vamos a testear el balanceo con haproxy. Hemos realizado una prueba con una carga de 20000 peticiones con una concurrencia de 50, todo ello con el comando `ab -n 20000 -c 50 http://192.168.2.103/index.html` 

![enter image description here](https://raw.githubusercontent.com/jchermoso/swap_jchermoso/master/practica3/Captura%20de%20pantalla%20de%202018-04-21%2019-23-23.png)


## Conclusiones

Como podemos comprobar, nginx resulta mejor balanceador ya que el tiempo en el que se tarda en realizar las peticiones es mucho menor, además, se puede comprobar que los datos a pasar en nginx son mucho menores que en haproxy.