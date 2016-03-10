# Práctica 2 SWAP
Luis Gil Guijarro 3ºC

## Probando el funcionamiento de archivos utilizando ssh.

Para ello utilizamos el comando descrito en el guion de prácticas el cual utiliza tar, un pipe y ssh para crear un archivo y la salida de este proceso de creación se redirige a ssh que lo deposita en la ip indicada, en mi caso 192.168.56.104.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/tar.png)

En mi caso ssh no solicita la contraseña debido a que previamente he realizado el paso de permitir que mi maquina Ubuntu-S1 pueda conectarse a Ubuntu-S2 usando ssh utilizando claves publicas-privadas proceso que explicaré como lo he llevado a cabo más adelante.

En la imagen podemos ver en primer lugar la creación de un directorio y tres archivos en él y después lo comprimimos y se lo mandos a la otra máquina utilizando ssh.

#Configurando ssh para acceso sin contraseñas modo root.

En primer lugar en la máquina secundaria creamos un par de claves público-privada usando el comando ssh-keygen:
![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh1.png)


A continuación en la máquina principal (Ubuntu-S1) modificamos el archivo /etc/ssh/sshd_config para que nos permita acceder usando ssh como root. 

En la máquina secundaria (Ubuntu-S2) ejecutamos ssh-copy-id orden que copia la clave públicas que hemos generado anteriormente en las claves autorizadas de la máquina principal permitiendonos loguearnos en dicha máquina sin necesidad de introducir contraseña.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh2.png)


Comprobamos que podemos entrar como root sin introducir la contraseña:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica2/imagenes/ssh3.png)

