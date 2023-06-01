---
title: "Introduction to Containers"
date: 2023-06-01T18:23:23-05:00
draft: false
tags:
    - DevOps
    - Notes
    - TryHackMe
---

Learning Outcomes:

By completing this room, you will know:
- What containerisation is and what containers are
- Where and why containerization is used?
- A fundamental understanding of the popular containerisation technology called Docker
- What makes Docker so popular
- How containerization works

## What is Containerization?

Basically, containerization is the process of packaging an application and all of its necessary resource such as libraries, packages, and dependencies into one package named a container. This is useful because many modern apps require frameworks and libraries to be installed before running them. This also lowers the computational overhead compared to running a Virtual Machine, as with VMs, you would also need an entire OS and other resources like CPU and RAM just to run the application.

Question 1: 
What is the name of the kernel feature that allows for processes to use resources of the Operating System without being able to interact with other processes? 

Answer: namespace

Question 2:
In a normal configuration, can other containers interact with each other? (yay/nay)

Answer: nay

## Intro to Docker

Docker is a popular open source containerization platform. It allows users to upload images to be deployed, managed and shared with ease within it's ecosystem.

Docker employs the same technology used in containerization to isolate applications into containers called the Docker Engine. The Docker Engine is essentially an API that runs on the host operating system, which communicates between the operating system and containers to access the system’s hardware (such as CPU, RAM, networking and disk).

Because of this, the Docker engine is extensive and allows you to do things like:
 - Connect containers together (for example, a container running a web application and another container running a database)
 - Export and import applications (images)
 - Transfer files between the operating system and container

Question 3: 
What does an application become when it is published using Docker? Format: An xxxxx (fill in the x's) 

Answer: An image

Question 4:
What is the abbreviation of the programming syntax language that Docker uses?

Answer: YAML

## History of Docker
Question 5: 
In what year was Docker originally created?

Answer: 2013

Question 6: 
Where was Docker first showcased?

Answer: Pycon

Question 7: 
What version of Unix had the first concepts of containerisation?

Answer: v7

There are many benefits to using a Docker container: its open-source, minimal, cheaper to run (uses less resources compared toa a VM), cross-platform, and it is easy to share Docker images with others.

## How Does Containerization Work?

Namespaces essentially segregate system resources such as processes, files and memory away from other namespaces.

Question 8:
What command can we use to view a list of running processes? 

Answer: ps aux

## Practical
Question 9: 
Containerise the applications in the static site. What is the flag? 

Answer: 
THM{APPLICATION_SHIPPED}
