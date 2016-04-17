# Pŕactica 4 SWAP

Luis Gil Guijarro 3ºA


##Preparación de la práctica.


Antes de empezar la práctica hemos creado dos archivos html llamados prueba.html
en /var/www/html con el siguiente contenido

´´´
<html>
<head>
<title> prueba</title>
</head>
<body>
archivo para realizar la prueba 
</body>
</html>

```

![img](https://github.com/LuisGi93/swap2016/blob/master/practica4/imagenes/test.png)

```

Comprobamos que todo va correcto haciendo un par de peticiones a nuestro balanceador:


##Monitorización de la carga utilizando ab.
## Balanceador de carga utilizando Nginx.

La instalación de Nginx se ha realizado utilizando apt:  apt-get install Nginx.

Una vez instalado y como queremos usarlo como balanceador de carga y no como servidor web tenemos que irnos a sus archivos de configuración y retocarlos. En concreto eliminamos los archivos default que se encuentra en /etc/nginx/sites-available y en /etc/nginx/sites-enabled.
Creamos un nuevo archivo de configuración llamado default.conf en el directorio /etc/nginx/conf.d/ y en el introducimos:

```
upstream apaches {
	server 192.168.56.103;
	server 192.168.56.104;
}

server{
	listen 80;
	server_name balanceador;
	
	access_log	/var/log/nginx/balanceador.access.log;
	error_log	/var/log/nginx/balanceador.error.log;
	root		/var/www/;


	location /
	{
	  proxy_pass http://apaches;
	  proxy_set_header Host $host;
	  proxy_set_header X-Real-IP $remote_addr;
	  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	  proxy_http_version 1.1;
	  proxy_set_header Connection "";
	}
}
```
Siendo los IPs 192.168.56.103 y 192.56.104 las IPs de los dos servidores web a los cuales vamos a balancear la carga que les llega y siendo 192.168.59.105 la IP de nuestro balanceador.
![img](https://github.com/LuisGi93/swap2016/blob/master/practica3/imagenes/nginx_balanceador.png)

Probamos que el balanceador funciona haciendo ```curl 192.168.59.105``` :

![img](https://github.com/LuisGi93/swap2016/blob/master/practica3/imagenes/nginx_probando.png)


Como podemos ver la carga se distribuye estilo round-robin, por turnos. Además vamos a probar algoritmo de balanceo por ponderación para ello modificamos en nuestro archivo default.conf y el bloque upstream queda:

```
upstream apaches {
	server 192.168.56.103 weight=2;
	server 192.168.56.104 weight=1;
}
```
De tal manera que la máquina 1 tiene el doble de peso. Weight nos indica el peso de carga que le asignamos a esa IP. Probamos:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica3/imagenes/nginx_peso.png)

Como podemos observar de seis llamadas que se han hecho en cuatro de ellas ha respondido el servidor primero y en dos de ellas el servidor segundo.



#haproxy

Tras haber instaldo haproxy para configurarlo como balanceador modificamos el fichero /etc/haproxy/haproxy.cfg y lo dejamos asi:
```
global
        daemon
        maxconn 256

defaults
        mode http
        contimeout 4000
        clitimeout 42000
        srvtimeout 43000

frontend http-in
        bind *:80
        default_backend servers

backend servers
        server          m1 192.168.56.103 maxconn 32
        server          m2 192.168.56.104 maxconn 32
```
Tras para nginx e iniciar haproxy probamos a hacer un curl a la IP del balanceador:
![img](https://github.com/LuisGi93/swap2016/blob/master/practica3/imagenes/haproxy_balanceador.png)

Como podemos ver haproxy balancea correctamente el tráfico.

Para distribuir la carga entre los servidores en haproxy según su peso modificamos la parte backend servers en haproxy.cfg tal que quede así si deseamos que el primer servidor reciba el doble de carga:

```
backend servers
        server          m1 192.168.56.103 maxconn 32 weight 2
        server          m2 192.168.56.104 maxconn 32 weight 1
```
Probamos esta configuración, la máquina uno tendría el doble de peso:

![img](https://github.com/LuisGi93/swap2016/blob/master/practica3/imagenes/haproxy_peso.png)

El balanceo de carga en haproxy parece menos mecánico que en nginx, en nginx si hacias tres peticiones en dos de ellas seguidas respondía el servidor 1 y en la siguiente el 2 mientras que en haproxy si haces cuatro peticones en lugar de responder 1-1-2-1-1 hace 1-1-1-2.

