---
layout: post
title:  "Varnish remove all parameters except"
author: Luis Mancilla Ávila
date:   2014-11-24 18:00
categories: varnish vmod libvmod-querystring
---

Analizando las URLs (requests) que llegan a nuestro balanceador de carga nos dimos cuenta que varias apuntaban al mismo recurso, pero que para nuestro servidor de caché era tratada como distinta. Esto es, generaba varios objetos de caché para un mismo recurso. 

Las siguientes URL's sirven para efectos de demostración:

<a class="text-small" href="http://www.example.com/627871/kerferd-whiting-architects?ad_content=627871&ad_medium=widget">http://www.example.com/627871/kerferd-whiting-architects?ad_content=627871&ad_medium=widget</a>

<a class="text-small" href="http://www.example.com/627871/kerferd-whiting-architects?iframe=true&width=80%&height=80%">http://www.example.com/627871/kerferd-whiting-architects?iframe=true&width=80%&height=80%</a>

<a class="text-small" href="http://www.example.com/627871/kerferd-whiting-architects">http://www.example.com/627871/kerferd-whiting-architects</a>

Con el objetivo de eliminar caché duplicado para nuestro servidor y poder así optimizar el uso de memoria, dedicamos un tiempo a revisar la configuración de los parámetros que queríamos aceptar para nuestra aplicación.

Una vez identificados estos parámetros, que denominaremos **x**, **y**, **z** en adelante, la intención es remover todos aquellos que no correspondan a los mencionados anteriormente. Para ello, utilizaremos la función **regsuball()** que nos proporciona Varnish a través de su VCL.

El detalle de la solución es definirla en **vcl_recv** como sigue a continuación:

<pre><code>
sub vcl_recv {
    ...
    # remove all parameters except x, y and z
    if (req.request == "GET" || req.request == "HEAD") {
        set req.url = regsuball(req.url, "([\?|&])(?!&|x|y|z)([%:\-_A-z0-9]+[=[%.\-_A-z0-9]+]?)", "");
        set req.url = regsub(req.url, "([\/]+[A-z0-9-_]+)(&)(.*)", "\1?\3");
    }

    ...
}
</code></pre>

<br />

# Solución utilizando libvmod-querystring #

Al momento de escribir este post, encontré en GitHub una librería para poder esto a cabo, libvmod-querystring.

*Importante: sólo funciona para versiones de Varnish 3.x*

Repositorio en GitHub: <https://github.com/Dridi/libvmod-querystring>

Para instalar este VMOD sigue las instrucciones desde la siguiente URL:

<https://github.com/Dridi/libvmod-querystring/blob/master/INSTALL>

En la configuración de Varnish:

<pre><code>
import querystring;

sub vcl_recv {
    ...

    set req.url = querystring.regfilter_except(req.url, "(x|y|z)");
    
    ...
}
</code></pre>

Ocupando la función **regfilter_except()** de la librería queda mucho más limpio.


Conclusión
----------------------------

Luego cada uno de las URL's mecionadas arriba ocuparán el mismo objeto para referirse a

<a class="text-small" href="http://www.example.com/627871/kerferd-whiting-architects">http://www.example.com/627871/kerferd-whiting-architects</a>

pero en el caso de, 

<a class="text-small" href="http://www.example.com/627871/kerferd-whiting-architects?x=cm">http://www.example.com/627871/kerferd-whiting-architects?x=cm</a>

será tratado como otro objeto al contener el parámetro x en el query string.

Para nuestro servidor, los objetos cacheados se reducen considerablemente, y elevan nuestro **hit ratio**.

### Notas finales

* Todo esto fue utilizando la versión 3.x de Varnish
* Al momento de escribir esta guía la versión 4.x de Varnish no soporta el módulo libvmod-querystring debido a cambios en el API VMOD. Por lo tanto, la compilación no es posible

### Pasos a futuro

* Redefinir la expresión regular utilizada, detallando los posibles valores que tendrá cada parámetro



