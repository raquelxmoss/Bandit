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
```
