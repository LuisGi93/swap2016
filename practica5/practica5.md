#Práctica 5

Luis Gil Guijarro 3ºA


En esta práctica vamos a crearnos unas bases de datos, insertar datos en ellas y después realizaremos copias de seguridad de ellas en otro ordenador. Para ello utilizaremos las máquinas virtuales creadas en prácticas anteriores.

##Creación de las BD, insercción de datos y replica con MySQL

Una vez haber entrado en MySQL hemos creado la base de datos "contactos"  y a continuación hemos creado la tabla datos utilizando el comando:
```
create table datos(nombre varchar(100), tlf int);
```

Y a continuación hemos insertado una serie de datos. En la imagen de más abajo podemos ver la tabla que hemos creado y los datos que hemos insertado en ella.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/insertardatossql.png)

## Replicación de BD con mysqldump.

Para replicar una base de datos que se encuentra en maquina1 en otra máquina a la que llamaremos máquina2 utilizaremos mysqldump. En primer lugar en mysql dentro de la máquina1 ejecutaremos:

```
mysql -u root -p
mysql> FLUSH TABLES WITH READ LOCK;
mysql> quit

```
Con estas ordenes bloquemos la escritura en estas tablas para evitar su modificación.
Y a continuación utilizamos mysqldump para crear un backup de la base de datos "contactos":

```
mysqldump contactos -u root -p > /root/ejemplodb.sql
```


![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/unlocksqll.png)
Todo lo descrito anteriormente podemos verlo en la captura de más arriba. A continuación en la otra máquina en la máquina2 ejecutamos el siguiente comando:
```
scp root@192.168.59.103:/root/ejemplodb.sql /root/

```

![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql.png)
Lo cual implicará la copia del backup creado en la máquina1 utilizando mysqldump en el directorio /root/ de la máquina2. Para que el comando anterior tuviera exito es necesario que desde la máquina2 se puediera acceder a la máquina1 utilizando ssh como root.

Una vez que tenemos este archivo en nuestra máquina es necesario crear la base de datos de la cual hemos hecho backup en la máquina1 en la máquina2 y a continuación la rellenamos con los datos contenidos en el archivo de backup como podemos ver en la imagen mostrada a continuación:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql2.png)



Podemos comprobar en la siguiente imagen que los datos insertados en la base de datos "contactos" en la máquina1 se encuentran en la base de datos "contactos" de la máquina2:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql3.png)


##Replicación BD utilizando configuración maestro-esclavo.

A continuación vamos a utilizar la opción que nos permite MySQL para configurar un demonio con el cual realizar la replicación de una BD. Para ello modificamos el archivo /etc/mysql/my.cf y dejamos los siguientes campos como siguen:
```
...

#bind-address 127.0.0.1
...

log_error = /var/log/mysql/error.log

...

server-id = 1

...

log_bin = /var/log/mysql/error.log
```
Y reiniciamos mysql:
```
sudo service mysql restart.
```
En la máquina1 que nos hará de maestro introduzco las siguentes sentencias de mysql:

```
mysql> CREATE USER esclavo IDENTIFIED BY 'esclavo';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
mysql> FLUSH PRIVILEGES;
mysql> FLUSH TABLES;
mysql> FLUSH TABLES WITH READ LOCK;
```
A continuación con la sentencia "SHOW MASTER STATUS" tomo nota del mysql...0001  y del número que hay debajo de "Position" y como la versión de mysql que tengo instalado es superior a la 5.5 en mi máquina esclavo ejecuto la orden "CHANGE MASER ..." que podemos ver en la imágen a continuación.


![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql5.png)

En esta imagen en la parte superior se está ejecutando mysql en la máquina1 y en la parte inferior mysql en la máquina2. En ella se puede observar como en primer  lugar me equivoqué con los parámetros MASTER_LOG_POSITIOM y por tanto el esclavo no funcionaba, teniendo que pararlo y reconfigurarlo.

A continuación muestro otra imagen donde en la parte inferior el campo "Seconds behind master" está a un valor distinto de null lo cual implica que nuestro esclavo funciona correctamente pero para comprobarlo inserto una serie de valores en la máquina maestro para ver si estos aparecen en la base de datos replicada en la máquina esclavo.


![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql6.png)

Tras la inserción de algunos datos en la base de datos "contactos" en la máquina1 estos aparecen en la base de datos de la máquina2:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql7.png)

A continuación por si hay dudas de que la parte superior es la máquina1 maestro y la inferior la máquina2 esclavo vuelvo a insertar datos en la máquina1 y en la 2 aparecen (ahora aparecen los hostname, esta captura pertenece al último apartado donde demuestro el correcto funcionamiento de la replicación utilizando MASTER TO MASTER):


![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql9.png)

Todo funciona correctamente.

## Master to master. Ejercicio adicional.

Ahora el objetivo es que lo insertado en la máquina1 se replique en la 2 y viceversa. Para ello tenemos que realizar las modificaciones mostradas en el anterior apartado sobre el archivo /etc/mysql/my.cf en la máquina2. Reinicar mysql y en la máquina1 ejecutar la sentencia "CHANGE MASTER TO MASTER_HOST.." parecido a la que ejecutamos en el anterior apartado sobre la máquina2 solo que habrá que modificar los campos "MASTER_HOST" por la IP de la máquina2 (192.168.59.104), el valor "MASTER_LOG_FILE" y "MASTER_LOG_POS" por el que muestra ejecutar el comando "SHOW MASTER STATUS" en la máquina2. 


![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql10.png)


Una vez ejecutada la sentencia "CHANGE MASTER TO MASTER_HOST.." en la máquina1 ejecutamos la sentencia "START SLAVE;" e insertamos datos en ambas máquinas para comprobar que funciona correctamente el MASTER TO MASTERT:
![img](https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql9.png)

Primero insertamos mon en la máquina1, comprobamos que se ha insertado en la máquina2. Todo correcto insertamos "mok" y comprobamos en la máquina1 que se replica en la BD de la máquina1. Esta imagen constata el correcto funcionamiento de la replicación de BD  MASTER to MASTER.
