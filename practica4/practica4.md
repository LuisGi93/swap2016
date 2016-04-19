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


Como se puede observar en las gráficas anteriores las columnas de los datos obtenidos utilizando Nginx y Haproxy son muy parecidas no habiendo ninguna clase de diferencia significativa. Lo más destacable es el grán escalon que ser puede observar en la primera gráfica del tiempo que se tarda en realizar el test entre la columna producto de realizar el test sobre un servidor directamente sin balanceador de por medio y las otras dos columnas el resultado que puedo extraer de la primera gráfica es que el uso de un balanceador produce un retardo significativo en servir un número elevado de peticiones.

A continucación muestro las gráficas de las desviaciones tipicias de la tabla de más arriba.

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/abtt_dt.png)

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/abrs_dt.png)

La desviación típica mide la desviación que han tenido los valores con respcto a la media. Observando la primera de las dos gráficas anteriores se puede observar como ante la toma de 10 muestras casi todos los valores que se han tomado con respcto al servidor directamente sin balanceador por en medio están muy parejos y posiblemente si tomáramos otras 10 muestras todas obtendrían un valor muy parecido. Sin embargo cuando hay un balanceador de por medio los valores fluctuan mucho de una medición a otra.





##Monitorización de la carga utilizando Siege.

Vamos a realizar el mismo ejercicio que hemos hecho con Apache Benchmark pero ahora utilizaremos Siege en su lugar. Utilizaremos el siguiente comando:
```
siege -b -t60s 192.168.59.104/m2.jpg
```
La dirección 192.168.59.104 es a un servidor apache sin balanceo tras la carga de 5 pruebas sobre esta IP la cambiaremos por la de los diferentes balanceadores. A diferencia del test anterior que se realizaba sobre una página web sencilla ahora en cada servidor apache va a ver una imagén (m2.jpg) identica en los dos servidores y el test se hará para obtener esta imagen lo que implica un cambio radical en cuanto al peso ya que la imágen pesa 16MB y la página pocos kb si llega. 


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegetabla.png)

A continuación mostramos los gráficos obtenidos de realizar la medía sobre las 5 mediciones para el servidor solo, para balanceador Nginx y para balanceador Haproxy. 


![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_media.png)

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_media2.png)

Throughput nos viene a indicar el número medio de bytes que se tranfieren en general desde el servidor  a todos los usuarios que ahora están accediendo al servidor. En la primera gráfica podemos observar como las medidas tomadas sobre un servidor Apache directamente indican que su rendimiento medio es más o menos 5 veces superior al rendimiento medio que indican las medidas obtenidas con un balanceador de por medio. Ahora si podemos apreciar una diferencia entre  utilizar como balanceador Nginx y Haproxy siendo superior Nginx por un par de MB/sec esto se puede observar muy bien en las tablas. Lo dicho para la primera gráfica se puede decir para la segunda la del Transaction rate.
![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_dt.png)

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/siegers_dt2.png)

Viendo las gráficas de las desviaciones típicas ocurre el caso inverso que en el test realizado con Apache Benchmark. Ahora las medidas que más fluctuan con respecto a la media son las del servidor solo lo cual nos indica que posiblemente para obtener un resultado más fiable hubiera sido mejor tomar más medidas. Esta fluctuación puede ser resultado o bien de la herramiento da medición (Siege) o bien debido al recurso obtenido que ahora es bastante  más realista. En este caso parece que las medidas obtenidas con Haproxy son las más fiable pero viendo que tanto Haproxy como Nginx sus medidas estan rozando al 1 seguramente ambas sean igual de fiables.

#Conclusión
La conclusión a la que llego es que el uso de balanceador acarrea un coste en rendimiento y en el retardo en que se tarda en servir las peticiones. Entre el uso de Nginx o de Haproxy como balanceador parece que Nginx es perceptiblemente más eficiente que Haproxy aunque no por mucho posiblemente para obtener mejores conclusión hubiera sido necesario tomar medidas sobre más valores en lugar de sobre solamente dos.
