---
layout: post
title: "Como instalar ElasticSearch - Logstash y Kibana (ELK)"
description: Curso completo de Elastic Stack I
date:   2019-09-21
image:  "img/ELK-1.jpg"
tags: ["Log Analytics", "Monitoring System", "Elastic Stack"]
---

Hello Hackers!!  
Elastic Stack, anteriormente conocido como ELK Stack, Es una colección de software de código abierto producido por Elastic que le permite buscar, analizar y visualizar registros generados desde cualquier fuente en cualquier formato, una práctica conocida como registro centralizado.  
El registro centralizado puede ser muy útil al intentar identificar problemas con sus servidores o aplicaciones, ya que le permite buscar a través de todos sus registros en un solo lugar.  

Elastic Stack tiene cuatro componentes principales:  
**Elasticsearch:** Es un motor de búsqueda RESTfull distribuido que almacena todos los datos recopilados.  
**Beats:** Es una familia de agentes muy ligeros que recolectan datos en equipos o servidores y los envían a Elasticsearch.    
**Logstash:** Es el componente que se encarga del procesamiento de datos de Elastic Stack que envía datos entrantes a Elasticsearch.   
**Kibana:** Es una interfaz web para buscar y visualizar registros.

Para este tutorial, vamos a aprender a cómo instalar todos los componentes de Elastic Stack.  

# Instalación y configuración de ElasticSearch
Para comenzar, ejecutemos el siguiente comando para importar la clave pública GPG de Elasticsearch en APT:  

{% highlight bash %}
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
{% endhighlight %}

A continuación, vamos a crear una lista de fuentes de paquetes para Elastic y debemos almacenarlo en el directorio sources.list.d, luego de lo anterior APT podra usar estas fuentes para la instalación que deseemos realizar.  

{% highlight bash %}
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
{% endhighlight %}

A continuación, actualizamos nuestra listas de paquetes y procedemos con la instalacion de ElasticSearch:  

{% highlight bash %}
sudo apt-get update && sudo apt-get install elasticsearch
{% endhighlight %}

> **Nota:** El archivo de configuración principal de Elasticsearch esta alojado en, **/etc/elasticsearch/elasticsearch.yml**. este tiene el formato YAML, lo que significa que la sangría es muy importante. Debes asegurarte de no agregar espacios adicionales si requieres editar este archivo.  
Una vez que haya finalizado la instalacion de Elasticsearch, vamos a iniciar el servicio.  

{% highlight bash %}
sudo systemctl start elasticsearch
{% endhighlight %}

Para validar que todo este funcionando correctamente, podemos realizar una peticion GET al API de Elastic.   

{% highlight bash %}
curl -X GET "localhost:9200"
{% endhighlight %}

Al ejecutar lo anterior, deberiamos haber obtenido como respuesta información básica sobre su nodo local.  
Algo similar a esto:  

{% highlight js %}
{
  "name" : "ZlJ0k2h",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "beJf9oPSTbecP7_i8pRVCw",
  "version" : {
    "number" : "6.4.2",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "04711c2",
    "build_date" : "2018-09-26T13:34:09.098244Z",
    "build_snapshot" : false,
    "lucene_version" : "7.4.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
{% endhighlight %}

Listo, hemos finalizado con la instalacion de ElasticSearch.  
Vamos a continuar con la instalacion de Kibana, el siguiente componente de Elastic Stack.  

# Instalación y configuración de Kibana
Según la documentación oficial , se debe instalar Kibana solo después de instalar Elasticsearch.  
La instalación en este orden garantiza que los componentes de los que depende cada producto estén correctamente configurados.  
Como ya ha agregado la fuente del paquete Elastic, podemos instalar kibana sin ningun problema de la siguiente forma:  

{% highlight bash %}
sudo apt install kibana
{% endhighlight %}

Luego de la instalacion, vamos a iniciar el servicio Kibana:  

{% highlight bash %}
sudo systemctl start kibana
{% endhighlight %}

Para validar si Kibana inicio correctamente, podemos abrir un navegador y dirigirnos a la siguiente URL:  

{% highlight bash %}
http://localhost:5601
{% endhighlight %}

Al ingresar, deberiamos visualizar algo parecido a lo siguiente:  

<figure>
  <img src="{{site.baseurl}}/img/kibana-start.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/kibana-start.png" title="Validando el funcionamiento de Kibana">Validando el funcionamiento de Kibana</a>.
  </figcaption>
</figure>

Vamos a continuar con la instalacion de Logstash.  

# Instalación y configuración de Logstash
Aunque es posible que Beats envíe datos directamente a la base de datos de Elasticsearch, se recomienda utilizar Logstash para procesar los datos. Esto nos permitirá recopilar datos de diferentes fuentes, transformarlos en un formato común y exportarlos a otra base de datos.  
Para instalar Logstash, ejecutamos el siguiente comando:  

{% highlight bash %}
sudo apt install logstash
{% endhighlight %}

Luego de finalizar la instalacion, vamos a crear un archivo de configuracion para realizar un test de funcionamiento sobre losgstash.  
Nos vamos a la siguiente ruta para almacenar nuestro archivo de configuracion:  

{% highlight bash %}
cd /etc/logstash/conf.d
{% endhighlight %}

El archivo tendra como nombre **example.yml**, el contenido del archivo sera el siguiente:  

{% highlight js %}
input{
  stdin{}
}

output{
  stdout{
    codec => json_lines
  }
}
{% endhighlight %}

El procesamiento se organiza en uno o más pipelines. En cada pipeline, uno o más plugins de entrada reciben o recopilan datos que luego se colocan en una cola interna. De manera predeterminada, esta es pequeña y se almacena en la memoria, pero puede configurarse para ser más amplia y permanecer en el disco para mejorar la confiabilidad y la persistencia.  

En resumen, la configuracion anterior nos va a permitir hacer una prueba de funcionamiento rapida; en la que estamos especificando que por medio del plugin stdin cualquier valor introducido en la consola va a generar un log en formato JSON.  
Para ejecutar y realizar el test, debemos iniciar logstash con el parametro -f para indicarle el archivo recien creado:  

{% highlight yml %}
cd /usr/share/logstash/bin
./logstash --path.settings /etc/logstash/ -f/etc/logstash/conf.d/text.yml
{% endhighlight %}

El resultado seria el siguiente:  

{% highlight bash %}
┌─[root@Horus] - [/usr/share/logstash/bin] - [vie oct 04, 10:22]
└─[$]> ./logstash --path.settings /etc/logstash/ -f /etc/logstash/conf.d/text.yml          
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
Sending Logstash logs to /usr/share/logstash/logs which is now configured via log4j2.properties
[2019-10-04T10:24:07,400][INFO ][logstash.runner          ] Starting Logstash {"logstash.version"=>"7.4.0"}
[2019-10-04T10:24:13,705][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
{% endhighlight %}

Si escribimos cualquier cosa dentro de la consola en la que acabamos de iniciar el servicio, nos deberia devolver un log formateado en JSON: 

{% highlight js %}
Gerh
{"host":"Horus","@version":"1","message":"Gerh","@timestamp":"2019-10-04T15:25:42.078Z"}
{% endhighlight %}

Con la prueba anterior, logramos validar que nuestro logstash ya se encuentra funcional.  
En la proxima leccion, vamos aprender a configurar los beats. 
