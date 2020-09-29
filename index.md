---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Index of Culpeo.blog
---
# Index of /culpeo.blog
* * *
Bienvenido a este pequeño blog que busca compartir experiencias y conocimientos de un `Sysadmin Patagónico`, y de las personas que nos encontremos en el camino.

## ¿Dudas?

Armá un [issue en GitHub](https://github.com/culpeoit/culpeoit.github.io/issues) con la pregunta!

## Posts por categoria
<ul>
{% for category in site.categories %}
<h2>{{ category | first }}</h2>
    <ul>
    {% for post in category.last %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
{% endfor %}
</ul>
