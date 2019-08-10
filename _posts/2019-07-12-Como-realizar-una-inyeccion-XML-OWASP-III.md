---
layout: post
title:  "Como hackear un servidor web con una inyeccion XML"
description: "Explotando los 10 riesgos mas criticos segun el OWASP III"
date:   2019-07-12
image:  OwaspTopTen.jpg
tags:   [Aprende Hacking, Injection XML External Entity, Pentesting WEB]
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual en las aplicaciones web.  
**A4-XML External Entities**  
Muchos procesadores XML antiguos o mal configurados evalúan referencias de entidades externas dentro de documentos XML.  
Por medio de una inyeccion XML, se pueden usar entidades externas para divulgar archivos internos mediante el controlador de URI de archivos, recursos compartidos de archivos internos, escaneo de puertos internos, ejecución remota de código y ataques de denegación de servicio.

## Conceptos Basicos  
El lenguaje de marcado extensible **(XML)** se utiliza para describir datos . El estándar XML es una forma flexible de crear formatos de información y compartir electrónicamente datos estructurados a través de la Internet pública , así como a través de redes corporativas.  

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A4: XML External Entities  
**Escenario de la prueba:**   
En la URL **http://172.17.0.2:9090/app/bulkproducts** nos encontramos con un uploader que sirve para subir archivos XML e interpretarlos y finalmente visualizar una tabla con los datos cargados.  

<figure>
  <img src="{{site.baseurl}}/img/UploadXMLGood.gif" >
	<figcaption>
    <a href="{{site.baseurl}}/img/UploadXMLGood.gif" title="Realizando pruebas sobre el uploader de XML">Realizando pruebas sobre el uploader de XML</a>.
  </figcaption>
</figure>

Luego de validar el comportamiento del aplicativo web, vamos a realizar una prueba simple para verificar si este uploader es vulnerable a inyecciones de XXE (XML External Entity).  
Para ello debemos utilizar el siguiente codigo de XML el cual solo realizara una consulta sobre el famoso archivo de **/etc/passwd**.  

{% highlight xml %}  
<!DOCTYPE foo [<!ELEMENT foo ANY >
<!ENTITY bar SYSTEM "file:///etc/passwd" >]>
<products>
   <product>
      <name>Playstation 4</name>
      <code>274</code>
      <tags>gaming console</tags>
      <description>&bar;</description>
   </product>
</products>
{% endhighlight %}    

El resultado es el siguiente:  

<figure>
  <img src="{{site.baseurl}}/img/xxe2.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/xxe2.png" title="Visualizando el contenido de /etc/passwd por medio de una inyeccion XXE">Visualizando el contenido de /etc/passwd por medio de una inyeccion XXE</a>.
  </figcaption>
</figure>

En la imagen anterior, podemos apreciar la visualizacion completa del archivo **/etc/passwd** el cual almacena las contraseñas de los usuarios del sistema en linux.  

## Recomendaciones
El entrenamiento del desarrollador es esencial para identificar y mitigar defectos de XXE. Aparte de esto, prevenir XXE requiere:  
  - De ser posible, utilice formatos de datos menos complejos como JSON y evite la serialización de datos confidenciales.  
  - Actualice los procesadores y bibliotecas XML que utilice la aplicación o el sistema subyacente. Utilice validadores de dependencias. Actualice SOAP a la versión 1.2 o superior.  
  - Deshabilite las entidades externas de XML y procesamiento DTD en todos los analizadores sintácticos XML en su aplicación.  
  - Implemente validación de entrada positiva en el servidor (“lista blanca”), filtrado y sanitización para prevenir el ingreso de datos dañinos dentro de documentos, cabeceras y nodos XML.  
  - Verifique que la funcionalidad de carga de archivos XML o XSL valide el XML entrante, usando validación XSD o similar.  


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