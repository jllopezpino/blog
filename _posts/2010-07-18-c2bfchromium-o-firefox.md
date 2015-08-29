---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-07-18 12:00:33+00:00
layout: post
slug: '%c2%bfchromium-o-firefox'
title: ¿Chromium o Firefox?
wordpress_id: 301
categories:
- Software Libre
tags:
- browser
- cromium vs firefox
- javascript
- multi-proceso
- navegador
- navegadores libres
- spidermonkey
- tracemonkey
- v8
- velocidad
- webkit
---

Para la [!BarraLibreCamp](http://notbarralibrecamp.info) estuve preparando una presentación llamada '[La guerra de los navegadores libres](http://www.slideshare.net/jlpino/firefox-vs-chromium-guerra-de-los-navegadores-libres)', finalmente no tuve tiempo de hacerla y no quedó todo lo completa que quería. El objetivo era aclarar un poco algunas de las diferencias que había ido encontrando entre ambos navegadores, que comentaré a continuación.


## Los contendientes


Dos navegadores libres se reparten hoy en día la cuota de mercado: **Firefox** y **Chromium**. El primero de ellos es desarrollado por la fundación **Mozilla**, es el más veterano y en los últimos años se ha hecho un hueco en un mercado dominado por Internet Explorer. El segundo llegó de la mano de **Google** y ha tenido un crecimiento espectacular.


### Diferencias entre Chrome y Chromium


Google liberó la mayor parte del código de su navegador (Google Chrome) y es lo que se conoce como el proyecto Chromium. Las [diferencias](http://en.wikipedia.org/wiki/Chromium_(web_browser)#Differences_between_Chromium_and_Google_Chrome) entre ambos son pequeñas:



	
  * Nombre y logo.

	
  * Chrome incorpora un sistema de actualizaciones llamado Google Update.

	
  * Chrome permite enviar estadísticas de uso e informes de errores.

	
  * Chromium no incluye un identificador de seguimiento llamado RLZ.




### Otros navegadores libres


Firefox y Chromium no son las únicas alternativas. En la Wikipedia  existe una extensa [lista de  navegadores libres](http://en.wikipedia.org/wiki/Category:Free_web_browsers), como Camino, Arora o Konqueror.




## Velocidad


El arranque de Firefox es endiabladamente lento, mientras que Chromium ha hecho un buen trabajo y rápidamente lo tenemos disponible para empezar a navegar. Leí que la gente de Mozilla se escudaba en que Firefox tiene que realizar las comprobaciones de actualizaciones en las extensiones durante el proceso de inicio y dichas conexiones causaban buena parte del retardo. Esperemos que en futuras versiones, como han prometido, no sea necesario reiniciar el navegador para instalar actualizaciones de extensiones y aceleren el arranque.

Durante la navegación Chromium también parece mucho más rápido. En cualquier caso, se nota como el excesivo uso de **extensiones** relentiza a ambos, por lo que yo siempre ando desactivando las que no utilizo.


### JavaScript


Una de las características que más me gusto de Chrome fue su rapidísimo motor **JavaScript**, llamado [V8](http://en.wikipedia.org/wiki/V8_%28JavaScript_engine%29), que mejoró por mucho los tiempos que conseguían hasta el momento todos los navegadores; pero Firefox se puso las pilas y a partir de la versión 3.0 incorporó un nuevo motor llamado [TraceMonkey](http://en.wikipedia.org/wiki/Tracemonkey#TraceMonkey) que le planta cara a V8. **No se puede decir que haya grandes diferencias** entre ambos hoy en día.


## Consumo de memoria


El uso de mucha memoria no tiene por qué ser malo, sabemos que todo funciona más rápido cuando tenemos toda la información en memoria. Por lo que en un equipo donde la memoria no escasea, es preferible un navegador que consuma más memoria y para ser más rápido. Sin embargo, creo que **ambos** navegadores hacen un **uso excesivo** de ella.

Con respecto al uso de memoria, siguen modelos distintos: Firefox sigue el modelo tradicional de utilizar un único proceso y Chromium emplea **un proceso por cada pestaña y extensión**.

El modelo multi-proceso provoca que haya información repetida en los distintos procesos, ya que la comunicación entre procesos es costosa; pero a cambio permite liberar más fácilmente la memoria usada cuando cerramos una pestaña o extensión. También permite que los fallos en en una pestaña, extensión o plug-in no afecten al resto, lo cual es útil y seguro.

También es muy útil el gestor de tareas (task manager) de Chromium e incluye también unas "estadísticas para nerds" de uso de memoria. Firefox ya está trabajando en adoptar también el modelo multi-proceso.


<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/chromium-task-manager.png">
        <img src="http://jllopezpino.files.wordpress.com/2010/07/chromium-task-manager.png">
    </a>
	<figcaption>Chromium Task Manager</figcaption>
</figure>


<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/chromium-uso-de-memoria.png">
        <img src="http://jllopezpino.files.wordpress.com/2010/07/chromium-uso-de-memoria.png">
    </a>
	<figcaption>Estadísticas de uso de memoria en Chromium</figcaption>
</figure>



## Configuración


Una de las opciones que más echo de menos en Chromium es la falta de menús para configurar muchas de las opciones como una contraseña maestra para las contraseñas guardadas o las aplicaciones predefinidas para abrir los ficheros.


## Extensibilidad


Hay que valorar positivamente la cantidad de extensiones en muy poco tiempo que han aparecido para Chromium, sin embargo Firefox lleva muchos años con nosotros y tiene un impresionante repertorio. Se estima que Firefox cuenta con más de 10.000 extensiones y Chromium con más de 3.000. Hace poco os comentaba las [extensiones de Firefox](http://lopezpino.es/2010/07/14/extensiones-para-firefox/) que más me gustaban y en Chromium no he encontrado reemplazo para algunas de ellas.


## Privacidad


Google ha sido criticado por utilizar en Chrome demasiadas opciones de [seguimiento de los usuarios](http://en.wikipedia.org/wiki/Google_Chrome#Usage_tracking), lo que puede ser una amenaza para el usuario. En las distintas compilaciones del proyecto Chromium algunas de ellas están desactivadas.

También hay que ser precavido con **extensiones** que envían peticiones con cada sitio que visitamos, como servicios para comprobar sitios peligrosos o de marcadores sociales.


## Estabilidad


El modelo multiproceso de Chromium lo hace mucho más estable, aunque también es verdad que me suele fallar más a menudo que Firefox. Chromium también incorpora un [sandbox, pero únicamente lo han implementado para Windows](http://www.chromium.org/developers/design-documents/sandbox).


## Estándares


El cumplimiento de los estándares depende, en parte, del motor gráfico que utilice el navegador. Chromium utiliza WebKit y Firefox utiliza Gecko.

Ambos respetan los estándares, aunque Firefox no pasa completamente el test Acid3:

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/firefox-acid3.png" alt="Acid3 en Firefox 3.6.6">
		<img src="http://jllopezpino.files.wordpress.com/2010/07/firefox-acid3.png">
	</a>
	<figcaption>Acid3 en Firefox 3.6.6</figcaption>
</figure>


<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/chromium-acid3.png" alt="Acid3 en Chromium 5">
		<img src="http://jllopezpino.files.wordpress.com/2010/07/chromium-acid3.png">
	</a>
	<figcaption>Acid3 en Chromium 5</figcaption>
</figure>



## Usuarios


Firefox se ha colocado tras unos años en el mercado como el segundo navegador más utilizado, intentando acercarse a la hegemonía de Internet Explorer. Por su parte, Chrome ha conseguido, gracias al respaldo de Google, hacerse con una parte considerable del mercado en muy poco tiempo.

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/estadisticas-navegadores.png" alt="Estadísticas de uso de los navegadores en Junio de 2010 según NetMarketShare">
		<img src="http://jllopezpino.files.wordpress.com/2010/07/estadisticas-navegadores.png">
	</a>
	<figcaption>Estadísticas de uso de los navegadores en Junio de 2010 según NetMarketShare</figcaption>
</figure>



## Otras muchas diferencias


Existen otras muchas diferencias entre ambos. Ahora mismo se me ocurren las siguientes:



	
  * Firefox tiene un mejor sistema de zoom.

	
  * Parece que Flash está mejor integrado en Chromium.

	
  * La interfaz de Chromium es mucho más cuidada, aunque ambos son muy personalizables mediante temas.

	
  * Chromium incorpora sincronización automática con los servicios de Google.




## Tabla comparativa


<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/07/firefox-vs-chromium.png" alt="Tabla comparativa de características entre Firefox y Chromium">
		<img src="http://jllopezpino.files.wordpress.com/2010/07/firefox-vs-chromium.png">
	</a>
	<figcaption>Tabla comparativa de características entre Firefox y Chromium</figcaption>
</figure>

