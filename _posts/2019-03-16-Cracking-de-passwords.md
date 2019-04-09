---
layout: post
title: "Password Cracking"
excerpt: "Ataque de fuerza bruta hacia archivos comprimidos y PDF'S"
image: https://hackingprofessional.github.io/Security/images/cracking.jpg
tags: 
  - Ataque de diccionario.
  - Password Cracking
  - Fuerza Bruta.
  - Obteniendo contraseñas de archivos protegidos
---

Se le denomina Password cracking, o Descifrado de contraseñas al proceso donde se intenta obtener acceso no autorizado a un sistema o software protegido por una contraseña. En otras palabras es obtener la contraseña correcta que le da acceso a un sistema protegido por un método de autenticación.
En el dia de hoy vamos aprender a realizar un ataque de fuerza bruta mediante un diccionario llamado "rockyou.txt" hacia PDF´s y archivos con un formato de compresión ZIP.

A continuacion, doy a mostrar el menu de opciones que nos brinda esta herramienta llamada *fcrackzip*, la cual como su nombre indica sirve para crackear archivos zip protegidos por una contraseña.

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/fcrackzipHelp.png">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/fcrackzipHelp.png" title="Salida del comando fcrackzip --help">Menu de opciones de la herramienta fcrackzip</a>.
  </figcaption>
</figure>

Para la instalacion de este software en debian se debe escribir lo siguiente en la consola:

```shell
sudo apt-get install fcrackzip
```
Luego de leer el menu de opciones vamos a proceder obteniendo la contraseña de un archivo ZIP con nuestra nueva herramienta que acabamos de instalar.

<script width="640" height="360" id="asciicast-H0iGUzPxkmYRAsOv6g24Wzfma" src="https://asciinema.org/a/H0iGUzPxkmYRAsOv6g24Wzfma.js" async>
</script>

Listo, nuestra contraseña es **jiujitsu**!  
Vamos a continuar con esta tematica pero ahora crackeando archivos PDF's protegidos con una contraseña.  
La herramienta que utilizaremos para este proceso se llama *pdfcrack*, para la instalacion de esta herramienta en sistemas debian se escribe lo siguiente en la consola:

```shell
sudo apt-get install pdfcrack
```
Para finalizar vamos a obtener la contraseña de un archivo PDF mediante un ataque de diccionario con la herramienta previamente instalada.
El siguiente menu corresponde a los parametros que podemos configurar para llevar el uso de *pdfcrack*.

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/PdfcrackHelp.png">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/PdfcrackHelp.png" title="Salida del comando pdfcrack --help">Menu de opciones de la herramienta pdfcrack</a>.
  </figcaption>
</figure>

A continuacion, muestro como obtengo la contraseña de un archivo pdf protegido con contraseña.

<script width="640" height="360" id="asciicast-234702" src="https://asciinema.org/a/234702.js" async>
</script>

Nuestra contraseña para desbloquear y ver el contenido del PDF es: "writeoff4*"  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/PdfUnlocked.png">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/images/PdfUnlocked.png" title="Archivo PDF desbloqueado">Ataque de diccionario hacia un PDF obteniendo un resultado exitoso.</a>.
  </figcaption>
</figure>

Como pueden ver así de sencillo y facil es obtener la contraseña de estos archivos, La recomendacion para evitar ser victima de este ataque es usar contraseñas complejas para dificultar estos procedimientos, mezclando carácteres especiales y usando combinaciones que no sigan patrones predecibles.
### Caracteristicas de una contraseña compleja:
 - Una longitud mínima de siete caracteres.
 - Incluye números
 - Incluye caracteres no estándar: ñ, /, #, &...
 - Letras mayúsculas y minúsculas
 - No es ninguna palabra incluida en un diccionario
 - Buscando en Google, arroja cero resultados

### Comandos usados:
  - fcrackzip -u -D -p '/usr/share/wordlists/rockyou.txt' Secretos.zip
  - pdfcrack Private_File.pdf -w /usr/share/wordlists/rockyou.txt

### Descarga los archivos utilizados de este tutorial para practicar lo aprendido:
  - [Secretos.zip](https://drive.google.com/file/d/1sZfs0JgLoilU7F5Wo-Rig4_dY_Ns5Qt-/view?usp=sharing)
  - [Privado.pdf](https://drive.google.com/file/d/1EixQD2Hy9QuWWuOTbLGzGKnjfe0ZwAYI/view?usp=sharing)
  
### Glosario
  - Diccionario

