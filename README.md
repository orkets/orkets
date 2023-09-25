Lian-Yu --> beginner CTF box Capture the flags and have fun.

Tools used in this write up
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

