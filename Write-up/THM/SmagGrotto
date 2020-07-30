# Smag Grotto
As usual we are going to run nmap to see what ports are open on the box
```
$nmap -sC -sV 10.10.24.225
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-30 13:50 EEST
Nmap scan report for smag.thm (10.10.24.225)
Host is up (0.10s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 74:e0:e1:b4:05:85:6a:15:68:7e:16:da:f2:c7:6b:ee (RSA)
|   256 bd:43:62:b9:a1:86:51:36:f8:c7:df:f9:0f:63:8f:a3 (ECDSA)
|_  256 f9:e7:da:07:8f:10:af:97:0b:32:87:c9:32:d7:1b:76 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Smag
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
As we can se from the scan result we have only 2 open ports:
```
Port 22 - SSH
Port 80 - HTTP
```
Before doing anything else let's check port 80
