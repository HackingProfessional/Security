---
layout: post
title:  "Aprende a configurar un cliente VPN SSL en Linux"
description: "Como usar forticlient en Debian (SSL VPN)"
date:   2019-04-15
image:  "img/fortinet.jpg"
tags:   [Linux, Fortinet, Herramientas Utiles]
---

Hello Engineers!  
En el dia de hoy, vamos aprender a configurar forticlient utilizando Debian (Kali-Linux) para conectarnos por un tunel a una VPN SSL.  
Inicialmente descargamos nuestro cliente VPN SSL desde el siguiente enlace:    
[FortiClient](https://drive.google.com/file/d/1kUeoPt4jgJVtdDTDZtRSoGeRX2CwsxAB/view?usp=sharing)  

Luego ejecutamos el siguiente comando para realizar la instalacion de este:  

{% highlight zsh %}
┌─[root@Kratos] - [~/Desktop/Luna/Tools] - [Wed Apr 24, 16:18]
└─[$]> apt-get install ./forticlient-sslvpn_4.4.2333-1_amd64.deb
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting 'forticlient-sslvpn' instead of './forticlient-sslvpn_4.4.2333-1_amd64.deb'
The following NEW packages will be installed:
  forticlient-sslvpn
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/4,158 kB of archives.
After this operation, 20.2 MB of additional disk space will be used.
Get:1 /root/Desktop/Luna/Tools/forticlient-sslvpn_4.4.2333-1_amd64.deb forticlient-sslvpn amd64 4.4.2333-1 [4,158 kB]
Selecting previously unselected package forticlient-sslvpn.
(Reading database ... 444359 files and directories currently installed.)
Preparing to unpack .../forticlient-sslvpn_4.4.2333-1_amd64.deb ...
Unpacking forticlient-sslvpn (4.4.2333-1) ...
Setting up forticlient-sslvpn (4.4.2333-1) ...
Processing triggers for mime-support (3.62) ...
Processing triggers for gnome-menus (3.31.4-3) ...
Processing triggers for desktop-file-utils (0.23-4) ...

{% endhighlight %}  

Despues para inicializar forticlient, realizamos una busqueda en el sistema y lo ejecutamos:  

<figure>
  <img src="{{site.baseurl}}/img/SearchForticlient.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/SearchForticlient.png" title="Buscando Forticlient en el sistema">Buscando Forticlient en el sistema</a>.
  </figcaption>
</figure>

Ahora procedemos a configurar la conexion, debemos ingresar los valores que corresponden a cada campo.  

{% highlight bash %}  
#------------------------------------
#----Parametros a tener en cuenta----
#------------------------------------
server = Se debe ingresar la IP de la Puerta de enlace remota
port = Se ingresa el puerto indicado.
user = Ingresar el usuario para realizar la autenticacion
password = Ingresar la contraseña para realizar la autenticacion
{% endhighlight %}  

<figure>
  <img src="{{site.baseurl}}/img/ConfigForticlient.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/ConfigForticlient.png" title="Configurando Forticlient">Configurando Forticlient</a>.
  </figcaption>
</figure>

<figure>
  <img src="{{site.baseurl}}/img/CertInvalidForticlient.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/CertInvalidForticlient.png" title="Error de certificado invalido">Error de certificado invalido</a>.
  </figcaption>
</figure>

<figure>
  <img src="{{site.baseurl}}/img/ConnectForticlient.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/ConnectForticlient.png" title="Conexion exitosa utilizando FortiClient">Conexion exitosa utilizando FortiClient</a>.
  </figcaption>
</figure>

Listo, hemos logrado conectarnos al tunel VPN.  
A continuacion, realizo una peticion a un recurso que hace parte de la VPN.  

<figure>
  <img src="{{site.baseurl}}/img/OpenFortiSIEM.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/OpenFortiSIEM.png" title="Accediendo al FortiSIEM conectado desde una VPN">Accediendo al FortiSIEM conectado desde una VPN</a>.
  </figcaption>
</figure>

