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

![img])(https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql.png)
Lo cual implicará la copia del backup creado en la máquina1 utilizando mysqldump en el directorio /root/ de la máquina2. Para que el comando anterior tuviera exito es necesario que desde la máquina2 se puediera acceder a la máquina1 utilizando ssh como root.

Una vez que tenemos este archivo en nuestra máquina es necesario crear la base de datos de la cual hemos hecho backup en la máquina1 en la máquina2 y a continuación la rellenamos con los datos contenidos en el archivo de backup como podemos ver en la imagen mostrada a continuación:

![img])(https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql2.png)



Podemos comprobar en la siguiente imagen que los datos insertados en la base de datos "contactos" en la máquina1 se encuentran en la base de datos "contactos" de la máquina2:

![img])(https://github.com/LuisGi93/swap2016/blob/master/practica5/capturas/transpadomanualsql3.png)


