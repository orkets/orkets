# Lian-Yu --> beginner CTF box Capture the flags and have fun.#

Tools used in this write up.

-Reconnaissance
  * nmap

-Enumeration
 * Directory Bruteforce using ffuf and Gobuster

-Exploitation
 * Steganography:
 (stegcracker, Steghide)

Privilege Escalation
 * pkexec



   
## First nmap scan
```
sudo nmap -T4 -sV -sS -T4  10.10.2.78
```

# Result:
```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-25 14:06 EDT
Nmap scan report for 10.10.2.78
Host is up (0.11s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
21/tcp  open  ftp     vsftpd 3.0.2
22/tcp  open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
80/tcp  open  http    Apache httpd
111/tcp open  rpcbind 2-4 (RPC #100000)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.47 seconds
```


**So as we can see ssh and ftp ports are open we can use that information later*

## Next Step --> Enumerate The Directory


after browsing the IP Adress and found nothing, I tried to fuzz the directory with ffuf

```
ffuf -w /your_path_to_directory-list-2.3-medium.txt -u http://YOUR-IP/FUZZ
```

### Findings
![DemoCreatorSnap_2023-09-25 21-14-14](https://github.com/orkets/orkets/assets/111442711/82bfcde2-ada8-4332-86bf-cb2922caa852)

Page Source shows a name that we need later 
![DemoCreatorSnap_2023-09-25 21-30-03](https://github.com/orkets/orkets/assets/111442711/2ec06634-9c85-4369-9347-6f3fb32d6722)


Using ffuf again on island directory 
```
ffuf -w /home/kali/Downloads/SecLists/Discovery/Web-Content/raft-small-directories.txt -u http://YOUR-IP/island/FUZZ
``` 
### Result

Directory found --> **2100**

Nothing intersting till you see the page source 

view-source:http://YOUR-IP/island/2100/

![DemoCreatorSnap_2023-09-25 22-17-45](https://github.com/orkets/orkets/assets/111442711/0a3eee78-1b8d-407a-afc4-be3aebed6395)


So technically it's a hint for a hidden file extension, run gobuster with the following command to see what's the file full name 
```
gobuster dir -u http://10.10.114.17/island/2100/  -w  /your_path_to_directory-list-2.3-medium.txt -x ticket 
```

### Result: 
```
green_arrow.ticket
```

You can use curl to review the file 

![DemoCreatorSnap_2023-09-26 08-52-52](https://github.com/orkets/orkets/assets/111442711/83ccfe09-b423-4037-b0a9-1324761d2459)

- **RTy8yhBQdscX** looks like base to decode specifically base58 (You can check from Cipher Identifier) 
- save it into a file then you can either use Cyberchef or you can decode it using your terminal like I did

```
!#th3h00d                            --> looks like a password for something 
```
Now i got both the username and password for something 
```
vigilante : !#th3h00d
```


So I took the credentials and tried to login via both ssh and FTP, but only looged in successfully via FTP

![DemoCreatorSnap_2023-09-26 09-25-42](https://github.com/orkets/orkets/assets/111442711/106de834-8f32-44ad-9af2-0bff670accd1)


found couple files, I downloaded these files by using “get” command. I tried to look up for something else if there's any and i got another username


![DemoCreatorSnap_2023-09-26 09-51-22](https://github.com/orkets/orkets/assets/111442711/0c98e68d-4161-4922-8ba8-14b6f60b54a9)


Cool!


I started to see what's hidden in these files. all of them have either jpg or png extention except for one 
![DemoCreatorSnap_2023-09-26 09-55-09](https://github.com/orkets/orkets/assets/111442711/c8b9c9d2-ae36-4868-8bd4-3a7cbe835672)


i tried to to go deeper with this specific file using exiftool 

![DemoCreatorSnap_2023-09-26 09-58-33](https://github.com/orkets/orkets/assets/111442711/88dae496-7aa7-4b87-a5c3-450ba4f71725)


## You can skip the following step by using stegcracker to open the aa.jpg file and it would get the password immediately but i thought why not trying to solve it :) ##

![DemoCreatorSnap_2023-09-26 10-16-25](https://github.com/orkets/orkets/assets/111442711/9aab7a50-bafc-4274-b8b3-546f6905a811)



Le'ts get back to the error

## Solving the error during opening the Leave_me_alone.png file ##
file extension is png but there's an error occuring so it means the headers of the file could potentially be damaged so I used hexeditor

Indeed, as evident from the initial line, the file header isn't formatted as a PNG. To address this, i did a brief online search for **PNG hex header** to locate the necessary information

![DemoCreatorSnap_2023-09-26 10-04-40](https://github.com/orkets/orkets/assets/111442711/cff3d51e-e9f0-46c3-b47f-04fd7f773aa0)

Let's make sure everything is on the right track by using exiftool again

![DemoCreatorSnap_2023-09-26 10-08-22](https://github.com/orkets/orkets/assets/111442711/c34544c7-f3d4-45b6-a1ac-9b210dbd3de9)

Cool! the error has gone and we can open the image now

![DemoCreatorSnap_2023-09-26 10-09-49](https://github.com/orkets/orkets/assets/111442711/fbbb67ea-348c-40f4-bea9-45fc28c592b5)

It says the password for something is 'password'. I checked the other files to see if we can use this password on and i found that it can be used on the aa.jpg 

I got 2 files the first one (passwd.txt) wasn't including a passwrod but the other one (shado) contains the SSH password for slade username as we found at the beginning 

![DemoCreatorSnap_2023-09-26 10-18-56](https://github.com/orkets/orkets/assets/111442711/c2b6d83c-7162-49af-a757-2f12cabab12b)

shado file 


![DemoCreatorSnap_2023-09-26 10-20-58](https://github.com/orkets/orkets/assets/111442711/09df3d5f-a393-4b9b-b44d-852ded295ba8)




and it did work

![DemoCreatorSnap_2023-09-26 10-24-44](https://github.com/orkets/orkets/assets/111442711/075e2f3a-4beb-40f4-adf1-d0d1ac5b6bd6)


The user.txt was easily found 

![DemoCreatorSnap_2023-09-26 10-26-04](https://github.com/orkets/orkets/assets/111442711/59939064-4ac7-450a-b872-02106a8dd90f)



## Privilege Escalation ##

before any testing i tries to see  what i can execute with sudo rights 
![DemoCreatorSnap_2023-09-26 10-27-29](https://github.com/orkets/orkets/assets/111442711/1527762d-d777-4092-8cc8-b3bbeec43ed7)

We can use sudo cause it says "PASSWD" so i looked up for /usr/bin/pkexec privilege escalation on google 

![DemoCreatorSnap_2023-09-26 10-33-27](https://github.com/orkets/orkets/assets/111442711/5c801965-7e68-4bda-9cec-859cb14d21e3)



![DemoCreatorSnap_2023-09-26 10-33-47](https://github.com/orkets/orkets/assets/111442711/265b2089-6fe2-4b57-bc96-8c6a40f0ca39)


![DemoCreatorSnap_2023-09-26 10-34-21](https://github.com/orkets/orkets/assets/111442711/96ae7430-66c5-413c-b25b-4c459f51dc58)


Mission accomplished!




