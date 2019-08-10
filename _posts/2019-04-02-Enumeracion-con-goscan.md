---
layout: post
title:  "Enumeracion de un sistema con GoScan"
description: "Como automatizar la fase inicial de un pentesting"
date:   2019-04-02
image:  "img/goscan.png"
tags:   [Information Gathering, Enumeracion, Herramientas Utiles]
---

En la realización de un pentesting la fase de enumeración, es uno de los puntos más importantes ya que gran parte de los pasos posteriores se basan de los resultados de esta fase inicial.  
**GoScan** es un escáner de red interactivo, que cuenta con autocompletado, el cual proporciona abstracción y automatización sobre nmap.  

<figure>
  <img src="https://raw.githubusercontent.com/marco-lancini/goscan/master/.github/goscan_logo.png" >
	<figcaption>
    <a href="https://github.com/marco-lancini/goscan" title="GoScan Logo">Logo oficial de GoScan.</a>.
  </figcaption>
</figure>

Esta herramienta se usa para encontrar puertos y servicios abiertos en el objetivo de nuestras pruebas; GoScan es compatible con una gran variedad de caracteristicas principales de enumeración.    
En el dia de hoy, vamos aprender a como instalar y usar esta gran herramienta desde 0.   

Inicialmente debemos descargarla desde el repositorio oficial, para esto debemos ejecutar lo siguiente:   

{% highlight shell %}
# Linux (64bit)
$ wget https://github.com/marco-lancini/goscan/releases/download/v2.4/goscan_2.4_linux_amd64.zip
$ unzip goscan_2.4_linux_amd64.zip

# Linux (32bit)
$ wget https://github.com/marco-lancini/goscan/releases/download/v2.4/goscan_2.4_linux_386.zip
$ unzip goscan_2.4_linux_386.zip
{% endhighlight %}

Luego de finalizar la descarga, vamos a proceder otorgandole permisos de ejecucion y luego movemos nuestro binario a la siguiente ruta:  

{% highlight shell %}
$ chmod +x goscan
$ sudo mv ./goscan /usr/local/bin/goscan
{% endhighlight %}  

## Como usar GoScan

**Cargando Objetivos** 
:    * Agrege un objetivo a traves de la CLI  (debe ser un CIDR válido): <br> `load target SINGLE <IP/32>`
     * Agregar múltiples objetivos desde un archivo de texto: <br> `load target MULTI <path-to-file>`

**Descubrimiento de Host**
:    * Realizar un barrido de ping: <br> `sweep <TYPE> <TARGET>`
     * Para agregar un Host luego de realizar un descubrimiento de ping: 
         * Agregue un único host activo a través del CLI: <br> `load alive SINGLE <IP>`
         * Sube múltiples hosts desde un archivo de texto: <br> `load alive MULTI <path-to-file>` 

**Escaneo de Puertos**
:    * Realizar un escaneo de puerto: <br> `portscan <TYPE> <TARGET>`
     * Si desea subir los resultados nmap desde un archivo XML: <br> `load portscan <path-to-file>`

**Enumeración de Servicios**
:    * Dry Run (solo mostrar comandos, sin realizar enumeracion): <br> `enumerate <TYPE> DRY <TARGET>`
     * Realizar la enumeración de los servicios detectados: <br> `enumerate <TYPE> <POLITE/AGGRESSIVE> <TARGET>`

**Escaneos especiales**
:    * *Testigo ocular* Tome capturas de pantalla de sitios web, servicios RDP y servidores abiertos VNC (SOLO KALI): `special eyewitness` (El script `EyeWitness.py` debe estar en la ruta del sistema.
     * Extraer información de dominio (Windows) de los datos de enumeración <br> `special domain <users/hosts/servers>`
     * *DNS*
         * Enumerar DNS (nmap, dnsrecon, dnsenum): <br> `special dns DISCOVERY <domain>` 
         * Bruteforce DNS: <br> `special dns BRUTEFORCE <domain>`
         * Reverse Bruteforce DNS: <br> `special dns BRUTEFORCE_REVERSE <domain> <base_IP>`

**Utilidades**
:    * Mostrar resultados: <br> `show <targets/hosts/ports>`
     * Importar archivo de configuración: <br> `set config_file <PATH>`
     * Cambie la carpeta de salida (por defecto `~/goscan`): <br> `set output_folder <PATH>` 
     * Modificar los switch's de nmap que trae por defecto:: <br> `set nmap_switches <SWEEP/TCP_FULL/TCP_STANDARD/TCP_VULN/UDP_STANDARD> <SWITCHES>`
     * Modificar las listas de palabras por defecto: <br> `set_wordlists <FINGER_USER/FTP_USER/...> <PATH>`

## Usando GoScan

<figure>
  <img src="https://raw.githubusercontent.com/marco-lancini/goscan/master/.github/demo.gif">
	<figcaption>
    <a href="https://raw.githubusercontent.com/marco-lancini/goscan/master/.github/demo.gif" title="DEMO">DEMO</a>.
  </figcaption>
</figure>

El repositorio oficial de esta maravillosa herramienta es  [GoScan](https://github.com/marco-lancini/goscan/)  
