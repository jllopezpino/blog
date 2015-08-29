---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-09-15 10:37:33+00:00
layout: post
slug: evitar-firewall-pidgin
title: Evitar un firewall con Pidgin
excerpt: "Cómo utilizar Piding, el cliente para chat y mensajería instantánea libre, cuando algunas empresas e instituciones filtran mediante un firewall el tráfico"
locale: es_ES
wordpress_id: 558
categories:
- Software Libre
tags:
- bosh
- chat
- facebook
- firewall
- gtalk
- http
- libpurple
- live
- messenger
- msn
- msn messenger
- pidgin
- proxy
- xmpp
---

[![](http://jllopezpino.files.wordpress.com/2010/09/pidgin-firewall.png?w=150)](http://jllopezpino.files.wordpress.com/2010/09/pidgin-firewall.png)

Algunas empresas e instituciones filtran mediante un **firewall** gran parte del tráfico, por lo que algunos clientes de **mensajería instantánea** no funcionan.

Pidgin es un cliente de chat y mensajería instantánea **multiplataforma** (puedes emplearlo en GNU/Linux, Windows, Mac OS...) y **multiprotocolo** (puedes conectar a Gtalk, Live, Yahoo, Skype, Facebook chat...).  A continuación explico la **configuración** que he utilizado para poder conectar a las redes de mensajería instantánea de Gtalk, Live Messenger (anteriormente MSN) y Facebook, **sin** tener que emplear ningún **proxy**.

Aunque el título de este post indica que la configuración es para Pidgin, todo lo descrito para este cliente debería ser aplicable para el resto de clientes que usan también la librería **libpurple**: **Adium, Proteus, Meebo, Empathy **y** QuteCorn**.


### Gtalk


Google emplea el protocolo abierto **XMPP** y usando un protocolo al que llaman **BOSH** se pueden transmitir mensajes de este protocolo mediante HTTP, pero no logré encontrar cómo emplearlo en Pidgin. La solución más simple es emplear un puerto distinto, usualmente el servidor talk.google.com permite emplear distintos puertos, como el puerto 80. Yo simplemente he activado la opción 'Forzar el uso de cifrado SSL antiguo (puerto 5223)' y puedo conectar sin problemas.

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/09/pidgin-gtalk.png" alt="Configuración de mi cuenta de Gtalk en Pidgin">
		<img src="http://jllopezpino.files.wordpress.com/2010/09/pidgin-gtalk.png">
	</a>
	<figcaption>Configuración de mi cuenta de Gtalk en Pidgin</figcaption>
</figure>



### Live Messenger


Para conectar a Live Messenger empleo el plugin [**msn-pecan**](http://code.google.com/p/msn-pecan/) y en al configuración únicamente he activado la opción 'Usar método HTTP':

<figure>
	<a href="http://jllopezpino.files.wordpress.com/2010/09/live-messenger-pidgin.png" alt="Configuración de Pidgin para conectar a Live Messenger tras un firewall">
		<img src="http://jllopezpino.files.wordpress.com/2010/09/live-messenger-pidgin.png">
	</a>
	<figcaption>Configuración de Pidgin para conectar a Live Messenger tras un firewall</figcaption>
</figure>



### Facebook


Se puede emplear XMPP para conectar a Facebook, pero hay otra alternativa que evita los Firewall y es emplear el plugin [facebookchat](http://code.google.com/p/pidgin-facebookchat/), con el que no debemos configurar nada.
