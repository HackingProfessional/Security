---
layout: post
title:  "Extraer contraseña de Windows con Mimikatz"
description: "Obteniendo la contraseña de administrador de Windows 7"
date:   2019-04-11
image:  Mimikatz.jpg
tags:   [Hacking Windows, Hacking from 0, Mimikatz]
---

Hello Hackers!!   
En el dia de hoy aprenderemos a usar una herramienta muy util llamada [Mimikatz.](https://github.com/gentilkiwi/mimikatz)  

Vamos a iniciar explicando que es Mimikatz.  
Mimikatz es una herramienta hecha en lenguaje C por Benjamin Delpy.  
Su funcion principal es extraer contraseñas en texto plano, hashes y la generacion de tickets de Kerberos (protocolo de autenticación) desde la memoria.  

[Descargar Mimikatz](https://github.com/gentilkiwi/mimikatz/releases){: .btn}  

Mimikatz esta disponible tanto como para la arquitectura de 32 bits y tambien para los equipos de 64 bits.  

## Aprendiendo a usar Mimikatz  
### Pantalla azul de la muerte (BSOD) con Mimikatz
Entre varias de las funciones que nos otorga esta herramienta, podemos provocar una pantalla azul de la muerte o un ataque BSOD (Blue Screen of Death) usando mimikatz.  
Para realizar el bsod en un sistema siga los pasos que se mencionan a continuación:

{% highlight bash %}
# Corremos mimikatz como Administrator
# Iniciamos el servicio mimidrv 
!+
{% endhighlight %}
Ahora iniciamos el BSOD como se indica a continuación en el siguiente comando.

{% highlight bash %}
!bsod
{% endhighlight %}

Como se puede ver a continuación, tenemos el error de la pantalla azul de la muerte.  
**Nota: este ataque puede dañar los datos y dañar el sistema. Utilizar con cuidado!!**  

<figure>
  <img src="{{site.baseurl}}/img/BlueScreen.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/BlueScreen.png" title="Pantallazo azul generado por Mimikatz">Pantallazo azul generado por Mimikatz</a>.
  </figcaption>
</figure>

### Hackear contraseña de Windows en texto plano

Escribimos el siguiente comando para obtener los privilegios de depuracion:  

{% highlight bash %}
privilege::debug
{% endhighlight %}

Ahora escribimos el siguiente comando para obtener las contraseñas de los usuarios en modo texto.

{% highlight bash %}
sekurlsa::logonPasswords
{% endhighlight %} 

<figure>
  <img src="{{site.baseurl}}/img/ExtraeraPassWin.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/ExtraeraPassWin.png" title="Extraccion de contraseñas con Mimikatz">Extraccion de contraseñas en Windows 7 con Mimikatz</a>.
  </figcaption>
</figure>

