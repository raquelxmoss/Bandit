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
