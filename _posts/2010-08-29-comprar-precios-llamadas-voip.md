---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-08-29 13:41:01+00:00
layout: post
# slug: comprar-precios-llamadas-voip
title: Comparar precios de llamadas VoIP
locale: es_ES
wordpress_id: 509
categories:
- Ocio
tags:
- barato
- llamadas
- precio
- python
- voip
- web scraping
---

Hace poco os comentaba que estaba estudiando las distintas [tarifas VoIP](http://lopezpino.es/2010/08/27/tarifas-voip/) y que había encontrado una compañía llamada **Betamax** que tiene múltiples páginas que ofrecen el servicios y a precios muy distintos, que además van cambiando con el tiempo.

En la red he encontrado un par de páginas que **comparan los precios** de esta compañía a todos los destinos, una de ellas nos da [extensa información sobre las compañías más baratas para cada destino](http://progx.ch/home-voip-prixbetamax-3-1-1.html) y la [otra](http://backsla.sh/betamax) es un poco más simple.

Yo por mi parte he hecho un pequeño script en Python para consultar los **precios a móviles de España** cuando quiera y no tener que depender de la actualización de estos sitios. Pensé en emplear [Scrapy](http://scrapy.org/), pero para una cosa tan simple no es en absoluto necesario.

El script simplemente obtiene la página adecuada del dominio (en todos los dominios está en la misma ruta, lo que facilita las cosas), obtiene el precio y finalmente **ordena por precio** todos los dominios para mostrar primero los más **baratos**.



{% highlight python %}
# -*- coding: utf-8 -*-
#!/usr/bin/python
import urllib
import re

lista = [
		'12voip.com',
		'actionvoip.com',
		'budgetsip.com',
		'calleasy.com',
		'cheapvoip.com',
		'dialnow.com',
		'freecall.com',
		'internetcalls.com',
		'intervoip.com',
		'jumblo.com',
		'justvoip.com',
		'lowratevoip.com',
		'netappel.fr',
		'nonoh.net',
		'poivy.com',
		'rynga.com',
		'sipdiscount.com',
		'smartvoip.com',
		'smsdiscount.com',
		'smslisto.com',
		'sparvoip.de',
		'voipbuster.com',
		'voipbusterpro.com',
		'voipcheap.co.uk',
		'voipcheap.com',
		'voipdiscount.com',
		'voipian.com',
		'voipraider.com',
		'voipstunt.com',
		'voipwise.com',
		'voipzoom.com',
		'webcalldirect.com',
		]

# Creamos el diccionario donde guardaremos la información
d = {}

# Por cada uno de los dominios
for current_domain in lista:
	# Obtenemos el contenido de la web
	url = 'http://www.' + current_domain + '/en/callingrates/mobile/cheap-calls-spain-eur.html'
	content = urllib.urlopen(url).read()

	# Almacenamos el precio, si lo encontramos
	m = re.search('(\d+\.\d+)">', content)
	if m:
		d[current_domain] = m.group(1)
	else:
		print 'No pude obtener el precio de ' + current_domain

# Recorremos el diccionario ordenado por valor,
# para tener los sitios ordenados por precio
for w in sorted(d, key=d.get, reverse=False):
  print w + "\t" + d[w]
{% endhighlight %}
