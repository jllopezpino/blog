---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-10-02 15:33:36+00:00
layout: post
# slug: autodescargar-subtitulos-de-subtitulos-es
title: Autodescargar subtítulos de Subtitulos.es
locale: es_ES
wordpress_id: 592
categories:
- Ocio
tags:
- data
- information retrieval
- python
- subtítulos
- web scraping
---

Las instituciones educativas suelen estar preocupadas porque sus estudiantes visiten sitios tan peligrosos como blogs de diarios españoles y páginas de subtítulos, por lo que en Escocia no tengo acceso a TVSubtitles, la página de donde solía [autodescargar subtítulos](http://lopezpino.es/2010/07/08/autodescargar-subtitulos/).

Para no tener que descargarlos manualmente, he creado un script en **Python** que los **autodescarga** del sitio español [subtitulos.es](http://www.subtitulos.es/), donde también suele haber subtítulos en inglés y en otros idiomas.

El script es muy simple y y tiene la gran desventaja de que descarga todos los capítulos de la temporada que le indiquemos, no sólo los más nuevos o los de un único idioma. Para incluir las series que desees, únicamente tienes que subtitulos los valores de la lista que hay al principio del código. El código de cada serie lo puedes ver en subtitulos.es.

Sobre los parámetro empleados al ejecutar **wget**:



	
  * Para conserver el nombre del archivo que nos envía el servidor en las cabeceras HTTP, empleo _--content-disposition permit_

	
  * Para evitar la protección contra [hotlinking](http://en.wikipedia.org/wiki/Hotlinking) del servidor, utilizo _--referer="http://www.subtitulos.es"_

	
  * Para que no sobreescriba los ficheros, empleo _-nc_




{% highlight python %}
import os
import urllib
import re

# Lista de series a descargar
# El primer número de la tupla es el número de la serie
# El segundo es la temporada
lista = [
		(26, 4),	# The Big Bang Theory
		(382, 2),	# Modern Family
		(13, 5),	# Dexter
		(367, 2),	# Community
		(24, 7),	# House
		(62, 7),	# The Office
		(35, 6),	# Weeds
		]

for serie in lista:
	# Obtenemos el contenido de la web
	url = 'http://www.subtitulos.es/ajax_loadShow.php?show=' + str(serie[0]) + '&season=' + str(serie[1])
	content = urllib.urlopen(url).read()

	# Buscamos todos los enlaces
	encontrados = re.findall('href="(.+)">', content)

	# Los descargamos, fingiendo que venimos de subtitulos.es
	# y usando el nombre que envía el servidor
	for resultado in encontrados:
		print resultado
		os.system('wget --content-disposition --referer="http://www.subtitulos.es" -nc ' + resultado)

{% endhighlight %}
