---
layout: post
title:  "Aprende que es una Deserialización Insegura"
description: "Explotando los 10 riesgos mas criticos segun el OWASP VII"
date:   2019-07-16
image:  OwaspTopTen.jpg
tags:   [Insecure Deserialization, Pentesting WEB, Hacking NodeJS]
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual y critica en las aplicaciones web.  

**A8 - Insecure Deserialization**  
Es una vulnerabilidad que se produce cuando se usan datos no confiables para abusar de la lógica de una aplicación, infligir un ataque de denegación de servicio (DoS) o incluso ejecutar código arbitrario cuando se deserializa.  
Una Aplicacion y APIs puede ser vulnerable, si deserializan objetos hostiles o manipulados por un atacante. Esto da como resultado dos tipos primarios de ataques:  
  - Ataques relacionados con la estructura de datos y objetos; donde el atacante modifica la lógica de la aplicación o logra una ejecución remota de código que puede cambiar el comportamiento de la aplicación durante o después de la deserialización.  
  - Ataques típicos de manipulación de datos; como ataques relacionados con el control de acceso, en los que se utilizan estructuras de datos existentes pero se modifica su contenido.  

## Conceptos Basicos  
**Serialización:** Se refiere a un proceso de conversión de un objeto a un formato que puede persistir en el disco enviado a través de secuencias o enviado a través de una red. El formato en el que se serializa un objeto puede ser binario o texto estructurado (por ejemplo, XML, JSON YAML …). Cabe resaltar que JSON y XML son dos de los formatos de serialización más utilizados en las aplicaciones web.  

**Deserialización:** Se refiere a transformar los datos serializados provenientes de un archivo, flujo o socket de red en un objeto.   

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## Deserialización Insegura  
**Escenario de la prueba #1:**   
Vamos aprender a explotar esta vulnerabilidad por medio de un uploader que se encuentra en un aplicativo WEB.  

Inicialmente vamos a dirigirnos al apartado para realizar la prueba de inyeccion de XSS reflejada.  
**http://172.17.0.2:9090/app/bulkproducts?legacy=true**  


<figure>
  <img src="{{site.baseurl}}/img/Deserialization.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/Deserialization.png" title="Formulario con un uploader">Formulario con un uploader</a>.
  </figcaption>
</figure>

El aplicativo web anterior se compone de un uploader que recibe archivos para cargarlos en la base de datos y mostrarlos en la siguiente tabla.  

<figure>
  <img src="{{site.baseurl}}/img/xss.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xss.png" title="Tabla renderizando lo cargado en el uploader">Tabla renderizando lo cargado en el uploader</a>.
  </figcaption>
</figure>

Vamos a crear un archivo JSON malicioso, con el cual obtendremos el archivo **/etc/passwd**.  
La carga util "maliciosa" que usaremos es la siguiente:  

{% highlight js %}
{"rce":"_$$ND_FUNC$$_function (){require('child_process').exec('id;cat /etc/passwd', function(error, stdout, stderr) { console.log(stdout) });}()"}
{% endhighlight %}    

Al cargar el archivo logramos obtener el siguiente resultado:  

<figure>
  <img src="{{site.baseurl}}/img/Result.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/Result.png" title="Ejecutando comandos desde el aplicativo WEB">Ejecutando comandos desde el aplicativo WEB</a>.
  </figcaption>
</figure>


## Recomendaciones  
El único patrón de arquitectura seguro es no aceptar objetos serializados de fuentes no confiables o utilizar medios de serialización que sólo permitan tipos de datos primitivos.  
Si esto no es posible, considere alguno de los siguientes puntos:  
  - Implemente verificaciones de integridad tales como firmas digitales en cualquier objeto serializado, con el fin de detectar modificaciones no autorizadas.  
Aísle el código que realiza la deserialización, de modo que se ejecute en un entorno con los mínimos privilegios posibles.   
  -  Registre las excepciones y fallas en la deserialización, tales como cuando el tipo recibido no es el esperado, o la deserialización produce algún tipo de error.  
  - Monitoree los procesos de deserialización, alertando si un usuario deserializa constantemente.  

## Aprende hacking explotando las vulnerabilidades mas criticas en aplicaciones WEB.  

 - [A1: Inyección](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-SQL-OWASP-1)   
 - [A2: Pérdida de Autenticación](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A3: Exposición de datos sensibles](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A4: Entidades Externas XML (XXE)](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-XML-OWASP-III)  
 - [A5: Pérdida de Control de Acceso](https://hackingprofessional.github.io/Security/Evadiendo-controles-de-acceso-OWASP-IV)  
 - [A6: Configuración de Seguridad Incorrecta](https://hackingprofessional.github.io/Security/El-riesgo-de-las-Configuraciónes-Incorrectas-de-Seguridad-OWAPS-V)  
 - [A7: Secuencia de Comandos en Sitios Cruzados (XSS)](https://hackingprofessional.github.io/Security/Aprende-a-realizar-una-inyeccion-XSS-OWASP-VI)  
 - [A8: Deserialización Insegura](https://hackingprofessional.github.io/Security/Aprende-que-es-Deserialización-Insegura-OWASP-VII)  
 - [A9: Componentes con vulnerabilidades conocidas](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  
 - [A10: Registro y Monitoreo Insuficientes](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  


 [Para mas informacion leer el siguiente PDF publicado por el OWASP](https://www.owasp.org/images/5/5e/OWASP-Top-10-2017-es.pdf)