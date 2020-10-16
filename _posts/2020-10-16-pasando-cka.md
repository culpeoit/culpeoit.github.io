---
layout: post
title:  "Aprobando el examen Certified Kubernetes Administrator de la CNCF"
categories: misc
---

Bueno, hoy oficialmente me dieron mi resultado del examen CKA que vengo preparando hace uno o dos meses, ya que esta vez si aprobé, voy a contar un poco cómo es este examen respecto a las habilidades necesarias.

*Disclaimer: No voy a tocar temas específicos del examen porque hay un NDA que no me permite hablar ese tipo de cosas, pero voy a contar cómo es tomar el examen.*

## Registrandose (y pagando) para el examen

Registrarse para el examen es bastante fácil, hay que ir a la [página del CKA de CNCF](https://www.cncf.io/certification/cka/) y le damos a `Register for Exam`. Nos va a mandar a la pagina de Linux Foundation para terminar el registro. El examen incluye un `retake` grátis. Yo personalmente en el primer intento estaba bastante nervioso así que aproveché el retake.

El costo del examen es de `300 USD`, pero siempre hay algún códio de descuento dando vueltas. En mi caso (08/10) el codigo era `BOOKS20` y me descontaron `60 USD` del total.

Para Argentina, todavía no me peleé con el banco por los impuestos del 65% que me van a cobrar, pero tengo todo como para justificar que es una plataforma educativa.

## Requisitos para rendir

Bueno, el examen es completamente online, por lo que los requisitos son:

1. Saber inglés. El examen está en inglés y creo que Chino y Japonés, por lo que sí o sí hay que saber inglés.
2. Internet, webcam y micrófono.
3. Una sala donde puedas estar solo/a y en silencio, sin ser interrumpido.
4. Navegador Chrome.

La lista completa está en el [handbook del candidato](https://docs.linuxfoundation.org/tc-docs/certification/lf-candidate-handbook).

## Tomar el examen

Esto es un poco extendiendo los requisitos, especialmente para los que no hayan tomado nunca en examen de este tipo.

Podés entrar hasta 15 minutos antes o después del horario acordado, pero hay que tener en cuenta que se ocupan unos 10 minutos aproximadamente preparándo las cosas para el examen.

Hay que estar solo en la habitación, y tiene que haber silencio. El escritorio tiene que estar completamente vacío al momento del examen, sólo se puede tener la notebook o PC que se vaya a usar para rendir. Se puede tener un vaso o botella de agua, siempre y cuando sea transparente y no tenga etiquetas.
Siempre hay que estar dentro del marco de la cámara, salirte del mismo puede concluir en que te cancelen el examen. Tampoco podés leer las preguntas en voz alta, susurrar o taparte la cara. Lo más probable es que te pausen el examen y te llamen la atención.

## Prepararse para el examen

El examen no es tan difícil, la actualización de Septiembre 2020 sacó contenido y redujo el tiempo, por lo que es menos agoviante también. Hay que saber [exactamente lo que dice la curricula](https://github.com/cncf/curriculum).

Para prepararme yo:
* Hice los cursos de [KodeKloud](https://kodekloud.com/), están un poco más caros ahora, pero valen la pena ya que también tienen mucha práctica y ejercicios de prueba.
* Instalé un cluster local de dos nodos con `kubeadm`.
* Usé mucho, pero mucho vim. Vim es el editor por defecto, también se puede usar nano pero hay que modificar la configuración para eso.
* Lei la [documentación oficial de kubernetes](https://kubernetes.io/docs/) y me amigué con cómo buscar contenido.

## Consejos para el momento del examen

En el examen podés tener dos pestañas abiertas en Chrome, una con el examen y otra con la documentación de Kubernetes. Dentro de la documentación no se puede acceder al foro (`discussion.kubernetes.io`) pero si al blog y al resto de la doc. Hay que tener cuidado cuando buscan dentro de la documentación porque un descuido te puede llevar al foro y te pueden penalizar por eso.

Ahora sí, consejos:

### Leer los conceptos claves y armar bookmarks

Yo busqué conceptos importantes en la curricula, así como también los objetos que sé que no se pueden crear con `kubectl create`, y los guardé en favoritos para tener los YAML a mano. De esta forma es más rápido para acceder a la documentación.

### Autocompletar, aliases y configuración de Vim

Esto es mucho muy importante, configuren el [autocompletar y el alias de kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/), ahorra mucho tiempo.

Hay gente que también configura variables del tipo: 
`export KILL = "--force --grace-time=0"` para finalizar pods sin darles tiempo a que hagan sigterm, pero yo no lo encontré tan necesario.

Respecto a vim, lo que yo configuré fue que el tab haga dos espacios y que me muestre el número de linea. YAML solo acepta dos espacios por tabulación, por lo que es muy importante.

Simplemente ejecuté lo siguiente:
`echo -e "set number \nset expandtab \nset ts=2" > ~/.vimrc`

### Usar comandos imperativos

No perder tiempo modificando o creando YAMLs a mano. Si solamente hay que ejecutar un pod, haganlo con confianza con `kubectl run pod`. Si hay algo que es muy detallado, generen el YAML con `kubectl run` y terminen de definirlo a mano.

Ej: `kubectl create deployment deployment-1 --image=nginx --ports=containerPort:80 --namespace=app1 --dry-run=client -o yaml > template.yaml`

En este caso `--dry-run=client` valida el objeto con el API server, pero no lo crea, simplemente devuelve su definición, mientras que `-o yaml` nos devuelve esa definición en YAML. Después modifican con vi y aplican con `kubectl apply -f template.yaml`.

Intenten ahorrar la mayor cantidad de tiempo para las preguntas más complejas o para cuando hay que hacer debugging.

### No pierdan tiempo

Son dos horas de examen y hay preguntas muy fáciles. Ante la menor duda marquen la pregunta y sigan con otra, cuando ya tengan la mayor cantidad de preguntas aseguradas recién ahí tomense el tiempo de analizarlas bien e ir a la documentación.

### Leer con detenimiento las preguntas

Las preguntas a veces tienen información esencial, leanlas con cuidado.

### Copiar y pegar

Cada tarea es específica, los objetos tienen su nombre, namespace y características. Evitense un problema y copien y pegen los nombres y demás. Simplemente hay que clickear sobre el nombre o caracteristica de la pregunta y se copia automáticamente.

## No desesperen

El examen no es imposible. Si algo no les sale, avancen y vuelvan a intentar más tarde. Yo llegué al punto de pensar que estaba mal configurado el cluster de la frustración, pero después de analizarlo bien encontré la manera de llegar a lo que buscaba.

Es un examen que no te permite memorizar, porque no hay respuestas, son todas tareas de configuración (bueno hay un par, pero son prácticas, no teóricas), por lo que teniendo bien en claro los conceptos es fácil de resolver. 

Y eso es todo, hice [un post en inglés de que NO hay que hacer cuando desaprobé el primer intento](https://fhielpos.ar/failing-cka.html), si tienen alguna duda más me pueden hablar en [Linkedin](https://linkedin.com/in/hielposfranco) o [Twitter](https://twitter.com/fhielposar) y con gusto los voy a ayudar.

Éxitos!     