# Bandit Log

## Level 0
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
password: bandit0
cat readme
```

FLAG: boJ9jbbUNNfktd78OOpsqOltutMc3MY1

## Level 1
```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
password: boJ9jbbUNNfktd78OOpsqOltutMc3MY1
cat ./-
```

FLAG: CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

Problem: "-" as a filename is used as a shortcut for STDIN/STDOUT (by convention)

[Reference](https://unix.stackexchange.com/questions/16357/usage-of-dash-in-place-of-a-filename)

## Level 2
```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
password: CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
cat "spaces in this filename"
```

Problem: cat-ing a file when the filename includes spaces

FLAG: UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

## Level 3
```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
password: UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
cat inhere/.hidden
```

Problem: discovering a dotfile with `ls -a`

FLAG: pIwrPrtPN36QITSp3EQaw936yaFoFgAB

## Level 4
```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
password: pIwrPrtPN36QITSp3EQaw936yaFoFgAB

for x in `find inhere -type f -print`;
  do
    file $x;
  done

cat -file07
```

The description says that we need to find the only human readable file in the inhere directory. To see the file format I used the file command on all the files in the inhere directory. `-file07` contained ASCII text, the human readable one, so I cat-ed that.

FLAG: koReBOKuIDDepwhWk7jZC0RTdopnAYKh

## Level 5

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

human-readable
1033 bytes in size
not executable

```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
password: koReBOKuIDDepwhWk7jZC0RTdopnAYKh

find inhere -type f ! -executable -size 1033c
cat inhere/maybehere07/.file2
```

FLAG: DXjZPULLxYr17uwoI01bNLQbtFemEgo7

Learning to use `find`'s flags. Particularly useful was combining `!` with `-executable`


## Level 6

The password for the next level is stored somewhere on the server and has all of the following properties:

owned by user bandit7
owned by group bandit6
33 bytes in size

```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
password: DXjZPULLxYr17uwoI01bNLQbtFemEgo7

find -type f -size 33c -group bandit6 -user bandit7
cat ./var/lib/dpkg/info/bandit7.password
```

FLAG: HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

Using find flags for user and group, as well as byte size

## Level 7

The password for the next level is stored in the file data.txt next to the word millionth

```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
password: HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
cat data.txt | grep millionth
```

FLAG: cvX2JJa4CFALtqS87jk27qwqGhBM9plV

## Level 8

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
password: cvX2JJa4CFALtqS87jk27qwqGhBM9plV
cat data.txt | sort | uniq -u
```

FLAG: UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

`uniq -u` prints only unique lines
`sort data.txt | uniq -u` would have also worked

## Level 9

The password for the next level is stored in the file data.txt in one of the few human-readable strings, beginning with several ‘=’ characters.

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
password: UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
strings data.txt | grep ===
```

FLAG: truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

`strings`, which I haven't used before, prints the strings of printable characters in files

## Level 10

The password for the next level is stored in the file data.txt, which contains base64 encoded data

```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
password: truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
base64 -d data.txt
```

FLAG: IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

I knew this command off by heart from watching CTF streams ✨

## Level 11

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220
password: IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
cat data.txt  | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

FLAG: 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

The Wikipedia page for ROT13 contained this implementation which uses `tr` (transliterate). The first string passed to it represents the character set we're using (A-Za-z), and the second argument shows what they should become. The use of ranges is interesting here.

## Level 12

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
password: 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

FLAG: 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

A hexdump is a dump from a file or storage (e.g. RAM) in hexidecimal. It can be useful for reverse engineering.

This one was a doozy! There's a good writeup [here](https://kongwenbin.wordpress.com/2016/08/26/overthewire-bandit-level-12-to-level-13/)

A key takeaway: `file myfile` will let you know what sort of compressed file it originally was (e.g. gzip, b2z, tar)

## Level 13

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
password: 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
cd ~
cat bandit14.private [confirmed that yep, that's a private key that's lying around]
ssh bandit14@localhost -i sshkey.private
cat /etc/bandit_pass/bandit14
```

FLAG: 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

The lesson here is that since I had bandit14's private key (why???) I could use that to log in as bandit14. I think I went too quickly to googling on this one, I should have just poked around a bit to find the ssh key lying around there.

## Level 14

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

ssh, telnet, nc, openssl, s_client, nmap

```bash
nmap localhost

PORT      STATE SERVICE
22/tcp    open  ssh
113/tcp   open  ident
30000/tcp open  ndmps

curl -u bandit14:4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e localhost:30000
Wrong! Please enter the correct current password

echo 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e | nc localhost 30000
```

FLAG: BfMYroe26WYalil77FoDi9qh59eK5xNr

NDMPS is a protocol meant to transport data between network attached storage (NAS) devices and backup devices

I had to look this up, I had never heard of `nc` before. Before I start googling I should man page each suggested tool because then I might be able to figure it out myself. nc (netcat) is apparenlty a TCP/IP swiss army knife.

I was relying on my web world instincts. What I'm confused about is that you can just submit the password without saying exactly what it is (even, like, the body of a request. Odd). I can't quite figure out the echo and piping part. After reading the nc man page, I tried iterations on this without luck.

```bash
nc -c "echo 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e" localhost 30000
```

This works though
```bash
nc localhost 30000
<type password, hit enter>
```

telnet also works
```bash
telnet localhost 30000
<type password, hit enter>
```

## Level 15

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…

Commands you may need to solve this level
ssh, telnet, nc, openssl, s_client, nmap

```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220
password: BfMYroe26WYalil77FoDi9qh59eK5xNr

openssl s_client -connect localhost:30001
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

FLAG: cluFn7wTiGryunymYOu4RcffSxQluehd

Figured this one out on my own, scanning the man page for s_client helped

## Level 16

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

ssh, telnet, nc, openssl, s_client, nmap

```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
password: cluFn7wTiGryunymYOu4RcffSxQluehd

nmap -p 31000-32000
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

openssl s_client -connect localhost:31790
cluFn7wTiGryunymYOu4RcffSxQluehd
```

For this one, I couldn't figure out how to tell which of these ports spoke ssl, so I just iterated through the list. Other writeups showed using the `-A` (aggressive) flag brought up that information. I had tried that while I was working things out, but the scan took too long that I quit out of it.

## Level 17

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19

cat, grep, ls, diff

```
diff passwords.new passwords.old
< kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
---
> hlbSBPAWJmL6WFDb06gpTx1pPButblOA
```

FLAG: kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

Easy one 🙂

## Level 18

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

```
ssh bandit18@bandit.labs.overthewire.org -p 2220 "echo '' > .bashrc"
bash: .bashrc: Operation not permitted

ssh bandit18@bandit.labs.overthewire.org -p 2220 "rm .bashrc"
rm: cannot remove '.bashrc': Permission denied

ssh bandit18@bandit.labs.overthewire.org -p 2220 -t "cat readme"
```

My original approach was to try and overwrite the .bashrc, but I didn't have permission to do that. I also wondered if you could supply your own bashrc but I didn't try doing that.

I had to look this up. The `-t` flag on `ssh`  forces a 'pseudo-terminal' allocation. This  is like if you logged in and the fired up `screen`, I think. So, kinda sneaky. I used this to then cat out the readme, and gain the flag.

FLAG: IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

## Level 19

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

```
ssh bandit19@bandit.labs.overthewire.org -p 2220
file bandit20-do
> setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=8e941f24b8c5cd0af67b22b724c57e1ab92a92a1, not stripped

./bandit20-do
./bandit20-do cat /etc/bandit_pass/bandit20
> GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

FLAG: GbKksEFF4yrVs6il55v6gwY5aVje5f0j

The file bandit20-do allows us to do anything as Bandit20. I had to look this one up, and it turns out I actually mis-read the instructions. If I had understood the instructions correctly I might have got it.

## Level 20

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think

Commands you may need to solve this level
ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)

```
ssh bandit20@bandit.labs.overthewire.org -p 2220

nmap localhost
ls
./suconnect 30000
> [hangs]
./suconnect 22
Read: SSH-2.0-OpenSSH_7.4p1
ERROR: This doesn't match the current password!

echo GbKksEFF4yrVs6il55v6gwY5aVje5f0j | nc -l localhost -p 5000 &

./suconnect 8080
```

FLAG: gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

I was really confused by this, I had to look it up and then I didn't understand the first 2 explanations I read. I think this challenge used to be slightly different in the past. In the end I had to create my own little server that would echo out the previous level's password, then hit it with the binary provided. That makes sense in retrospect but I still found this challenge weird.

## Level 21
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

cron, crontab, crontab(5) (use “man 5 crontab” to access this)

```
ssh bandit21@bandit.labs.overthewire.org -p 2220

ls /etc/cron.d/
cat /etc/cron.d/cronjob_bandit22
>
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

cat /usr/bin/cron/cronjob_bandit22.sh
#!/bin/bash
>
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
> Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

FLAG: Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

`* * * * *` => At every minute

`chmod 644`
The owner will be able to read and save this file.
The group will be able to read this file.
Everyone will be able to read this file.

(got this one without looking up the solution)

## Level 22

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

```
ssh bandit22@bandit.labs.overthewire.org -p 2220

cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null

cat /usr/bin/cronjob_bandit23.sh
>
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget

echo I am user bandit23 | md5sum | cut -d ' ' -f 1
> 8ca319486bfbbc3663ea0fbe81326349

cat tmp/8ca319486bfbbc3663ea0fbe81326349
> jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

FLAG: jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

No help needed with this one!

## Level 23

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

```
ssh bandit23@bandit.labs.overthewire.org -p 2220

cat /etc/cron.d/cronjob_bandit24
> * * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null

cat /usr/bin/cronjob_bandit24.sh
>
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        timeout -s 9 60 ./$i
        rm -f ./$i
    fi
done

ls -la /var/spool/bandit24
total 476
drwxrwx--x 2 bandit24 bandit23 479232 Aug  5 18:20 .
drwxr-xr-x 5 root     root       4096 Oct 16  2018 ..

mkdir /tmp/mossy23
touch /tmp/mossy23/script.sh
vim /tmp/mossy23/script.sh
> 
#!/bin/sh
cat /etc/bandit_pass/bandit24 > /tmp/mossy23/bandit24pass

touch /tmp/mossy23/bandit24pass
chmod 666 /tmp/mossy23/bandit24pass

cp /tmp/mossy/script.sh /var/spool/bandit24
[wait 1 minute]

cat /tmp/mossy/bandit24pass
> UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

FLAG: UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

`chmod 777` => owner,group,others. 7 = rwx

Had to look this one up--I got stuck on a silent permissions error

## Level 24

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

```
ssh bandit24@bandit.labs.overthewire.org -p 2220
echo 1234 | nc localhost 30002
> I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Fail! You did not supply enough data. Try again.

echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 1234" | nc localhost 30002
> I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Wrong! Please enter the correct pincode. Try again.
```

FLAG: uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

I wrote a few different iterations of bash scripts that would loop through the numbers and submit them to the daemon. Unfortunately the daemon is slow to timeout, and submitting  many requests at a time seems to overwhelm it--at least the way I was doing it.

The following was the best solution I found online:

```
echo "" > pins && for i in {0000..9999}; do echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i >> pins; done && cat pins | nc localhost 30002
```
What this does is load up all the pins, correctly formatted, into a file. Then it submits that file to the daemon.

I like this approach. It kind of reminds me of creating chips for Nand2Tetris, where I would calculate both possible results of a branch before selecting which path to go down.

## Level 25

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

```
ssh bandit25@bandit.labs.overthewire.org -p 2220
cat /etc/shells
> # /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
/usr/bin/screen
/usr/bin/tmux
/usr/bin/showtext

cat /etc/passwd | grep 26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext

ls
> bandit26.sshkey

ssh bandit26@localhost -i bandit26.sshkey -t "tmux"
> (no dice)

cat /usr/bin/showtext
> #!/bin/sh

export TERM=linux

more ~/text.txt
exit 0

(minimize window very very small so that more would require you to scroll)
(hit v to open up vim)
Now you can open files with vim!

:e /etc/bandit_pass/bandit26
```

FLAG: 5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z

This one was totally bizarre and [this writeup](https://medium.com/@coturnix97/overthewires-bandit-25-26-shell-355d78fd2f4d) was useful.

The takeaway is that utils can be used to hop to other utils. `more` can open `vim` which can open a `shell` which is not the shell that the user's default.

## Level 26

Good job getting a shell! Now hurry and grab the password for bandit27!

```
./bandit27-do
> Run a command as another user.
  Example: ./bandit27-do id

./bandit27-do whoami
> bandit27 (I am actually bandit 26)

./bandit27-do cat /etc/bandit_pass/bandit27
```

FLAG: 3ba3118a22e93127a4ed485be72ef5ea

Note to self: try running a file if it's highlighted in red 🤦‍♀️

## Level 27

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

FLAG: 0ef186ac70e04ea33b4c1853d2526fa2

¯\\_(ツ)_/¯ I know how to clone a repo

## Level 28

There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.

The password was redacted but still visible in the git history, easily found with a `git show`

FLAG: bbc96594b4e001778eee9975372716b2

## Level 29

There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

`git branch -r`

FLAG: 5b90576bedb2cc04c86a9e924ce42faf

## Level 30

`git show secret` [secret being the name of a tag!]

FLAG: 47e603bb428404d265f59c42920d81e5

Takeaway: don't forget about tags

## Level 31

I had to edit the .gitignore and push a file to the remote

FLAG: 56a9bf19c63d650ce78e6ec0354ee45e

## Level 32

After all this git stuff its time for another escape. Good luck!

FLAG: c9c3199ddf4121b10cf581a98d51caee

## [FIN]

