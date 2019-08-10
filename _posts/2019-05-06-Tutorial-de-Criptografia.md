---
layout: post
title:  "Tutorial de criptografia I"
description: "Aprende criptografia desde 0"
date:   2019-05-06
image:  "img/criptografia.jpg"
tags:   [Aprende Criptografia, Criptoanalisis, Tipos de cifrado]
---

Hello Hackers!!  
En este artículo, aprenderemos acerca de una rama importante de la informatica llamada Criptografia, con ella lograremos evitar que nuestra información caiga en manos equivocadas.  
La información juega un papel vital en el funcionamiento de negocios, organizaciones, operaciones militares, etc. La información en las manos equivocadas puede llevar a la pérdida de negocios o resultados catastróficos. Para asegurar la comunicación, una empresa puede usar la criptografía para cifrar la información.  
La criptografía en pocas palabras es transformar la información en un formato ilegible no humano y viceversa.  


## ¿Qué es la criptografía?
La criptografía es el estudio y la aplicación de técnicas que ocultan el significado real de la información al transformarla en formatos ilegibles no humanos y viceversa.  
**Ejemplo:**  
Supongamos que deseamos cifrar el siguiente mensaje *"Codigo Secreto"*, una forma de hacerlo seria reemplazando cada letra de la frase con la tercera letra sucesiva del alfabeto.  
El mensaje codificado quedaria de la siguiente manera: *"Frgljr Vhfuhwr"*  
Esta tecnica que acabamos de emplear es conocida como: **Cifrado Cesar**.  

Si queremos descifrar nuestro mensaje, tendremos que retroceder tres letras en el alfabeto usando la letra que queremos descifrar.  
A continuacion, muestra cómo se realiza la transformación.  

| **c** | **o** | **d** | **i** | **g** | **o** |   | **s** | **e** | **c** | **r** | **e** | **t** | **o** |
|-------|-------|-------|-------|-------|-------|---|-------|-------|-------|-------|-------|-------|-------|
| d     | p     | e     | j     | h     | p     |   | t     | f     | d     | s     | f     | u     | p     |
| e     | q     | f     | k     | i     | q     |   | u     | g     | e     | t     | g     | v     | q     |
| **f** | **r** | **g** | **l** | **j** | **r** |   | **v** | **h** | **f** | **u** | **h** | **w** | **r** |

El proceso de transformación de la información en una forma ilegible no humana se llama **encriptación**.  
El proceso de revertir el cifrado se llama **descifrado**.  
La información encriptada se conoce como **cifrado**.  

## ¿Qué es el criptoanalisis?  
El análisis criptográfico es el arte de tratar de descifrar los mensajes cifrados sin el uso de la clave que se utilizó para cifrar los mensajes. El criptoanálisis utiliza algoritmos y análisis matemáticos para descifrar algun texto cifrado.  

El éxito de un ataques de criptoanálisis depende:  
	- Potencia de cómputo disponible  
	- Cantidad de tiempo disponible

La siguiente es una lista de los ataques de Criptoanalisis comúnmente utilizados:  

**Ataque de diccionario**: Este tipo de ataque usa una lista de palabras para encontrar una coincidencia del texto sin formato o la clave. Se utiliza principalmente cuando se intentan descifrar contraseñas cifradas.   

**Ataque de fuerza bruta**: Este tipo de ataque utiliza algoritmos que intentan adivinar todas las combinaciones lógicas posibles del texto simple que luego se cifran y se comparan con el cifrado original.

**Ataque de tabla arco iris**: este tipo de ataque compara el texto cifrado con los hashes precalculados para encontrar coincidencias.

## Que es el cifrado Simetrico
El cifrado simétrico, también conocido como “Shared Key” o “Shared Secret”, es aquel en donde se utiliza una sola llave para cifrar y descifrar la información.  
**Ejemplo**  
Si un **usuario A** quiere enviarle un **mensaje X** al **usuario B**, y para ello utiliza la llave **"SecurePassword"** para cifrar la información.  
Para que el **usuario B** pueda descifrar la informacion debe utilizar la misma llave que utilizo el **usuario A**, el cual es: **"SecurePassword"**.  
Entre los algoritmos de cifrado simétricos podemos mencionar AES, 3DES, DES y RC4.  
Los algoritmos 3DES y AES son utilizados comúnmente por el protocolo IPSEC para establecer conexiones de VPN.  
El algoritmo RC4 es utilizado en tecnologías de redes inalámbricas para el cifrado de información en los protocolos de seguridad WEP y WPA version 1.  

## Que es el cifrado asimetrico 
También conocido como **"Criptografía de Llave Pública"**, Este emplea dos llaves en vez de una para ejercer las funciones de cifrado y descifrado de la información.

<figure>
  <img src="{{site.baseurl}}/img/Asimetrico.jpg" >
	<figcaption>
    <a href="{{site.baseurl}}/img/Asimetrico.jpg" title="Explicacion del cifrado Asimetrico">Explicacion del cifrado Asimetrico</a>.
  </figcaption>
</figure>

## Que es el Hashing

El Hashing es una función matemática que no tiene inversa y produce un resultado de longitud fija. A diferencia de la función de cifrado que se utiliza para garantizar la **confidencialidad** de la información, la función de hashing es utilizada en seguridad para garantizar la **integridad** de la información.

### Tres pilares de la seguridad informatica
La gestión de la información se establece gracias a los siguientes tres pilares fundamentales:  

**La confidencialidad**, su objetivo es prevenir la divulgación no autorizada de la información sobre nuestra organización.  

**La integridad**, su objetivo es prevenir modificaciones no autorizadas de la información.  

**La disponibilidad**, su objetivo es prevenir interrupciones no autorizadas de los recursos informáticos.  

Hoy en día los algoritmos de hashing principalmente empleados son MD5 y SHA-1.    
El **Hash** es un grupo de caracteres alfanuméricos, el cual es el resultado de aplicarle la función de hashing a un mensaje o archivo para comprobar la autenticidad.  

### Calcular un hash en Linux 

{% highlight zsh %}
┌─[root@Kratos] - [~/Desktop/MyCodes/BotTelegram] - [Mon May 06, 20:46]
└─[$]> md5sum index.js 
c0bc802345bbaf09d0867eed64c59a99  index.js

┌─[root@Kratos] - [~/Desktop/MyCodes/BotTelegram] - [Mon May 06, 20:46]
└─[$]> sha256sum index.js 
89760bd9a51e4a0cd853d59e6e0cdc2490e058349d08a5d4c1940394ff151977  index.js
{% endhighlight %}  

### Calcular el hash de una cadena de texto

{% highlight zsh %}
┌─[root@Kratos] - [~/Desktop/MyCodes/BotTelegram] - [Mon May 06, 20:46]
└─[$]> echo -n texto | md5sum
62059a74e9330e9dc2f537f712b8797c  -

┌─[root@Kratos] - [~/Desktop/MyCodes/BotTelegram] - [Mon May 06, 20:46]
└─[$]> echo -n texto | sha256sum
0d389a0d43622f91b3f68ed29fd2c4f0676a8aabd44ea5c63f8ee5511bff7289  -
{% endhighlight %}  

## Glosario 

**MD5**: Este es el acrónimo de Message-Digest 5. Se utiliza para crear valores hash de 128 bits. En teoría, los hashes no se pueden revertir en el texto plano original. MD5 se utiliza para cifrar contraseñas y verificar la integridad de los datos. MD5 no es resistente a la colisión. La resistencia a la colisión es la dificultad para encontrar dos valores que producen los mismos valores hash.  

**SHA**: Este es el acrónimo de Secure Hash Algorithm. Los algoritmos SHA se utilizan para generar representaciones condensadas de un mensaje (resumen del mensaje).  
Tiene varias versiones tales como:  
	- **SHA-0**: Produce valores hash de 120 bits. Se retiró del uso debido a fallas importantes y se reemplazó por SHA-1.  
	- **SHA-1**: Produce valores hash de 160 bits. Es similar a las versiones anteriores de MD5. Tiene debilidad criptográfica y no se recomienda su uso desde el año 2010.  
	- **SHA-2**: Tiene dos funciones hash, a saber, SHA-256 y SHA-512. SHA-256 usa palabras de 32 bits, mientras que SHA-512 usa palabras de 64 bits.  
	- **SHA-3**: Este algoritmo fue conocido formalmente como Keccak.  

**RC4**: Este algoritmo se utiliza para crear cifrados de flujo. Se utiliza principalmente en protocolos como Secure Socket Layer (SSL) para cifrar la comunicación de Internet y la privacidad equivalente por cable (WEP) para proteger las redes inalámbricas.  

**BLOWFISH**: Este algoritmo se utiliza para crear cifrados con clave y simétricamente bloqueados. Se puede utilizar para cifrar contraseñas y otros datos.  

**AES**: Este es el acrónimo de Advanced Encryption Standard.  El algoritmo más utilizado hoy en día. Es un esquema de cifrado por bloques adoptado como un estándar de cifrado por el gobierno de los Estados Unidos. 
