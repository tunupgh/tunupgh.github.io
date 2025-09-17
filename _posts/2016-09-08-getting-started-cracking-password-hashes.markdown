---
layout: post
title: Getting Started Cracking Password Hashes with John the Ripper
date: 2016-09-08 21:23
comments: true
categories:
- infosec
- cracking
- passwords
- john
---
Want to get started with password cracking and not sure where to begin? In this post we'll explore how to get started with it.

Most systems don't store passwords on them. Instead they store hashes of passwords and when authentication takes place, the password is hashes and if the hashes match authentication is successful. Different systems store password hashes in different ways depending on the encryption used. 

Password hash cracking usually consists of taking a wordlist, hashing each word and comparing it against the hash you're trying to crack. This is a variation of a dictionary attack because wordlists often are composed of not just dictionary words but also passwords from public password dumps. This type of cracking becomes difficult when hashes are [salted](https://en.wikipedia.org/wiki/Salt_(cryptography)).

The tool we are going to use to do our password hashing in this post is called **John the Ripper**. John is a great tool because it's free, fast, and can do both wordlist style attacks and brute force attacks. A brute force attack is where the program will cycle through every possible character combination until it has found a match.

## Setup

To get setup we'll need some password hashes and John the Ripper.

#### Sample Password Hashes

A group called KoreLogic used to hold DEFCON competitions to see how well people could crack password hashes. Their contest files are still posted on their site and it offers a great sample set of hashes to begin with.

Download the [password hash file bundle](http://contest-2012.korelogic.com/) from the KoreLogic 2012 DEFCON challenge.
Or use this [mirror](/images/cmiyc_2012_password_hash_files.tar.bz2).

Extract the file using this linux command:

```
tar jxf cmiyc_2012_password_hash_files.tar.bz2
```

This expands into 19 different hashdumps including `des`, `md5`, and `ntlm` type encryption. Each of the 19 files contains thousands of password hashes. This should be a great data set to test our cracking capabilities on.

#### John the Ripper
Next we'll need the cracking tool itself. If you're using Kali Linux, this tool is already installed. Download [John the Ripper](http://www.openwall.com/john/) here. In my case I'm going to download the free version `John the Ripper 1.8.0 (sources, tar.gz, 5.2 MB)`.

Once downloaded, extract it with the following linux command:

```
tar zxvf john-1.8.0.tar.gz
```

Then follow the instructions in docs/INSTALL to complete the install. 


#### Getting a Wordlist

We'll need a good wordlist to go through to see if any passwords in it, match our hashes. 

* A basic word list containing 3,559 words can be found bundled in the John the Ripper tarball in the run dir. This is a list of the most common passwords seen in public hash dumps.
* If using Kali linux, a good size wordfile is located at `/usr/share/wordlists/rockyou.txt.gz`. Unzip it with `gunzip` and you've got a good wordfile to workwith.
* A large word list containing 1,493,677,782 words can be found at [crackstation](https://crackstation.net/buy-crackstation-wordlist-password-cracking-dictionary.htm).

The size word list you need depends on your needs. If you have a large hashdump, chances are even cracking 5% of the hashes will result in a victory, which may get you admin access. But if you have a only one password hash, you'll need 100% success rate and probably need a bigger wordlist. 


## Basic John Usage

Use John to begin the cracking with this command:

```
$ john hashes-3.des.txt
Loaded 10297 password hashes with 3741 different salts (descrypt, traditional crypt(3) [DES 32/32])
```

This simple command does the following:

* Detected there are 10,297 password hashes in the file and their salts.
* Auto detected the passwords were DES encrypted
* Will first attempt single crack mode
* Will then attempt to use the built in wordlist (most common passwords) to crack passwords
* Will then go into incremental mode

## Checking Status

While John the Ripper is running, press any key (like enter) to see a status output. Or to check from another terminal you can run `john --status`.  The output looks like this:

```
DES cracking speed: 94g 0:01:08:34 74% 2/3 0.02284g/s 2784p/s 97648c/s 269491C/s day?..Hal?
1                   2   3          4   5   6          7       8        9         10
```

Here is what each section means:

1. Type of encryption it is trying to crack with
2. Number of successful password guesses
3. Time elapsed since started
4. Percent completed for that pass
5. Current pass / number of passes total
6. g/s = successful guesses per second
7. p/s = passwords tested per second
8. c/s = crypts (password hashes) computed per second
9. C/s = crypts tested per second (in versions below 1.8.0 this was "c/s")
10. The current word it's trying.

## Passes/Modes

John has three modes to attempt to crack hashes. If you do not indicate the mode, all 3 will be used and you will see `x/3` in your status output indicating which mode it's on.

See http://www.openwall.com/john/doc/MODES.shtml for detailed description of each mode.

#### (1) Single Crack ####
This mode attempts to mangle the username and try it as the password. Example: if the username was "jackson" it would try the following passwords:

> jackson <br>
> JACKSON <br>
> jackson1 <br>
> j-ackson <br>
> Jackson= <br>
> jacks0n <br>

It tries hundreds of variations of the username. It tries this password on all hashes in your file so the more usernames you give it, the greater chance of it finding something in the single crack mode.

This is a great mode to start with because it's the fastest and sometimes works wonderfully.

To just use this mode do the following:

```
john --single hashes-3.des.txt
```


#### (2) Wordlist Mode ####
In this mode, John is using a wordlist to hash each word and compare the hash with the password hash. If you do not indicate a wordlist, John will use the one it comes bundled with which has about 3,500 words which are the most common passwords seen in password dumps. 

To use try just the wordlist mode do the following:

```
john --wordlist=password.lst hashes-3.des.txt
```

#### (3) Incremental ###

Attempts a brute force style attack, trying every combination of characters possible. This type of attempt will never complete because it will just keep trying higher and higher password lengths. 

To try just the incremental mode, do this command:

```
john --incremental hashes-3.des.txt
```

## Word mangling rules

John has the ability to take a wordlist and mangle the words in it to try variations of that word. It will add numbers to the end of the word and try replacing letters with numbers and adding other random symbols. So if the word list contains the word `jackson`, with `rules` turned on it would try each of these plus hundreds more.

> jackson <br>
> JACKSON <br>
> jackson1 <br>
> j-ackson <br>
> Jackson= <br>
> jacks0n <br>

By simply enabling `--rules` when invoking John, the mangling rules applied are usually decent. However, you can modify the config file to alter the way the mangling is done. Read here for further information on how to do that:

http://www.openwall.com/john/doc/RULES.shtml

Additionally you can see what others have used for rules like KoreLogic:

http://contest-2010.korelogic.com/rules.html

## Final Example

To use a larger word list, with DES encryption only, and rule mangling turned on, use the following:

```
john --format:descrypt --wordlist=crackstation-human-only.txt --rules hashes-3.des.txt
```

The best way to get John to run in the background is using the standard linux `screen` command.


## Resources

http://www.openwall.com/john/doc/

http://www.openwall.com/john/doc/EXAMPLES.shtml


