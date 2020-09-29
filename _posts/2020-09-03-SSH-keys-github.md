---
layout: post
title:  "Usando SSH keys con Github"
date:   2020-09-03
categories: misc
---
Conectarse por SSH usando claves públicas ahorra muchos problemas, aunque cuando tenemos muchas claves puede volverse un poco complicado. Hoy voy a explicar como uso `Github con SSH` para evitar tener que ingresar mis credenciales cada vez que hago un push.

## Generando las claves

Generar claves en SSH es bastante sencillo, [además de que ya lo hicimos alguna vez]({% post_url 2020-06-29-accediendo-servidores-ssh %}). En este caso, vamos a ir a nuestra carpeta de SSH con `cd $HOME/.ssh` y una vez ahí vamos a crear nuestra clave para Github con `ssh-keygen`

```console
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/culpeo/.ssh/id_rsa): github
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in github
Your public key has been saved in github.pub
```

Ahora tenemos `nuestra clave pública en el archivo github.pub` , por lo que vamos a pegar su contenido en nuestra configuración de Github [en el siguiente link](https://github.com/settings/ssh/new).

Una vez guardada la clave ya podemos clonar nuestros repos con SSH y ya no nos va a pedir la contraseña de nuestra cuenta.

## Usando un archivo config en SSH

Ahora, yo tengo varias claves públicas para administrar varios servidores, y se vuelve tedioso que SSH me pida los distintos passphrase hasta encontrar la clave correcta, por lo que prefiero especificarle directamente que clave usar. Para eso, vamos a `crear un archivo config en .ssh`, en el mismo voy a poner lo siguiente:

```config
Host github.com
  HostName github.com
    User git
      IdentityFile ~/.ssh/github
      IdentitiesOnly yes
```

Y adicionalmente, puedo indicarle mis otros servidores para que sea más rápido de ingresar también:

```config
Host servidor1
    HostName 192.168.1.1
    User root
        IdentityFile ~/.ssh/servidor1
        IdentitiesOnly yes

Host servidor2
    HostName 172.16.1.1
    Port 9999
    User culpeo
        IdentityFile ~/.ssh/servidor2
        IdentitiesOnly yes
```

Ahora simplemente puedo acceder con `ssh servidor1` o `ssh servidor2` en vez de: `ssh culpeo@172.16.1.1 -p 9999 -i .ssh/servidor2` . Bastante mejor, no?

### Fin


