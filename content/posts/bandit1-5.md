---
title: "Learning Cybersecurity with OverTheWire: Bandit 1 - 5"
date: 2023-04-02T11:02:00-05:00
draft: false
tags:
    - Linux
    - CTF
---

I stumbled across this repository of wargames called OverTheWire. They teach cybersecurity concepts through a gaming like method, and I love games so this seemed like a good place to start. This was highly recommended on reddit so I am documenting my path through the wargames, including difficulties I faced.

OverTheWire recommends starting with Bandit, so I grab a cup of Death Wish Coffee and get to work!

## Bandit Level 0
Level Goal: *The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.*

I boot up my ParrotOS machine, and attempt to log in. I see that it says login with port 2220, but I am curious to see what happens if I just try and SSH. I am promptly denied.

![1sshdenied.png](/bandit1-5/1sshdenied.png)

I enter the proper command. I get some interesting information, and I am now ready to play. I have completed level 0!

![2success](/bandit1-5/2success.png)

## Bandit Level 0 → Level 1
Level Goal: *The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.*

So being logged into Bandit0, my first thought is to use "ls" to display all the files since I'm already in the home directory. Like they mentioned, there is a readme file. I use "cat readme" to display the contents of the file to the terminal.

![3password](/bandit1-5/3password.png)

I have the password, and now I will try to login to the next level.

![4loggedin](/bandit1-5/4loggedin.png)

## Bandit Level 1 → Level 2
Level Goal : *The password for the next level is stored in a file called - located in the home directory.*

Well, I have never actually worked with a '-' as the file name before! I tried 'cat' and 'nano' but it would not open. The hint on the site tells me to Google search it, so I do just that. Reading the answer seems so obvious and that I should have known it, but now I get it. With '-' file names, specifying the entire location of the file will allow you to concatenate it. 

![5dashfile](/bandit1-5/5dashfile.png)

I have that password, and now I will log into the next level.

## Bandit Level 2 → Level 3
Level Goal: *The password for the next level is stored in a file called spaces in this filename located in the home directory*

So this file literally has spaces in the file name. I actually knew this one! If you enclose the file name in quotation marks, you can concatenate the file.

![7spaces](/bandit1-5/7spaces.png)

## Bandit Level 3 → Level 4
Level Goal: *The password for the next level is stored in a hidden file in the inhere directory.*

So in the inhere directory there is a hidden file. If you use the -a flag with ls, you can display all the files, even hidden ones.

![8lscommand](/bandit1-5/8lscommand.png)

## Bandit Level 4 → Level 5
Level Goal: *The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.*

When listing all the files in this directory, I see ten different files. When you concatenate them, most of them give a funky text option in the terminal. However for me, one file gives a readable response, and this file has the flag.

![9funky](/bandit1-5/9funky.png)

Those were the first 5 levels of Bandit. I knew some of these, but I didn't know all of them. It was good to learn more about Linux and get some hands on experience. 