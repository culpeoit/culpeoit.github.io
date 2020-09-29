---
layout: post
title:  "Administracion básica de PostgreSQL - Backups y restores"
date:   2020-07-13 
categories: servidores
---
<p align="center"><img src="/assets/img/postgres.png" alt="PostgreSQL" width="50%" /></p>

Hoy restauré diez veces un backup en PostgreSQL así que, por qué no hablar un poco de `dumps y restores`?

## Haciendo un backup de una base de datos

Hacer un backup es bastante simple con el comando `pg_dump`. Simplemente indicamos la base y el archivo de destino:

``` console
root@culpeo-it:/# pg_dump -d BASE_DE_DATOS -f ARCHIVO_DESTINO
```

Esto va a crearnos un archivo con las instrucciones SQL necesarias para recrear nuestra base de datos. Pero, hay que tener en cuenta de que esto va a llevar consigo todos los permisos pre-existentes, por lo que al momento de restaurar podemos encontrar problemas de no existir los usuarios necesarios.

Para hacer un dump sin permisos ni propietario, agregamos los siguientes parámetros:

``` console
root@culpeo-it:/# pg_dump -d BASE_DE_DATOS -f ARCHIVO_DESTINO --no-owner --no-privileges
```

También podemos elegir otros formatos para exportar la base, así como si exportar solamente los datos, el esquema, entre otros.

[Más información del comando pg_dump](https://www.postgresql.org/docs/9.2/app-pgdump.html)

## Restaurando una base de datos

Para recrear la base de datos, primero vamos a crear una base de datos vacia donde vamos a volcar nuestro backup. Una vez que tenemos nuestra base, procedemos a restaurar, pero tenemos que tener en cuenta cómo hicimos el backup.

Si bien lo más lógico es utilizar el comando `pg_restore` , este comando no funciona si el dump es en texto plano (por defecto, pg_dump hace un volcado en texto plano). Por lo que, para backups en texto plano vamos a utilizar directamente el comando `psql`.

``` console
root@culpeo-it:/# psql -d BASE_DE_DATOS -f ARCHIVO_BACKUP
```
Una vez ejecutado, vamos a ver cómo automáticamente se comienzan a crear todas las tablas y registros que componen a nuestra base de datos.

En caos de tener un backup que no es en texto plano `(tar, directory o custom)` , debemos usar el comando `pg_restore` de la siguiente manera.

``` console
root@culpeo-it:/# pg_restore DIRECCION_BACKUP -d BASE_DE_DATOS
```

[Más información de pg_restore](https://www.postgresql.org/docs/9.2/app-pgrestore.html)

### Fin

Si bien este es un pantallazo rápido, hay mucho más para ver e investigar! 
