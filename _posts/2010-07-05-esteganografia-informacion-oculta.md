---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-07-05 15:09:28+00:00
layout: post
# slug: esteganografia-informacion-oculta
title: Esteganografía, información oculta
excerpt: "Artículo explicado conceptos como la esteganografía digital, la esteganografía analógica, el estegoanálisis y las marcas de agua"
locale: es_ES
wordpress_id: 88
categories:
- Seguridad
tags:
- esteganografía
- estegoanálisis
- marcas de agua
- ocultar
- privacidad
- Steganalysis
- steganography
- watermarks
---

# ¿Qué es la esteganografía?


La [esteganografía](http://en.wikipedia.org/wiki/Steganography) consiste en esconder un mensaje de forma que únicamente el emisor y el receptor conozcan su existencia ofreciendo [seguridad  mediante la oscuridad](http://en.wikipedia.org/wiki/Security_through_obscurity), usualmente esta información se esconde en otro fichero llamado contenedor. La palabra viene de las palabras griegas _steganos_, que significa **oculto**, y _graphos_, que significa **escritura**.

Se suele confundir con la **criptografía**, pero ésta consiste en cifrar o codificar información de manera que no pueda ser leída por una persona aneja, sin embargo en la esteganografía dicha persona no debe conocer que se está transmitiendo información sensible. Se pueden complementar: si un mensaje cifrado es  también esteganografiado, el nivel de seguridad será mayor.

[![](http://jllopezpino.files.wordpress.com/2010/06/ejemplo-esteganografia.png)](http://jllopezpino.files.wordpress.com/2010/06/ejemplo-esteganografia.png)


# ¿Por qué?


Dice Peter Wayner que "un enemigo sólo puede controlar tu información si puede encontrarla" y no es difícil encontrar casos en los que por **privacidad** deseemos que una información caiga en manos no deseadas. Las tecnologías no son malas ni buenas por sí misma y la estenografía como tal puede utilizarse para causas loables (evitar la censura o guardar un secreto gubernamental) o atroces (esconder un mensaje entre terroristas).

Aparte de permitirnos comunicaciones privadas, puede también ser útil para incorporar marcas de agua, rastrear documentos, identificar ficheros o mejorar estructuras de datos sin que el software antiguo deje de funcionar.

Además, hoy en día la red está llena de **contenido multimedia** (sonidos, imágenes y vídeos) permitiéndonos esconder la información entre todo ese ruido. Una octava parte de una imagen puede ser utilizada para esconder información sin que la calidad de la imagen disminuya significativamente, por lo que podemos esconder una **gran cantidad de información**.

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/06/papers-steganography-watermarking.png" alt="Número de artículos sobre esteganografía y marcas de agua por el IEEE. Extraído del libro 'Digital watermarking and steganography' publicado por Morgan Kaufmann">
		<img src="http://jllopezpino.files.wordpress.com/2010/06/papers-steganography-watermarking.png">
	</a>
	<figcaption>Número de artículos sobre esteganografía y marcas de agua por el IEEE. Extraído del libro 'Digital watermarking and steganography' publicado por Morgan Kaufmann</figcaption>
</figure>



# Esteganografía física


El primer registro de uso de esteganografía data de 440 a.c., cuando Hérotodo cuenta cómo se escondían mensajes cubriendo las tablillas que las portaban con cera o tatuado en la cabeza de un esclavo. Se han usado muchas formas y lugares para esconder mensajes de esteganografía física: tinta invisible, el uso de determinadas partes de un texto (como la primera letra de cada párrafo), mensajes en la parte de atrás de sellos postales, los signos de puntuación de microfilmes, transmisiones mediante signos en fotos o mensajes en anuncios clasificados de periódicos. En la actualidad la esteganografía física sigue siendo muy utilizada por militares, agencias de inteligencias, policía y criminales.


# Esteganografía digital


Hay multitud de aplicaciones de esteganografía digital y métodos para esconder información, el[ Steganography and Research Center ha registrado más de 800 aplicaciones](http://www.sarc-wv.com/news/safdb35.aspx). Hay múltiples técnicas, para hacerlo, como por ejemplo:




  * Reemplazar el ruido de un fichero (imagen o sonido) con el mensaje  que queremos transmitir.


  * Utilizar algunos de los píxeles de una imagen o momentos del archivo  de sonido.


  * Adoptar el mismo perfil estadístico que otro mensaje, por ejemplo,  que las apariciones de las distintas letras sea igual a la que utiliza  un texto en Español.


  * Imitar la estructura de un texto, como por ejemplo, la crónica de un  partido de fútbol.


  * Reemplazar aquellos datos que supuestamente son aleatorios por la  información a transmitir.


  * Cambiar el orden de los elementos de una lista.


  * Añadir información al final del fichero que no altera su funcionamiento.


Como se puede ver, el objetivo siempre es que o un humano o una  máquina (o ambos) no sean capaces de distinguir el mensaje transmitido  de uno que no lleve un mensaje oculto. Estas técnicas además pueden ser  combinadas de diversas maneras.

Algunos ejemplos curiosos de uso de la esteganografía digital con los que he dado documentándome sobre el tema son:


  * [Un módulo en Perl que permite convertir cualquier fichero PDF en un sistema de ficheros virtual](http://www.securitybydefault.com/2008/12/como-cruzar-la-frontera-de-arabia-saudi.html).


  * [Convertir un fichero torrent en una imagen](http://torrentfreak.com/hidim-converts-torrents-into-png-images-090714/) o [esconderlo en una imagen o audio ya existente](http://stegtorrent.sourceforge.net/).


  * [Piet, un lenguaje de programación cuyos programas parecen pinturas abstractas](http://www.dangermouse.net/esoteric/piet.html).


  * [Estenografía en códigos QR](http://2d-code.co.uk/images/pdf/qr-code-steganography.pdf).




# Debilidades


En función de la técnica utilizada, los datos que hemos escondido pueden ser más débiles o fuertes a ciertos ataques. Estos ataques pueden ser de distinto tipo: detectar si se transmite un mensaje, leerlo, reemplazarlo o eliminarlo. Algunas de las técnicas de ataque más comunes son:




  * Analizar estadísticamente el fichero para intentar averiguar si esconde un mensaje.


  * Compararlo con el fichero original, si el atacante dispone de él.


  * Comprobar si el algoritmo utilizado es conocido para revelar el mensaje.


  * Emplear el mismo algoritmo para reemplazar el mensaje.


  * Añadir sonido para eliminarlo.


  * Cambiar el formato.


  * Compresión.




## Estegoanálisis


El [estegoanálisis](http://en.wikipedia.org/wiki/Steganalysis) consiste en determinar qué paquetes contienen información oculta mediante estenografía y, si es posible, recuperar dicha información. No podemos garantizar que ningún método de esteganografía sea perfecto, y el estegoanálisis utiliza las debilidades de los distintos métodos para lograr detectar los mensajes.

Algunos métodos de estenoanálisis se basan en:




  * Análisis visual o auditivo: algunos métodos dejan marcas significativas que inspeccionando el fichero transmitido con cuidado nos permiten detectar anormalidades.


  * Análisis estructural: la inclusión de un mensaje oculto a veces provoca cambios en la estructura del fichero y si dichos cambios provocan que la estructura no cumpla un patrón determinado.


  * Análisis estadístico.




# Marcas de agua


Las [marcas de agua](http://en.wikipedia.org/wiki/Watermarks) son datos imperceptibles que se ocultan en objetos como tejidos, etiquetas de ropa, billetes o embalajes sin alterarlos. La utilización de la esteganografía para esconder marcas de aguas digitales puede sernos útil para diversos fines:




  * Monitorizar emisiones: por ejemplo para identificar los programas, canciones o anuncios que se emiten en televisión.


  * Identificar al autor de una obra, al incluirse información sobre éste.


  * Seguimiento de un dato, por ejemplo para detectar filtraciones de un documento judicial a la prensa o de una película en Internet.


  * Incluir una firma digital dentro de la obra.


  * Control de copias, realizando reproductores que puedan o no puedan reproducir ciertos contenidos en función de la presencia de una marca de agua.


  * Control de dispositivos: por ejemplo juguetes que interactúen con programas de televisión o grabadores de vídeo que se activan al detectar una marca de agua determinada.




# Referencias






  * La Wikipedia inglesa siempre es un buen punto de partida para empezar a informarte sobre algún tema y encontrar referencias.


  * La principal fuente de referencia de este trabajo ha sido el libro de Peter Wayner [_Disappearing Cryptography. Information hiding: steganography & watermarking_](http://books.google.es/books?id=qMB9AiFUWF0C&lpg=PP1&ots=giC8Wy3g9f&dq=disappearing%20cryptography&pg=PP1#v=onepage&q&f=false).


  * [Digital Watermarking and Steganography, 2ª edición](http://www.amazon.com/Watermarking-Steganography-Kaufmann-Multimedia-Information/dp/0123725852). (The Morgan Kaufmann Series)


  * El Observatorio de la Seguridad de la Información, perteneciente al Instituto Nacional de Tecnologías de la Comunicación publicó este año un interesante artículo de [introducción a la Esteganografía](http://www.inteco.es/Seguridad/Observatorio/Actualidad_Observatorio/noticia_articulo_esteganografia).


  * [Breve introducción a lo que es la esteganografía](http://geneura.ugr.es/~jmerelo/atalaya/esteganografia.htm) por J.J. Merelo.




# Presentación


<iframe src="//www.slideshare.net/slideshow/embed_code/key/HijwQ1ooKzIMqM" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/jlpino/esteganografia-4678216" title="Esteganografia" target="_blank">Esteganografia</a> </strong> from <strong><a href="//www.slideshare.net/jlpino" target="_blank">José Luis López Pino</a></strong> </div>
