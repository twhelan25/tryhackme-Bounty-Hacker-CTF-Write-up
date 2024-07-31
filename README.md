![intro](https://github.com/user-attachments/assets/cc79dcbb-e793-41b2-835b-4e435651a585)

# tryhackme-Bounty-Hacker-CTF-Write-up

This is a walkthrough for the tryhackme CTF Bounty Hacker. I will not provide any flags or passwords as this is intended to be used as a guide.

## Scanning/Reconnaissance

First off, let's store the target IP as a variable for easy access.

Command: export ip=xx.xx.xx.xx

Next, let's run an nmap scan on the target IP:
```bash
nmap -A -v $ip -D RND:10 -oN nmap.txt
```

Command break down:

-A: This flag enables aggressive scanning. It combines various scan types (like OS detection, version detection, script scanning, and traceroute) into a single scan.

-v: increases verbosity, providing more detailed output during the scan.

—$ip: provides the target IP we stored as the variable $ip.

-D RND:10 Nmap can send additional packets to confuse network intrusion detection systems (IDS) or hide the true source of the scan by randomly selecting up to 10 decoy IP addresses.

-oN nmap.txt: This option specifies normal output that should be saved to a file named “nmap.txt.

This scan reveals a few open ports that will prove very usep.

Let's check on the webserver on port 80: 

![80](https://github.com/user-attachments/assets/376107c9-bda0-4172-ae2d-e401ec4557c7)

Our nmap scan revealed an ftp server with anonymous login allowed, so let's check that out:
```bash
ftp $ip
```
The ftp server seems very unstable, and keeps going to passive mode. After a few attemps, I was able to ls and see locks.txt and task.txt, but it keeps stalling when I try to get them. I'll keep trying and run gobuster and nikto scans in the mean time.
```bash
gobuster dir -u $ip -w=/usr/share/wordlists/dirb/common.txt -o bust.txt
```
```bash
nikto -h $ip | tee nikto.txt
```
The scan reveals an /images directory that contains the main site image. 
I was able to finally get the task.txt and locks.txt.

![ftp](https://github.com/user-attachments/assets/bd5b2702-3903-4c06-aa92-75b37bd721b5)

We have a message from lin(answer to one of the questions) and a password list.
# Privilege Escalation
Now let's use what we've gathered so far and take a stab at brute forcing port 22 ssh:
```bash
hydra -l lin -P locks.txt $ip ssh
```
And we are now logged onto the target as lin!
We can now cat user.txt
# Privilege Escalation/Exploitation
sudo -l reveals something nice:

![sudo -l](https://github.com/user-attachments/assets/f7d6fc5c-12c2-42b1-a053-308c1e4ac04d)


Let's head to gtfobins.github.io and see what we can find about sudo tar.

![gtfo](https://github.com/user-attachments/assets/d96172d1-4f58-4371-b9f2-d011f40ffa6e)

![exploit](https://github.com/user-attachments/assets/7bfab2e4-4b74-47c0-802c-ce2c918fe568)

The exploit worked and we can now get root.txt! I hope you enjoyed this CTF.
