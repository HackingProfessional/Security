---
layout: post
title:  "Realizando auditorias de seguridad sobre AWS con prowler"
description: "Aprende a realizar auditorias sobre entornos cloud de AWS"
date:   2020-11-22
image:  "img/AWS-HACKED.jpg"
tags:   [Cloud, AWS, Pentesting, Security]
---

Hello Hackers!  
En el presente articulo, estaremos aprendiendo a utilizar una herramienta muy util para realizar auditorias de seguridad sobre un entorno cloud de AWS.  
Para ello estaremos usando una herramienta de seguridad llamada **prowler**.
La cual sirve para realizar evaluaciones teniendo en cuenta las mejores prácticas de seguridad sobre AWS.  

Todos esto basandose en los controles del [CIS](https://d0.awsstatic.com/whitepapers/compliance/AWS_CIS_Foundations_Benchmark.pdf) y más de 100 verificaciones adicionales que ayudan en GDPR, HIPAA, PCI-DSS, ISO-27001, FFIEC, SOC2 y otros.  

El CIS evalua aspectos de seguridad y estos se pueden dividir en 4 controles de seguridad, los cuales son:  

 - Identity and Access Management
 - Logging
 - Monitoring
 - Networking

*Prowler se ha escrito en bash con AWS-CLI y funciona en Linux y OSX.*  

# Instalando Prowler
Como requisito debemos tener instalado aws-cli para ello podemos ejecutar el siguiente comando:  

{% highlight bash %}
pip install awscli detect-secrets
{% endhighlight %} 

AWS-CLI también se puede instalar usando "brew", "apt", "yum" o manualmente desde https://aws.amazon.com/cli/

Por otro lado, tambien es necesario instalar el siguiente paquete.  

{% highlight bash %}
sudo apt install jq
{% endhighlight %} 

Luego de lo anterior, ya podemos proceder con la descarga del prowler.  

{% highlight bash %}
git clone https://github.com/toniblyx/prowler
cd prowler
{% endhighlight %} 

El siguiente paso es autenticarnos sobre aws-cli.  

{% highlight bash %}
aws configure

export AWS_ACCESS_KEY_ID="ASXXXXXXX"
export AWS_SECRET_ACCESS_KEY="XXXXXXXXX"
export AWS_SESSION_TOKEN="XXXXXXXXX"
{% endhighlight %} 


## Nota
Para poder auditar de manera exitosa toda la infraestructura es necesario tener acceso a un usuario con permisos elevados o que tengan las siguientes politicas administradas por AWS.

{% highlight bash %}
arn:aws:iam::aws:policy/SecurityAudit
arn:aws:iam::aws:policy/job-function/ViewOnlyAccess
{% endhighlight %} 

# Usando Prowler
Luego de estar autenticado sobre AWS-CLI, ejecutamos prowler de la siguiente manera:  

{% highlight bash %}
./prowler
{% endhighlight %} 

Otra manera de ejecutarlo, es por medio de un contenedor de docker:  

{% highlight bash %}
docker run -ti --rm --name prowler --env AWS_ACCESS_KEY_ID --env AWS_SECRET_ACCESS_KEY --env AWS_SESSION_TOKEN toniblyx/prowler:latest
{% endhighlight %} 

Luego de la ejecuccion previa, se iniciara de manera automatica prowler y empezara auditar la infraestructura.  


<figure>
  <img src="{{site.baseurl}}/img/prowler.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/prowler.png" title="Ejecutando Prowler">Ejecutando Prowler</a>.
  </figcaption>
</figure> 

Hay una gran variedad de parametros que podemos usar sobre esta herramienta.
Si deseas obtener mas informacion relacionada te invito leer [la documentacion oficial de este repositorio](https://github.com/toniblyx/prowler).  

