---
layout: post
title:  "Como compartir archivos a través de HTTP"
description: "Aprende a compartir rápidamente archivos y carpetas a través de HTTP en Linux"
date:   2019-04-02
image:  "img/shareHttpLinux.png"
tags:   [Aprende Python, Aprende Ruby, Aprende NodeJS]
---

Durante el proceso para comprometer totalmente un servidor victima, es muy probable que tengamos que subir a este exploits o scripts que automaticen ciertos procesos.    
En el dia de hoy, vamos aprender diferentes métodos para servir un solo archivo o un directorio completo con otros sistemas en su red de área local a través de un navegador web.  

## Como Servir archivos y carpetas sobre HTTP en Linux   
### Python   

{% highlight shell %}
#Python 3.x
python3 -m http.server

#Python 2.x
python -m SimpleHTTPServer 6969
{% endhighlight %}

### Ruby  

{% highlight ruby %}
ruby -run -e httpd . -p 6969
{% endhighlight %}

### NodeJs  
Para levantar el servidor HTTP con NodeJs debemos instalar la siguiente libreria:     

{% highlight shell %}
npm install http-server -g
{% endhighlight %}

Luego corremos lo siguiente:  

{% highlight js %}
http-server -p 6969
{% endhighlight %}

### PHP  

{% highlight php %}
php -S localhost:6969 -t .
{% endhighlight %}

