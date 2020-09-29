---
layout: post
title:  "Accediendo a archivos de Windows desde WSL"
date:   2020-07-14
categories: misc
---
<p align="center"><img src="/assets/img/wsl.png" alt="NO" width="50%" /></p>

`Windows Subsystem for Linux` es una herramienta increible para trabajar desde un sistema Linux sin necesidad de cambiar de sistema (si es que trabajamos regularmente en Windows), ni de instalar una máquina virtual que nos consuma recursos innecesarios.
El problema con WSL viene cuando queremos pasar un archivo de Windows a WSL o viceversa. Si alguna vez intentaste esto, te habrás dado cuenta de que es sumamente dificil encontrar el directorio donde se almacena WSL en Windows, y es que, justamente, no es recomendable pasar archivos de Windows a Linux ¿Por qué? Simplemente porque Windows es capaz de dejar los archivos y/o carpetas accedidas en solo lectura, corrompiendo todo lo que toca.

Como esto es bastante vago como para una explicación, y para que no piensen que lo estoy inventando todo, les dejo el [link del blog de Developers de Windos](https://devblogs.microsoft.com/commandline/do-not-change-linux-files-using-windows-apps-and-tools/).

## ¿Cómo transferimos archivos entonces?

Bueno, afortunadamente, Linux puede copiar y mover archivos sin romper nada de ningún lado, por lo que simplemente tenemos que navegar hasta nuestro disco desde la consola.

``` console
culpeo@culpeo-PC:~$ cd /mnt
culpeo@culpeo-PC:/mnt$ ls
c  d
```

Como verán, el directorio `/mnt/` es el que nos va a permitir ingresar a nuestros discos, en mi caso `C: y D:` , por lo que podemos copiar archivos de Windows a WSL o de WSL a Windows sin probglemas ni preocupaciones.

``` console
culpeo@culpeo-PC:~$ cp /mnt/Program\ Files\ \(x86\)/Software/archivo.txt /home/culpeo/archivo.txt
```
