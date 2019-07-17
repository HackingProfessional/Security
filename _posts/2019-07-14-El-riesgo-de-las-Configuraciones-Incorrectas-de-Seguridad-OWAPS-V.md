---
layout: post
title: "Configuraciónes Incorrectas de Seguridad"
excerpt: "Explotando los 10 riesgos mas criticos segun el OWASP V"
image: "https://hackingprofessional.github.io/Security/images/OwaspTopTen2.png"
tags: 
  - Fingerprinting
  - Hacking NodeJS
  - Generando error en aplicativos
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual y critica en las aplicaciones web.  

**A6-Security Misconfiguration**  
Una configuración errónea de seguridad surge cuando dichas configuraciones se definen, implementan y se mantienen con valores predeterminados.   
La buena seguridad requiere una configuración segura definida e implementada para la aplicación, el servidor web, la base de datos y la plataforma.   
Una aplicación puede ser vulnerable si:  
  - El manejo de errores revela a los usuarios trazas de la aplicación u otros mensajes demasiado informativos.  
  - Las cuentas predeterminadas y sus contraseñas siguen activas y sin cambios.  
  - Falta hardening adecuado en cualquier parte del stack tecnológico, o permisos mal configurados en los servicios de la nube.  
  - Se encuentran instaladas o habilitadas características innecesarias (ej. puertos, servicios, páginas, cuentas o permisos).  
  - Para los sistemas actualizados, las nuevas funciones de seguridad se encuentran desactivadas o no se encuentran configuradas de forma adecuada o segura.  

## Conceptos Basicos  
**Stack Trace**  
Es un informe que proporciona información sobre las subrutinas del programa.  
Se usa comúnmente para ciertos tipos de depuración, donde un stack trace puede ayudar a los ingenieros de software a descubrir dónde se encuentra un problema o cómo varias subrutinas trabajando juntas durante la ejecución.   
Un Stack trace, también se conoce como seguimiento de pila o retroceso de pila.  

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A6: Security Misconfiguration  
**Escenario de la prueba #1:**   
Vamos aprender a explotar esta vulnerabilidad por medio de un formulario que no valida los tipos de datos recibidos.  

Inicialmente vamos a dirigirnos al apartado para realizar la prueba donde se evidencia una configuracion incorrecta sobre el aplicativo web.  
**http://172.17.0.2:9090/app/calc**  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/calc-web.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/calc-web.png" title="Formulario con funcionalidad de calculadora">Formulario con funcionalidad de calculadora</a>.
  </figcaption>
</figure>

La funcionalidad normal del formulario previamente indicado es para realizar calculos matematicos, en otras palabras es una calculadora WEB.  
Si realizamos pruebas de funcionalidad sobre el aplicativo logramos identificar que la entrada de texto permite todo tipo de caracteres e incluso no numericos, si en vez de colocar una operacion matematica correcta y colocamos valores aleatorios, podemos generar un error en el sistema dando como resultado una exposicion de informacion que podria ser util en la primera de un pentesting.  
Para este caso en concreto, el error indica que se esta usando la libreria **mathjs** y revela rutas donde se alojan diferentes archivos de la aplicacion.  


<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ErrorFatal.gif" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/ErrorFatal.gif" title="Generando un error en el aplicativo">Divulgacion de informacion debido a una falta de validacion de caracteres aceptados</a>.
  </figcaption>
</figure>

## Encabezado X-Powered-By  
**Escenario de la prueba #2:**   

El encabezado **X-Powered-By** se envía de forma predeterminada en cada respuesta; Una buena practica que se debe realizar antes de poner a produccion un sitio web es deshabilitar este tipo de encabezados, ya que de esta manera podemos evitar que los atacantes consigan informacion util por medio de tecnicas de fingerprinting y footprinting.  
**Fingerprinting**  
Es una etapa que se realiza durante un pentesting, la cual consiste en recolectar información directamente del sistema de una organización, para aprender mas sobre su configuración y comportamiento.  

Una forma sensilla para obtener los encabezados de un sitio web es usando **CURL** junto con el parametro **--HEAD**.  

```zsh  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus] - [Sun Jul 07, 14:39]
└─[$]> # curl --HEAD http://172.17.0.2:9090/app/calc
HTTP/1.1 302 Found
X-Powered-By: Express
Location: /login
Vary: Accept
Content-Type: text/plain; charset=utf-8
Content-Length: 28
set-cookie: connect.sid=s%3A8RsuyMpTKS2kAuTgKot5U3ZPxbH2Tj8L.3fZu23xZRbXj7rb7fSelYdNreVFKotHrSrXhtOLPDNA; Path=/; HttpOnly
Date: Sun, 07 Jul 2019 19:58:50 GMT
Connection: keep-alive
```  

Como pudimos apreciar la salida del comando anterior revela que el servidor esta siendo uso de la libreria **EXPRESS**.  

## Recomendaciones  
  - Use una plataforma minimalista sin funcionalidades innecesarias, componentes, documentación o ejemplos. Elimine o no instale frameworks y funcionalidades no utilizadas.  
  - Siga un proceso para revisar y actualizar las configuraciones apropiadas de acuerdo a las advertencias de seguridad y siga un proceso de gestión de parches.  
  - La aplicación debe tener una arquitectura segmentada que proporcione una separación efectiva y segura entre componentes y acceso a terceros, contenedores o grupos de seguridad en la nube (ACLs).  
  - Envíe directivas de seguridad a los clientes (por ej. cabeceras de seguridad).  
  - Utilice un proceso automatizado para verificar la efectividad de los ajustes y configuraciones en todos los ambientes.   

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