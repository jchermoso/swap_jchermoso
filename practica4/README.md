# Mario Chaves Caballero
# Juan Carlos Hermoso Quesada
# Práctica 4. Asegurar la granja web

## Generar e instalar un certificado automático 

Procedemos activar el módulo SSL de Apache 

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_21-17-34.jpg)

Y a generar los certificados SSL con **OpenSSL** tras haber creado el directorio en el que meterlos (en este caso, **/etc/apache2/ssl**). Los
certificados se deberán de copiar en todas las máquinas que vayan a usarse.

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_21-17-22.jpg)

## Configurar conexión

Una vez creados los certificados, toca indicar que son los nuevos certificados en el archivo **default-ssl.conf** en **SSLCertificateFile**
y en **SSLCertificateKeyFile**. 

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_21-17-46.jpg)

Y después activamos el archivo con **a2ensite default-ssl** y reiniciamos **Apache**. Una vez hecho esto, debemos abrir los puertos 
**80** y **443** (**HTTP** y **HTTPS**) en todas las máquinas que vayamos a usar.

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_16-52-14.jpg)

En el balanceador (**nginx**), tendremos que especificar también el puerto 443, aparte de encender el SSL e indicar las localizaciones de los
certificados SSL.

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_21-17-50.jpg)

## Iptables

Una vez hecho esto, procedemos a colocar reglas **iptables**. Para ello, creamos un script en el directorio **/etc/init.d** para 
que se ejecute cada vez que se encienda la máquina. Así pues, ahí meteremos nuestras reglas

![](https://github.com/jchermoso/swap_jchermoso/blob/master/practica4/photo_2018-05-09_21-17-38.jpg)


