---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-07-16 10:00:51+00:00
layout: post
# slug: puntos-debiles-de-un-sitio-web
title: Puntos débiles de un sitio web
locale: es_ES
wordpress_id: 248
categories:
- Seguridad
- Web
tags:
- ajax
- bombas de descompresión
- buscadores
- campos ocultos
- captchas
- contraseñas
- cookies
- cross site scripting
- denegación de servicio
- dos
- entradas
- ficheros
- fuerza bruta
- hacker
- javascript
- json
- kevin mitnick
- md5
- metaetiquetas
- passwords
- puntos débiles
- rainbow tables
- robots.txt
- security checklist
- seguridad
- seguridad web
- sesiones
- spam
- validación
- xml
- xss
---

Dice el reputado [Kevin Mitnick](http://en.wikipedia.org/wiki/Kevin_Mitnick) que  "[los hacker malos siempre van a buscar el eslabón más débil de la cadena](http://www.publico.es/ciencias/302108/hacker/malos/siempre/buscar/eslabon/debil/cadena)", por lo que de nada sirve invertir gran tiempo en protegernos ante un tipo de ataque o en una parte concreta de nuestro sitio si no nos preocupamos por defender el resto de ataques.

Cuando revisamos la seguridad de un sitio web, nunca estamos seguros de si hemos descuidado algún punto, por lo que usualmente se realizan las listas de tareas conocidas como "[security checklist](http://www.google.es/search?q=security+checklist)". Sin recurrir a la forma de lista, voy a describir brevemente algunos de los puntos que no se nos debe olvidar revisar de una aplicación web.

Al final me ha quedado un artículo más largo de lo que en un principio planteé, pero incluso así posiblemente me deje puntos débiles fuera, así que se agradece la participación en los comentarios.


### Validación de todo tipo de entradas


Lo primero que realiza un atacante contra nuestro sitio es verificar cómo se comporta el sistema ante entradas maliciosas, por lo que debemos revisar **todas las entradas** de información que recibe el sistema, no únicamente peticiones GET y formularios enviados mediante GET o POST. Las entradas suelen ser empleadas para múltiples propósitos peligrosos, como realizar [operaciones no deseadas en la base de datos](http://en.wikipedia.org/wiki/Sql_injection).

Una de las entradas críticas que en muchas ocasiones no se suelen revisar es la subida de **ficheros** por parte de los usuarios. La amenaza en este tipo de entradas puede venir tanto en el **nombre** del archivo como en su **contenido**. En su contenido, aparte de los típicos programas maliciosos que podemos recibir,  podemos encontrarnos las conocidas como **bombas de descompresión**, ficheros que al descomprimirlos ocupan una barbaridad con el objetivo de dejar nuestro sistema sin recursos.

Hay dos tipos de entradas que los desarrolladores que de están iniciando suelen olvidar: las cookies y los campos ocultos. Las **cookies** almacenan información en el cliente que puede ser manipulada para generar también entradas maliciosas, por lo que no debemos almacenar en ellas información importante (como confiar el precio de un artículo del carrito de compra). Los **campos ocultos de los formularios** también son fácilmente manipulables.




### Mostrando información


Tenemos que ser muy cuidadosos con la información que mostramos no solo a usuarios sino también a los buscadores.

Para evitar que los **buscadores** rastreen información que no deseamos, podemos definir un fichero [robots.txt](http://es.wikipedia.org/wiki/Est%C3%A1ndar_de_exclusi%C3%B3n_de_robots) o añadir [**metaetiquetas**](http://www.robotstxt.org/meta.html) al código HTML.

Puede ser útil esconder la información del **software** que estamos utilizando, para que les sea a los atacantes más difícil explotar las vulnerabilidades conocidas de éste. Por ejemplo, podemos ocultar la información del metatag generador, los pies de página que incluyen (aunque a veces no lo permite la licencia) y la cabecera de la respuesta HTTP.

También hay que ser cuidadosos con las operaciones de **lectura de ficheros**, que pueden dejar a descubierto archivos indeseados si no tenemos cuidado con las entradas del programa.

Personalmente también me gusta ocultar las **direcciones de correo electrónico** personales de los sitios web para evitar el **SPAM**, en su lugar prefiero incluir un formulario de contacto o mostrar las direcciones en imágenes en vez de en texto.


### Comprobaciones del lado del cliente


Como en el caso de la información almacenada en las cookies, el control lo tiene el usuario. Si incluimos en nuestro código operaciones del lado del cliente (usualmente en JavaScript), no podemos estar seguros de que el cliente las ejecute. Hay extensiones para los navegadores como [NoScript para Firefox](https://addons.mozilla.org/es-ES/firefox/addon/722/) que sólo ejecuta código en los sitios en los que confiemos.

Por lo tanto, si realizamos validaciones importantes como comprobar el tamaño de las contraseñas o que la dirección de correo sea válida, debemos **volver a comprobar** estas entradas del lado del **servidor**, ya que no estamos seguros de que estas validaciones realmente se hayan realizado. Hay casos extremos como hacer la comprobación de contraseñas del lado del cliente.


### Operaciones de alta carga


Otro punto débil de nuestros sitios serán aquellas operaciones que generen una alta carga en el sistema. Los atacantes buscarán estas operaciones para solicitarlas de forma recurrente y **bloquear nuestro sistema** provocando que otros usuarios no puedan acceder, lo que se conoce como un** [ataque de denegación de servicio (DOS)](http://en.wikipedia.org/wiki/Denial-of-service_attack)**.

Las soluciones para este caso son intentar emplear **cachés** para estas operaciones siempre que sea posible o **limitar** el acceso a estas operaciones de alguna forma.


### Identificación de los usuarios


Para evitar ataques por **fuerza bruta** es recomendable el uso de **captchas** u otras limitaciones como bloquear el acceso a la cuenta durante un periodo de tiempo o impedir demasiadas consultas provenientes desde una misma IP. Cada una tiene sus ventajas y sus inconvenientes: la utilización de captchas disminuye la accesibilidad del sitio, el bloqueo de la cuenta puede ser utilizado para molestar a los usuarios y el bloqueo de IPs puede impedir el acceso a usuarios legítimos que compartan IP con un atacante mediante [NAT](http://en.wikipedia.org/wiki/NAT).

Es muy recomendable evitar que los usuarios elijan contraseñas excesivamente simples debido a que sean muy cortas, a que sean muy comunes (como '1234' o 'password') o a que no empleen números y otros símbolos no alfabéticos. Tampoco podemos ser demasiado restrictivos o los usuarios terminarán no pudiendo recordar la contraseña y apuntándola en un papel o olvidándola.

También suele resultar conveniente no facilitar información sobre el nombre de usuario utilizado para la identificación de los usuarios, con medidas como:



	
  * No mostrar públicamente ningún listado de nombres de usuario.

	
  * Utilizar un nombre de usuario para la identificación distinto al mostrado públicamente. En algunos sitios se utiliza la dirección de correo.

	
  * No informar al usuario de si un usuario existe o no en el sistema en sitios como el formulario de registro o el de identificación.


No debería ni pasarse por nuestra cabeza almacenar las contraseñas de  los usuarios en texto plano. Es bastante común aplicar la función hash [MD5](http://en.wikipedia.org/wiki/Md5) a la contraseña  antes de almacenarla, aunque si algún atacante consigue hacerse con esta  información, puede emplear ataques de diccionario o con [rainbow tables](http://www.passcracking.com/) (tablas hash inversas) para  averiguar la contraseña.

También hay que prestar interés a **sesiones** empleadas para la identificación de los usuarios y que para que sean permanentes suelen ser incluidas en cookies. Los identificadores de sesión generados deben ser lo suficientemente **aleatorios** como para que sean **impredecibles**, aunque generalmente el lenguaje o framework que utilicemos ya se encargará de este aspecto.


### Comunicaciones


A veces las aplicaciones **AJAX** evalúan el contenido obtenido en la comunicación **JSON** directamente, por lo que cualquier código inyectado en dicha comunicación sería ejecutado también. Para evitar esto es conveniente reemplazar los caracteres de escape como siempre, pero también utilizar un _parser_ en vez de evaluar el código directamente.

En documentos **XML**, también muy empleados en comunicaciones, se pueden insertar cadenas maliciosas para cambiar el comportamiento de nuestro sistema o del cliente.


### Código ajeno


Por último, la ejecución de código, aunque sea de forma **autorizada** por nuestra parte, puede comprometer también nuestra seguridad. Por ejemplo, permitir la ejecución de scripts escritos en JavaScript y que no estén alojados en nuestro servidor nos hace **depender de la seguridad de otros**.

También puede darse la situación de ejecución de código de forma **no autorizada** en el cliente (Cross Site Scripting o XSS) o en el servidor, por ejemplo, facilitado por funciones que lean ficheros situados remotamente como es el caso de include() y require() en PHP. En ambos casos debemos ser cuidadosos con la validación de las entradas que recibimos del cliente.

<figure>
	<a href="https://xkcd.com/327/">
        <img src="http://jllopezpino.files.wordpress.com/2010/07/xkcd-sql-injection1.png">
    </a>
	<figcaption>Tira de xkcd.com</figcaption>
</figure>

