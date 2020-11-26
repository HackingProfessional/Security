---
layout: post
title:  "Guia para Interceptar trafico de aplicaciones Android"
description: "Aprende hacking moderno enfocado a entornos mobiles"
date:   2020-11-21
image:  "img/android-proxy.png"
tags:   [Android, Proxy, Pentesting, Security]
---

Hello Hackers!!  
Durante una evaluación móvil, normalmente habrá dos sub-evaluaciones: la interfaz móvil y la API de backend. Para examinar la seguridad de la API, necesitará documentación extensa, como archivos Swagger o Postman, o puede dejar que la aplicación móvil genere todo el tráfico por usted y simplemente intercepte y modifique el tráfico a través de un proxy (ataque MitM).  

A veces es muy fácil configurar tu proxy. Otras veces, puede ser muy difícil y llevar mucho tiempo.  
En esta guía, usaré el proxy Burp Suite de PortSwigger, pero los mismos pasos, por supuesto, se pueden usar con cualquier proxy HTTP. El proxy se alojará en 192.168.1.100 en el puerto 8080 en todos los ejemplos. Las comprobaciones comienzan de forma muy básica, pero aumentan hacia el final.  

 - [¿Está configurado su proxy en el dispositivo?](#Configurar-el-dispositivo)
 - [¿Burp escucha en todas las interfaces?](#¿Burp-escucha-en-todas-las-interfaces?)
 - [¿Puede su dispositivo conectarse a su proxy?](#¿Puede-su-dispositivo-conectarse-a-su-proxy?)
 - [¿Puede proxy el tráfico HTTP?](#¿Como-utilizar-el-proxy-sobre-el-tráfico-HTTP?)
 - [¿Su certificado de Burp está instalado en el dispositivo?](#¿Su-certificado-de-Burp-está-instalado-en-el-dispositivo?)
 - [¿Su certificado de Burp está instalado como certificado raíz?](#¿Su-certificado-de-Burp-está-instalado-como-certificado-raíz?)
 - [¿Su certificado tien una vida útil adecuada?](#¿Su-certificado-tiene-una-vida-útil-adecuada?)
 - [¿Conoce el proxy de la aplicación?](#¿Conoce-el-proxy-de-la-aplicación?)
 - [¿La aplicación utiliza puertos personalizados?](#¿La-aplicación-utiliza-puertos-personalizados?)
 - [¿La aplicación utiliza fijación SSL?](#¿La-aplicación-utiliza-fijación-SSL?)
    - Fijación a través de networkSecurityConfig
    - Fijar a través de OkHttp
    - Fijar a través de Ofuscated OkHttp en aplicaciones ofuscadas
    - Anclar a través de varias bibliotecas
    - Anclar en marcos de aplicaciones de terceros (Flutter, Xamarin, Unity)

# Configurar el dispositivo
Primero, debemos asegurarnos de que todo esté configurado correctamente en el dispositivo. Estos pasos se aplican independientemente de la aplicación que intente para MitM.  

## ¿Está configurado su proxy en el dispositivo?
Un primer paso obvio es configurar un proxy en el dispositivo. La interfaz de usuario cambia un poco según la versión de Android, pero no debería ser demasiado difícil de encontrar.  

**Validacion inicial**  
Ir a *Configuración>Conexiones> Wi-Fi*, seleccione la red Wi-Fi en la que se encuentra, haga clic en *Avanzado>Proxy>Manual* e ingrese los detalles de su Proxy:

**Nombre de host de proxy:** 192.168.1.100
**Puerto de proxy:** 8080

<figure>
  <img src="{{site.baseurl}}/img/1-configurando-proxy.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/1-configurando-proxy.png" title="Redireccionando el trafico hacia nuestro proxy">Redireccionando el trafico hacia nuestro proxy</a>.
  </figcaption>
</figure> 

## ¿Burp escucha en todas las interfaces?
De forma predeterminada, Burp solo escucha en la interfaz local (127.0.0.1), pero como queremos conectarnos desde un dispositivo diferente, Burp necesita escuchar en la interfaz específica que se ha unido a la red Wi-Fi. Puede escuchar en todas las interfaces o escuchar en una interfaz específica si sabe cuál desea.   
Normalmente, suelo elegir "escuchar en todas las interfaces".  
Tenga en cuenta que Burp tiene una API que puede permitir a otras personas en la misma red Wi-Fi consultar su proxy y recuperar información de él.  

**Validacion inicial**  
Para validar vamos a dirigirnos al enlace http://192.168.1.100:8080 desde su computadora host.  
Debería aparecer la pantalla de bienvenida de Burp.  


En Burp, vaya a *Proxy>Opciones>* Haga clic en su proxy en la ventana Proxy Listeners> marque 'Todas las interfaces' en la configuración Bind to Address

<figure>
  <img src="{{site.baseurl}}/img/burp_allinterfaces.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/burp_allinterfaces.png" title="Configurando Burp para que escuche en todas las interfaces">Configurando Burp para que escuche en todas las interfaces</a>.
  </figcaption>
</figure> 

## ¿Puede su dispositivo conectarse a su proxy?
Algunas redes tienen aislamiento de host/cliente y no permiten que los clientes se comuniquen entre sí. En este caso, su dispositivo no podrá conectarse al proxy ya que el enrutador no lo permite.  

**Validacion inicial**  
Abra un navegador en el dispositivo y vaya a http://192.168.1.100:8080 . Debería ver la pantalla de bienvenida de Burp. También debería poder navegar a http://burp en caso de que ya haya configurado el proxy en la verificación anterior.  

**Solución**  
A continuacion, se enumeran alguna de ellas:
- Configure una red inalámbrica personalizada donde el aislamiento de host/cliente esté deshabilitado
- Aloje su proxy en un dispositivo que sea accesible, por ejemplo, una instancia de AWS ec2
- Realice un ataque de suplantación de identidad ARP para engañar al dispositivo móvil haciéndole creer que usted es el enrutador.
- Utilice adb reverse para proxy su tráfico a través de un cable USB:
  - Configure el proxy en su dispositivo para ir al *127.0.0.1:8080*
  - Conecte su dispositivo a través de USB y asegúrese de que *adb devices* muestre su dispositivo
  - Ejecutar *adb reverse tcp:8080 tcp:8080* que envía todo el tráfico recibido en *dispositivo:8080* a *host:8080*
  - En este punto, debería poder navegar a http://127.0.0.1:8080 y ver la pantalla de bienvenida de Burp

## ¿Como utilizar el proxy sobre el tráfico HTTP?
Los pasos para el tráfico HTTP suelen ser mucho más sencillos que el tráfico HTTPS, por lo que una comprobación rápida aquí es asegurar que su proxy esté configurado correctamente y sea accesible para el dispositivo.

**Validacion inicial**  
Vaya a http://neverssl.com y asegúrese de ver la solicitud en Burp. Neverssl.com es un sitio web que no usa HSTS y nunca lo enviará a una versión HTTPS, lo que lo convierte en una prueba perfecta para el tráfico de texto sin formato.  

**Solución**  
 - Repase las verificaciones anteriores nuevamente, algo puede estar mal.
 - La intercepción de Burp está habilitada y la solicitud está esperando su aprobación.

## ¿Su certificado de Burp está instalado en el dispositivo?
Para interceptar el tráfico HTTPS, el certificado de su proxy debe estar instalado en el dispositivo.  

**Validacion inicial**  
Vaya a *Configuración>Seguridad>Credenciales de confianza>Usuario* y asegúrese de que su certificado esté en la lista. Alternativamente, puede intentar interceptar el tráfico HTTPS desde el navegador del dispositivo.

**Solución**  
Esto está documentado en muchos lugares, pero aquí hay un resumen rápido:  
 - Navegue a http://burp en su navegador
 - Haga clic en el 'Certificado CA' en la parte superior derecha; comenzará una descarga
 - Use adb o un administrador de archivos para cambiar la extensión de der a crt
  - *adb shell mv /sdcard/Download/cacert.der/sdcard/Download/cacert.crt*
 - Navegue hasta el archivo usando su administrador de archivos y abra el archivo para comenzar la instalación

## ¿Su certificado de Burp está instalado como certificado raíz?
Las aplicaciones en versiones más recientes de Android no confían en los certificados de usuario de forma predeterminada.
Alternativamente, puede volver a empaquetar aplicaciones para agregar los controles relevantes al archivo *network_security_policy.xml*, pero tener su CA raíz en la tienda de CA del sistema le ahorrará un dolor de cabeza en otros pasos (como marcos de terceros), por lo que es mi método preferido.  

**Validacion inicial**    
Vaya a *Configuración>Seguridad>Credenciales confiables>* Sistema y asegúrese de que su certificado esté en la lista.

**Solución**  
Para que su certificado aparezca como certificado raíz, su dispositivo debe estar rooteado con Magisk.

 - Instale el certificado de cliente de la forma habitual (consulte la comprobación anterior)
 - Instale el módulo MagiskTrustUser
 - Reinicie su dispositivo para habilitar el módulo
 - Reinicie una segunda vez para activar la copia del archivo

Alternativamente, puede:

 - Asegúrese de que el certificado tenga el formato correcto y cópielo y péguelo en el directorio */system/etc/security/cacerts* usted mismo. Sin embargo, para que esto funcione, su partición */ system* debe tener permisos de escritura. Algunos métodos de rooteo permiten esto, pero es muy malo a comparacion de Magisk. También es un poco tedioso obtener el certificado en el formato correcto.
 - Modifique *networkSecurityConfig* para incluir certificados de usuario como anclajes de confianza. Sin embargo, es mucho mejor tener su certificado como certificado del sistema, por lo que rara vez adopto este enfoque.  

## ¿Su certificado tien una vida útil adecuada?
Google (y por tanto Android) está acortando agresivamente la vida útil máxima aceptada de los certificados.
Si la fecha de vencimiento de su certificado de hoja está demasiado adelantada en el futuro, Android / Chrome no lo aceptará.

**Validacion inicial**  
Conéctese a su proxy mediante un navegador e investigue la duración del certificado tanto de la CA raíz como del certificado. Si tienen menos de 1 año, está listo. Si son más largos, me gusta ir a lo seguro y crear una nueva CA. También puede utilizar la última versión del navegador Chrome en Android para validar la duración de su certificado. Si algo va mal, Chrome mostrará el siguiente *error:ERR_CERT_VALIDITY_TOO_LONG*.  

**Solución**  
Hay dos posibles soluciones aquí:

 - Asegúrese de tener instalada la última versión de Burp, que reduce la vida útil de los certificados generados.
 - Cree su propia CA raíz que solo sea válida durante 365 días. Los certificados generados por esta CA raíz también tendrán una duración inferior a 365 días. Esta es mi opción preferida, ya que el certificado se puede compartir con los miembros del equipo y se pueden instalar en todos los dispositivos utilizados durante las interacciones.

# Configurar la aplicación
Ahora que el dispositivo está listo para funcionar, es hora de echar un vistazo a los detalles de la aplicación.  

## ¿Conoce el proxy de la aplicación?
Muchas aplicaciones simplemente ignoran la configuración de proxy del sistema. Las aplicaciones que usan bibliotecas estándar generalmente usarán la configuración de proxy del sistema, pero las aplicaciones que dependen del lenguaje interpretado (como Xamarin y Unity) o que se compilan de forma nativa (como Flutter) generalmente requieren que el desarrollador programe explícitamente la compatibilidad con el proxy en la aplicación.  

**Validacion inicial**  
Al ejecutar la aplicación, debería ver sus datos HTTPS en la pestaña Proxy de Burp, o debería ver errores de conexión HTTPS en el registro de eventos de Burp en el panel del Tablero . Dado que todo el dispositivo es proxy, verá muchas solicitudes bloqueadas de aplicaciones que usan SSL Pinning (por ejemplo, Google Play), así que deberia analizar si puede encontrar un dominio que esté relacionado con la aplicación. Si no ve ninguna conexión fallida relevante, lo más probable es que su aplicación no tenga conocimiento del proxy.

Como verificación adicional, puede ver si la aplicación utiliza un marco de terceros. Si la aplicación está escrita en Flutter, definitivamente no reconocerá el proxy, mientras que si está escrita en Xamarin o Unity, es muy probable que ignore la configuración del proxy del sistema.

 - Descompilar con apktool
  - *apktool d myapp.apk*
 - Revisar lo siguiente:
  - Flutter: *myapp/lib/arm64-v8a/libflutter.so*
  - Xamarin: *myapp/unknown/assemblies/Mono.Android.dll*
  - Unity: *myapp/lib/arm64-v8a/libunity.so*

**Solución**  
Recomiendo revisar lo siguiente:

  - Utilice ProxyDroid (solo root). Aunque es una aplicación antigua, todavía funciona muy bien. ProxyDroid usa iptables para redirigir con fuerza el tráfico a su proxy.
  - Configure un punto de acceso personalizado a través de una segunda interfaz inalámbrica y use iptables para redirigir el tráfico usted mismo. Puede encontrar la configuración en [la documentación de mitmproxy](https://docs.mitmproxy.org/stable/howto-transparent/), que es otro proxy HTTP útil. La misma configuración exacta funciona con Burp.  

En ambos casos, ha pasado de una configuración "compatible con proxy" a una de "proxy transparente". Hay dos cosas que debes tener en cuenta:

 - Deshabilite el proxy en su dispositivo. Si no lo hace, Burp recibirá solicitudes transparentes y con proxy, que no son compatibles entre sí.
 - Configure Burp para admitir el proxy transparente a través de *Proxy>Opciones>proxy activo>editar>Gestión de solicitudes>Admitir proxy invisible*

<figure>
  <img src="{{site.baseurl}}/img/burp_transparent.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/burp_transparent.png" title="Configurando Burp para que acepte proxy invisible">Configurando Burp para que acepte proxy invisible</a>.
  </figcaption>
</figure> 

## ¿La aplicación utiliza puertos personalizados?
Esto solo se aplica realmente si su aplicación no es compatible con el proxy. En ese caso, usted (o ProxyDroid) utilizará iptables para interceptar el tráfico, pero estas reglas de iptables solo tienen como objetivo puertos específicos. En el código fuente de ProxyDroid , puede ver que solo los puertos 80 (HTTP) y 443 (HTTPS) están dirigidos. Si la aplicación utiliza un puerto no estándar (por ejemplo, 8443 o 8080), no será interceptado.

**Validacion inicial**  
Este es un poco más complicado. Necesitamos encontrar el tráfico que sale de la aplicación y que no va a los puertos 80 o 443. La mejor forma de hacerlo es escuchar todo el tráfico que sale de la aplicación. Podemos hacer esto usando tcpdump en el dispositivo o en la máquina host en caso de que esté trabajando con un segundo punto de acceso Wi-Fi.  
Ejecute el siguiente comando en un shell adb con privilegios de root:

{% highlight bash %}
tcpdump -i wlan0 -n -s0 -v
{% endhighlight %}

Verá muchas conexiones diferentes. Idealmente, debería iniciar el comando, abrir la aplicación y detener tcpdump tan pronto como sepa que la aplicación ha realizado algunas solicitudes. Después de un tiempo, verá conexiones a un host remoto con un puerto no predeterminado. En el siguiente ejemplo, hay varias conexiones a 192.168.2.70 en el puerto 8088:  

<figure>
  <img src="{{site.baseurl}}/img/port8088.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/port8088.png" title="Analizando el trafico con tcpdump">Analizando el trafico con tcpdump</a>.
  </figcaption>
</figure> 

Alternativamente, puede enviar la salida de tcpdump a un pcap usando *tcpdump -i wlan0 -n -s0 -w /sdcard/output.pcap*.   Después de recuperar el archivo output.pcap del dispositivo, se puede abrir con WireShark e inspeccionar:  

<figure>
  <img src="{{site.baseurl}}/img/wireshark.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/wireshark.png" title="Analizando el trafico con wireshark">Analizando el trafico con wireshark</a>.
  </figcaption>
</figure> 

**Solución**  
Si su aplicación no conoce el proxy y se comunica a través de puertos personalizados, ProxyDroid no podrá ayudarlo. ProxyDroid no le permite agregar puertos personalizados. Esto significa que tendrá que usar iptables manualmente.


 - O configura un segundo punto de acceso donde su máquina host actúa como el enrutador, y así puede realizar un MitM.
 - O usa ARP spoofing para realizar un MitM activo entre el enrutador y el dispositivo
 - O puede usar iptables usted mismo y reenviar todo el tráfico a Burp. Dado que Burp está escuchando en un host separado, la mejor solución es usar adb reverse para asignar un puerto en el dispositivo a su instancia de Burp. De esta manera, no necesita configurar un punto de acceso separado, solo necesita conectar su dispositivo a través de USB.
   - En el equipo: *adb reverse tcp:8080 tcp:8080*
   - En el dispositivo, como root: *iptables -t nat -A OUTPUT -p tcp -m tcp --dport 8088 -j REDIRECT --to-ports 8080*

## ¿La aplicación utiliza fijación SSL?
En este punto, debería estar recibiendo fallas de conexión HTTPS en el panel de registro de eventos de Burp. El siguiente paso es verificar si se utiliza la fijación SSL y deshabilitarla. Aunque muchos scripts de Frida afirman ser omisiones de raíz universales, no hay uno que se acerque siquiera. Las aplicaciones de Android se pueden escribir en muchas tecnologías diferentes, y solo algunas de esas tecnologías suelen ser compatibles. A continuación, puede encontrar varias formas en las que se puede implementar la fijación SSL y formas de evitarlo.

Tenga en cuenta que algunas aplicaciones tienen varias formas de anclar un dominio específico, y es posible que deba combinar scripts para deshabilitar todos los anclajes SSL.

### Anclar a través de Android: networkSecurityConfig
Android permite que las aplicaciones realicen anclajes SSL mediante el archivo *network_security_config.xml*.
Este archivo hace referencia a *AndroidManifext.xml*, el cual esta localizado en el directorio *res/xml*.  
El nombre suele ser *network_security_config.xml* pero no tiene por qué serlo. 
Por ejemplo, la aplicación Microsoft Authenticator tiene los siguientes dos pines definidos:  

<figure>
  <img src="{{site.baseurl}}/img/networksecurityconf.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/networksecurityconf.png" title="Analizando pines del app Microsoft Authenticator">Analizando pines del app Microsoft Authenticator</a>.
  </figcaption>
</figure> 

**Solución**  
Utilice cualquiera de los scripts de derivación universal normales:  
 - Ejecuta Objeción y ejecuta el siguiente comando: *android sslpinning disable*
 - Utilice el código compartido de Frida: *frida -U --codeshare akabe1/frida-multiple-unpinning -f be.nviso.app*
 - Elimine la configuración networkSecurityConfig en AndroidManifest usando: *apktool d* y *apktool b*
 Por lo general, es mucho más rápido hacerlo a través de Frida y solo rara vez se necesita.

### Fijar a través de OkHttp
Otra forma popular de anclar dominios es a través de la biblioteca OkHttp.  
Para ello se debe hacer una validación rápida usando grep para OkHttp y/o sha256. Lo más probable es que encuentre referencias (o incluso hashes) relacionadas con OkHttp y lo que sea que se esté fijando:  
<figure>
  <img src="{{site.baseurl}}/img/pinning.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/pinning.png" title="Usando grep sobre ficheros">Usando grep sobre ficheros</a>.
  </figcaption>
</figure> 

**Solución**  
Utilice cualquiera de los scripts de derivación universal normales:  
 - Ejecuta Objeción y ejecuta el siguiente comando: *android sslpinning disable*
 - Utilice el código compartido de Frida: *frida -U --codeshare akabe1/frida-multiple-unpinning -f be.nviso.app*
 - Descompila el apk usando apktool y modifica los dominios anclados. De forma predeterminada, OkHttp permitirá conexiones que no estén ancladas específicamente. Por lo tanto, si puede encontrar y modificar el nombre de dominio que está anclado, la fijación se desactivará. Sin embargo, usar Frida es mucho más rápido, por lo que rara vez se adopta este enfoque.

