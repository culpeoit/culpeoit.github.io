---
layout: post
title:  "Conectando Docker con nuestra base de datos PostgreSQL local"
date:   2020-07-20 
categories: servidores
---

Hace un tiempo me encontré con la necesidad de conectar una de mis aplicaciones en Docker con mi base de datos PostgreSQL que estan en la misma máquina virtual, por lo que hoy voy a hablar un poco de eso.

## Networking en Docker

Docker crea su propia red virtual sobre la que trabajan los contenedores, lo mismo hace `Docker-Compose` , creando su propia red para cada proyecto que se genere. Podemos ver nuestras redes con el comando `docker network ls` :

``` console
root@culpeo:~# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
46ff9d350d73        bridge              bridge              local
afc2c0ea86cc        host                host                local
7f4a975ff5ee        none                null                local
95dbae5a12e4        culpeo_default      bridge              local
```

Lo ideal es hacer las siguiente configuraciones en base a sobre qué red esté nuestro contenedor. En mi caso, voy a usar culpeo_default, que es una red creada por docker-compose.

Para ver un poco más de información ejecutamos el comando `docker network inspect culpeo_default` y nos vamos a centrar en el primer bloque del json que nos da el comando.

``` json
"Name": "culpeo_default",
        "Id": "95dbae5a12e4c7fb3bc9529e98231d568ab8a8b3256d7d57154b8f6e218a7728",
        "Created": "2020-04-29T23:10:01.966518068-03:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.24.0.0/16",
                    "Gateway": "172.24.0.1"
                }
            ]
        }
```

Bajo la sección `IPAM` vamos a encontrar la configuración de la red. En este caso el gateway siempre es el host sobre el que corre dicho contenedor.

## Configuración de PostgreSQL

Ya tenemos la IP de nuestro host dentro de Docker, junto con la dirección de red y su máscara. Ahora, nos falta configurar PostgreSQL para que acepte nuestras conexiones desde la red de Docker.

### postgresql.conf

Primero, vamos a decirle a Postgres que queremos que escuche por peticiones en ciertas IP. Para hacer esto vamos a modificar el archivo `/etc/postgresql/9.6/main/postgresql.conf` y buscar el siguiente campo:

``` conf
#listen_addresses = '*'                 # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
					# defaults to 'localhost'; use '*' for all
```

Podemos indicar las IPs separadas por coma, o usar algunas de las siguientes opciones:

* `*` todas lasinterfaces IP disponibles
* `0.0.0.0` todas las direcciones IPv4
* `::` todas las direcciones IPv6 

Lo ideal es no permitir que escuche en tantas direcciones si no es necesario. En este caso voy a seleccionar `*` y sacar el comentario inicial.

### pg_hba.conf

Ahora necesitamos darle acceso a las personas que quieran entrar desde la red de Docker.

Para esto, vamos a ir al archivo `pg_hba.conf` en el mismo directorio que estábamos y vamos a agregar la siguiente linea:

``` conf
TYPE	DATABASE		USER		ADDRESS		OPTIONS
host    base-pruebas		culpeo		172.24.0.0/16	md5
```

En este caso, optamos por decirle a Postgres tanto a qué base puede acceder esa red como qué usuario se puede logear desde esa red. Tener en cuenta que le di privilegios a `TODA LA RED`, por lo que cualquier contenedor podría conectarse.

Si bien en este caso le indiqué a Postgres qué base y qué usuario puede acceder la red, también podría haber puesto `all` para ambos campos y permitir a todos los usuarios acceder a todas las bases.

En cuanto a OPTIONS, hay varias opciones, en este caso utilizamos `md5` que se basa en una contraseña hasheada mientras que `password` , por ejemplo, las enviaría en texto plano.

### Fin

Con estos pasos ya realizados solo basta con reiniciar Postgres para que los cambios impacten.

``` console
systemctl restart postgresql
```

En caso de tener que hacer troubleshooting, es una buena opción iniciar un contenedor con nmap y ver que el gateway tenga el puerto 5432 abierto para descartar que el firewall no nos esté bloqueando el acceso.

