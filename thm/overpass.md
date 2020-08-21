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

Reload the page and find a private ssh key
![sshkey](/thm/images/overpass/sshkey.png)

When we try to login with this private key we're asked for a passphrase so lets use john to crack it.


## Privilege escalation
