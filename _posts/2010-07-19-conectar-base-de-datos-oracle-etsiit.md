---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-07-19 13:28:57+00:00
layout: post
slug: conectar-base-de-datos-oracle-etsiit
title: Conectar a la base de datos Oracle de la ETSIIT en Linux
locale: es_ES
wordpress_id: 3
categories:
- General
tags:
- base de datos
- bases de datos
- bd
- databases
- db
- etsiit
- forms
- gbd
- gestión de bases de datos
- informática
- linux
- oracle
- pbd
- programación de bases de datos
- sql plus
- ubuntu
- ugr
---

Esta es una entrada para alumnos de la [ETSIIT de la UGR](http://etsiit.ugr.es/) que quieren realizar sus prácticas de las asignaturas de bases de datos en Linux y no reciben ninguna ayuda por parte de los profesores. Aunque no interesará a la mayoría de los lectores, quiero publicar este artículo en el blog para dejarles a mis futuros compañeros unas instrucciones detallando los pasos que seguí y los problemas que encontré.


### Introducción


Instalar la **base de datos** de **Oracle** en nuestro PC supone utilizar muchos recursos y a veces instalarla y desinstalarla no es cosa simple. Por eso yo siempre he preferido instalar únicamente los programas cliente cuando he tenido que realizar prácticas en la ETSIIT de asignaturas como **_Bases de datos_, _Programación de bases de datos_ o _Gestión de bases de datos_**. Además, esto nos asegura que cuando tengamos que hacer la defensa en los ordenadores de la escuela no nos surgirá algún problema tonto de última hora por usar otra versión del servidor.

Para poder hacer las prácticas de BD y GBD es suficiente con **SQL*Plus**, pero para PBD necesitaremos también instalar [Forms](http://www.oracle.com/technology/products/forms/index.html). El segundo no lo he utilizado nunca en **Linux**, pero parece que hay versión y su configuración debe ser similar a la de SQL*Plus.

Todos los pasos aquí detallados fueron los que seguí en Ubuntu 9.10 y 10.04.


### Requisitos


Para poder seguir estas instrucciones necesitarás:



	
  * Conexión a la [VPN de la UGR](http://etsiit.ugr.es/FAQ/doku.php?do=show&id=vpn) y por lo tanto conexión a Internet siempre que quieras usarlo.

	
  * Cliente para Linux y compatible con tu arquitectura, que explicaremos de dónde descargar a continuación.

	
  * Una cuenta en la base de datos de la escuela, que te facilitará tu profesor de prácticas de la asignatura.





### Descargando y configurando el software


En primer lugar deberemos contar con las librerías del kernel para acceso asíncrono de entrada y salida, en las distribuciones derivadas de Debian basta con instalar el paquete '_libaio1_':

{% highlight css %}
sudo apt-get install libaio1
{% endhighlight %}

A continuación, descargamos tanto el paquete que incluye los ficheros básicos como el que incluye el SQL*Plus, en su versión para [arquitecturas 32 bits](http://www.oracle.com/technology/software/tech/oci/instantclient/htdocs/linuxsoft.html) o para [arquitecturas 64 bits](http://www.oracle.com/technology/software/tech/oci/instantclient/htdocs/linuxx86_64soft.html), y descomprimimos el fichero zip descargado o instalamos el paquete rpm descargado.

Ahora debemos crear una variable de entorno llamada LD_LIBRARY_PATH para que SQL*Plus pueda encontrar sus propias librerías. Para esto debemos hacer:

{% highlight bash %}
export LD_LIBRARY_PATH=/ruta
{% endhighlight %}

Siendo _ruta_ donde tenemos el fichero _libsqlplus.so_. Si hemos instalado a través de los paquetes RPM, esta ruta puede ser aproximadamente (dependiendo de la versión y la arquitectura): /usr/lib/oracle/11.1/client64/lib

Para que las variables de entorno se creen cada vez que ejecutamos un terminal, podemos poner estos dos comandos en el fichero ~/.bashrc (si utilizamos bash).


### Conectando


Con lo anteriormente expuesto, ya debería ser suficiente para poder conectar al servidor de la escuela (siempre estemos conectados a la VPN de la escuela). Para guardar los datos de conexión lo mejor es crear un fichero llamado _tnsnames.ora_ que contenga lo siguiente:

{% highlight text %}
bd =(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=oracle0.ugr.es)(PORT=1521)))(CONNECT_DATA=(SID=PRACTBD)))
{% endhighlight %}

Ese fichero puede estar en cualquier directorio de nuestro sistema de archivos, pero tenemos que declarar otra variable de entorno para indicar dónde se encuentra. Si por ejemplo se encuentra directamente en el 'home' de mi usuario:

{% highlight text %}
export TNS_ADMIN=~
{% endhighlight %}

Finalmente, para conectar utilizamos:

{% highlight bash %}
./sqlplus usuario/password@bd
{% endhighlight %}


### Errores comunes


Si tienes cualquier problema, siéntete libre de contactarme a través de los comentarios de esta entrada para que te eche una mano. De todas formas estos son los errores más comunes que pueden aparecerte al ejecutar sqlplus:

{% highlight text %}
sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory
{% endhighlight %}

No has fijado correctamente la variable de entorno LD_LIBRARY_PATH, comprueba la ruta que has usado y si has incluido el comando en el fichero .bashrc debes abrir de nuevo la terminal de comandos.

{% highlight text %}
sqlplus: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
{% endhighlight %}

No has instalado la librería libaio.

{% highlight text %}
ORA-12154: TNS:could not resolve the connect identifier specified
{% endhighlight %}

Quiere decir que el identificador de la conexión (nosotros hemos usado 'bd') no se encuentra. Puede ser porque hayas definido mal el fichero tnsnames.ora, porque no hayas definido bien la variable de entorno que apunta al directorio donde se encuentra o porque al conectar con sqlplus no estás poniendo el '@bd' del final.

{% highlight text %}
ERROR: ORA-12545: Connect failed because target host or object does not exist
{% endhighlight %}

No encuentra el servidor, así que comprueba que estás conectado a la VPN de la UGR porque ahora [utilizas una IP de la misma](http://www.showmyip.com/) y comprueba que puedes hacer ping al servidor:

{% highlight bash %}
ping oracle0.ugr.es
{% endhighlight %}


### Enlaces


Fuentes de información:



	
  * [Hilo sobre cómo conectar en el foro de la escuela.](https://etsiit.ugr.es/apps/foro/index.php?idhebra=10559)

	
  * [Installing SQL*Plus in Ubuntu.](http://samushka.blogspot.com/2009/04/installing-oracle-sqlplus-in-ubuntu.html)


Otros enlaces interesantes relacionados:

	
  * [Instrucciones de instalación y configuración de Oracle Developer en Windows](http://flanagan.ugr.es/docencia/2005-2006/2/developer/TutorialInstalacion.html).

	
  * [Interesantes manuales de bases de datos](http://www.jorgesanchez.net/bd/index.html).




pbd =(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=oracle0.ugr.es)(PORT=1521)))(CONNECT_DATA=(SID=PRACTBD)))
