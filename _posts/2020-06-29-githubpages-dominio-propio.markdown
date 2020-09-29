---
layout: post
title:  "Migrando Github Pages a un dominio propio"
date:   2020-06-29 
categories: gcloud
---
En esta guía voy a migrar Github Pages a un dominio propio, es decir, voy a seguir usando Github Pages para hostear el sitio, pero en vez de tener una dirección del estilo `<usuario>.github.io` voy a pasar a un dominio propio que es `culepo.blog`.

## Comprando el dominio

Esto es bastante sencillo. Simplemente fui a [NameCheap](https://namecheap.com) y elegí el dominio que quería comprar.

## Servidor DNS

Necesitamos que alguien reciba y resuelva las peticiones DNS de nuestro dominio, por lo que vamos a necesitar algún proveedor. En mi caso voy a usar [Cloud DNS de Google Cloud](https://cloud.google.com/dns).

### Creando una zona DNS

Vamos a necesitar una `zona DNS` para las solicitudes que vayan a nuestro dominio, por lo que vamos a `Network Services -> Cloud DNS` y creamos nuestra primera zona. O a través del CLI:

``` console
gcloud beta dns --project=proyecto managed-zones create culpeo-blog-dns --description="DNS para culpeo.blog" --dns-name=culpeo.blog.
```

### Configurando los DNS de nuestro dominio

Una vez que tenemos la zona DNS creada, tenemos que decirle a nuestro dominio que use ese servidor para resolver las peticiones. En mi caso voy a usar los `DNS que me da Google`:

```console
ns-cloud-e1.googledomains.com
ns-cloud-e2.googledomains.com
ns-cloud-e3.googledomains.com
ns-cloud-e4.googledomains.com
```

### Pasando Github Pages a un dominio propio

Ahora debemos ir a nuestro repo de github donde tenemos alojado el sitio como cuando installamos `Jekyll en Github Pages`. En `Setings -> Options` bajamos hasta `Github Pages` y en `Custom Domain` ponemos nuestro nuevo dominio.

### Agregando los registros DNS

Para terminar la configuracion, hay que decirle al DNS que las peticiones de `culpeo.blog` las resuelva a nuestro `Github Pages`, es decir `<usuario>.github.io`. Para esto, podemos crear registro `CNAME` en el DNS, pero como yo lo voy a hacer en el registro principal, es decir, `culpeo.blog`, necesito especificar un `ANAME` o `ALIAS`, que no estan disponibles en Cloud DNS. Afortunadamente, Github también provee las [direcciones IP para poder cargar un registro `A`](https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain).

Vamos a Cloud DNS y agregamos las IP que nos da `Github Pages`:
``` console
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
Por comodidad, voy a hacer esto a traves de la GUI.
![Google Cloud DNS - Add record](/assets/img/gcloud-dns-gp.png)

Una vez hecho esto, luego de unos minutos (según el TTL que haya tenido el DNS previo), nuestro dominio debería mostrar el contenido de `Github Pages`.

### Usando HTTPS

Después de unas horas, `Github` se encarga de generar un certificado `HTTPS` para nuestra página, por lo que podemos indicarle de que fuerce los request de HTTP a HTTPS, esto lo hacemos una vez más desde la configuración del repo, con la opción `enforce HTTPS`.
![Enforce HTTPS - Github Pages](/assets/img/enforce-https-gp.png)
[Ver imagen en tamaño completo](/assets/img/enforce-https-gp.png)

`Y eso es todo!` Ya migramos nuestro `Github Pages` a un dominio propio.



