---
layout: post
title:  "Montando un blog simple con Jekyll y Github Pages"
date:   2020-06-26
categories: misc 
---
Para celebrar el inicio de este pequeño blog, voy a hacer una guía de justamente como hice para armarlo con `Jekyll` y `Github Pages`

## Primero, ¿qué es Jekyll?

Jekyll es un generador de sitios estáticos. Simplemente le damos texto en algun lenguaje `markup` y a traves de plantillas lo convierte en una web estática. Ésto, sumado a la posibilidad de hostearlo directamente en Github Pages nos ahorra lidiar con bases de datos, usuarios, permisos, cuando solo necesitamos una solución buena y rápida.

## Instalando Jekyll

### Pre-requisitos

Jekyll es un `RubyGem`, por lo que primero vamos a necesitar:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [RubyGems](https://rubygems.org/pages/download)
* [GCC](https://gcc.gnu.org/install/) y [Make](https://www.gnu.org/software/make/)

También se pueden consultar los requisitos actualizados en la [web de Jekyll](https://jekyllrb.com/docs/installation/#requirements).

### Instrucciones

Ya teniendo todos los pre-requisitos instalados podemos avanzar con la instalación de Jekyll.

* Instalar `Jekyll` y `bundler`.
{% highlight console %}
gem install jekyll bundler
{% endhighlight %}

* Creamos nuestro repo de github
{% highlight console %}
mkdir <usuario>.github.io
cd <usuario>.github.io
git init
{% endhighlight %}

* Ahora que ya tenemos nuestro repo de github listo, creamos la estructura de archivos con Jekyll en el directorio actual
{% highlight console %}
jekyll new .
{% endhighlight %}

Lo que nos deberia dejar con una estructura similar a esta:
{% highlight console %}
├ 404.html
├ Gemfile
├ Gemfile.lock
├ _config.yml
├ _posts
│   └ 2020-06-26-blog-github-jekyll.markdown
├ _site
├ about.md
└ index.md 
{% endhighlight %}

* Ahora necesitamos adecuar nuestro `Gemfile` a `Github Pages`, abriendolo con cualquier editor de texto ajustamos las versiones segun lo que indique [Github Dependency Versions](https://pages.github.com/versions/). En mi caso necesito especificar la version de `github-pages` a la 206.

{% highlight ruby %}
source "https://rubygems.org"

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
# gem "jekyll", "~> 3.8.7"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.0"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 206", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.6"
end
{% endhighlight %}

Y una vez editado actualizamos con `bundle update` para tener los Gemfiles actualizados.

## Pruebas y configuración

Si seguimos todos los pasos correctamente, ya podríamos previsualizar nuestro sitio de manera local. Para hacerlo ejecutamos el siguiente comando:

{% highlight console %}
bundle exec jekyll serve
{% endhighlight %}

Esto va a levantar un pequeño webserver para que podamos previsualizar nuestro sitio desde `localhost:4000`

A partir de este punto, vamos a poder empezar a configurar el sitio, para eso vamos a modificar el archivo `_config.yml`

``` yaml
title: Culpeo IT
email: culpeoit@gmail.com
description: >- # this means to ignore newlines until "baseurl:"
   Tecnología desde la Patagonia.
baseurl: "" # the subpath of your site, e.g. /blog
url: "https://culpeopat.github.io" # the base hostname & protocol for your site, e.g. http://example.com
twitter_username: culpeoit
github_username:  culpeopat
permalink: /blog/:categories/:title:output_ext
# Build settings
markdown: kramdown
theme: jekyll-theme-minimal
plugins:
  - jekyll-feed
```

Desde este archivo vamos a poder configurar cosas como el titulo de la pagina, correo de contacto, formato de las URL, entre otros. Adicionalmente, se puede elegir un tema, en mi caso opté por [jekyll-theme-minimal](https://github.com/pages-themes/minimal). Tener en cuenta que `Github Pages` [solo admite algunos temas](https://pages.github.com/themes/) y que para poder previsualizar el tema nuevo desde nuestra PC vamos a tener que editar el Gemfile y hacer el respectivo `bundle install` para instalar las dependencias.

### Creando nuevos posts

Con nuestro sitio ya configurado, podemos empezar a crear entradas. Éstas deben ir en el directorio `_posts` con el formato `AAAA-MM-DD-nombre-del-post.markdown` y el contenido debe ir en formato markdown.

### Primer commit y subirlo a Github Pages

Lo unico que nos resta hacer es subir nuestro sitio a hithub y habilitar Github Pages.

``` console
# Agregamos el directorio en el que estamos a stagging
git add .
# Commiteamos el sitio
git commit -m "Primer commit"
# Subimos el sitio al repo de github
git push
```

Ahora vamos a nuestro repo en `[Github.com](https://github.com)` -> `Settings` -> `Options` y bajamos hasta la opción `Github Pages`. Seleccionamos desde que branch queremos publicar el sitio y le damos a save.

También pueden ver cómo habilitar `Github Pages` desde esta guía en [Github Help](https://help.github.com/en/enterprise/2.13/user/articles/configuring-a-publishing-source-for-github-pages).

### Accediendo al sitio!

Ya podemos acceder a nuestro sitio entrando a la URL de `https://<usuario>.github.io`, tal cual el nombre de nuestro repo.

