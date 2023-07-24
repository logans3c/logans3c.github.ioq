---
layout: post
title:  "Introductory Networking WriteUp – TryHackMe"
description: this article is a writeup for room1 in intro to network category
date:   2021-07-20 13:49:56 +0300
---


In this article I tried to make a write-up for the "introductory networking" room in [THM](https://tryhackme.com/room/introtonetworking)

> After I will write the answer, I will explain why I choose it

## [Task1] Introduction  :
    
    The topics that will be covering in this room are :
        The OSI Model
        The TCP/IP Model
        How these models look in practice
        An introduction to basic networking tools
            
## [Task2] The OSI Model: An Overview:

    #1《 Which layer would choose to send data over TCP or UDP》

    • Answer : 4

    Cause : The main purpose of Transport Layer is to choose the protocol over which the data is to be transmitted.

    #2《Which layer checks received packets to make sure that they haven’t been corrupte 》

    • Answer : 2

    Cause : The Data link layer serves an important function when it receives data,  
    it checks the received information to make sure that it hasn’t been corrupted during transmission.

    #3《In which layer would data be formatted in preparation for transmission? 》

    • Answer : 2

    Cause : One of the additional jops of Data link Layer is present the data in a format suitable for transmission.

    #4《Which layer transmits and receives data?》

    • Answer : 1

    Cause : The Physical Layer is where the electrical pulses that make up data transfer over a network are sent and received.

    #5《Which layer encrypts, compresses, or otherwise transforms the initial data to give it a standardised format?》

    • Answer : 6
        
    Cause : The Presentation layer translates the data into a standardised format, as well as handling any encryption, compression or other transformations to the data.

    #6《Which layer tracks communications between the host and receiving computers?》

    • Answer : 5 
      
    Cause : The Session Layer is responsible to logged a connection between the host and remote computer.

    #7《Which layer accepts communication requests from applications?》

    • Answer : 7

    Cause : The Application Layer works almost exclusively with applications, providing an interface for them to use in order to transmit data.

    #8《Which layer handles logical addressing?》

    • Answer : 3

    Cause :  The Network layer  takes the IP address for the page and figures out the best route to take. At this stage we’re working with what is referred to as "Logical addressing"

    #9《When sending data over TCP, what would you call the “bite-sized” pieces of data?》

    • Answer : Segments

    Cause : bite-sized pieces (over TCP these are called segments, over UDP they’re called datagrams)
       
    #10《[Research] Which layer would the FTP protocol communicate with?》

    • Answer : 7

    Cause : Check This Map
    
![image](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRf-bkg3kHc7tTv4iOIIkjf5V1vBON0H2N_lw&usqp=CAU)

    #11《Which transport layer protocol would be best suited to transmit a live video?》

    • Answer : UDP

    Cause : UDP would be used in situations where speed is more important.

■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task 3] Encapsulation :

    #1《How would you refer to data at layer 2 of the encapsulation process (with the OSI model)?》

    • Answer : Frames

    Cause : The data in Data link Layer called  "Frames"

    #2《How would you refer to data at layer 4 of the encapsulation process (with the OSI model), if the UDP protocol has been selected?》

    • Answer : Datagrams

    Cause : bite-sized pieces (over TCP these are called segments, over UDP they’re called datagrams)

    #3《What process would a computer perform on a received message?》

    • Answer : de-encapsulation

    Cause : When the message is received by the second computer, it reverses the process.

    #4《Which is the only layer of the OSI model to add a trailer during encapsulation?》

    • Answer : Data Link

    Cause : The Data link Layer adds a piece on at the end of the transmission, which is used to verify that the data has not been corrupted on transmission.
    
    #5《Does encapsulation provide an extra layer of security (Aye/Nay)?》

    • Answer : AYE

    Cause : The data link layer adds a piece on at the end of the transmission, which is used to verify that the data has not been corrupted on transmission; this also has the added bonus of increased security, as the data can’t be intercepted and tampered with without breaking the trailer.

■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task 4] The TCP/IP Model:

    #1《Which model was introduced first, OSI or TCP/IP?》

    • Answer : TCP/IP

    Cause : TCP/IP Model is fewer years older the OSI model

    #2《Which layer of the TCP/IP model covers the functionality of the Transport layer of the OSI model 》

    • Answer : Transport 

    Cause : same supposes 

    #3《Which layer of the TCP/IP model covers the functionality of the Session layer of the OSI model 》

    • Answer : Application 

    Cause : Same Functionality 

    #4《The Network Interface layer of the TCP/IP model covers the functionality of two layers in the OSI model. These layers are Data Link and?.. (Full Name)?》

    • Answer : Physical 

    Cause : absolutely you know the cause. 

    #5《Which layer of the TCP/IP model handles the functionality of the OSI network layer?》

    • Answer : Internet

    Cause : Same Functionality 

    #6《What kind of protocol is TCP》

    • Answer : Connection-based

    Cause : ********

    #7《What is SYN short for》

    • Answer : Synchronise

    Cause : SYN is short for Synchronise

    #8《What is the second step of the three way handshake?》

    • Answer  : SYN / ACK

    Cause : It's step 2 👉👈

    #9《What is the short name for the “Acknowledgement” segment in the three-way handshake?》

    • Answer : ACK

    Cause : Acknowledgement is long for ACK

■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

> In this part of the Writeup I will explain how and why to do, not just the cause like another part.
  
■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task5] Ping :

    #1《What command would you use to ping the bbc.co.uk website?》
    
    • Answer : ping bbc.co.uk

    #2《Ping muirlandoracle.co.uk What is the IP address?》
    
    • Answer :

![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/ping%231.png)

    #3《What switch lets you change the interval of sent ping requests?》
    
    • Steps : 1- open your terminal
              2- type : man ping
              3- read the manual of ping command
    • Answer : -i
    
    #4《What switch would allow you to restrict requests to IPV4?》
    
    • Steps : Do the previous steps
    
    • Answer : -4
    
    #5《What switch would give you a more verbose output?》
    
    • Steps : Do the previous steps
    
    • Answer : -v

■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task6] Traceroute :

    #1《Use traceroute on tryhackme.com Can you see the path your request has taken?》
    
    • Steps : Type : traceroute tryhackme.com
    
    • Answer : No answer needed
    
    #2《What switch would you use to specify an interface when using Traceroute?》
    
     • Steps : 1- type : man traceroute in your terminal
               2- read the manual of traceroute command
     • Answer : -i
    
    #3《What switch would you use if you wanted to use TCP SYN when tracing the route?》
    
    • Steps : Do the previous steps
    
    • Answer : -T
    
    #4《[Lateral Thinking] Which layer of the TCP/IP model will traceroute run on by default (Windows)?》
    
    • Answer : Internet
    
    • Cause : By default, the Windows traceroute utility (tracert) operates using the same ICMP protocol that ping utilises, and the Unix equivalent operates over UDP. This can be altered with switches in both instances.
    
■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task7] WHOIS :

    #1《Perform a whois search on facebook.com 》
    
    • Answer : just type (whois facebook.com) in your terminal
    
    #2《 What is the registrant postal code for facebook.com?》
    
    • Answer : 

![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/whois%231.png)

    #2《When was the facebook.com domain first registered? 》
    
    • Answer : 

![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/whois%232.png)

    #4《Perform a whois search on microsoft.com 》
    
    • Answer : just type (whois microsoft.com) in your terminal.
    
    #5《Which city is the registrant based in? 》
    
     • Answer :
     
![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/whois%233.png)

    

    #5《[OSINT] What is the name of the golf course that is near the registrant address for microsoft.com? 》
    
    • Answer :
    
![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/whois%234.png)

    #7《What is the registered Tech Email for microsoft.com? 》
    
    Answer :

![image](https://raw.githubusercontent.com/0xM00D/0xm00d.github.io/master/media/whois%235.png)

■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■

## [Task8] DIG :
    
    #1《What is DNS short for? 》
    
    • Answer : Domain Name System
    
    #2《What is the first type of DNS server your computer would query when you search for a domain? 》
    
    • Answer : Recursive
    
    Cause : The first thing that your computer does is check its local cache to see if it's already got an IP address stored for the website; if it does, great. If not, it goes to the next stage of the process.
    Assuming the address hasn't already been found, your computer will then send a request to what's known as a recursive DNS server.
    
    #3《What type of DNS server contains records specific to domain extensions (i.e. .com, .co.uk*, etc)*? Use the long version of the name. 》
    
    Answer : Top-Level Domain
    
    #4《Where is the very first place your computer would look to find the IP address of a domain? 》
    
    Answer : Local Cache
    
    5#《[Research] Google runs two public DNS servers. One of them can be queried with the IP 8.8.8.8, what is the IP address of the other one? 》
    
    Answer : 8.8.4.4

![image](https://www.lifewire.com/thmb/PfYyQpj2IYPnwe1BDj_vCJ0TrMQ=/1179x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/google-public-dns-4deb7e959ce34bdb9f2b9fe13d0a56a0.png)

    #6《If a DNS query has a TTL of 24 hours, what number would the dig query show? 》
    
    Answer : 86400
    
    Cause : 60*60*24


> The
> END

### You can reach me via [linkedin](https://www.linkedin.com/in/logan0x/). I thank everyone who reads and wish you good days. See you in next write-up …
    
    
