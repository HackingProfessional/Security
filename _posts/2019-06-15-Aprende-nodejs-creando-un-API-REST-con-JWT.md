---
layout: post
title:  "Crear una API REST segura con Node.JS"
description: "Construyendo una API con Node.JS, JWT, Bcrypt, Express y MongoDB"
date:   2019-06-15
image:  API.png
tags:   [Aprende Nodejs, Creando una API REST, Prácticas de programación segura]
---

Hello Programmers!!  
En este artículo, aprenderemos a desarrollar una API REST Sencilla implementando operaciones CRUD (Crear, Leer, Actualizar y Eliminar).  
Nuestra desarrollo tendrá una API pública, y podremos acceder a ella sin ninguna autenticación.
Tambien tendremos una API protegida y para consumirla deberemos autenticarnos.

El motor de base de datos que usaremos para este desarrollo sera MongoDB y en el almacenaremos una coleccion llamada "videojuegos".

## Conceptos Basicos

**Node.JS**, es un entorno en tiempo de ejecución multiplataforma, de código abierto, para la capa del servidor basado en el lenguaje de programación ECMAScript, asíncrono, con I/O de datos en una arquitectura orientada a eventos y basado en el motor V8 de Google.  
Si deseas aprender a instalar Node.JS te invito a mi [siguiente post](https://hackingprofessional.github.io/Security/Aprende-nodejs-creando-un-bot-en-telegram/)  
[Articulo completo](https://es.wikipedia.org/wiki/Node.js)  

**API REST**, es cualquier interfaz entre sistemas que use HTTP para obtener datos o generar operaciones sobre esos datos en todos los formatos posibles, como XML y JSON. 
Las operaciones más importantes relacionadas con los datos en cualquier sistema REST y la especificación HTTP son cuatro: POST (crear), GET (leer y consultar), PUT (editar) y DELETE (eliminar).

**JWT**, JSON Web Token (abreviado JWT) es un estándar abierto basado en JSON propuesto por IETF (RFC 7519) para la creación de tokens de acceso que permiten la propagación de identidad y privilegios.
[Articulo Completo](https://es.wikipedia.org/wiki/JSON_Web_Token)  

**Bcrypt**, es una función de hashing de passwords diseñado por Niels Provos y David Maxieres, basado en el cifrado de Blowfish. Se usa por defecto en sistemas OpenBSD y algunas distribuciones Linux y SUSE. Lleva incorporado un valor llamado salt, que es un fragmento aleatorio que se usará para generar el hash asociado a la password, y se guardará junto con ella en la base de datos. Así se evita que dos passwords iguales generen el mismo hash y los problemas que ello conlleva, por ejemplo, ataque por fuerza bruta a todas las passwords del sistema a la vez.  
[Articulo Completo](https://en.wikipedia.org/wiki/Bcrypt)  

**Express.JS**, Es un framework para Node.js que sirve para ayudarnos a crear aplicaciones web en menos tiempo ya que nos proporciona funcionalidades como el enrutamiento, opciones para gestionar sesiones y cookies, entre otras cosas.  
[Articulo Completo](https://expressjs.com/es/)  

**Mongodb,** es un sistema de base de datos NoSQL orientado a documentos de código abierto.  
En lugar de guardar los datos en tablas, tal y como se hace en las bases de datos relacionales, MongoDB guarda estructuras de datos BSON (una especificación similar a JSON) con un esquema dinámico, haciendo que la integración de los datos en ciertas aplicaciones sea más fácil y rápida.  
[Articulo Completo](https://es.wikipedia.org/wiki/MongoDB)  

## Flujo de Trabajo
  - Registro de usuario a través del formulario de registro. (Nombre, Email, Contraseña)
  - El usuario se autentica mediante correo electrónico y contraseña.
  - Para poder consumir los recursos protegidos, el usuario debe enviar el token JWT en el encabezado.

## Desarrollando nuestra API 

Vamos a iniciar creando una carpeta donde almacenaremos nuestro proyecto y luego de eso ejecutamos el comando **npm init** para la creaccion de nuestra API.  

{% highlight sh %}
mkdir API-REST-NODEJS  ----> Crear carpeta en linux con el binario MKDIR
cd API-REST-NODEJS  ----> Nos dirigimos a la carpeta contenedora de nuestro proyecto
npm init ----> Inicializacion de un proyecto con NPM
{% endhighlight %}  

### Que es el archivo package.json
Con el comando *npm init* se crea un archivo llamado package.json. En este archivo, queda reflejada la configuración del proyecto de NodeJs tales como:  
  - Nombre del proyecto.  
  - Autor.  
  - Version.  
  - Dependencias.  
  - Scripts.  
  - Repositorio Git.  

El contenido del archivo debería tener una estructura similar a esta:  

{% highlight js %}
{
  "name": "olimpo",
  "version": "1.0.0",
  "description": "Construyendo una API REST con NodeJS",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Gerh",
  "license": "MIT"
}
{% endhighlight %}  

Ahora debemos instalar las siguientes dependencias:  

{% highlight zsh %}
╭─[/home/gerh/Escritorio/Prometheus/MyCodes/Blog/API-REST-NODEJS]─[root@Raeghal]─[0]─[1112]
╰─[:)] # npm install bcrypt body-parser express jsonwebtoken mongoose morgan --save
{% endhighlight %}  

{% highlight zsh %}
╭─[/home/gerh/Escritorio/Prometheus/MyCodes/Blog/API-REST-NODEJS]─[root@Raeghal]─[0]─[1112]
╰─[:)] # npm install nodemon -g
{% endhighlight %}  

Cada dependencia cumplira con un papel importante en nuestro desarrollo:  

### Para que sirven las dependecias previamente instaladas?

**bcrypt**, Esta dependencia nos ayudara a hashear de manera simple las contraseñas de los usuarios.
De esta manera, garantizamos la confidencialidad de estos datos sensibles que estaran almacenados en nuestra base de datos.  

**body-parser**, Con esta dependencia vamos a extraer toda la parte del cuerpo de un flujo de solicitud entrante y lo expone en **req.body**; como algo con lo que es más fácil interactuar. 

**Mongoose**, es una herramienta de modelado de objetos MongoDB diseñada para trabajar en un entorno asíncrono.  

**Morgan**, Es middleware del registrador de solicitudes HTTP para node.js  

**Nodemon**, Esta dependencia observará los archivos en el directorio en el que se inició nodemon, y si cualquier archivo cambia, nodemon reiniciará automáticamente su aplicación de node.JS.  

Después de instalar las dependencias, su archivo **package.json** tendrá la lista de todas las dependencias instaladas:  

{% highlight js %}
"dependencies": {
    "bcrypt": "^3.0.6",
    "body-parser": "^1.19.0",
    "express": "^4.17.1",
    "jsonwebtoken": "^8.5.1",
    "mongoose": "^5.5.14",
    "morgan": "^1.9.1"
}
{% endhighlight %}  

### Creando nuestra estructura de directorios
Ahora que hemos finalizado la instalacion de estas dependencias podemos continuar creando las carpetas que contendran toda la logica del API REST.  

{% highlight sh %}
cd API-REST-NODEJS    ===> Accediendo a nuestro directorio principal
mkdir config    ===> Creando el directorio Config
mkdir routes    ===> Creando el directorio routes
mkdir app   ===> Creando el directorio app
cd app     ===> Entramos al directorio app
mkdir api    ===> Creando el directorio api
cd api     ===> Entramos al directorio api
mkdir controllers    ===> Creando el directorio controllers
mkdir models     ===> Creando el directorio models
{% endhighlight %}  

### Utilizando el patron MVC  
Para este desarrollo utilizaremos el patron Modelo Vista Controlador (MVC), El cual es un estilo de arquitectura de software que separa los datos de una aplicación, la interfaz de usuario, y la lógica de control en tres componentes distintos.  

### Creando un servidor con node.JS y express.JS
Creamos un archivo con el nombre "server.js" dentro del directorio raiz y su contenido sera el siguiente:  

{% highlight js %}
const express = require('express');  
const logger = require('morgan');
const bodyParser = require('body-parser');
const app = express();
app.use(logger('dev'));
app.use(bodyParser.urlencoded({extended: false}));
app.get('/', function(req, res){
 res.json({"tutorial" : "Construyendo una API REST con NodeJS"});
});

app.listen(3000, function(){ console.log('El servidor ha sido inicializado: http://localhost:3000');});
{% endhighlight %}  

Para validar todo lo que hemos hecho, vamos a inicializar nuestro servidor con el siguiente comando:  

{% highlight zsh %}
╭─[/home/gerh/Escritorio/Prometheus/MyCodes/Blog/API-REST-NODEJS]─[root@Raeghal]─[0]─[1137]
╰─[:)] # nodemon server.js 
[nodemon] 1.19.1
[nodemon] to restart at any time, enter `rs`
[nodemon] watching: *.*
[nodemon] starting `node server.js`
El servidor ha sido inicializado: http://localhost:3000
{% endhighlight %}  

Luego de ello, si accedemos a nuestro navegador con el link http://localhost:3000 deberia respondernos lo siguiente:  

{% highlight js %}
{
  tutorial: "Construyendo una API REST con NodeJS"
}
{% endhighlight %}  

### Creaccion de modelos y entidades con Node.JS  
Los modelos dentro de NodeJS son representaciones de una entidad de la base de datos y más concretamente van a representar a un único registro o documento de nuestra base de de datos.  
Para este tutorial, vamos a tener una colección en la base de datos llamada Usuarios, dentro de ella se guardarán documentos de tipo Usuario.   
Por lo tanto debemos crear un modelo de Usuario con todos sus campos y cuando queramos crear una usuario haremos una instancia de ese modelo.  
Vamos a crear un archivo con el nombre **"users.js"**, en la ruta: **/API-REST-NODEJS/app/api/models/users.js**   

Dentro de el codificaremos lo siguiente:  

{% highlight js %}
// Cargamos el módulo de mongoose
const mongoose = require('mongoose');
// Cargamos el módulo de bcrypt
const bcrypt = require('bcrypt');
// Definimos el factor de costo, el cual controla cuánto tiempo se necesita para calcular un solo hash de BCrypt. Cuanto mayor sea el factor de costo, más rondas de hash se realizan. Cuanto más tiempo sea necesario, más difícil será romper el hash con fuerza bruta.
const saltRounds = 10;
//Definimos los esquemas
const Schema = mongoose.Schema;
// Creamos el objeto del esquema con sus correspondientes campos
const UserSchema = new Schema({
 nombre: {
  type: String,
  trim: true,  
  required: true,
 },
 email: {
  type: String,
  trim: true,
  required: true
 },
 password: {
  type: String,
  trim: true,
  required: true
 }
});
// Antes de almacenar la contraseña en la base de datos la encriptamos con Bcrypt, esto es posible gracias al middleware de mongoose
UserSchema.pre('save', function(next){
  this.password = bcrypt.hashSync(this.password, saltRounds);
  next();
});
// Exportamos el modelo para usarlo en otros ficheros
module.exports = mongoose.model('User', UserSchema);
{% endhighlight %}  

### Creacion de controladores 
Continuemos creando un controlador para el modelo recien creado, debe estar almacenado en la ruta **/API-REST-NODEJS/app/api/controllers/users.js**:  

{% highlight js %}
// Cargamos el modelo recien creado
const userModel = require('../models/users');
// Cargamos el módulo de bcrypt
const bcrypt = require('bcrypt'); 
// Cargamos el módulo de jsonwebtoken
const jwt = require('jsonwebtoken');

// Codificamos las operaciones que se podran realizar con relacion a los usuarios
module.exports = {
 create: function(req, res, next) {
  
  userModel.create({ nombre: req.body.nombre, email: req.body.email, password: req.body.password }, function (err, result) {
      if (err) 
       next(err);
      else
       res.json({status: "Ok", message: "Usuario agregado exitosamente!!!", data: null});
      
    });
 },
authenticate: function(req, res, next) {
  userModel.findOne({email:req.body.email}, function(err, userInfo){
     if (err) {
      next(err);
     } else {
      if(bcrypt.compareSync(req.body.password, userInfo.password)) {
        const token = jwt.sign({id: userInfo._id}, req.app.get('secretKey'), { expiresIn: '1h' });
        res.json({status:"Ok", message: "El usuario ha sido autenticado!!!", data:{user: userInfo, token:token}});
      }else{
        res.json({status:"error", message: "Invalid email/password!!", data:null});
      }
     }
    });
 },
}
{% endhighlight %}    

Nuestro controlador de usuario incluira el modelo de usuario y los modulos jsonwebtoken y bcrypt.  
Tambien hemos definido dos metodos:  

**Metodo Create**  
Con este metodo podremos crear nuevos usuarios.

**Metodo Authenticate**   
En este metodo, hemos creado una funcion que realiza una busqueda en la base de datos por medio del correo electronico, con ello logramos validar la existencia de un usuario.  
Luego comparamos la contraseña simple que se envia a través del formulario de inicio de sesión contra la contraseña de la base de datos; cabe aclarar que si contraseña coincide se generara el token JWT con una validez de 1 hora.  

### Creacion de Rutas
Luego de codificar lo anterior, vamos a proceder con la creacion de rutas para los métodos de control de los usuarios anteriores.  
Este archivo debe estar almacenado en **/API-REST-NODEJS/routes/users.js**  

{% highlight js %}
// Cargamos el modulo express
const express = require('express');
const router = express.Router();
// Cargamos el controlador del usuario
const userController = require('../app/api/controllers/users');
// Especificamos nuestras rutas teniendo en cuenta los metodos creados en nuestro controlador, y especificando que seran rutas que usaran el metodo POST
router.post('/register', userController.create);
router.post('/authenticate', userController.authenticate);
module.exports = router;
{% endhighlight %}    

Listo, hemos finalizado todo lo relacionado con los usuarios.  
Ahora vamos a proceder con la creacion del modelo, el controlador y el archivo de ruta para los videojuegos.  

### Creacion de CRUD para videojuegos
Nuestra API tendra la funcionalidad de leer, crear, actualizar y eliminar registros de videojuegos.  
**Creacion del controlador**  
Creamos un archivo en la siguiente ruta:  
**/API-REST-NODEJS/app/api/controllers/videogames.js**  
El contenido debe ser el siguiente:  

{% highlight js %}
const videogameModel = require('../models/videogames');
module.exports = {
// Metodo para la busqueda de videojuegos por ID
 getById: function(req, res, next) {
  console.log(req.body);
  videogameModel.findById(req.params.videogameId, function(err, videogameInfo){
   if (err) {
    next(err);
   } else {
    res.json({status:"success", message: "Videogame found!!!", data:{videogames: videogameInfo}});
   }
  });
 },
//Metodo para retornar todos los videojuegos registrados en la base de datos
getAll: function(req, res, next) {
  let videogamesList = [];
videogameModel.find({}, function(err, videogames){
   if (err){
    next(err);
   } else{
    for (let videogame of videogames) {
     videogamesList.push({id: videogame._id, name: videogame.name, released_on: videogame.released_on});
    }
    res.json({status:"success", message: "Videogames list found!!!", data:{videogames: videogamesList}});
       
   }
});
 },
//Metodo para actualizar algun registro de la base de datos por ID
updateById: function(req, res, next) {
  videogameModel.findByIdAndUpdate(req.params.videogameId,{name:req.body.name}, function(err, videogameInfo){
if(err)
    next(err);
   else {
    res.json({status:"success", message: "Videogame updated successfully!!!", data:null});
   }
  });
 },
//Metodo para eliminar algun registro de la base de datos por ID
deleteById: function(req, res, next) {
  videogameModel.findByIdAndRemove(req.params.videogameId, function(err, videogameInfo){
   if(err)
    next(err);
   else {
    res.json({status:"success", message: "Videogame deleted successfully!!!", data:null});
   }
  });
 },
//Metodo para crear algun registro nuevo
create: function(req, res, next) {
  videogameModel.create({ name: req.body.name, released_on: req.body.released_on }, function (err, result) {
      if (err) 
       next(err);
      else
       res.json({status: "success", message: "Videogame added successfully!!!", data: null});
      
    });
 },
}
{% endhighlight %}    

**Creacion del Modelo**  
Creamos un archivo en la siguiente ruta:  
**/API-REST-NODEJS/app/api/models/videogames.js**  
El contenido debe ser el siguiente:  

{% highlight js %}
// Cargamos el módulo de mongoose
const mongoose = require('mongoose');
//Definimos el esquema
const Schema = mongoose.Schema;
// Creamos el objeto del esquema con sus correspondientes campos
const VideogameSchema = new Schema({
 name: {
  type: String,
  trim: true,  
  required: true,
 },
 released_on: {
  type: Date,
  trim: true,
  required: true
 }
});
// Exportamos el modelo para usarlo en otros ficheros
module.exports = mongoose.model('Videogame', VideogameSchema)
{% endhighlight %}    

**Creacion de manejador de rutas**  
Creamos un archivo en la siguiente ruta:  
**/API-REST-NODEJS/routes/videogames.js**  
El contenido debe ser el siguiente:  

{% highlight js %}
// Cargamos el modulo express
const express = require('express');
const router = express.Router();
// Cargamos el controlador de videojuegos
const videogameController = require('../app/api/controllers/videogames');
// Especificamos nuestras rutas teniendo en cuenta los metodos creados en nuestro controlador
router.get('/', videogameController.getAll);
router.post('/', videogameController.create);
router.get('/:videogameId', videogameController.getById);
router.put('/:videogameId', videogameController.updateById);
router.delete('/:videogameId', videogameController.deleteById);
module.exports = router;
{% endhighlight %}    

Finalmente, actualizamos nuestro **server.js** con el siguiente contenido:  

{% highlight js %}  
const express = require('express');
const logger = require('morgan');
const videogames = require('./routes/videogames') ;
const users = require('./routes/users');
const bodyParser = require('body-parser');
const mongoose = require('./config/database'); //Importando la configuracion de conexion a la BD
var jwt = require('jsonwebtoken');
const app = express();
app.set('secretKey', 'ClaveSecreta'); // Clave Secreta para nuestro JWT

// Conectando a la base de datos de Mongo
mongoose.connection.on('error', console.error.bind(console, 'Error de conexion en MongoDB'));

app.use(logger('dev'));
app.use(bodyParser.urlencoded({extended: false}));
app.get('/', function(req, res){
res.json({"tutorial" : "Construyendo una API REST con NodeJS"});
});
// Rutas publicas
app.use('/users', users);
// Rutas privadas que solo pueden ser consumidas con un token generado
app.use('/videogames', validateUser, videogames);
app.get('/favicon.ico', function(req, res) {
    res.sendStatus(204);
});
// Para acceder a las rutas de peliculas hemos definido middleware para validar al usuario.
function validateUser(req, res, next) {
  jwt.verify(req.headers['x-access-token'], req.app.get('secretKey'), function(err, decoded) {
    if (err) {
      res.json({status:"error", message: err.message, data:null});
    }else{
      // add user id to request
      req.body.userId = decoded.id;
      next();
    }
  });
  
}
// Manejando errores HTTP 404 para solicitudes de contenido inexistente
app.use(function(req, res, next) {
 let err = new Error('Not Found');
    err.status = 404;
    next(err);
});
// Manejo de errores, respuestas con codigo HTTP 500, HTTP 404
app.use(function(err, req, res, next) {
 console.log(err);
 
  if(err.status === 404)
   res.status(404).json({message: "Not found"});
  else 
    res.status(500).json({message: "Error interno en el servidor!!"});
});
app.listen(3000, function(){
 console.log('El servidor ha sido inicializado: http://localhost:3000');
});
{% endhighlight %}    

### Configurando el archivo de conexion para MongoDB
Este archivo lo almacenaremos en la siguiente ruta:  
**/API-REST-NODEJS/config/database.js**
Y codificamos lo siguiente:  

{% highlight js %} 
//Cargando el modulo de mongoose
const mongoose = require('mongoose');
//Configurando la conexion para MongoDB, Debemos indicar el puerto y la IP de nuestra BD 
const mongoDB = 'mongodb://172.17.0.2:27017/BinaryChaos';
mongoose.connect(mongoDB);
mongoose.Promise = global.Promise;
module.exports = mongoose;
{% endhighlight %}    

Si deseas aprender a como crear una coleccion

### Probando Nuestra API  
Las operaciones HTTP disponibles para nuestra API son:  

  - POST (crear un recurso o generalmente proporcionar datos)  
  - GET (recuperar un índice de recursos o un recurso individual)  
  - PUT (actualizar o reemplazar un recurso)  
  - DELETE (eliminar un recurso)

Registro de usuarios.  
**POST http://localhost:3000/users/register**    

<figure>
  <img src="{{site.baseurl}}/img/CrearUsuarioPOST.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/CrearUsuarioPOST.png" title="Validando el servicio de registro de usuarios">Validando el servicio de registro de usuarios</a>.
  </figcaption>
</figure>

**Servicio de autenticacion, para generar el token**  
**POST http://localhost:3000/users/authenticate**  

<figure>
  <img src="{{site.baseurl}}/img/AuthPOST.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/AuthPOST.png" title="Validando el servicio de autenticacion sobre un usuario registrado">Validando el servicio de autenticacion</a>
  </figcaption>
</figure>

**Listado completo de videojuegos sin token de autenticacion**  
**GET http://localhost:3000/videogames**  

<figure>
  <img src="{{site.baseurl}}/img/ErrorJWT.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/ErrorJWT.png" title="Intentando consumir un servicio protegido sin ningun token">Intentando consumir un servicio protegido sin ningun token</a>
  </figcaption>
</figure>

**Listado completo de videojuegos con token de autenticacion**  
**GET http://localhost:3000/videogames**  

<figure>
  <img src="{{site.baseurl}}/img/LeerRegistroGET.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/LeerRegistroGET.png" title="Consumiendo un servicio protegido con un token de autenticacion">Consumiendo un servicio protegido con un token de autenticacion</a>
  </figcaption>
</figure>

**Listado de videojuegos por ID (Listado Dinamico)**  
**GET http://localhost:3000/videogames/XXXXXXX**  
<figure>
  <img src="{{site.baseurl}}/img/LeerDinamicoGET.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/LeerDinamicoGET.png" title="Consumiendo un servicio protegido con un token de autenticacion">Consumiendo un servicio protegido con un token de autenticacion</a>
  </figcaption>
</figure>  

**Actualizacion de videojuegos por ID**  
**PUT http://localhost:3000/videogames/XXXXXXX**  
<figure>
  <img src="{{site.baseurl}}/img/ActualizarRegistroPUT.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/ActualizarRegistroPUT.png" title="Actualizar un registro existente de la base de datos">Actualizar un registro existente de la base de datos</a>
  </figcaption>
</figure>

**Eliminacion de videojuegos por ID**  
**DELETE http://localhost:3000/videogames/XXXXXXX**  
<figure>
  <img src="{{site.baseurl}}/img/EliminarRegistro.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/EliminarRegistro.png" title="Actualizar un registro existente de la base de datos">Actualizar un registro existente de la base de datos</a>
  </figcaption>
</figure>

Puedes encontrar el código completo en [GitHub](https://github.com/hackingprofessional/API-REST-NODEJS)
