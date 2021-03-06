---
layout: post
title:  "Aprende NoSQL creando un CRUD"
description: "Aprendiendo a Programar con NoSQL desde 0"
date:   2019-04-20
image:  "img/MongoDB-NoSQL.png"
tags:   [Aprende a programar, Aprende NoSQL, Aprende MongoDB]
---

Hello Programmers!!  
En el dia de hoy, vamos aprender a crear una base de datos en NoSQL con MongoDB, y luego de esto para tener una idea de como realizar operaciones con Mongo, vamos a crear un CRUD desde 0.  

## Conceptos Basicos  

**CRUD,** Es el acrónimo de "Crear, Leer, Actualizar y Borrar" (En Ingles: Create, Read, Update and Delete), que se usa para referirse a las funciones básicas en bases de datos o la capa de persistencia en un software.  
[Articulo Completo](https://es.wikipedia.org/wiki/CRUD)  

**Mongodb,** es un sistema de base de datos NoSQL orientado a documentos de código abierto.  
En lugar de guardar los datos en tablas, tal y como se hace en las bases de datos relacionales, MongoDB guarda estructuras de datos BSON (una especificación similar a JSON) con un esquema dinámico, haciendo que la integración de los datos en ciertas aplicaciones sea más fácil y rápida.  
[Articulo Completo](https://es.wikipedia.org/wiki/MongoDB)  

**SQL,** El Lenguaje de Consulta Estructurado popularmente conocido por sus siglas en inglés como SQL, es un tipo de lenguaje de programación que ayuda a solucionar problemas específicos o relacionados con la definición, manipulación e integridad de la información representada por los datos que se almacenan en las bases de datos.  
[Articulo Completo](https://styde.net/que-es-y-para-que-sirve-sql/)  

**NoSQL,** Son estructuras que nos permiten almacenar información en aquellas situaciones en las que las bases de datos relacionales generan ciertos problemas debido principalmente a problemas de escalabilidad y rendimiento de las bases de datos relacionales donde se dan miles de usuarios concurrentes y con millones de consultas diarias.  
[Articulo Completo](https://www.acens.com/wp-content/images/2014/02/bbdd-nosql-wp-acens.pdf)  

Debido a que los problemas evolucionaron, las soluciones y los sistemas también; Hay situaciones donde el uso de SQL es problemático o por lo menos no se adapta a ciertos escenarios.  

<figure>
  <img src="{{site.baseurl}}/img/NoSQlvsSQL.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/NoSQlvsSQL.png" title="Diferencias entre NoSQL y SQL">Imagen alusiva acerca NoSQL y SQL</a>.
  </figcaption>
</figure>

## Instalando MongoDB en Windows

  - Inicialmente debemos descargar el instalador para Windows del [centro de descargas](https://www.mongodb.com/download-center/community).  
  - Luego iniciamos el instalador como administrador.  
  - Creamos las siguientes carpetas de almacenamiento y configuracion de MongoDB.  

{% highlight shell %}
C:\data  
C:\data\db
{% endhighlight %}  

  - Agregamos a nuestras variables de etorno la carpeta donde se instalo el MongoDB.
  - Iniciamos el servicio de MongoDB de la siguiente manera:  

{% highlight shell %}
C:\mongodb\bin\mongod.exe
{% endhighlight %}  

<figure>
  <img src="{{site.baseurl}}/img/mongod.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/mongod.png" title="Iniciando el servicio mongod">Iniciando el servicio mongod</a>.
  </figcaption>
</figure>

  - Finalmente iniciamos la shell de Mongo.

{% highlight shell %}
C:\mongodb\bin\mongo.exe
{% endhighlight %}  

<figure>
  <img src="{{site.baseurl}}/img/StartMongo.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/StartMongo.png" title="Abriendo la Shell de Mongo">Abriendo la Shell de Mongo</a>.
  </figcaption>
</figure>
  
## Creando un CRUD en NoSQL con MONGODB  
### CREATE
Para almacenar datos en MONGODB es necesario crear una base de datos; nosotros podemos crear nuestra BD con el comando **use.**  

{% highlight bash %}
# En este caso creamos una base que se llama db_Atenea 
C:\Windows\system32>mongo
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
> use db_Atenea
switched to db db_Atenea
{% endhighlight %}  

Una vez creada la base de datos, es necesario crear una **coleccion** para almacenarlos.  
Una coleccion es una entidad que contiene un conjunto de objetos JSON.  

Nuestra coleccion traera la siguiente informacion:

<figure>
  <img src="{{site.baseurl}}/img/User1Json.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/User1Json.png" title="Coleccion de datos a guardar">Coleccion de datos a guardar</a>.
  </figcaption>
</figure>


{% highlight js %}
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
> db.usuarios.insert({identificacion:1, name:"Gerardo Eliasib", username:"Gerh", address:{ city: "Hyrule", geo:{ long:-0.127551, lat:51.5072} }, website:"hackingprofessional.github.io", gender:"male", age: 18 });
{% endhighlight %}  

la estructura del comando anterior es la siguiente:  

{% highlight js %}
//Estructura General
db.collection.operation(objects)
// Estructura para insertar una coleccion
db.collection.insert({...})
{% endhighlight %}  

Luego de la ejecucion del comando anterior, la salida debe ser la siguiente:  

{% highlight js %}
WriteResult({ "nInserted" : 1 })
{% endhighlight %}  

De esta manera logramos almacenar en la base de datos *db_atenea*, un documento JSON en la coleccion llamada **usuarios**.  

### READ

Para consultar un registro insertado seguimos la misma estructura indicada anteriormente, **db.collection.operation()**.
Inicialmente vamos a indicarle a Mongo que nos retorne todos los objetos JSON de la coleccion **usuarios**, con la operacion **find**.  

{% highlight js %}
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
> db.usuarios.find()
{ "_id" : ObjectId("5cb391895adc14adb1e7f4b5"), "identificacion" : 1, "name" : "Gerardo Eliasib", "username" : "Gerh", "address" : { "city" : "Hyrule", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "hackingprofessional.github.io", "gender":"male", "age": 18 }  
{ "_id" : ObjectId("5cb396885adc14adb1e7f4b6"), "identificacion" : 2, "name" : "Leanne Graham", "username" : "Leanne", "address" : { "city" : "Roma", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "Sin Website", "phone" : "3002917586", "gender":"male", "age": 20 }  
{ "_id" : ObjectId("5cb396f95adc14adb1e7f4b7"), "identificacion" : 3, "name" : "Sofia Elizabeth", "username" : "Elizabeth", "address" : { "city" : "Londres", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "BinaryChaos.com", "phone" : "3002917789", "gender":"female", "age": 18 }
{% endhighlight %}  

Si queremos realizar una consulta mas especifica, solo debemos indicarle de la siguiente manera:  

{% highlight js %}
//Buscando en la coleccion usuarios, donde la propiedad identificacion sea igual a 2
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
> db.usuarios.find({identificacion:2})
{ "_id" : ObjectId("5cb396885adc14adb1e7f4b6"), "identificacion" : 2, "name" : "Leanne Graham", "username" : "Leanne", "address" : { "city" : "Roma", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "Sin Website", "phone" : "3002917586", "gender":"male", "age": 20 }
{% endhighlight %}  

MongoDB tambien nos permite realizar busquedas mas precisas con el uso de operadores, los cuales son:  

#### Operadores de consultas
#### Comparativos  

| Nombre           | Descripcion                                                                 |
| --------         | ------------------------------------------------------------------          |
| [$eq](https://docs.mongodb.com/manual/reference/operator/query/eq)         | Coincide con valores que son iguales a un valor especificado.               |
| [$gt](https://docs.mongodb.com/manual/reference/operator/query/gt)         | Coincide con los valores que son mayores que un valor especificado.         |
| [$gte](https://docs.mongodb.com/manual/reference/operator/query/gte)        | Coincide con los valores que son mayores o iguales a un valor especificado. |
| [$in](https://docs.mongodb.com/manual/reference/operator/query/in)         | Coincide con cualquiera de los valores especificados en una matriz.         |
| [$lt](https://docs.mongodb.com/manual/reference/operator/query/lt)         | Coincide con valores que son menores que un valor especificado.             |
| [$lte](https://docs.mongodb.com/manual/reference/operator/query/lte)        | Coincide con valores que son menores o iguales a un valor especificado.     |
| [$ne](https://docs.mongodb.com/manual/reference/operator/query/ne)         | Coincide con todos los valores que no son iguales a un valor especificado.  |
| [$nin](https://docs.mongodb.com/manual/reference/operator/query/nin)        | No coincide con ninguno de los valores especificados en una matriz.         |

#### Logicos  

| Nombre           | Descripcion                                                          |
| --------         | ------------------------------------------------------------------   |
| [$and](https://docs.mongodb.com/manual/reference/operator/query/and)        | Devuelve todos los objetos si todas las expresiones son verdaderas.  |
| [$or](https://docs.mongodb.com/manual/reference/operator/query/or)         | Devuelve todos los objetos si alguna expresion es verdadera.         |

Existen muchos mas operadores, te invito a leer el siguiente articulo:  
[Articulo Completo](https://docs.mongodb.com/manual/reference/operator/query/)  

{% highlight js %}
//Buscando en la coleccion usuarios, donde la propiedad identificacion sea igual o mayor a 2
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
db.usuarios.find( {identificacion: {$gte:2}} )
{ "_id" : ObjectId("5cb396885adc14adb1e7f4b6"), "identificacion" : 2, "name" : "Leanne Graham", "username" : "Leanne", "address" : { "city" : "Roma", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "Sin Website", "phone" : "3002917586", "gender":"male", "age": 20 }  
{ "_id" : ObjectId("5cb396f95adc14adb1e7f4b7"), "identificacion" : 3, "name" : "Sofia Elizabeth", "username" : "Elizabeth", "address" : { "city" : "Londres", "geo" : { "long" : -0.127551, "lat" : 51.5072 } }, "website" : "BinaryChaos.com", "phone" : "3002917789", "gender":"female", "age": 18 }
{% endhighlight %}  

Si necesitamos agregar más condiciones, solo hay que colocar las propiedades que deseamos filtrar.  

{% highlight js %}
//Buscando en la coleccion usuarios, donde la propiedad website sea diferente de [Sin Website], y la propiedad Cuidad sea igual a Londres o Hyrule
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
db.usuarios.find( {"address.city": {$in: ["Londres", "Hyrule"] }, website: {$ne:"Sin Website"}})
{% endhighlight %}  

### UPDATE

Para actualizar alguna propiedad de un objeto JSON que existe en la coleccion, es necesario ejecutar la operación update.  
La estructura de esta es la siguiente:

{% highlight js %}
db.collection.update({query}, {update},{options})
{% endhighlight %}  

Vamos a actualizar el numero telefonico del objeto con id **5cb391895adc14adb1e7f4b5**.  
*Para este ejemplo, el objeto con id **5cb391895adc14adb1e7f4b5** corresponde al usuario **Gerardo Eliasib**.*  

{% highlight js %}
// Actualizando el objeto JSON sin borrar los datos que previamente tenia.
// Usando el operador SET, para agregar propiedades nuevas a un objeto JSON.
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
db.usuarios.update({"_id" : ObjectId("5cb391895adc14adb1e7f4b5")}, {$set: {phone : 0} } )
{% endhighlight %}  

{% highlight js %}
// Actualizando el objeto JSON reemplazando por completo los datos que tiene.
// TODO EL REGISTRO SERA REEMPLAZADO POR LOS VALORES QUE COLOQUEMOS!
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
db.usuarios.update({"_id" : ObjectId("5cb391895adc14adb1e7f4b5")}, {phone : 1} )
{% endhighlight %}  

### DELETE

Para eliminar algun un objeto JSON que existe en la coleccion, es necesario ejecutar la operación remove.  
La estructura de esta es la siguiente:

{% highlight js %}
db.collection.remove({query})
{% endhighlight %}  

**NOTA:**  
*Antes de ejecutar un remove, es recomendable realizar un **find** para validar lo que se va a borrar*  

{% highlight js %}
//Eliminando el registro con _id que tiene el valor: 5cb391895adc14adb1e7f4b5
-----------------------------
MongoDB shell version v4.0.8
-----------------------------
db.usuarios.remove({"_id" : ObjectId("5cb391895adc14adb1e7f4b5")})
{% endhighlight %}  

Luego de la ejecucion del comando anterior, la salida debe ser la siguiente:  

{% highlight js %}
WriteResult({ "nRemoved" : 1 })
{% endhighlight %}  

**Nota:**  
Todos los operadores y selectores de consulta que utilizamos con la operacion *find* pueden ser usados con esta operacion remove.  

