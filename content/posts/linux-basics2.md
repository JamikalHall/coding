---
title: "Linux Basics For Hackers Notes: Chapter 2"
date: 2023-06-01T15:45:06-05:00
draft: true
---

“In Linux, nearly everything you deal with directly is a file, and most often these will be text files; for instance, all configuration files in Linux are text files.”

It is crucial to be able to effectively manipulate text files in Linux and Linux apps. This chapter introduces several commands and techniques for doing so.

cat is the most basic command for text display, but it has limitations. Doing as the book says, running the cat command on etc/snort/snort.conf will display the entire configuration file, which is incredibly long, and this is not an efficient or practical way to view and work with the file.

**Head** and Tail are two commands that are used to display the beginning and ending lines of a file respectively. head will by default, display the first 10 lines and tail will display the last 20 lines.

Both of these can be configured to display more or less by using the ‘-’ switch to specify the exact number of lines you want to display.

For very long files, displaying the line count may be useful. You can do this with the NL (number lines) command.

Grep is the most widely used text manipulation command, as it allows you to filter the content of a file or display. For example, with the conf file, you could display the file using cat, then pipe that result into ‘grep output’, and you would only display lines that include the word output.

The sed command lets you search for occurrences of a word or a text pattern and then perform some action on it. The name of the command is a contraction of stream editor, because it follows the same concept as a stream editor. In its most basic form, sed operates like the Find and Replace function in Windows. Here is an example below.

For working with larger files, the commands More and Less are useful. More shows the contents of a file one page at a time, which is helpful if you only need to see a certain page of a file. Less is similar to More, but with additional functionality. With Less, you can scroll through a file, but you can also filter it for terms.

Here are the exercises for Chapter 2.

    Navigate to /usr/share/wordlists/metasploit. This is a directory of multiple wordlists that can be used to brute force passwords in various passwordprotected devices using Metasploit, the most popular pentesting and hacking framework.

2. Use the cat command to view the contents of the file passwords.lst.
I had a typo! At first, I thought I had the wrong installation or was in the wrong directory, so I just used -ls to see everything and lo and behold, it just turns out that I cannot type sometimes!

3. Use the more command to display the file passwords.lst.

4. Use the less command to view the file passwords.lst.

5. Now use the nlcommand to place line numbers on the passwords in passwords.lst. There should be 88,396 passwords.
I got 88,397!

Use the tail command to see the last 20 passwords in passwords.lst.
I typed the command too fast, but you can see that the last passwords match.

7. Use the cat command to display passwords.lst and pipe it to find all the passwords that contain 123

Summary: A fun and quick chapter. I definitely see some the power that Linux offers if you take some time to familiarize yourself with the commands. The fact that you can do all of these things from the command line is what makes Linux so interesting to learn.