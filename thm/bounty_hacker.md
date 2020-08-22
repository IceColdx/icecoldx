# Overpass Writeup

![bannner](/thm/images/bounty_hacker/banner.png)

Writeup for room - [Bounty Hacker](https://tryhackme.com/room/cowboyhacker)

## Scanning and Enumeration

Let's start with an nmap scan.

```
nmap <IP> -sV -sC -T4
```

![nmap scan](/thm/images/bounty_hacker/nmap.png)

We find 3 open ports, it seems anonymous login is enabled on FTP port 21 so let's login.

![ftp](/thm/images/bounty_hacker/ftp.png)

tasks.txt contains the name we should use for ssh and in our attack.
I'd say there are about 20 passwords in lock.txt, we could try to do that manually but what's the fun in that? HYDRA!

![hydra](/thm/images/bounty_hacker/hydra.png)

Let's try sudo -l.

![sudo](/thm/images/bounty_hacker/sudo.png)

Time to use https://gtfobins.github.io/ and search for tar.

![gtfo](/thm/images/bounty_hacker/gtfo.png)

It tells us we can run the following with sudo permissions:

```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
![tar](/thm/images/bounty_hacker/tar.png)

Now that we've escalated our privileges to root we can cat the last flag.

Hope this writeup helped you!
