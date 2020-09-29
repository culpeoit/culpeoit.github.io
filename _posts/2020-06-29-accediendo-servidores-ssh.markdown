---
layout: post
title:  "Accediendo a servidores por SSH"
date:   2020-06-29 
categories: servidores
---
En esta pequeña guía les voy a comentar cómo configuro mis accesos por SSH para acceder a un servidor que está publicado en internet para hacerlo de manera segura y confiable.

## Configurar SSH para no usar contraseñas

Como primer medida evito usar el acceso a un servidor a través de contraseñas, ya que por más complejas que sean, no hay manera de verificar quién las está usando.
Para evitar esto, suelo configurar al servidor SSH para que sólo haga autenticación a través de las `keys RSA`. Para hacer esto vamos a modificar el archivo ubicado en `/etc/ssh/sshd_cofig` y modificamos los siguientes campos:
``` console
PasswordAuthentication no
PubkeyAuthentication yes
```
Y reiniciamos el servicio `sshd`. 
```console
# Para sysvinit
service ssh restart
# Para systemd
systemctl restart sshd
```
Haciendo esto, deshabilitamos el acceso por contraseña y solo permitimos el acceso a través de la public key.

### Obtener nuestra public key

Ahora que no podemos acceder por contraseña, vamos a necesitar decirle al servidor cuál es nuestra `public key` para autenticarnos. Para esto, primero tenemos que crearla *desde la PC que vayamos a usar para conectarnos*, por lo que si vamos a conectarnos desde multiples PC, vamos a necesitar crear la public key para cada una.

Para crear la public key abrimos una consola y ejecutamos el comando `ssh-keygen`, el que nos va a pedir un nombre de archivo donde guardar la key, y una vez indiquemos el nombre del archivo, nos va a preguntar si queremos agregar una `passphrase` o contraseña para dicha key, esto nos agrega otra capa de seguridad para evitar que alguien que tenga acceso a nuestra PC no pueda loguearse en los servidores con nuestra key sin saber la contraseña, pero es un paso opcional.
Y una vez hecho todos los pasos vamos a tener una salida similar a esta:

```console
culpeo@patagonico-PC:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/culpeo/.ssh/id_rsa): culpeo
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in culpeo.
Your public key has been saved in culpeo.pub.
The key fingerprint is:
SHA256:91k8Pyded/NQMGD5BsbdzVvR/0lINsSib8XxHAl12Zw culpeo@patagonico-PC
The key's randomart image is:
+---[RSA 2048]----+
|           .==o*X|
|           o=B.EX|
|          ..=oO =|
|         o   +oB.|
|        S = ..+ +|
|       o o =   o.|
|      . o - + +.o|
|           . = o+|
|         .      .|
+----[SHA256]-----+
```

Y nuestra key va a estar en el `archivo.pub` que hayamos elegido.

### Configurar nuestra public key en el servidor SSH

Con nuestra public key ya generada, ahora necesitamos decirle al servidor SSH al que nos vamos a conectar cuál es nuestra key. Para esto, vamos a `/home/usuario/.ssh/known_hosts` y editamos el archivo agregando el contenido de nuestro `archivo.pub`.

## Cambiar el puerto por defecto

Todos sabemos que el puerto 22 es de SSH, por lo que nunca va a faltar el que vea el puerto en Shodan e intente entrar. Si bien es bastante dificil que alguien logre entrar sin la llave correspondiente, a mi personalmente me gusta usar un puerto no conocido para los puertos expuestos a internet.

Para cambiar el puerto volvemos a `/etc/ssh/sshd_config` y cambiamos la siguiente linea:
``` console
#	Port 22
```

Vamos a sacar el comentario (`#`) y cambiar el 22 por un puerto aleatorio que sea `mayor a 1023 y menor a 65535`.

## Conectarse al servidor

Ahora simplemente abrimos cualquier `terminal, PowerShell o Putty` si estamos en Windows y ejecutamos:

``` console
ssh <IP del Servidor> -p PUERTO
```

Y un ejemplo con su salida sería:

``` console
PS C:\Users\Culpeo> ssh root@172.16.42.75 -p 2102
Enter passphrase for key 'C:\Users\Admin/.ssh/id_rsa':
Linux odoo12 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Jun 29 17:53:18 2020 from 201.254.xxx.xxx
Linux odoo12 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Jun 29 17:53:18 2020 from 201.254.xxx.xxx
root@culpeo-remoto:~#
```

Y eso es todo! Ya tenemos una conexión segura con nuestro servidor remoto.
