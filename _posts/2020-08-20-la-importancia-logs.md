---
layout: post
title:  "La importancia de los logs (y de leerlos)"
date:   2020-08-20 
categories: misc
---

Hoy vengo con una reflexión que es: "La importancia de los logs", pero más importante aún, de leerlos también.

Últimamente, [vengo haciendo scripts bastantes sencillos](https://github.com/culpeoit/scripts/), pero siempre agrego un logger por más sencillo que sea. Hoy, un dev me pidió reiniciar el servicio de un PostgreSQL que venía andando excelente con sus 100+ días de uptime. Pero bueno, un reinicio no le hace daño a nadie y accedí. Acto seguido, todas las aplicaciones que dependían de ese servicio no inician.

Ingreso al log de las instancias, obviamente no pueden conectarse al servicio de postgres en ese puerto. Al estar en otra red, pensé que a lo mejor el puerto se había cambiado en la configuración de postgres y al no haberse reiniciado el servicio nunca, el cambio no había impactado. Accedí a la configuración y no, el puerto estaba bien y el firewall aceptaba conexiones en ese puerto. Para descartar, accedí a la consola de psql y ahí recibí el mismo error, no podía conectarse al servidor. Definitivamente el problema estaba en el servicio de Postgres. A los logs!

Después de 5 minutos de troubleshooting, la solución estuvo frente mio todo el tiempo:


```console
cat /var/log/postgresql/postgresql-11-main.log
pg_ctl: could not start server
Examine the log output.
2020-08-20 10:33:02.254 -03 [14074] FATAL:  data directory "/var/lib/postgresql/11/main" has invalid permissions
2020-08-20 10:33:02.254 -03 [14074] DETAIL:  Permissions should be u=rwx (0700) or u=rwx,g=rx (0750).
```

Un chmod 0700 más tarde todos los servicios estaban andando con normalidad.

Resta ver qué cambio los permisos en primer lugar, pero si no hubiese sido por los logs, nunca lo hubiese detectado.