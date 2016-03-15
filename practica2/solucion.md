# Práctica 2 SWAP
Luis Gil Guijarro 3ºC

## Probando el funcionamiento de la copia de archivos utilizando ssh.

Para ello utilizamos el comando descrito en el guion de prácticas el cual utiliza tar, un pipe y ssh para crear un archivo y la salida de este proceso de creación se redirige a ssh que lo deposita en la ip indicada, en mi caso 192.168.56.104.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/tar.png)

En mi caso ssh no solicita la contraseña debido a que previamente he realizado el paso de permitir que mi maquina Ubuntu-S1 pueda conectarse a Ubuntu-S2 usando ssh utilizando claves publicas-privadas proceso que explicaré como lo he llevado a cabo más adelante.

En la imagen podemos ver en primer lugar la creación de un directorio y tres archivos en él y después lo comprimimos y se lo mandos a la otra máquina utilizando ssh.

#Configurando ssh para acceso sin contraseñas modo root.

En primer lugar en la máquina secundaria creamos un par de claves público-privada usando el comando ssh-keygen (notose que ejecutamos como usuario root los comandos, esto es para que despues sea el usuario root el que pueda loguearse en la máquina remota):

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh1.png)


A continuación en la máquina principal (Ubuntu-S1) modificamos el archivo /etc/ssh/sshd_config para que nos permita acceder usando ssh como root. 

En la máquina secundaria (Ubuntu-S2) ejecutamos ssh-copy-id orden que copia la clave públicas que hemos generado anteriormente en las claves autorizadas de la máquina principal permitiendonos loguearnos en dicha máquina sin necesidad de introducir contraseña.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh2.png)


Comprobamos que podemos entrar como root sin introducir la contraseña:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh3.png)



#Clonando carpeta /var/www usando rsync

En primer lugar instalamos rsync en nuestro sistema con apt, después en la maquina secundaria ejecutamos el comando:

rsync -avz -e ssh root@192.168.56.103:/var/www/ /var/www/

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/rsync1.png)

Tras la ejecución de esta orden podemos comprobar que el contenido en la máquina del directorio /var/www/ es identico al contenido de ese mismo directorio en la máquina 1.

Para perfeccionar el clonado podriamos haber ejecutado la misma orden de más arriba pero con la opcion --delete:

rsync -avz --delete -e ssh root@192.168.56.103:/var/www/ /var/www/


Lo que habría dado lugar al mismo proceso de clonado pero se habrían borrado aquellos ficheros de la máquina secundaria que se hubieran borrado en la máquina 1.


#Manteniendo actualizado el directorio /var/www entre las dos máquinas.

Cron es un viejo conocido de las prácticas de las diversas asignaturas de la etsiit su especialidad es ejecutar tareas que previamente hemos configurado para que se ejecuten de manera periódica. En este momento vamos a utilizar cron para mantener identico el directorio /var/www entre las dos máquinas para ello tenemos que editar el fichero /etc/crontab

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/cron0.png "crontab antes de nada")

Como podemos observar en la imagen de más arriba el fichero crontab se divice en una serie de columnas separadas por espacios en blanco, cada una de estas columnas le especifica a cron cuando tiene que ejecutar la tarea que podemos observar en cron. Para la práctica nos piden ejecutar una tarea que mantenga actualizado el directorio /var/www entre las dos máquinas y que esta tarea se ejecute cada hora:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/cron1.png "crontab modificado")

Y ahora  esperamos :pray: :

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/cron2.png "prueba crontab funciona")

Como podemos observar el resultado es satisfactorio y con la tarea que hemos programado el directorio /var/www se actualizará cada hora.
