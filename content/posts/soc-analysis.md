---
title: "Analyzing Threats and Building Detection Rules"
date: 2023-06-04T11:48:54-05:00
draft: false
tags:
 - Blue Team
 - Projects
 - Red Team
---

In this lab, I wanted to hone my skills and conduct a SOC investigation starting from scratch, setting up every aspect and attacking the VMs I deploy to gain a better understanding of how SOCs work. This lab is from [Eric Cupuano](https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part) and I am following his steps so this would not be possible without him. As a security analyst, I expect that one of the most important parts of the job is to detect suspicous activities, mitigate them, then create rules (and also fine-tune them) so that similar attacks cannot happen. 

Step 1 of the lab is to get everything set up. This includes installing VMWare Workstation, installing a Windows VM, configuring the network, disabling some security measures built into Windows, installing Sysmon, installing LimaCharlie, a free Endpoint Detection Response tool, and then finally setting up an attack machine to run exploits on the vulnerable Windows VM. 

First I download the necessary files. This is VMWare Workstation, a Windows 11 VM, and a Ubuntu Server ISO. 

![1](/soc-analyst/1-downloadedfiles.png)

I boot up VMWare Workstation, then add the Windows VM to VMware.

![2](/soc-analyst/2-win11vm.png)

Then I set up my Ubuntu Attack VM.

![3](/soc-analyst/3-loggedintoubuntu.png)

I set up the network settings. Since this lab will be just local and may take multiple days, I do not want the IP addresses to change, so I assign them static IPs.

I allow Ubuntu to install. Give it a quick ping test to make sure it can talk to the internet.

![4](/soc-analyst/4-pingtest.png)

Good to go! Now we power on the windows machine.

It logs in automatically as User and I am presented with the default Windows 11 start screen.

I navigate to Settings, then to the Windows Security settings, then Virus & Threat Protection. I disabled everything here, including tamper protection.

![5](/soc-analyst/5-disabledwin11stuff.png)

Then I ensure Defender cannot be turned back on by disabling it in the Group Policy Editor.

![6](/soc-analyst/6-disabledefender.png)

I also run a command to disable the AntiSpyware registry value for Windows Defender.

![7](/soc-analyst/7-registrycommand.png)

Next, we need to boot Windows into safe mode so we can disable more services in the Registry.

![8](/soc-analyst/8-safemodeboot.png)

Now that I am in Safe Mode, I go to disable the following services:

- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Sense
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdBoot
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisDrv
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisSvc
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdFilter

![9](/soc-analyst/9-disableservices.png)

I find the Start Value for these services and change them to 4. Going through this process really showcases how in-depth an OS can be. I had no idea most of this stuff was here and could be disabled.

Next, I exit safe boot and load up my vulnerable machine.

I disable a bunch of timeout settings to make sure my VM doesn't go idle.

![10](/soc-analyst/10-timeout.png)

Next I setup LimaCharlie, the endpoint detection and response solution I will be using for this lab.

![11](/soc-analyst/11-limacharlier.png)

To do this, first we will need to add a sensor to LimaCharlie. This creates a listener that waits for the installation to be completed on our Windows 11 machine.

![12](/soc-analyst/12-lcaddsensor.png)


Next I install LimaCharlie to our Windows 11 VM.

![13](/soc-analyst/13-lcsuccess.png)

Success!

![14](/soc-analyst/14-sucess2.png)

I also go through the process of installing Sysmon and having those logs get forwarded to LimaCharlie as well. Though I do not use Sysmon much in this lab, it is still a good introduction, and I plan to use it more in the future.

![15](/soc-analyst/15-sysmoninstalled.png)


I will also configure LimaCharlie to ingest the sysmon logs as well.

![16](/soc-analyst/16-sysmonadded.png)

Now that this has been completed, I will continue work on the attack machine. One of the modern luxuries of Windows 11 is that it now has native SSH support, or at the very least we do not have to continue to use Putty! I can SSH from a powershell prompt into my ubuntu machine. This is for ease of copy and pasting, however, I also have this functionality within the VMware software.

![17](/soc-analyst/17-windowsssh.png)

Now that I am in my Ubuntu machine, I will install Sliver, a Command and Control framework by BishopFox. I run these commands as root.

```
# Download Sliver Linux server binary
wget https://github.com/BishopFox/sliver/releases/download/v1.5.34/sliver-server_linux -O /usr/local/bin/sliver-server
# Make it executable
chmod +x /usr/local/bin/sliver-server
# install mingw-w64 for additional capabilities
apt install -y mingw-w64
```
---
Now that sliver is installed, I run some commands to generate a C2 payload to use on the Windows11 VM.

![18](/soc-analyst/18-sliver.png)

Next I spin up a quick python server that delivers (unsecure!) web traffic on port 80. 

```
python3 -m http.server 80
``` 

This allows for an easy download of the payload to the windows machine.

![19](/soc-analyst/19-c2downloaded.png)

I then use Sliver to start an HTTP listener.

![20](/soc-analyst/20-sliverhttp.png)

I activate the payload on my windows machine and I see the session on my Sliver terminal. Crazy stuff. I can see the potential of such a tool with a proper red team engagement, such as phishing, naming the file something else and perhaps taking more steps to help disguise my intentions.

![21](/soc-analyst/21-confirmedlistener.png)
![22](/soc-analyst/22-sessions.png)

I activate the session and I have a crazy amount of access. first I use the info command to get some basic info about the win11 machine.

![23](/soc-analyst/23-sliverinfo.png)

I can also determine what user I am and what privileges they have.

![24](/soc-analyst/24-whoamiprivs.png)

Then I get insight into the processes running on the Windows VM. Tools that Sliver sees as defensive are marked in red, and it's own payloads are green. It notes that Sysmon is installed(wow!). 

![25](/soc-analyst/25-processes.png)

I now go to LimaCharlie, and look through what is being detected. I notice a lot of processes here, and I look forward to becoming more familiar with these processes so I can determine what is benign and what is possibly a threat. With this EDR, most normal processes are signed with a green checkmark indicating they are not a threat though I know that even normal processes can be exploited.

I find my payload and I notice that it is not signed and it is showing as active on the network.

![26](/soc-analyst/26-hashofpayload.png)

LimaCharlie shows where the file originated from, so I navigate there using File System, then going to the user's downloads. I find the file and get the hash of the payload. I then search for the hash on VirusTotal and there are no results, meaning that this file does not exist on VirusTotal, which is a red flag in and of itself. VirusTotal has hashes of many different malware samples and files so our file not being there is suspicious.

Next we use the timeline to see the moment-to-moment telemetry. I add the filter of my payload, and can even see when it was first created!

![27](/soc-analyst/27-timeline.png)

---

After taking some time to analyze some telemetry, I want to now do some purposeful adversarial actions so I can detect it with LimaCharlie. We are going to dump the Local Security Authority Subsystem Service or lsass.exe file from memory, then save it locally to the Sliver C2 server. This is a common tactic that adversaries use to gain access to Active Directory credentials. This is something I will remember when I continue my Red Team studies.

When trying to dump the file I get an error. I do not claim to be an expert in using Sliver or why this error occurred, but what is important is whether we can detect the attack. 

![28](/soc-analyst/28-lsass.png)

I navigate to LimaCharlie, then to the timeline. Since I know that this would be a sensitive process, I type that into LimaCharlie, and sure enough, the attack is there.

![29](/soc-analyst/29-sensitiveprocess.png)

I can see the details around the attack, and now I can create a detection and response rule that will alert anytime this activity occurs.

Here I create a detection that will alert whenever SENSITIVE_PROCESS_ACCESS events end with LSASS.exe. The blog notes that this would be noisy in a production environment and I can see that; there would likely be many events causing this alert to fire a bunch, so it would need to be tweaked.

![30](/soc-analyst/30-lsassrules.png)

I can test that the rule works as expected. I go ahead and save it.

![31](/soc-analyst/31-testlsass.png)

I want test our rule to make sure it works. I run the command from my C2 server again. Once the command runs, I navigate to LimaCharlie's detections screen and I have successfully detected the event based on the rules I just created! I defintely see the power of creating custom rules. However, with the rule in its current state, I am only able to detect when LSASS is accessed and can't really do anything about it.

![32](/soc-analyst/32-detected.png)

--- 
In the blog post I am following, I have learned about a rule that many ransomware attacks tend to take; deleting Volume Shadow Copies.

"Volume Shadow Copies provide a convenient way to restore individual files or even an entire file system to a previous state which makes it a very attractive option for recovering from a ransomware attack. For this reason, it’s become very predictable that one of the first signs of an impending ransomware attack is the deletion of volume shadow copies."

One command that would do this is 
```
vssadmin delete shadows /all
```
This is not a command that would likely be executed often in a healthy environment, but it is one that we would want to be aware of.

I navigate to Sliver, and use the shell command to open a shell terminal on the windows machine. I run the above command, though there were not any shadow copies to delete.

![33](/soc-analyst/33-shell.png)

The malicious activity was detected! I was able to see all the activity and I was able to see the event that correlated with the detections. I navigate to the event timeline and see where I executed the above command.  

![34](/soc-analyst/34-detected!.png)

From here, I can now craft a detection rule based on this activity. I add some actions to take place when this is detected. First, we use the action:report to send a report to the detections tab in LimaCharlie. The action:task and the deny_tree command docs state that it will "Tell the sensor that all activity starting at a specific process (and its children) should be denied and killed. Great for ransomware mitigation."

![35](/soc-analyst/35-de.png)

Lastly, I want to test it out again. I run the vssadmin command and attempt deletion of all the shadow files. If my rule is working as expected, I should get an error when I try to run whoami.

![36](/soc-analyst/36-shelltermed.png)

As expected, the shell was terminated, meaning that the rule works as it should. The process associated with the shadow volume deletion was killed. 

Key Takeaways:

Setting up and going through this lab was an excellent learning experience for learning how to set up and detect threats on a vulnerable machine. This was my first time running a Command and Control tool, as well as defending from something like that. I see why it is so important to have the proper security guards in place to prevent, or at least deter some of the potential threat vectors that attackers can use. Overall, I feel very good about this lab and I'm super excited to learn more. Some potential labs that I would like to explore are hardening Windows and Linux systems to create some defense in depth to help protect against some attacks like this.

Areas Of Improvement:
- I want to use Sysmon more. While there are other ways to get logs and other telemetry, I want to explore the pros and cons of Sysmon.
- I want to learn more about potential indicators of compromise. What is suspicious behavior, what is normal behavior, etc.
- LimaCharlie is a great EDR tool. What are some other tools I could use to investigate suspicious activity?