## This a write-up for the machine Brooklyn Nine Nine which is a easy box from TryHackMe.
First we start a nmap scan
```
nmap -sC -sV <Target IP>
```
From the nmap result we have 3 open ports:
 - **21** (FTP)
 - **22** (SSH)
 - **80** (HTTP)
