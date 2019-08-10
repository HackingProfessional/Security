---
layout: post
title:  "Aprende NodeJS creando un bot en Telegram"
description: "Aprende NodeJS desde 0"
date:   2019-05-02
image:  "img/NodeJSbot.png"
tags:   [Aprende a programar, Aprende Nodejs, Creacion de bots]
---

Hello Programmers!!  
En este artículo, aprenderemos a crear un bot en telegram desde 0 con Node JS.

## Conceptos Basicos

**Node JS**, es un entorno en tiempo de ejecución multiplataforma, de código abierto, para la capa del servidor basado en el lenguaje de programación ECMAScript, asíncrono, con I/O de datos en una arquitectura orientada a eventos y basado en el motor V8 de Google.  
[Articulo completo](https://es.wikipedia.org/wiki/Node.js)  

### Instalando Node JS
La instalacion de Node JS es como la de cualquier otro software, inicialmente debemos descargar el instalador de la pagina principal.  
[NodeJS](https://nodejs.org/es/)  
Recomiendo descargar la versión LTS, ya que esta es la versión más estable.  
Una vez acabada la instalacion, vamos a crear nuestra carpeta para el proyecto.  

{% highlight zsh %}
╭─[~/Desktop/MyCodes]─[root@Arthorias]─[0]─[3457]
╰─[:)] # mkdir Kratos
{% endhighlight %}  

Para empezar a preparar nuestro proyecto ejecutamos el siguiente comando:  

{% highlight zsh %}
╭─[~/Desktop/MyCodes/Kratos]─[root@Arthorias]─[0]─[3457]
╰─[:)] # npm init
{% endhighlight %}    

## Que es el archivo package.json
Con este comando se crea un archivo llamado **package.json**. 
En este archivo, queda reflejada la configuración del proyecto de NodeJs tales como:
	- Nombre del proyecto.  
	- Autor.  
	- Version.  
	- Dependencias.  
	- Scripts.  
	- Repositorio Git.  

El contenido del archivo debería tener una estructura similar a esta:  

{% highlight js %}
{
   "name": "kratos",
   "version": "1.0.0",
   "description": "Bot de telegram en Node JS",
   "main": "index.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "author": "Gerh",
   "license": "MIT"
}
{% endhighlight %}  

Vamos a crear un script para la ejecucion de nuestro proyecto, para ello debemos modificar nuestro **package.json** de la siguiente manera:  

{% highlight js %}
{
   "name": "kratos",
   "version": "1.0.0",
   "description": "Bot de telegram en Node JS",
   "main": "index.js",
   "scripts": {
     "start": "node index.js"
   },
   "author": "Gerh",
   "license": "MIT"
}
{% endhighlight %}  

Una vez que acabemos de editar el archivo **package.json** procedemos a instalar la libreria de Telegram para nodejs.  
Para realizar la instalacion debemos ejecutar el siguiente comando:  

{% highlight zsh %}
╭─[~/Desktop/MyCodes/Kratos]─[root@Arthorias]─[0]─[3457]
╰─[:)] # npm install --save node-telegram-bot-api
+ node-telegram-bot-api@0.30.0
added 83 packages from 131 contributors in 7.511s
{% endhighlight %}  

Vamos a continuar creando nuestro **index.js**, aqui tendremos basicamente todo el codigo de nuestro proyecto.  
El contenido inicial de nuestro archivo debe ser el siguiente:  

{% highlight js %}
//Importando la libreria node-telegram-bot-api 
const TelegramBot = require('node-telegram-bot-api');
// Creando nuestra variable que almacenara nuestro token para autenticarnos con el bot creado con BotFather
const token = 'Escribe tu Token generado con BotFather';
// A continuacion, creamos nuestro bot y configuramos el parametro polling igualandolo a True, Con esto logramos que el bot esté en constante proceso de escucha y procesamiento de datos respecto al token de la API de Telegram.
const bot = new TelegramBot(token, {polling: true});
// A partir de estas tres líneas de código, ya podríamos empezar a crear comandos y eventos para darle funcionalidad a nuestro bot.
{% endhighlight %}  

## Generando nuestro token con [@BotFather](https://www.t.me/botfather)
Para crear y obtener nuestro Token, el cual nos permitirá tener una comunicación con la API de Telegram debemos interactuar con el padre de los bots, [@BotFather](https://www.t.me/botfather).  

### Algunos comandos de BotFather son:
  - /newbot — para crear un nuevo bot  
  - /token — para generar el token  
  - /revoke — revocar acceso al token  
  - /setname — cambiar el nombre del bot  
  - /setdescription — cambiar la descripción del bot  
  - /setabouttext — cambiar el texto "about me"  
  - /setuserpic — cambiar la foto de perfil  

<figure>
  <img src="{{site.baseurl}}/img/BotFather.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/BotFather.png" title="Generando un token para nuestro Bot">Generando un token para nuestro Bot de Telegram</a>
  </figcaption>
</figure>

Cabe aclarar que por razones de seguridad no debes revelar tu token a terceros, por ello en el capture de pantalla anterior oculto el contenido de mi Token.  
Como dato adicional, es posible revocar y generar un nuevo token por medio del [@BotFather](https://www.t.me/botfather).  

## Comandos y eventos
Para el desarrollo de cualquier bot en telegram debemos tener en cuenta 2 conceptos:  

**Comandos**  
La funcionalidad de este es realizar una acción cuando se le llama.  
*Ejemplo:* Si nosotros queremos que luego del comando **/start**, nuestro bot **GerhBot66** responda: "Hola @Usuario, soy un bot y mi nombre es **Gerh**"

{% highlight js %}
//Declaramos la funcion
bot.onText(/^\/start/, function(msg){
  // Imprimimos en consola el mensaje recibido.
  console.log(msg);
  // msg.chat.id se encarga de recoger el id del chat donde se está realizando la petición.
  var chatId = msg.chat.id;
  // msg.from.username se encarga de recoger el @alias del usuario.
  var username = msg.from.username;
  // Enviamos un mensaje indicando el id del chat, y concatenamos el nombre del usuario con nuestro saludo
  bot.sendMessage(chatId, "Hola, " + username + " soy un bot y mi nombre es Gerh");
});
{% endhighlight %}  

**Eventos**  
La idea principal de un evento es mantener constante escucha hasta recibir el llamado y luego proceder con la ejecuccion de alguna accion.  
*Ejemplo:* Cuando un usuario envia cualquier mensaje a nuestro Bot, el chat genera el evento: "Se ha recibido el mensaje".  
Para crear estos eventos se utiliza:  

{% highlight js %}
//Declaramos la funcion indicando que el evento esperado sera un "message"
bot.on('message', function(msg){
    console.log(msg);
    // msg.chat.id se encarga de recoger el id del chat donde se está realizando la petición.
    var chatId = msg.chat.id;
    // Enviamos nuestro mensaje indicando el id del chat. 
    bot.sendMessage(chatId, 'Se ha recibido el mensaje');
});
{% endhighlight %}  

## Iniciando nuestro bot

Luego de la explicacion anterior, el codigo final de nuestro proyecto queda de la siguiente manera:  

{% highlight js %}
const TelegramBot = require('node-telegram-bot-api');
const token = 'ESCRIBE TU TOKEN';

const bot = new TelegramBot(token, {polling: true});

bot.onText(/^\/start/, function(msg){
    console.log(msg);
    var chatId = msg.chat.id;
    var username = msg.from.username;
    bot.sendMessage(chatId, "Hola, " + username + " soy un bot y mi nombre es Gerh");
});

bot.on('message', function(msg){
    console.log(msg);
    var chatId = msg.chat.id;
    // send a message to the chat acknowledging receipt of their message
    bot.sendMessage(chatId, 'Received your message');
});
{% endhighlight %}  

Para poder realizar pruebas sobre nuestro bot ejecutamos lo siguiente:  
*Recordemos que el comando **npm start**, ejecutara **node index.js***

{% highlight zsh %}
╭─[~/Desktop/MyCodes/Kratos]─[root@Arthorias]─[0]─[3457]
╰─[:)] # npm start
> kratos@1.0.0 start /root/Desktop/MyCodes/Kratos
> node index.js
{% endhighlight %}  

Luego de la ejecucion de nuestro bot, debemos realizar una busqueda en telegram con el nombre del telegram para mi ejemplo utilice el nombre de **GerhBot66**.

<figure>
  <img src="{{site.baseurl}}/img/TestFinalBotTel.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/TestFinalBotTel.png" title="Probando nuestro bot de telegram creado en Node JS">Probando nuestro bot de telegram creado en Node JS</a>
  </figcaption>
</figure>

Luego de la interaccion con el bot, obtenemos la salida de nuestro **console.log()**

{% highlight js %}
{ message_id: 10,
  from:
   { id: 7700000,
     is_bot: false,
     first_name: 'Gerh',
     username: 'GerhSephiroth',
     language_code: 'en' },
  chat:
   { id: 7700000,
     first_name: 'Gerh',
     username: 'GerhSephiroth',
     type: 'private' },
  date: 1557183411,
  text: 'Gerardo Esta Aqui' }
{% endhighlight %}  
