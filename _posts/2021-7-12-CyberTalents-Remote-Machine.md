---
layout: default
title:  "How to pwn cybertalents remote machine"
description: Writeup for "remote" Machine
tags: CyberTalents
date:   2021-07-10 13:49:56 +0300
categories: jekyll update
---
### Hi there! today we well pwn “remote” machine on CyberTalents :  
![whois](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/whois%234.png)  
  
> In the beginning, I want to inform you that you can pwn this machine in a very simple way using Metasploit but in this writeup, we will use a more difficult way to pwn it.  
  
Now Let's Start ..  
  
**Enumeration** :  
![nmpa_scan](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/nmap%20scan.png)  
  
 The scan told us there is http service at port 1337, let's take a look  
![the site](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/website%20photo.png)  
  
As we saw in the scan and on the site, the site is running on a HttpFileServer 2.3  
  
so let's search for exploit :  
![searchsploit](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/searchsploit.png)  
  
 
#### Let's first know simply what is the affected part and how it can be exploited :   
 The infected parameter is the search parameter. If we type %00{.exec|cmd.} in the
 search box, the cmd will open in the target machine.  
 The exec command is part of the infected software, there are save, read , write, and  many other commands  
 For more : [https://nvd.nist.gov/vuln/detail/CVE-2014-6287#vulnCurrentDescriptionTitle](https://nvd.nist.gov/vuln/detail/CVE-2014-6287#vulnCurrentDescriptionTitle)  
  
  
so to get reverse shell we need to upload netcat to the victim machine and then execute it  
and we well use the previous script to helping us  
  
now let's copy this script to our machine directory to edit it :  
  
```bash  
searchsploit -m windows/remote/39161.py  
```  
then let's take a look on the script to know how it works ...

```bash
gedit 39161.py
```


#### After two minutes of analysis I've understood what Script do :
    The script contains three main functions
	One to save a subscript that downloads netcat
	And one for executing this subscript
	And the third is to run the netcat
	
### To implement these three functions ,We have to do three things
#### First, we modify ngrok to help us do the second and third steps
    We need to add some modification to ngrok to make it running multiple simultaneous tunnels, one for HTTP and one for TCP
    To see how we can do this: https://stackoverflow.com/questions/25522360/ngrok-configure-multiple-port-in-same-domain
   
   - This is my config ![ngrok_config](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/ngrok_config.png)

#### Second, we create a web server and upload the netcat on it
- We will use ```python -m SimpleHTTPServer {port-num}```
- But you should use this command in the nc.exe directory , you can find  nc.exe and many useful tools in usr/share/windows-resources/binaries

#### Third, we need to run ngrok
- In this situation, we will run ngrok with this command
```
./ngrok start --all
```

## Now let's edit the python script
#### first, we need to change ip_addr variable to the HTTP web server URL that ngrok gave us

#### second ,  we need to change ip_addr in vbs3 variable to the TCP link that ngrok gave us

#### Third , we need to change the local_port variable to the port of the TCP link that ngrok gave us.

#### Final look 
![script_image](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/script_image.png)


#### Exploit 
![get_shell](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/get_shell.png)
It works ! And we get reverse shell

#### Post Exploitation
![postexp#1](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/postexp%231.png)

I tried to put the flag as written but it was wrong  
I searched for about 10 minutes until I found a folder called mRemoteNG In C:\Program Files (x86)  
I googled that name and found that it is a program that allows you to view all of your remote connections in a simple yet powerful tabbed interface.  
Now we need to know what version of the software we have installed to search for an exploit
 
#### to do that we have two step
- first ,  Run WMIC  
Type wmic and press Enter.  

- Second ,  Pull list of installed applications  
At the wmic:root\cli> prompt, type the following command:
```
 /node:TargetComputerNameHere product get name, version, vendor
```

After a few moments, a list will be displayed in the command prompt detailing the programs installed on the target computer.
##### My operation 
![version](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/version.png)

#### Now let's search for an Exploit  

I found this awesome blog [https://ethicalhackingguru.com/how-to-exploit-remote-connection-managers/](https://ethicalhackingguru.com/how-to-exploit-remote-connection-managers/)  

I did what the blog said in my terminal :  
![password](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/password.png)  

let's decrypt the password using this github script  
[https://github.com/haseebT/mRemoteNG-Decrypt](https://github.com/haseebT/mRemoteNG-Decrypt)  

![decrypt](https://raw.githubusercontent.com/logans3c/logans3c.github.io/master/media/remot_cybertalents/decrypt.png)


#### I hope this writeup was useful

### you can reach me on social media platforms on username: logan0x
### [MY LINKEDIN](https://www.linkedin.com/in/logan0x/)