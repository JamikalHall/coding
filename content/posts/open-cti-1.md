---
title: "Investigating Malware with OpenCTI"
date: 2023-06-04T11:48:54-05:00
draft: false
tags:
 - TryHackMe
 - Blue Team
 - Projects
---

In this post, I document how I used OpenCTI, an open-source threat intelligence platform to investigate malware.

*"As a SOC analyst, you have been tasked with investigations on malware and APT groups rampaging through the world. Your assignment is to look into the CaddyWiper malware and APT37 group. Gather information from OpenCTI to answer the following questions."*

**Question 1**: 
What is the earliest date recorded related to CaddyWiper?  Format: YYYY/MM/DD

To find the answer to this question, I first search for CaddyWiper. There is only 1 result so I click into it. 

![1](/opencti/1.png)

From here, I am looking for dates. I see March as a potential option, but looking further I can see the reports section, and the earliest on is March 15, 2022.

![2](/opencti/2.png)

**Answer**: 2022/03/15

**Question 2**:
Which Attack technique is used by the malware for execution?

Currently I am on the overview screen. I am looking for any mention of attack techniques, so I think of the kill chain, which is a set of actions taken by an adversary to exploit their objectives(or simply put, how an attacker works their way through a system). I won't find it on the Overview page, so I navigate to Knowledge.

![3](/opencti/3.png)


On the knowledge page, I can see documentation on the Global Kill Chain. I want to know more about execution, so I scroll down and I learn that CaddyWiper has the ability to "dynamically resolve and use APIs."

![image](/opencti/4.png)

 I don't immediately know what that means so I investigate a bit further by going to the MITRE ATT&CK site to get more info. While this part is not specifically relevant to the question being asked, I want to understand a bit more on how this particular malware works.

From MITRE, 

*"Adversaries may interact with the native OS application programming interface (API) to execute behaviors. Native APIs provide a controlled means of calling low-level OS services within the kernel, such as those involving hardware/devices, memory, and processes.[1][2] These native APIs are leveraged by the OS during system boot (when other system components are not yet initialized) as well as carrying out tasks and requests during routine operations."*

In other words, CaddyWiper can utilize the APIs used by the OS to do what it needs to do. 

**Answer**: Native API

**Question 3**: 
How many malware relations are linked to this Attack technique?

This question is specifically asking about the Native API attack pattern. I click into it's Overview page, then it's knowledge page, which shows 113 Malware relations. This is showing the amount of malware in this database that has been reported using this technique.

![image](/opencti/5.png)

From here, we can access the side panel and navigate to Arsenal > Malware to see the reported malware.

**Question 4**:
Which 3 tools were used by the Attack Technique in 2016? (Ans: Tool1, Tool2, Tool3)

In a similar vein, in order to see the Tools, you would navigate to Arsenal > Tools.You then see the three tools from 2016.

![image](/opencti/6.png)

**Answer**: Bloodhound, Empire, ShimRatReporter

**Question 5**: What country is APT37 associated with?

Doing a search for APT37 will reveal the Intrusion Set that they belong to. Here you see that they belong to North Korea.

![image](/opencti/7.png)

**Question 6**: Which Attack techniques are used by the group for initial access? (Ans: Technique1, Technique2)

On APT37's page, you can navigate to Knowledge > Global Kill Chain and find the codes representing the techniques APT37 uses for initial access.

![image](/opencti/8.png)

**Answer**: T1189, T1566

And with that, we are done with the room!

Key Takeaways:

This was another fun lab to go through and actually learn how to use the OpenCTI platform for threat intelligence. Of course, learning how to actually use the platform was the biggest hurdle, but once I overcame that, using OpenCTI was very straightforward. This room has shown me another useful resource to look for information on known threats and 
gain more insight as to how they work. OpenCTI also has information on APTs and organizations so I can see why this would helpful to analysts and engineers. 
