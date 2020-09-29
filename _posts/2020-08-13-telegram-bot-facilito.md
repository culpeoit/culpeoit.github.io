---
layout: post
title:  "Bot de Telegram facilito con Python"
date:   2020-08-13 
categories: python
---

Hoy vengo con algo corto y rápido: Cómo crear un bot de Telegram.

Esto lo aprendí literalmente hoy, por lo que la funcionalidad es poca y casi nula, pero es como para arrancar ya que a veces no es tan intuitivo cómo arrancar con algunas cosas.

## 1. Registrar el bot en Telegram

Primero lo primero, vamos a necesitar nuestro `TOKEN` para interactuar con la `API de Telegram`. Para esto, vamos a abrir un chat con [@BotFather](https://t.me/botfather) y le vamos a decir que queremos registrar un nuevo bot. Este paso es bastante simple por lo que no voy a profundizar mucho.

## 2. Dependencias de Python

Ahora necesitamos el SDK de Telegram para Python, lo podemos instalar fácilmente con pip:

```console
pip install python-telegram-bot
```

o desde el [repositorio oficial en GitHub.](https://github.com/python-telegram-bot/python-telegram-bot)

## 3. Importar dependencias en Python

Ahora podemos empezar a codear nuestro bot. Primero vamos a importar `Updater y CommandHandler`

```python
from telegram.ext import Updater, CommandHandler
```

## 4. Armar el bot

Ya podemos empezar a modelar nuestro main, por lo que procedemos a inicializar nuestro bot `con el TOKEN que nos dio BotFather:`

```python
from telegram.ext import Updater, CommandHandler

def main():
    updater = Updater(TOKEN, use_context=True) # Reemplazar TOKEN con el TOKEN de BotFather
```

Ahora podemos indicarle al bot nuestro primer comando, en este caso `/start/` , y lo vamos a hacer de la siguiente manera:

```python
from telegram.ext import Updater, CommandHandler

def main():
    updater = Updater(TOKEN, use_context=True) # Reemplazar TOKEN con el TOKEN de BotFather
    # Creamos nuestro comando /start agregandole un handler al updater que creamos antes
    updater.dispatcher.add_handler(CommandHandler("start", start))
```

Ahora para indicar el funcionamiento de ese comando, creamos la función `start` que declaramos como segunda variable de `CommandHandler` :

```python
from telegram.ext import Updater, CommandHandler

def start(update, context):
    # Le decimos al método que responda "Hola mundo"
    update.message.reply_text("Hola mundo")

def main():
    updater = Updater(TOKEN, use_context=True) # Reemplazar TOKEN con el TOKEN de BotFather
    # Creamos nuestro comando /start agregandole un handler al updater que creamos antes
    updater.dispatcher.add_handler(CommandHandler("start", start))
```

Tener en cuenta que la función `start` debe recibir un update y el contexto. El update es sumamente importante ya que es el que contiene la información del chat y a quien le vamos a anexar una respuesta.

Ahora simplemente agregamos `updater.start_polling()` para iniciar el bot y `updater.idle()` para que el bot no se finalice y siga esperando por futuros comandos.

```python
from telegram.ext import Updater, CommandHandler

def start(update, context):
    # Le decimos al método que responda "Hola mundo"
    update.message.reply_text("Hola mundo")

def main():
    updater = Updater(TOKEN, use_context=True) # Reemplazar TOKEN con el TOKEN de BotFather
    # Creamos nuestro comando /start agregandole un handler al updater que creamos antes
    updater.dispatcher.add_handler(CommandHandler("start", start))
    # Iniciamos el bot
    updater.start_polling()
    # Mantenemos el bot en standby
    updater.idle() 
```

Y listo, ahora deberiamos poder escribirle `/start` a nuestro bot y que el nos responda `Hola mundo` . Tener en cuenta que los bots **no pueden iniciar conversaciones**, siempre debe ser el usuario quien escriba primero para que el bot pueda empezar a interactuar.

Sé que es medio básico, pero como ya dije, es solo para tener ese empujón de cómo empezar, de acá en adelante la imaginación es el límite.
