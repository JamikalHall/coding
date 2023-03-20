---
title: "Linux Basics For Hackers Notes: Chapter 1"
date: 2023-03-20T10:08:47-05:00
draft: true
tags:
    - Blue Team
    - Linux
    - Notes
---
I originally wrote this a year ago on Medium. I have gone through these steps again, but I am trying ParrotOS instead of Kali. I did this to gain experience with a different distribution and in case I want to do exercises on HTB.

Linux Basics for Hackers Notes: Chapter 1

The first thing I did for this was set up my Kali Linux VM. I downloaded Oracle’s VM box and set it up according to the instructions in the book. Next, I took notes on important definitions that may come up again, as well as general knowledge given in the book. Below are these definitions in my own words.

    Binaries — Files that can be executed, the Linux equivalent of .exe files on Windows.
    Case Sensitivity — Linux is case sensitive unlike Windows, so a file called DESKTOP.txt would be completely different from a file called desktop.txt.
    Directory — equivalent to a folder in Windows. A way to organize files.
    Home — a directory where the files a user creates will be saved by default (usually).
    Kali — The distribution of Linux specifically designed for pentesting.
    root — Linux’s admin/superuser account, designed to allow a user just about complete control of the entire system.
    script — A series of commands run in an interpreter, which converts the line into instructions the computer can act up.
    shell — An interpreter for running commands in Linux. The most common is Bash (bourne-again shell).
    Terminal — a command line interface.

Chapter one also includes a diagram of the Linux file structure.

![lb4hackers1.png](/lb4hackers1.png)

The root (/) of the filesystem is at the top of the tree, and the following are the most important subdirectories to know:

    /Root The home directory of the all-­powerful root user
    /etc Generally contains the Linux configuration files — files that control when and how programs start up
    /Home The user’s home directory
    /mnt Where other filesystems are attached or mounted to the filesystem
    /Media Where CDs and USB devices are usually attached or mounted to the filesystem
    /Bin Where application binaries (the equivalent of executables in Microsoft Windows) reside
    /Lib Where you’ll find libraries (shared programs that are similar to Windows DLLs)

## Next, Chapter 1 discusses basic Linux commands to navigate the filesystem.

**pwd**— present working directory. This command will show you the current directory you are in.

**whoami** — tells you what user you are currently logged as.

**cd** — Change directory. This combined with a directory location, such as /home or /etc, will change your directory to where you specify, as long as it is valid. You can also use two dots ‘..’ to move up a directory level. You can continually combine these to continually move up levels in the file structure.

**ls** — list will show the files and directories contained in the current directory. You can list the contents of other directories as well. Adding the -l switch will provide more detailed info about the files in the directory. Adding the -a switch will list hidden files.

## These two commands are for getting assistance with a tool or command.

**help** — using this command after another, such as aircrack-ng, will provide the tool’s dedicated help page to show how to use it(assuming the tool has a help page). -h and -? also provide help pages.

**man** — the manual page will give even more detailed guidance for a tool or command

## These commands talk about finding things in Linux.

**locate** — this command, followed by a keyword specifying what it is that you are looking for, will go through your entire filesystem and return every occurrence of that word.

**whereis** — this command is used to find the location of a binary file, as well as its source and man page if they are available.

**which** — only returns the location of binaries within the PATH variable, which is what the operating system uses to determine what directories to look for the commands you execute at the command line.

**find** — the most powerful search tool; allows you to specify a directory and several other parameters such as date of creation or modification, the owner, the group, permissions and the size.

**grep** — can be used as a filter to search for keywords. It is often used when output is piped from one command to another.