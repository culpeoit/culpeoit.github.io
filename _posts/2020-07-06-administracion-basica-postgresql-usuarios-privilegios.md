---
layout: post
title:  "Administración básica de PostgreSQL - Usuarios y privilegios"
date:   2020-07-06
categories: servidores
---
<p align="center"><img src="/assets/img/postgres.png" alt="PostgreSQL" width="50%" /></p>

En el trabajo tenemos un par de aplicativos web que corren sobre un servidor `PostgreSQL` , por lo que se me ocurrió contar un poco cómo administro esas bases de manera básica.

## Usuarios y permisos

Primero hay que definir a un usuario en #PostgreSQL#:

PostgreSQL usa el término #role#, un rol puede ser tanto un usuario como un grupo, pero un rol no necesariamente es siempre un usuario.

### Creando usuarios

Como primer medida, necesitamos estar logeados en el usuarios de `PostgreSQL` , por lo que hacemos `su postgres`. Una vez en el usuario, ingresamos a la consola de administración con el comando `psql` :

``` console
postgres@culepo:~$ psql
postgres=#
```
Ahora procedemos a crear un usuarios con el comando `CREATE USER`

``` console
postgres=# CREATE USER culpeo WITH PASSWORD contraseña;
```

Adicionalmente podemos agregar permisos al final de la sentencia, como `CREATEDB` o `CREATEUSER` , pero en este caso vamos a seguir `el principio de mínimo privilegio` y solo vamos a darle permisos al usuario en la base de datos que necesite.

### Listar usuarios

Para corroborar que nuestro usuario se haya creado bien, vamos a entrar a la consola de PostgreSQL con el comando `psql` y dentro ejecutar el comando `\du`

``` console
postgres@culpeo:~$ psql
psql (9.6.10)
Digite «help» para obtener ayuda.

postgres=# \du
                                     Lista de roles
 Nombre de rol |                         Atributos                          | Miembro de
---------------+------------------------------------------------------------+------------
 postgres      | Superusuario, Crear rol, Crear BD, Replicación, Ignora RLS | {}
 culpeo        |                                                            | {}

postgres=#
```

### Otorgar permisos a usuarios

Vamos a crear una base de datos de pruebas para probar los permisos de los usuarios con el comando `CREATE DATABASE prueba;` .

Ahora vamos a darle a nuestro usuario #culpeo# los permisos para crear esquemas dentro de una base. Para eso, necesitamos pasarle la base de datos `prueba` y el permiso `CREATE` , junto con el usuario.

``` console
postgresql=# GRANT CREATE ON DATABASE prueba TO culpeo;
```

En caso de que querramos darle todos los permisos dentro de una base datos a un usuario:

``` console
postgresql=# GRANT ALL PRIVILEGES ON DATABASE prueba TO culpeo;
```

Pueden ver más opciones para el comando GRANT en: [Comando GRANT en PostgreSQL Docs](https://www.postgresql.org/docs/9.0/sql-grant.html)


### Eliminar usuarios

Sin abandonar la consola de `psql` podemos eliminar un usuario con el comando `DROP USER` .

``` console
postgres=# DROP USER culpeo;
DROP ROLE
```

Hay que tener en cuenta que si el usuario tiene permisos específicos en una base de datos, es necesario primero quitarle los permisos con `DROP OWNED BY` para poder eliminarlo.

``` console
postgres=# DROP OWNED BY culpeo;
DROP OWNED
postgres=# DROP USER culpeo;
DROP ROLE
```

### Salir de psql

Para abandonar la consola de `psql` , es necesario ejecutar el comando `\q` y para abandonar el usuario `postgres` ejecutamos exit posteriormente.

``` console
postgres=# \q
postgres@culpeo:~$ exit
root@culpeo:~#
```
