---
layout: post
title:  "Como interceptar trafico HTTP de aplicaciones Android"
description: "Aprende hacking moderno enfocado a entornos mobiles"
date:   2020-11-20
image:  "img/android-proxy.png"
tags:   [Android, Proxy, Pentesting, Security]
---

Hello Hackers!!  
En el presente articulo, aprenderemos a interceptar trafico HTTP sobre aplicaciones android.
Para ello estaremos usando una apk vulnerable llamada [AndroGoat]().  
Cabe resaltar, que actualmente la mayoría de las aplicaciones utilizan protocolos como HTTP(S) para enviar y recibir desde y hacia servidores.  
Por otro lado, es importante mencionar que el análisis del tráfico entre la aplicación móvil y los puntos finales ayudará a probar las vulnerabilidades del lado del servidor.  


<figure>
  <img src="{{site.baseurl}}/img/diagrama-proxy-server.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/diagrama-proxy-server.png" title="Diagrama para entender la funcionalidad de un proxy">Diagrama para entender la funcionalidad de un proxy</a>.
  </figcaption>
</figure> 

**Herramientas necesarias:** cualquier proxy, por ejemplo, Burp Suite
*Nota: el dispositivo móvil y la computadora portátil deben estar en la misma red.*  


Inicialmente, debemos configurar el proxy de la siguiente manera:  

<figure>
  <img src="{{site.baseurl}}/img/configurando-burp.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/configurando-burp.png" title="Configurando burpsuite para que escuche sobre todas las interfaces">Configurando burpsuite para que escuche sobre todas las interfaces</a>.
  </figcaption>
</figure> 


<figure>
  <img src="{{site.baseurl}}/img/burp-configurado.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/burp-configurado.png" title="Validando configuracion">Validando configuracion</a>.
  </figcaption>
</figure> 


En Dispositivo móvil, siga los pasos a continuación para habilitar el proxy.  
1. Dirigirse a *Configuración > WiFi > Mantenga presionado el WiFi conectado > Modificar red > verifique Opciones avanzadas*
2. Establezca los valores siguientes y presione Guardar
Proxy: Manual
Nombre de host: 127.0.0.1
Puerto: Número de puerto del proxy

<figure>
  <img src="{{site.baseurl}}/img/wifi-with-proxy.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/wifi-with-proxy.png" title="Configurando el SSID para que envie trafico al proxy">Configurando el SSID para que envie trafico al proxy</a>.
  </figcaption>
</figure> 

Ahora podemos proceder con una prueba basica usando nuestra app vulnerable.  
Para ello, debemos iniciar AndroGoat → Interceptación de red → Toque el botón 'HTTP'.

<figure>
  <img src="{{site.baseurl}}/img/AndroGoat-HTTP.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/AndroGoat-HTTP.png" title="Utilizando androgoat para que envie peticiones HTTP">Utilizando androgoat para que envie peticiones HTTP</a>.
  </figcaption>
</figure> 

Si las configuraciones previas tuvieron exito deberia poder visualizar el trafico de la app.  

<figure>
  <img src="{{site.baseurl}}/img/Burp-Capturando-HTTP.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/Burp-Capturando-HTTP.png" title="Capturando trafico HTTP con Burp de una app">Burp-Capturando-HTTP</a>.
  </figcaption>
</figure> 