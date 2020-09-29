---
layout: post
title:  "Montando un servidor NGINX"
date:   2020-06-29 
categories: servidores
---
Montar un `servidor HTTP o HTTPS` es bastante simple, en esta pequeña guia vamos a instalar y configurar NGINX en un Debian.

## Instalando NGINX

Si bien este paso es relativamente simple, hay distintas alternativas segun la distribución que se esté usando. En mi caso basta con un `update y apt install`.

```console
apt update
apt install nginx -y
```
En este caso usamos `-y` para que instale los paquetes sin solicitar confirmación.

Para otras distribuciones, visitar la [documentación oficial de NGINX](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)

Si la instalación finaliza con éxito deberían ver la página de inicio de NGINX en [localhost](localhost) o [127.0.0.1](127.0.0.1)

## Configración inicial

### Modificando los sitios

Lo primero que debemos hacer es deshabilitar el sitio por defecto que incluye NGINX, o en este caso, modificarlo.

Por defecto, NGINX aloja los sitios en `/etc/nginx/sites-available` , ahi vamos a encontrar un archivo llamado `default` que va a contener la información de nuestro sitio. El archivo tiene una estructura similar a esta:

```console
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html/;

        # Add index.php to the list if you are using PHP
        index index.html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {
                deny all;
        }
}
```

Omití gran parte de los comentarios para que sea más simple de entender, pero el archivo original puede diferir de este un poco.

Voy a explciar algunos de los términos clave:

* `listen 80` : Puerto en el que el servidor va a escuchar esperando peticiones
* `default_server` : Este flag le indica a NGINX que este es el sitio por defecto, es decir que si le llega una petición desconocida, responde con el contenido de este archivo. *Nota: No puede haber más de un default_server.*
* `root /var/www/html` : Indica la ubicación de los archivos donde se aloja nuestra web (solo si está en el mismo servidor)
* `server_name` : El nombre de nuestro sitio, por ejemplo `dominio.com`, o `culpeo.blog` .

Una vez modificados los valores que necesitemos, como `server_name`, y de que cargamos los archivos html en `/var/www/html o la carpeta root` que tengamos en el archivo de configuración, podemos guardar el archivo y renormarlo a nuestro sitio.

``` console
cd /etc/nginx/sites-available/
nano default
mv default sitio.com
```

Y solo para simular cómo sería crear un sitio desde cero vamos a borrar el archivo `/etc/nginx/sites-enabled/default` y lo volveremos a crear en el siguiente paso.

### Habilitando sitios

Para habilitar un sitio, basta con hacer una copia del archivo del sitio que se encuentra en `sites-available` en `sites-enabled` , pero es una buena practica hacer un `link virtual` entre ambos archivos. Esto lo hacemos con el comando `ln -s`

``` console
cd /etc/nginx/
ln -s sites-available/sitio.com sites-enabled/sitio.com
```
Ahora necesitamos reiniciar NGINX con el comando `nginx -s reload` , esto lo que hace es comprobar la sintaxis de los archivos de configuración y reiniciar NGINX, en caso de haber un error de sintaxis lo devolvera en la misma consola.

Y con esto ya tenemos nuestro servidor web andando de manera local. Ahora podemos acceder al servidor de manera local editando nuestro archivo hosts o publicarlo en internet y accederlo con la IP pública.
