---
layout: post
title: "Aprende a configurar un cliente VPN SSL en Linux"
excerpt: "Como usar forticlient en Debian (SSL VPN)"
image: "https://hackingprofessional.github.io/Security/images/fortinet.jpg"
tags: 
  - Fortinet
  - Linux
  - Redes
---

Hello Engineers!  
En el dia de hoy, vamos aprender a configurar forticlient utilizando Debian (Kali-Linux) para conectarnos por un tunel a una VPN SSL.  
Inicialmente descargamos nuestro cliente VPN SSL desde el siguiente enlace:    
[FortiClient](https://drive.google.com/file/d/1kUeoPt4jgJVtdDTDZtRSoGeRX2CwsxAB/view?usp=sharing)  

Luego ejecutamos el siguiente comando para realizar la instalacion de este:  

```zsh
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

```

Despues para inicializar forticlient, realizamos una busqueda en el sistema y lo ejecutamos:  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/SearchForticlient.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/Como-instalar-forticlient-para-linux" title="Buscando Forticlient en el sistema">Buscando Forticlient en el sistema</a>.
  </figcaption>
</figure>

Ahora procedemos a configurar la conexion, debemos ingresar los valores que corresponden a cada campo.  

```
------------------------------------
----Parametros a tener en cuenta----
------------------------------------
server = Se debe ingresar la IP de la Puerta de enlace remota
port = Se ingresa el puerto indicado.
user = Ingresar el usuario para realizar la autenticacion
password = Ingresar la contraseña para realizar la autenticacion
```

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ConfigForticlient.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/Como-instalar-forticlient-para-linux" title="Configurando Forticlient">Configurando Forticlient</a>.
  </figcaption>
</figure>

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/CertInvalidForticlient.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/Como-instalar-forticlient-para-linux" title="Error de certificado invalido">Error de certificado invalido</a>.
  </figcaption>
</figure>

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/ConnectForticlient.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/Como-instalar-forticlient-para-linux" title="Conexion exitosa utilizando FortiClient">Conexion exitosa utilizando FortiClient</a>.
  </figcaption>
</figure>

Listo, hemos logrado conectarnos al tunel VPN.  
A continuacion, realizo una peticion a un recurso que hace parte de la VPN.  

<figure>
  <img src="https://hackingprofessional.github.io/Security/images/OpenFortiSIEM.png" width="40%" height="55%">
	<figcaption>
    <a href="https://hackingprofessional.github.io/Security/Como-instalar-forticlient-para-linux" title="Accediendo al FortiSIEM conectado desde una VPN">Accediendo al FortiSIEM conectado desde una VPN</a>.
  </figcaption>
</figure>

<div class="profile-card-wrap">
	<input id="check" type="checkbox" class="check"><label for="check" class="toggle"> + </label>
	<div class="content" data-text="Hi there !">
		<div class="title">@igor_marty</div>
		<p>I'm a web designer from France who love Polymer-project, HTML/CSS and dogs. <br/> <br/> Teaching is what I like, Learning is what I love.</p>
	</div>
	<div class="link-info">
		<div class="social">
			<a class="link oc" href="https://openclassrooms.com/membres/igormarty" target="_blank"><i class="fa fa-graduation-cap"></i></a>
			<a class="link tw" href="https://twitter.com/igor_marty" target="_blank"><i class="fa fa-twitter"></i></a>
			<a class="link cp" href="https://codepen.io/IMarty/" target="_blank"><i class="fa fa-codepen"></i></a>
			<a class="link gh" href="https://github.com/IMarty" target="_blank"><i class="fa fa-github"></i></a>
			<a class="link li" href="https://fr.linkedin.com/in/igormarty" target="_blank"><i class="fa fa-linkedin"></i></a>
			<a class="link gp" href="https://plus.google.com/u/0/+IgorMarty" target="_blank"><i class="fa fa-google-plus"></i></a>
		</div>
		<div class="photo"></div>
	</div>
</div>
