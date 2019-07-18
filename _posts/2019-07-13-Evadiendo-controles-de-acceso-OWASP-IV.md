---
layout: post
title: "Evadiendo controles de acceso"
excerpt: "Explotando los 10 riesgos mas criticos segun el OWASP IV"
image: "https://hackingprofessional.github.io/Security/images/OwaspTopTen.jpg"
tags: 
  - Analizando codigo fuente
  - Hacking NodeJS
  - Enumerando API REST
---

Hello Engineers!  
En el dia de hoy, vamos a aprender acerca de una debilidad muy usual y critica en las aplicaciones web.  
**A5-Broken Access Control**  
Las restricciones de control de acceso implican que los usuarios no pueden actuar fuera de los permisos previstos. Típicamente, las fallas conducen a la divulgación, modificación o destrucción de información no autorizada de los datos, o a realizar una función de negocio fuera de los límites del usuario.   
Las vulnerabilidades comunes de control de acceso incluyen:   
  - Pasar por alto las comprobaciones de control de acceso modificando la URL, el estado interno de la aplicación o HTML, utilizando una herramienta de ataque o una conexión vía API.  
  - Permitir que la clave primaria se cambie a la de otro usuario, pudiendo ver o editar la cuenta de otra persona.   
  - Elevación de privilegios. Actuar como un usuario sin iniciar sesión, o actuar como un administrador habiendo iniciado sesión como usuario estándar.  
  - Manipulación de metadatos, como reproducir un token de control de acceso JWT (JSON Web Token), manipular una cookie o un campo oculto para elevar los privilegios, o abusar de la invalidación de tokens JWT.   
  - La configuración incorrecta de CORS permite el acceso no autorizado a una API.  
  - Forzar la navegación a páginas autenticadas como un usuario no autenticado o a páginas privilegiadas como usuario estándar.  
  - Acceder a una API sin control de acceso mediante el uso de verbos POST, PUT y DELETE.  
  
## Conceptos Basicos  
**API**  
Es un conjunto de rutinas que provee acceso a funciones de un determinado software.  
Normalmente, Son utilizadas por los programadores para construir sus aplicaciones sin necesidad de volver a programar funciones ya hechas por otros, reutilizando código que se sabe que está probado y que funciona correctamente.  


## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A5: Broken Access Control  
**Escenario de la prueba:**   
Vamos aprender a explotar esta vulnerabilidad por medio de la lectura del codigo fuente de una aplicacion web y utilizando otros metodos.  

Inicialmente vamos a dirigirnos al apartado para realizar la prueba de evacion de control de acceso.  
**http://172.17.0.2:9090/app/admin**  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/Image1.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/Image1.png" title="Error indicando problemas de autenticacion">Error indicando problemas de autenticacion</a>.
  </figcaption>
</figure>

Al ingresar a la URL anterior, podemos apreciar un error que indica falta de permisos para visualizar el contenido.  
Durante las pruebas de penetracion sobre sitios web, uno de los pasos que se deben realizar es el analisis sobre el codigo fuente del aplicativo, de esta forma podriamos encontrar fallos en la logica del desarrollador u otros datos que nos sean util para la recopilacion de informacion.  
Para visualizar el codigo HTML y JS de una pagina web, solo debemos dar click derecho y clickear en la opcion de **ver codigo fuente**.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image2.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image2.png" title="Identificando un enlace relativo con posible informacion delicada">Identificando un enlace relativo con posible informacion delicada</a>.
  </figcaption>
</figure>

Otra manera de hacerlo seria usando la herramienta **CURL**; para ello debemos escribir el siguiente comando:  

```zsh  
# Para poder consumir el servicio o realizar dicha peticion HTTP GET al sitio web debemos especificarle las cookies de sesion.
# Luego de ejecutar curl, podemos exportar la salida con el operador | y filtrar el contenido con otra herramienta de kali llamada GREP, la cual nos ayudara a buscar en todo el codigo fuente la palabra a buscar.
# curl http://pagina.com | grep Palabra-A-Buscar

┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Blog/Junio] - [Sat Jul 06, 12:01]
└─[$]> # curl 'http://172.17.0.2:9090/app/admin' -H 'Cookie: connect.sid=s%3Au0_6VEKI2UuqJ-CLlinyCazEwv-H713M.%2BBJ5ebpPYBnxmAO%2BiUrod0q33P%2FaAKNpyG95VNYldy0'  | grep admin
<div id='admin-body' class='page-body'>
    <a href='/app/admin/users'>List Users</a><br>
    var div = document.getElementById('admin-body');
```  

Logramos identificar una enlace relativo:  
**http://172.17.0.2:9090/app/admin/users**  
Al navegar a este enlace podemos visualizar el siguiente contenido:  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image3.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image3.png" title="Evadiendo el control de acceso por falta de validacion de roles">Evadiendo el control de acceso por falta de validacion de roles</a>.
  </figcaption>
</figure>

Como podemos apreciar sin tener un rol administrativo y gracias a una validacion incorrecta de roles puedo visualizar contenido de caracter sensible.  
Ahora vamos a continuar nuestra auditoria, interceptando la peticion con Burpsuite para analizar los recursos consumidos que realiza el aplicativo antes de la renderizacion.  

<figure>  
  <img src="https://hackingprofessional.github.io/Security/images/A5-BurpSuite.gif" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/A5-BurpSuite.gif" title="Interceptando peticiones con BurpSuite">Interceptando peticiones con BurpSuite</a>.
  </figcaption>
</figure>  

Como logramos apreciar, el sitio web para cargar los datos realiza el consumo de una API situada en la siguiente URL:  
**http://172.17.0.2:9090/app/admin/usersapi**  

El contenido es un JSON:  

```js  
{
    success: true,
    users: [{
            id: 1,
            name: "Admin",
            login: "GerhAdmin",
            email: "GerhAdmin",
            password: "$2a$10$QuCG9ZU6tglTSOekyiVjtuiLKVrmXE.mAMS/87UE27GJJiiVJ.CAC",
            role: null,
            createdAt: "2019-07-06T16:03:40.774Z",
            updatedAt: "2019-07-06T16:03:40.774Z"
        },
        {
            id: 2,
            name: "Daniela Hernandez",
            login: "Daniela",
            email: "Daniela@gmail.com",
            password: "$2a$10$d/UKRr976ImXCFGFyGNz6.YFgn.JegqmAJAhSGRsFngtkBsyrdcsa",
            role: null,
            createdAt: "2019-07-06T17:08:45.194Z",
            updatedAt: "2019-07-06T17:08:45.194Z"
        },
        {
            id: 3,
            name: "Sasuke Uchiha",
            login: "Sasuke",
            email: "Sasuke-la-hostia@gmail.com",
            password: "$2a$10$iQ3OooTXii6GLG/Xd6qPhOIV51GF1ouSBB1KARM2siSaMTVUNK5zq",
            role: null,
            createdAt: "2019-07-06T17:09:54.958Z",
            updatedAt: "2019-07-06T17:09:54.958Z"
        }
    ]
}
```  

Entre los datos que expone al consumir el API, esta el campo de la contraseña codificado en Bcrypt.  
Para descifrar el contenido de estos Hash, te invito al siguiente [blog]().  

## Laboratorio #2  
**Manipulacion de datos mediante una configuracion incorrecta de permisos**  
A continuacion, vamos a realizar un ultimo ejercicio donde podremos apreciar la posibilidad de manipular datos sobre registros de una base de datos.  
En la siguiente captura de imagen, logramos ver un formulario de actualizacion de datos sobre el usuario autenticado.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image4.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image4.png" title="Mostrando la contraseña del usuario Administrador">Mostrando la contraseña del usuario Administrador</a>.
  </figcaption>
</figure>

Si analizamos el codigo fuente del formulario anterior logramos identificar lo siguiente:  

```html  
<form id="editUser" name="editUser" action="/app/useredit" method="post">
  <fieldset>
    <h3> Update User Information </h3><br>
    
    <input type="hidden" name="id" value="3" id="editUser_userId" />
       <!-- Codigo HTML resumido -->
  </fieldset>
</form>
```  

El formulario tiene un input junto con un parametro que indica ser de tipo oculto y su ID es **editUser_userId**, el cual por el nombre tan descriptivo este valor podria tratarse del ID del usuario a editar.  
Ademas si realizamos una prueba de funcionalidad logramos apreciar que al realizar la actualizacion de datos se realiza una peticion HTTP POST y envia este parametro:  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image5.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image5.png" title="Analizando peticion interceptada con BurpSuite">Analizando peticion interceptada con BurpSuite</a>.
  </figcaption>
</figure>

Vamos a realizar una manipulacion de parametros, para intentar cambiar las credenciales de acceso sobre el usuario **Administrador**.  

```php  
id=1&name=Admin+Hacked&email=HackedAdmin%40gmail.com&password=7Y8tBxHbmi62iAG&cpassword=7Y8tBxHbmi62iAG
```  

Si ahora intentamos iniciar sesion, con el usuario administrador podemos apreciar que logramos manipular la contraseña del administrador de este sistema de informacion.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image6.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image6.png" title="Manipulando credenciales de un usuario, sin roles administrativos">Manipulando credenciales de un usuario, sin roles administrativos</a>.
  </figcaption>
</figure>

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/image7.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/image7.png" title="Verificando la manipulacion realizada sobre el usuario GerhAdmin">Verificando la manipulacion realizada sobre el usuario GerhAdmin</a>.
  </figcaption>
</figure>


## Recomendaciones  
El control de acceso sólo es efectivo si es aplicado del lado del servidor o en Server-less API, donde el atacante no puede modificar la verificación de control de acceso o los metadatos.  
  - Con la excepción de los recursos públicos, la política debe ser denegar de forma predeterminada.  
  - Implemente los mecanismos de control de acceso una vez y reutilícelo en toda la aplicación, incluyendo minimizar el control de acceso HTTP (CORS).  
  - Los controles de acceso al modelo deben imponer la propiedad (dueño) de los registros, en lugar de aceptar que el usuario puede crear, leer, actualizar o eliminar cualquier registro.  
  - Los modelos de dominio deben hacer cumplir los requisitos exclusivos de los límites de negocio de las aplicaciones.  
  - Deshabilite el listado de directorios del servidor web y asegúrese que los metadatos/fuentes de archivos (por ejemplo de GIT) y copia de seguridad no estén presentes en las carpetas públicas.  

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