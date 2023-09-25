Lian-Yu --> beginner CTF box Capture the flags and have fun.

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

## Next Step --> Enumerate The Directory


after browsing the IP Adress and found nothing, I tried to fuzz the directory with ffuf

```
ffuf -w /home/kali/Downloads/SecLists/Discovery/Web-Content/raft-small-directories.txt -u http://YOUR-IP/FUZZ
```

### Findings
![DemoCreatorSnap_2023-09-25 21-14-14](https://github.com/orkets/orkets/assets/111442711/82bfcde2-ada8-4332-86bf-cb2922caa852)

Page Source
![DemoCreatorSnap_2023-09-25 21-30-03](https://github.com/orkets/orkets/assets/111442711/2ec06634-9c85-4369-9347-6f3fb32d6722)


Using ffuf again on island directory 
```
ffuf -w /home/kali/Downloads/SecLists/Discovery/Web-Content/raft-small-directories.txt -u http://YOUR-IP/island/FUZZ
``` 
### Result

Directory found --> 2100

view-source:http://YOUR-IP/island/2100/




