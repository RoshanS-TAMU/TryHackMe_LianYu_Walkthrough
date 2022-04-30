# Lian_Yu TryHackMe Walkthrough
Lian_Yu is a relatively easy TryHackMe room which focuses primarily on network and web.

The obejctives for this room were as follows:
1. What is the Web Directory you found? (Hint: in numbers)
2. what is the file name you found?
3. what is the FTP Password?
4. what is the file name with SSH password?
5. user.txt
6. root.txt

Tools used:
1. Enumeration: Nmap, Gobuster
2. Cryptography: Stegcracker, Base64, Base58
3. Privilege Escalation: Pkexec
_______________________________________________________________________________________

I start as always with scanning and enumeration:
```
sudo nmap -sC -sV -v <machine_ip>
```
There seem to be open FTP, SSH, and HTTP ports on this machine. Our likely goal will be to SSH into the machine as a user or root.
![image](https://media.github.tamu.edu/user/17583/files/dac2a580-c849-11ec-839b-1d543e1c929a)
![image](https://media.github.tamu.edu/user/17583/files/e910c180-c849-11ec-93fc-0ddc5dda10dd)

I started by connecting to the HTTP server, which led me to this page:
![image](https://media.github.tamu.edu/user/17583/files/53296680-c84a-11ec-842b-084c2a916f15)
![image](https://media.github.tamu.edu/user/17583/files/3bea7900-c84a-11ec-989c-b04a23b3b01c)

One of the most effective tools for brute-forcing web directories is gobuster. I decided to run it on this server with a medium wordlist at first:
![image](https://media.github.tamu.edu/user/17583/files/1447e080-c84b-11ec-9651-a01b99736107)

I started by connecting to /island on the server, which led me to this page. The code word "vigilante" was in white text.
![image](https://media.github.tamu.edu/user/17583/files/e7e09400-c84b-11ec-9c25-ec673e1ded9d)

I used gobuster again on /island, which returned a value of /2100:
![image](https://media.github.tamu.edu/user/17583/files/c97a9880-c84b-11ec-8cc5-2bd171ccc58e)

Since this is a 4-letter directory, I entered it as the first flag.

However, the page itself doesn't seem to offer any useful information:
![image](https://media.github.tamu.edu/user/17583/files/a05a0800-c84b-11ec-8f70-68b69b549f39)

Gobuster doesn't yield any results either.
![image](https://media.github.tamu.edu/user/17583/files/92f74a80-c858-11ec-9f1a-c061b5acf938)

I checked the HTML source code, which contained this message:
![image](https://media.github.tamu.edu/user/17583/files/6f2e0780-c84c-11ec-89b9-8edde2d844b7)

I took a look at the YouTube link to see if it offered any clues, but the connection timed out.
![image](https://media.github.tamu.edu/user/17583/files/879e2200-c84c-11ec-80e9-72a7c80fd3a2)

Instead I tried using gobuster with the ```-x .ticket``` tag
![image](https://media.github.tamu.edu/user/17583/files/d0ee7180-c84c-11ec-947e-14e49983cc68)
This satisfies our second question.

Opening the file, we get what looks like a Base64-encoded password, and possibly our next flag:
![image](https://media.github.tamu.edu/user/17583/files/b8cb2200-c84d-11ec-8076-ad44c85bf97b)

I attempted to decode it using B64, but it didn't yield anything. After attempting various codes on CyberChef, I converted it to readable text using Base58:
![image](https://media.github.tamu.edu/user/17583/files/93d6af00-c84d-11ec-955e-15516c28bf58)
![image](https://media.github.tamu.edu/user/17583/files/ef08a180-c84d-11ec-8990-1f0cc9e29ad6)
I entered this as the answer for #3, confirming that it is the FTP password. 

Using "vigilante" as the username and the password we just found, I connected to the FTP server:
![image](https://media.github.tamu.edu/user/17583/files/37c05a80-c84e-11ec-8936-8ea4f994eed4)

I see a .jpg file and two .png's. I extract the .jpg first to test out some steganography tools on it.
![image](https://media.github.tamu.edu/user/17583/files/224d2f80-c852-11ec-8c9a-de8560d8cb36)

Using stegcracker, I created a new file aa.jpg.out from aa.jpg. There seemed to be 2 files hidden inside, a cryptic ```passwd.txt``` and a ```shado``` file:
![image](https://media.github.tamu.edu/user/17583/files/304f8000-c853-11ec-9d6a-15f305742a61)

This looks like a password file! Now let's try connecting via SSH:
![image](https://media.github.tamu.edu/user/17583/files/478e6d80-c853-11ec-8d75-53ef58c346b2)
Even though Shado was clearly the correct password file, I was not able to get in as vigilante. 

However, I recalled seeing an FTP server directory called .other_user. I went back to check, and there did indeed turn out to be 2 users:
![image](https://media.github.tamu.edu/user/17583/files/c091c300-c85b-11ec-9210-39969416e751)

Let's try logging as slade:
![image](https://media.github.tamu.edu/user/17583/files/0185d980-c854-11ec-8407-20255418b4e7)
(user.txt flag output omitted.)

![image](https://media.github.tamu.edu/user/17583/files/8f61c480-c854-11ec-8ad6-1f2289c478a0)
Pkexec seems to be a vulnerability that allows users to run commands as root without sudo - which means not needing a password. A quick look at the manual and I was able to successfully pwn the machine.

![image](https://media.github.tamu.edu/user/17583/files/36def700-c855-11ec-9ccb-d06024f8c31f)
(Flag output omitted.) 
_______________________________________________________________________________________

Try out this challenge for yourself at www.tryhackme.com/rooms/lian_yu!



