---
layout: post
title: "Como extraer los metadatos de un archivo"
description: "Obteniendo informacion oculta de los archivos"
date: 2019-08-19
image:  "img/Metadata.png"
tags: ["Metadatos","Enumeracion"]
---

Hello Engineers!  
En el dia de hoy, vamos aprender a extraer los metadatos de nuestros ficheros desde Kali-Linux usando la herramienta [ExifTool](https://github.com/exiftool/exiftool).  

## Conceptos Basicos  
**¿Que son los MetaDatos?**  
Los metadatos son datos que proporcionan información sobre otros datos.  
En otras palabras, Es información que describe el contenido de algun fichero.  
Los metadatos que pueden ser extraidos de algun archivo pueden ser características, calidad, información del creador, hora y fecha de creación, propósito de creación, procedimiento de creación, ubicación geográfica, software utilizado, entre otros.  

## Instalacion de ExifTool en Kali-Linux
Para instalar la herramienta que nos permitira leer los metadatos de algun archivo se debe ejecutar el siguiente comando:  

{% highlight zsh %}
╭─[/home/gerh/Escritorio/Prometheus]─[root@Raeghal]─[0]─[1095]
╰─[:)] # apt-get install exiftool
{% endhighlight %}

## Uso de ExifTool
Para extraer y visualizar los metadatos de algun archivo se debe ejecutar la herramienta previamente instalada seguido del nombre del archivo a analizar.

{% highlight zsh %}
╭─[/home/gerh/Escritorio/Prometheus/Blog/News/Imgs/Metadata]─[root@Raeghal]─[0]─[1099]
╰─[:)] # exiftool Windows\ Event\ Forwarding.docx
ExifTool Version Number         : 11.16
File Name                       : Windows Event Forwarding.docx
Directory                       : .
File Size                       : 14 kB
File Modification Date/Time     : 2019:06:09 02:43:47-05:00
File Access Date/Time           : 2019:06:09 02:43:47-05:00
File Inode Change Date/Time     : 2019:06:09 02:43:47-05:00
File Permissions                : rw-r--r--
File Type                       : DOCX
File Type Extension             : docx
MIME Type                       : application/vnd.openxmlformats-officedocument.wordprocessingml.document
Zip Required Version            : 20
Zip Bit Flag                    : 0x0006
Zip Compression                 : Deflated
Zip Modify Date                 : 1980:01:01 00:00:00
Zip CRC                         : 0x82872409
Zip Compressed Size             : 385
Zip Uncompressed Size           : 1422
Zip File Name                   : [Content_Types].xml
Creator                         : gerh@megabank.com
Revision Number                 : 4
Create Date                     : 2017:10:31 18:42:00Z
Modify Date                     : 2017:10:31 18:51:00Z
Template                        : Normal.dotm
Total Edit Time                 : 5 minutes
Pages                           : 2
Words                           : 299
Characters                      : 1709
Application                     : Microsoft Office Word
Doc Security                    : None
Lines                           : 14
Paragraphs                      : 4
Scale Crop                      : No
Heading Pairs                   : Title, 1
Titles Of Parts                 : 
Company                         : Mega-Bank
Links Up To Date                : No
Characters With Spaces          : 2004
Shared Doc                      : No
Hyperlinks Changed              : No
App Version                     : 14.0000
{% endhighlight %}

Para esta demostracion se uso el siguiente [archivo](), entre todo el resultado previamente mostrado se logro identificar la siguiente informacion delicada:

{% highlight markdown %}
Creator                         : gerh@megabank.com
Application                     : Microsoft Office Word
Total Edit Time                 : 5-minutes
Create Date                     : 2017:10:31 18:42:00Z
Modify Date                     : 2017:10:31 18:51:00Z
{% endhighlight %}

Esta informacion obtenida podria ser usada para realizar ataques de phising, si deseas conocer el riesgo de mantener estos metadatos, te invito a leer la siguiente [publicacion](https://hackingprofessional.github.io/Security/how-to-hack-windows-8-with-Phishing/) donde realizo una demostracion sobre un entorno vulnerable.  
[Hackeando Reel]()

## Cómo prevenir la divulgación de información de metadatos
Asegúrese de que todos los documentos compartidos con partes externas estén desinfectados y no incluyan información que pueda ser útil para los atacantes, tales como: números de versión de software, detalles del autor, incluyendo su nombre de usuario, números de teléfono directos y direcciones de correo electrónico individuales. Si se deben adjuntar metadatos a los documentos, debe limitarse a información genérica para reducir la superficie de ataque.  

El popular software Microsoft Office y LibreOffice permite a los usuarios eliminar algunos de los metadatos con bastante facilidad. Sin embargo, incluso cuando se tiene cuidado, siempre habrá algunos metadatos adjuntos.   

En conclusión, cuanto menos digan los metadatos sobre el documento, más difícil es para los delincuentes cibernéticos utilizar la información extraída para propósitos malintencionados.  
