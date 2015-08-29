---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-07-08 19:33:03+00:00
layout: post
slug: autodescargar-subtitulos
title: Autodescargar subtítulos de TVSubtitles
locale: es_ES
wordpress_id: 24
categories:
- Ocio
tags:
- autodescargar
- descarga manual
- descargar
- descomprimir
- feed
- filtrar
- pipes
- python
- script
- series
- subdownloader
- subtítulos
- yahoo pipes
---

Ayer os comentaba el método que utilizo para [autodescargar series](http://lopezpino.es/2010/07/07/autodescargar-series/), pero como no domino demasiado el inglés suelo necesitar también **subtítulos** para ver las series.

Existen programas que automáticamente los descargan como [SubDownloader](http://subdownloader.net/), sin embargo, no he conseguido buenos resultados con ellos. Por lo tanto, he tenido que idear un método para descargarlos.

**Actualización**: he publicado otra entrada sobre cómo [autodescargar subtítulos de Subtitulos.es](http://lopezpino.es/2010/10/02/autodescargar-subtitulos-de-subtitulos-es/)


## Crear el feed con Yahoo Pipes


No existen demasiados feeds de los que descargar subtítulos por desgracia. En español podemos encontrar los de [ArgenTeam](http://www.argenteam.net/) y en **múltiples idiomas** (yo los descargo en inglés) el sitio más actualizado es [TVsubtitles](http://www.tvsubtitles.net/showrss.php).

TVsubtitles nos ofrece un feed con los últimos subtítulos del idioma deseado, por lo que tendremos que **filtrar** únicamente aquellos que nos interesan. Para ello, Yahoo tiene una herramienta maravillosa llamada **[Pipes](http://pipes.yahoo.com/pipes/)** que te permite manipular feeds.

Para hacer el filtro con las series que tú desees, copia [mi pipe](http://pipes.yahoo.com/diogo/english_subtitles) y sustituye las reglas de filtrado que yo he utilizado por las tuyas propias. Mi pipe utiliza el feed de subtítulos en **inglés**, si lo deseas en otro idioma, cámbialo.

En todos los casos selecciono aquellos subtítulos que contienen el nombre de la serie, excepto con House que al ser una palabra muy común empleo una expresión regular para seleccionar únicamente los que empiecen por esa palabra.

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/yahoo-pipes.png" alt="Yahoo Pipes">
		<img src="http://jllopezpino.files.wordpress.com/2010/07/yahoo-pipes.png">
	</a>
	<figcaption>Yahoo Pipes</figcaption>
</figure>



# Script para autodescargar subtítulos


Una vez que tenemos el feed con los subtítulos deseados tenemos que encontrar un programa que los autodescargue. Yo no encontré ninguno y me creé mi propio **script** en **Python** que, además de descargar los subtítulos, los **descomprime**.

Como se puede observar, los pasos seguidos por el script son:



	
  1. Descarga el feed del pipe que utilizo

	
  2. Descarga los ficheros con wget

	
  3. Los descomprime con unzip

	
  4. Borra los ficheros comprimidos.


{% highlight python %}

# -*- coding: utf-8 -*-
#!/usr/bin/python

import feedparser
import os
import re

url = 'http://pipes.yahoo.com/pipes/pipe.run?_id=61df00fe888ac82a512b4a8dac1cc5de&_render=rss'
feed = feedparser.parse(url)
print feed["channel"]["title"]

for item in feed["entries"]:
 file_url = item["enclosures"][0]["href"]
 print file_url
 os.system('wget -nc ' + file_url)

ficherosZip = os.listdir('.')

for i in ficherosZip:
 if re.search('\.zip$', i):
 os.system('unzip -o "' + i + '"')
 os.system('rm "' + i + '"')

{% endhighlight %}

Este código necesita feedparser, desde las distribuciones debian se puede descargar mediante:

{% highlight bash %}

sudo apt-get install python-feedparser

{% endhighlight %}

Los subtítulos a veces desaparecen rápidamente del feed, por lo que es recomendable programar el script para que se ejecute cada cierto tiempo. Yo empleo [Gnome Schedule](http://gnome-schedule.sourceforge.net/) para ello.


# Páginas para descargar subtítulos


Si no conseguimos descargarlos automáticamente (rara vez pasa) o preferimos el método **manual**, algunas páginas interesantes para **descargar subtítulos** son:



	
  * En español

	
    * [SubsTeam](http://subs-team.tv/).

	
    * [ArgenTeam](http://www.argenteam.net/).

	
    * [Subtitulos.es](http://www.subtitulos.es/): subtítulos al momento de todas las series pero de baja calidad. Se desarrollan de forma colaborativa.

	
    * [TheSubFactory](http://thesubfactory.net/foros/index.php).




	
  * En múltiples idiomas:

	
    * [Addic7ed](http://www.addic7ed.com): subtítulos rápidos.

	
    * [Subscene](http://subscene.com/): podemos encontrar packs de series antiguas.

	
    * [Allsubs](http://www.allsubs.org): gran cantidad de subtítulos pero muy mal organizados.

	
    * [Opensubtitles](http://www.opensubtitles.org).





