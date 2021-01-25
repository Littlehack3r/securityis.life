+++
showonlyimage = true
draft = false
image = "img/aocday7.png"
date = "2021-25-05T18:25:22+05:30"
title = "AOC - Day7 - Networking"
weight = 0
+++

Basics of wireshark!

<!--more-->

Disclaimer: TryHackMe has released walkthroughs with each of their daily challenges. My walkthroughs are to track my thought process and as I go through these challenges. It is a way for me to deeply understand the material and practice report writing. I publish these walkthroughs to hold myself accountable and because it is super fun to look back on old work to see how far I have grown. Future Shannon, you better be better than this! (also ur pretty go you keep killing it) 

*Open "pcap1.pcap" in Wireshark. What is the IP address that initiates an ICMP/ping?*

I only had to scroll a little to spot an ICMP request. 

![Image1](/img/Day7.1.png)

However, it is important to note the proper way to search for this in Wireshark is with the following filter:
Icmp.resp_in

![Image2](/img/Day7.2.png)

As seen in both screenshots, the answer is 10.11.3.2

*If we only wanted to see HTTP GET requests in our "pcap1.pcap" file, what filter would we use?*

http.request.method == "GET"

![Image3](/img/Day7.3.png)

*Now apply this filter to "pcap1.pcap" in Wireshark, what is the name of the article that the IP address "10.10.67.199" visited?*

A bit of a needle in the haystack, but the only file I could see without an extension was "reindeer-of-the-week".  

![Image4](/img/Day7.4.png)

*Let's begin analysing "pcap2.pcap". Look at the captured FTP traffic; what password was leaked during the login process?
There's a lot of irrelevant data here - Using a filter here would be useful!*

Filter: ftp
Password Leaked: plaintext_password_fiasco

![Image5](/img/Day7.5.png)

*Continuing with our analysis of "pcap2.pcap", what is the name of the protocol that is encrypted?*

SSHv2 is clearing using Diffie-Hellman to encrypt the traffic. 

![Image6](/img/Day7.6.png)

Analyse "pcap3.pcap" and recover Christmas!
*What is on Elf McSkidy's wishlist that will be used to replace Elf McEager?*

I was able to search the packet's strongs for "wishlist.txt" and found a zip file. I exported the zip file and viewed its contents. 
![Image7](/img/Day7.7.png)
![Image8](/img/Day7.8.png)
![Image9](/img/Day7.9.png)
![Image10](/img/Day7.10.png)

Answer: Rubbery Ducky


















