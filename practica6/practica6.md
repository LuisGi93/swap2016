#Práctica 5

Luis Gil Guijarro 3ºA

En esta práctica vamos a crear un RAID1 en un servidor sobre una máquina virtual y vamos a comprobar su correcto funcionamiento.


##Creación del RAID1.


En primer lugar le añadimos a nuestra máquina virtual dos nuevos discos a los que he llamado "Raid1aa.vdi" y "Raid1bb.vdi". Utilizando la orden
```
 fdisk -l
```
comprobamos que identidad le ha asignado Linux a estos dos discos introducidos. En mi caso los dos nuevos discos insertados son conocidos como /dev/sdb  y /dev/sdc.


![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/fdisk.png)

Una vez que conocemos la identidad de estos dos discos y hemos instalado el programa mdadm procedemos a la creación del RAID1 utilizando este programa. Para ello insertamos la orden:
```
mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc
```
y a continuación pasamos a darle formato al RAID1 recientemente creado utilizando mkfs. Para finalizar creamos el directorio /dat que será el utilizado para montar el RAID1.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/fdisk.png)

Si queremos que el RAID1 se monte cada vez que iniciamos el equipo en el fichero /dev debemos editar el fichero /etc/fstab. Obtenemos en primer lugar el identificador único del dispositivo utilizando la orden:
```
ls -l  /dev/disk/by-uuid/
```
![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/byuuid.png)

y en el fichero /etc/fstab lo editamos tal cual podemos ver en la imagen de abajo:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/fstab.png)

Ahora ya si tenemos el RAID1 listo para ser utilizado.

##Probando el RAID1.

En primer lugar nos desplazamos al directorio /dat que es en el que se encuentra montado el RAID1. Una vez allí le añadimos algunos datos como por ejemplo un archivo vacio llamado "swap2016". Utilizando la orden:
```
cat /proc/mdstat
```
comprobamos el estado del RAID1 y vemos que está activo y compuesto por los discos /dev/sdb y /dev/sdc. A continuación podemos simular un fallo en el disco /dev/sdb para a continuación removerlo del RAID1 utilizando la siguiente secuencia de órdenes:

```
sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb
sudo mdadm --manage --remove /dev/md0 /dev/sdb
```
![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/removedisk.png)

Como se puede comprobar en la imágen de más arriba el RAID1 sigue funcionando y no tenemos ningún problema en acceder a los ficheros almacenados en /dat.
A continuación pasamos a añadir de nuevo el disco que /dev/sdb que anteriormente habíamos retirado del RAID1 y podemos ver como el RAID1 se reconstruye correctamente.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/anadimosinformacion y add.png)

En la imagen de arriba podemos ver como anter de añadir /dev/sdb de nuevo al RAID en primer lugar añadimos algunos archivos más a /dat para comprobar si cuando se reconstruya el RAID1 estos siguen alli.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica6/capturas/ultima.png)

Una vez finalizado el proceso de reconstrucción no hay ningún problema en acceder a los archivos  de /dat.

