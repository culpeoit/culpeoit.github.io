---
layout: post
title:  "Simple HTTP server en Python3"
categories: misc
---

Hoy vengo con algo rápido y cortito, un comando que últimamente uso mucho en mi lab personal: `python3 -m http.server 8080`

¿Qué hace esto? Simplemente levanta un server HTTP en el puerto 8080 con Python3.

¿Para qué sirve? Muchas veces, y cuando digo muchas veces es MUCHAS veces, he necesitado pasar algún archivo a una VM de mi laboratorio virtual, como una key de ssh por ejemplo, y realmente las opciones suelen ser bastante engorrosas. Con el HTTP server de Python3, que por cierto es simplemente para pruebas o este tipo de cosas bastante básicas y no para producción, con un simple `wget` o `curl` ya puedo acceder al archivo con facilidad.

Un ejemplo rápido sería cuando quiero pasar una key de ssh de la `PC A` a la `PC B`:

**PC A**: `cd .ssh && python3 -m http.server 8080`

**PC B**: `curl pc-a:8080/key.pub > ~/.ssh/authorized_keys`

Y eso es todo!
