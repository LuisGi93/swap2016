# Pŕactica 4 SWAP

Luis Gil Guijarro 3ºA


##Preparación de la práctica.


Antes de empezar la práctica hemos creado dos archivos html llamados prueba.html
en /var/www/html con el siguiente contenido

```
<html>
<head>
<title> prueba</title>
</head>
<body>
archivo para realizar la prueba 
</body>
</html>

```

Comprobamos que todo va correcto haciendo un par de peticiones a nuestro balanceador:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/test.png)





##Monitorización de la carga utilizando ab.

Vamos a utilizar Apache Benchmark para comprobar el funcionamiento en primer lugar de uno de los  servidores Apache solo sin utilizar granja web para balancear el tráfico, para ello ejecuto 10 veces el comando:

```
ab -n 1000 -c 10 192.168.59.103/prueba.html

```
A partir de los datos obtenidos saco la media y la desviación típica.

En la tabla de más abajo se puede observar el resultado de haber ejecutado el comando ab anterior 10 veces para el servidor solo otras 10 utilizando Nginx como balanceador y otras 10 utilizando Haproxy. Los datos tomados de la salida producida por ab han sido el tiempo total que le ha llevado a realizar el test y el tiempo medio de peticiones por segundo. Todo esto ha sido recopilado en la tabla que podemos ver a continuación:


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/abtabla.png)

Y a continuación en una gráfica utilizando las medias:


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/abtt.png)

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/abrs.png)

##Monitorización de la carga utilizando Siege.

Vamos a realizar el mismo ejercicio que hemos hecho con Apache Benchmark pero ahora utilizaremos Siege en su lugar. Utilizaremos el siguiente comando:
```
siege -b -t10s 192.168.59.104
```
La dirección 192.168.59.104 es a un servidor apache sin balanceo tras la carga de 10 pruebas sobre esta IP la cambiaremos por la de los diferentes balanceadores.
Ahora solo tendremos en cuenta el parámetro "request per second" ya que sería absurdo utilizar el tiempo que tarda en llevarse a cabo la medida porque siempre es el mismo.
La tabla obtenida de las mediciones la podemos observar más abajo:


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegetabla.png)

A continuación mostramos los gráficos obtenidos de realizar la medía sobre las 10 mediciones para el servidor solo, para balanceador nginx y para balanceador haproxy. TAmbién realizamos una gŕafica con las desviaciones típicas:


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_media.png)

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_dt.png)


