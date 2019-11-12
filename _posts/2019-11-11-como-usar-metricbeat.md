---
layout: post
title: "Como monitorear el rendimiento de un equipo con Elasticsearh"
description: Curso completo de Elastic Stack I
date:   2019-11-11
image:  "img/ELK-1.png"
tags: ["Log Analytics", "Monitoring System", "Elastic Stack"]
---

Hello Hackers!!  
En el [tutorial anterior](https://hackingprofessional.github.io/Security/como-instalar-Elasticsearch-logstash-kibana-ELK/), logramos aprender a instalar y configurar elasticsearch, kibana y logstash.  
A continuacion, vamos a implementar el uso del beat llamado Metricbeat.  

**Metricbeat**  
Metricbeat es un remitente (o agente) ligero que se utiliza para recopilar las métricas del sistema y las métricas de la aplicación y enviarlas al servidor de Elastic Stack (es decir, Elasticsearch ). Aquí las métricas del sistema se refieren a CPU, memoria, disco y estadísticas de red (IOPS) y las métricas de las aplicaciones significa monitorear y recopilar métricas de aplicaciones como Apache , NGINX , Docker , Kubernetes y Redis, etc.  
En este artículo demostraremos cómo instalar metricbeat en servidores Linux y luego cómo metricbeat envía datos al servidor Elastic Stack (es decir, Elasticsearch) y luego verificaremos desde la GUI de Kibana si los datos de las métricas son visibles o no.  

# Instalación y configuración de Metricbeat
Para descargar e instalar Metricbeat, use los comandos que funcionan segun su sistema:  

## DEB
{% highlight bash %}
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.2-amd64.deb
sudo dpkg -i metricbeat-7.4.2-amd64.deb
{% endhighlight %}

## RPM
{% highlight bash %}
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.2-x86_64.rpm
sudo rpm -vi metricbeat-7.4.2-x86_64.rpm
{% endhighlight %}

## MAC
{% highlight bash %}
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.2-darwin-x86_64.tar.gz
tar xzvf metricbeat-7.4.2-darwin-x86_64.tar.gz
{% endhighlight %}

## Windows
Descarga desde el siguiente [link de descarga](https://www.elastic.co/downloads/beats/metricbeat)  
Y luego ejecutar lo siguiente:  

{% highlight powershell %}
PS > cd 'C:\Program Files\Metricbeat'
PS C:\Program Files\Metricbeat> .\install-service-metricbeat.ps1
{% endhighlight %}

# Configurando Metricbeat
Para configurar Metricbeat, debemos editar el archivo de configuración.  
Para **rpm** y **deb**, normalmente el archivo de configuración se encuentra en la ruta **/etc/metricbeat/metricbeat.yml**.
Cabe resaltar, que normalmente en la carpeta donde se instalo Metricbeat se encuentra un archivo de configuración de ejemplo completo **/etc/metricbeat/metricbeat.reference.yml**.
Para **mac** y **win**, busque en el archivo de configuracion en la carpeta donde se encuentra Metricbeat.  
El archivo de configuración de Metricbeat usa YAML para su sintaxis.  

## Habilitando los modulos a iniciar
El directorio modules.d contiene configuraciones predeterminadas para todos los módulos disponibles en Metricbeat.  

Por ejemplo, para habilitar las configuraciones apache y mysql podemos usar el siguiente comando:  

{% highlight bash %}
cd /usr/share/metricbeat/bin
./metricbeat modules enable apache mysql
{% endhighlight %}

Para ver una lista de módulos habilitados y deshabilitados, podemos ejecutar:  

{% highlight bash %}
./metricbeat modules list
{% endhighlight %}

Teniendo en cuenta lo anterior, vamos a proceder iniciando el servicio para ya empezar a monitorear el rendimiento de nuestro equipo.

# Monitoreando con Metricbeat
Metricbeat viene empaquetado con paneles de Kibana de ejemplo, visualizaciones y búsquedas para visualizar datos de Metricbeat en Kibana.  
El comando que se muestra aquí carga los paneles del paquete Metricbeat.  

{% highlight zsh %}
┌─[root@Horus] - [/Prometheus/metricbeat-7.4.2-linux-x86_64] - [mar nov 12, 05:03]
└─[$]> ./metricbeat setup --dashboards -c metricbeat.yml 
Loading dashboards (Kibana must be running and reachable)
Loaded dashboards
{% endhighlight %}

La configuracion que usaremos para el archivo **metricbeat.yml**, sera la que viene por defecto:  

{% highlight yml %}
###################### Metricbeat Configuration Example #######################
#==========================  Modules configuration ============================

metricbeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: false

  # Period on which files under path should be checked for changes
  #reload.period: 10s

#==================== Elasticsearch template setting ==========================

setup.template.settings:
  index.number_of_shards: 1
  index.codec: best_compression
  #_source.enabled: false

#============================== Dashboards =====================================
# These settings control loading the sample dashboards to the Kibana index. Loading
# the dashboards is disabled by default and can be enabled either by setting the
# options here or by using the `setup` command.
setup.dashboards.enabled: true

# The URL from where to download the dashboards archive. By default this URL
# has a value which is computed based on the Beat name and version. For released
# versions, this URL points to the dashboard archive on the artifacts.elastic.co
# website.
#setup.dashboards.url:

#============================== Kibana =====================================

# Starting with Beats version 6.0.0, the dashboards are loaded via the Kibana API.
# This requires a Kibana endpoint configuration.
setup.kibana:

  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  #host: "localhost:5601"

  # Kibana Space ID
  # ID of the Kibana Space into which the dashboards should be loaded. By default,
  # the Default Space will be used.
  #space.id:

#================================ Outputs =====================================

# Configure what output to use when sending the data collected by the beat.

#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["localhost:9200"]

  # Optional protocol and basic auth credentials.
  #protocol: "https"
  #username: "elastic"
  #password: "changeme"

#================================ Processors =====================================

# Configure processors to enhance or manipulate events generated by the beat.

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~

{% endhighlight %}

Luego del comando anterior, ya podemos inicializar Metricbeat.  


{% highlight yml %}
┌─[root@Horus] - [/Prometheus/metricbeat-7.4.2-linux-x86_64] - [mar nov 12, 05:11]
└─[$]> ./metricbeat -e -c metricbeat.yml 
2019-11-12T05:11:44.853-0500    INFO    instance/beat.go:607    Home path: [/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64] Config path: [/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64] Data path: [/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64/data] Logs path: [/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64/logs]
2019-11-12T05:11:44.854-0500    INFO    instance/beat.go:615    Beat ID: 70608630-6085-4abe-94e4-54f7e4b8b4a1
2019-11-12T05:11:44.856-0500    INFO    [seccomp]       seccomp/seccomp.go:124  Syscall filter successfully installed
2019-11-12T05:11:44.856-0500    INFO    [beat]  instance/beat.go:903    Beat info       {"system_info": {"beat": {"path": {"config": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64", "data": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64/data", "home": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64", "logs": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64/logs"}, "type": "metricbeat", "uuid": "70608630-6085-4abe-94e4-54f7e4b8b4a1"}}}
2019-11-12T05:11:44.856-0500    INFO    [beat]  instance/beat.go:912    Build info      {"system_info": {"build": {"commit": "15075156388b44390301f070960fd8aeac1c9712", "libbeat": "7.4.2", "time": "2019-10-28T19:49:40.000Z", "version": "7.4.2"}}}
2019-11-12T05:11:44.856-0500    INFO    [beat]  instance/beat.go:915    Go runtime info {"system_info": {"go": {"os":"linux","arch":"amd64","max_procs":4,"version":"go1.12.9"}}}
2019-11-12T05:11:44.857-0500    INFO    [beat]  instance/beat.go:919    Host info       {"system_info": {"host": {"architecture":"x86_64","boot_time":"2019-11-12T04:12:43-05:00","containerized":false,"name":"Horus","ip":["127.0.0.1/8","::1/128","192.168.1.111/24","fe80::ae50:c940:69fb:b506/64","192.168.34.1/24","fe80::250:56ff:fec0:1/64","192.168.138.1/24","fe80::250:56ff:fec0:8/64"],"kernel_version":"5.3.0-kali1-amd64","mac":["4c:ed:fb:00:af:6d","70:c9:4e:16:6b:06","00:50:56:c0:00:01","00:50:56:c0:00:08"],"os":{"family":"","platform":"kali","name":"Kali GNU/Linux","version":"2019.4","major":2019,"minor":4,"patch":0,"codename":"kali-rolling"},"timezone":"-05","timezone_offset_sec":-18000,"id":"8a699c239cae4baaabd543f381089f57"}}}
2019-11-12T05:11:44.858-0500    INFO    [beat]  instance/beat.go:948    Process info    {"system_info": {"process": {"capabilities": {"inheritable":null,"permitted":["chown","dac_override","dac_read_search","fowner","fsetid","kill","setgid","setuid","setpcap","linux_immutable","net_bind_service","net_broadcast","net_admin","net_raw","ipc_lock","ipc_owner","sys_module","sys_rawio","sys_chroot","sys_ptrace","sys_pacct","sys_admin","sys_boot","sys_nice","sys_resource","sys_time","sys_tty_config","mknod","lease","audit_write","audit_control","setfcap","mac_override","mac_admin","syslog","wake_alarm","block_suspend","audit_read"],"effective":["chown","dac_override","dac_read_search","fowner","fsetid","kill","setgid","setuid","setpcap","linux_immutable","net_bind_service","net_broadcast","net_admin","net_raw","ipc_lock","ipc_owner","sys_module","sys_rawio","sys_chroot","sys_ptrace","sys_pacct","sys_admin","sys_boot","sys_nice","sys_resource","sys_time","sys_tty_config","mknod","lease","audit_write","audit_control","setfcap","mac_override","mac_admin","syslog","wake_alarm","block_suspend","audit_read"],"bounding":["chown","dac_override","dac_read_search","fowner","fsetid","kill","setgid","setuid","setpcap","linux_immutable","net_bind_service","net_broadcast","net_admin","net_raw","ipc_lock","ipc_owner","sys_module","sys_rawio","sys_chroot","sys_ptrace","sys_pacct","sys_admin","sys_boot","sys_nice","sys_resource","sys_time","sys_tty_config","mknod","lease","audit_write","audit_control","setfcap","mac_override","mac_admin","syslog","wake_alarm","block_suspend","audit_read"],"ambient":null}, "cwd": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64", "exe": "/datos/gerh/Escritorio/Prometheus/Tests/metricbeat-7.4.2-linux-x86_64/metricbeat", "name": "metricbeat", "pid": 4612, "ppid": 3770, "seccomp": {"mode":"filter","no_new_privs":true}, "start_time": "2019-11-12T05:11:43.570-0500"}}}
2019-11-12T05:11:44.858-0500    INFO    instance/beat.go:292    Setup Beat: metricbeat; Version: 7.4.2
2019-11-12T05:11:44.858-0500    INFO    [index-management]      idxmgmt/std.go:178      Set output.elasticsearch.index to 'metricbeat-7.4.2' as ILM is enabled.
2019-11-12T05:11:44.859-0500    INFO    elasticsearch/client.go:170     Elasticsearch url: http://localhost:9200
2019-11-12T05:11:44.859-0500    INFO    [publisher]     pipeline/module.go:97   Beat name: Horus
2019-11-12T05:11:44.861-0500    INFO    [monitoring]    log/log.go:118  Starting metrics logging every 30s
2019-11-12T05:11:44.861-0500    INFO    kibana/client.go:117    Kibana url: http://localhost:5601
2019-11-12T05:11:45.265-0500    INFO    kibana/client.go:117    Kibana url: http://localhost:5601

{% endhighlight %}

Luego de iniciar el servicio, debemos agregar el patron de indice desde kibana.  
Para ello podemos hacerlo de la siguiente manera:  

> **Nota:** Para visualizar y explorar datos en Kibana, debe crear un patrón de índice. Un patrón de índice le dice a Kibana qué índices de Elasticsearch contienen los datos con los que desea trabajar. Un patrón de índice puede coincidir con un solo índice, múltiples índices y un índice de resumen.

> **Nota:** Para crear un patron de indice, debemos dirigirnos a **Management -> Kibana -> Index Patterns**

<figure>
  <img src="{{site.baseurl}}/img/Kibana-PatronIndice.gif">
	<figcaption>
    <a href="{{site.baseurl}}/img/Kibana-PatronIndice.gif" title="Creando un patron de indice desde Kibana">Creando un patron de indice desde Kibana</a>.
  </figcaption>
</figure>

Luego de la creacion del patron de indice, podemos dirigirnos a **Discover**.  
En este apartado, lograremos apreciar la recepcion de todos los logs.  

<figure>
  <img src="{{site.baseurl}}/img/Kibana-Discover.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/Kibana-Discover.png" title="Analisis de logs con Kibana - Apartado Discover">Analisis de logs con Kibana - Apartado Discover</a>.
  </figcaption>
</figure>

Si ingresamos al apartado de **Dashboards**, podremos apreciar graficas relacionadas a las metricas obtenidas por medio de Metricbeat.

> **Nota:** Para poder encontrar estas dashboards, debemos buscarlas con el nombre **Metricbeat**.

<figure>
  <img src="{{site.baseurl}}/img/Kibana-Dashboard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/Kibana-Dashboard.png" title="Analisis de logs con Kibana - Apartado Dashboards">Analisis de logs con Kibana - Apartado Dashboards</a>.
  </figcaption>
</figure>

Con este dashboard, puedes detectar problemas y garantizar la optimización de cada instancia para el uso. Por ejemplo, si observas un uso continuamente alto de CPU y problemas de rendimiento en la misma instancia, posiblemente esto indique que CPU es el cuello de botella y que esta instancia necesita más potencia de CPU. Además, si observas una instancia con bajo uso de CPU durante mucho tiempo, esto significa que la instancia está sobredimensionada en CPU, y posiblemente pueda reducirse o consolidarse con otra instancia.  

<figure>
  <img src="{{site.baseurl}}/img/Kibana-Dashboard-Detallado.gif">
	<figcaption>
    <a href="{{site.baseurl}}/img/Kibana-Dashboard-Detallado.gif" title="Analisis detallado con Kibana - Apartado Dashboards">Analisis detallado con Kibana - Apartado Dashboards</a>.
  </figcaption>
</figure>

Si deseas revisar la documentacion oficial, te invito a revisar el siguiente [link](https://www.elastic.co/products/beats/metricbeat).  
En la proxima leccion, vamos aprender a monitorear transacciones sinteticas con Heartbeat.  

