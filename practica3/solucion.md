# Pŕactica 3 SWAP
Luis Gil Guijarro 3ºC

## Balanceador de carga utilizando Nginx.

La instalación de Nginx se ha realizado utilizando apt:  apt-get install Nginx.

Una vez instalado y como queremos usarlo como balanceador de carga y no como servidor web tenemos que irnos a sus archivos de configuración y retocarlos. En concreto eliminamos los archivos default que se encuentra en /etc/nginx/sites-available y en /etc/nginx/sites-enabled.
Creamos un nuevo archivo de configuración llamado default.conf en el directorio /etc/nginx/conf.d/ y en el introducimos:

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

Siendo los ips 192.168.56.103 y 192.56.104 las ips de los dos servidores web a los cuales vamos a balancear la carga que les llega.
