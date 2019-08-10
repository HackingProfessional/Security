---
layout: post
title:  "Esteganografia I"
description: "Tecnicas para ocultar informacion."
date:   2019-03-16
image:  Esteganografia.png
tags:   [Estegoanalisis, Esteganografia, Herramientas Utiles]
---

En el dia de hoy vamos aprender a como ocultar informacion privada en una imagen por medio de esteganografia.  
La cual es una tecnica que consiste en ocultar texto o cualquier otra informacion dentro de un archivo, con la intension de que no se perciba a simple vista y pase inadvertido dicho contenido oculto.  
Para extraer dicho contenido usualmente se debe usar una clave de acceso que se establece al inicio del proceso de incorporacion del mensaje esteganografico.  

Sabiendo lo anterior, vamos a proceder con la instalacion de la herramienta que nos ayudara para la leccion de hoy.  

{% highlight shell %}
sudo apt-get install steghide
{% endhighlight %}  

Una vez finalice la instalacion vamos a iniciar observando las opciones que nos brinda *steghide* con el siguiente comando:  

{% highlight shell %}
steghide --help
{% endhighlight %}  

Luego de ver todos los parametros que podemos incluir y usar con esta herramienta, a continuacion voy a proceder a ocultar un *archivo.txt* en una foto.  

<script width="640" height="360" id="asciicast-234703" src="https://asciinema.org/a/234703.js" async>
</script>

Si abrimos la imagen podemos identificar que a simple vista no hay ningun cambio o algo que muestre indicio de ser una foto con un mensaje esteganografico incorporado.  

<figure>
  <img src="{{site.baseurl}}/img/PhotoSteg.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/PhotoSteg.png" title="Imagen con contenido oculto">Imagen con contenido oculto</a>.
  </figcaption>
</figure>

Asi de facil es ocultar informacion con *steghide*, vamos a continuar extrayendo la informacion oculta que hemos embebido anteriormente en el archivo "photo.jpg"; para ello realizamos lo siguiente:  

<script width="640" height="360" id="asciicast-234756" src="https://asciinema.org/a/234756.js" async>
</script>

Excelente, hemos aprendido a extraer informacion oculta de una archivo JPG, Ahora vamos a aprender a identificar la presencia de mensajes ocultos o el uso de esteganografia en archivos.  
Esto se logra por medio de estegoanalisis, Debido a que la esteganografía es invasiva, es decir, deja huellas en el medio de transporte utilizado, las técnicas de estegoanálisis intentan detectar estos cambios.    
A continuacion, voy a dar a mostrar el uso de varias herramientas sobre los archivos previamente indicados para analizar sus diferencias.  

Inicialmente con la salida del comando **ls -la** podemos identificar los tamaños en bytes de cada archivo, Si comparamos "photo.jpg" contra "TruePhoto.jpg", podemos notar que el archivo que aloja la informacion oculta ocupa mas espacio que la foto original.  
Tener en cuenta que los archivos mencionados son un duplicado de una foto, solo que uno de ellos tiene la informacion que ocultamos.  

<figure>
  <img src="{{site.baseurl}}/img/Diference.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/Diference.png" title="Salida del comando ls -la">Diferencia entre peso del archivo original y del archivo con informacion oculta</a>.
  </figcaption>
</figure>

### Herramientas Iniciales
  - file (Verifica el tipo de archivo.)  

{% highlight shell %}
file photo.jpg  
file TruePhoto.jpg
{% endhighlight %}  

<figure>
  <img src="{{site.baseurl}}/img/usingfile.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/usingfile.png" title="Salida del comando file">Diferencia entre la salida arrojada por el comando file de cada archivo</a>.
  </figcaption>
</figure>

  - exiftool (Extraer metadatos de un archivo.)  

{% highlight shell %}
exiftool photo.jpg  
exiftool TruePhoto.jpg
{% endhighlight %}  

<figure>
  <img src="{{site.baseurl}}/img/usingexiftool.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/usingexiftool.png" title="Salida del comando exiftool">Analizando metadatos de archivos</a>.
  </figcaption>
</figure>

  - binwalk (Verificar si un archivo tiene otros archivos embebidos o incrustados.)  
  
{% highlight shell %}
binwalk photo.jpg  
binwalk TruePhoto.jpg
{% endhighlight %}

<figure>
  <img src="{{site.baseurl}}/img/usingbinwalk.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/usingbinwalk.png" title="Salida del comando binwalk">Diferencia entre la salida arrojada por el comando binwalk de cada archivo</a>.
  </figcaption>
</figure>

Hasta aqui dejare la leccion del dia, Hasta ahora hemos aprendido a ocultar y a realizar un analisis basico hacia archivos con contenido oculto; en la proxima ocasion estaremos aprendiendo mas de esteganografia con otras herramientas.

### Comandos usados:  
  - steghide embed -cf photo.jpg -ef Secreto.txt
  - steghide info photo.jpg
  - steghide extract -sf photo.jpg
  
### Descarga los archivos utilizados de este tutorial para practicar lo aprendido:  
  - [Archivos usados en la Leccion](https://drive.google.com/drive/folders/10zV_q8FjEBgmTwDJWunvlgJXnCvaKq-Z?usp=sharing)
  
### Lecturas adicionales  
  - [Wikipedia](https://es.wikipedia.org/wiki/Esteganograf%C3%ADa)
  
