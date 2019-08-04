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
