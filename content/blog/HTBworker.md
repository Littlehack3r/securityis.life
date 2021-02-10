+++
showonlyimage = true
draft = false
image = "img/worker.png"
date = "2016-11-05T18:25:22+05:30"
title = "HackTheBox - Worker"
weight = 2
+++

<!--more-->

HackTheBox is an online trainings platform. I performed this engagement on the HackTheBox pwnmachine. 

Worker is qualified as a "Medium" box and is rumored to have Azure components. 

Let's get started!

`nmap 10.129.2.29 -sV -sC`
>	-sV: probe open ports to determine service/version
>	-sC: performs a script scan and is the equivalent to --script=default

![Image1](/img/worker1.png)

The target's network had two ports exposed. Port 80 (HTTP) and port 3690 (TCP/UDP). Port 3690 was hosting a svnserve program which is a "lightweight" server that allows customers to communicate over TCP/IP. Port 80 was hosting a Microsoft server. 

I started down the port 80 rabbit hole first, but accessing it through the browser 

![Image2](/img/worker2.png)

When the website is clicked on, the user is brought to the Microsoft ISS homepage: 
> https[://]www.iis[.]net/?utm_medium=iis-deployment

Perhaps we can use this later, but for now I will focus my attention back on the SVN server. 

The first thing to do is to install SVN on my computer using:  

`sudo apt-get install subversion`

To figure out which commands to run, I looked at the SVN documentation. The [man-page](https://www.unix.com/man-page/redhat/1/svn/) claims: 
> list url [url ...] [--username name] [--password pass] [--no-auth-cache] [-rvR]> (Alias: ls) List directory entries of a URL."

I knew "list" was the command to start us but what was the correct URL? The server's documentation showed: "Clients contact an [svnserve](https://durak.org/sean/pubs/software/version-control-with-subversion-1.5/svn.serverconfig.svnserve.html) server by using URLs that begin with the svn:// or svn+ssh:// scheme."

Therefore I determined the command to be: 

`svn list svn://10.129.2.29`

![Image3](/img/worker3.png)


We see a file and a directory hosted at this URL. I went back to the man page to run other commands that might be useful.

`svn log svn://10.129.2.29 ` shows me that the user "nathen" is primarily using this server. 

![Image4](/img/worker4.png)



I do not have permission to "cat" or "checkout" the repository's contents


![Image5](/img/worker5.png)

![Image6](/img/worker6.png)


 
Clearly, I needed credentials to access anything. 

My next option on the man-page is to use diff. 


> diff [target...] [--username name] [--password pass] [--no-auth-cache] [--no-diff-deleted] [-rxN]
	      (Alias: di) Display file changes as contextual diffs. The target can be a directory, in which it operates  recursively.  The  target can be an URL, although this is only useful if two revisions are also given. 

Based on these directions, I provided the following command to look at the difference in the first 2 revisions.

`svn diff -r 1:2 svn://10.129.2.29`

![Image7](/img/worker7.png)


Hello nathen! We see you really care for wendel and have made them your password. Yippie! 

Although it is great to have credentials so early, I realized I do not actually need them when I went to check out 

As you can see, the permission was denied on my end because I was not using sudo. Turns out I could have saved myself some time. It is always good to "sudo !!" At the beginning of work. 

![Image8](/img/worker8.png)


No credentials were even needed to check out the repo, but they will come in handy later. 

![Image9](/img/worker9.png)

Either way, I now have all the files. Dimension.worker.htb is clearly a website. However, the "moved.txt" file has informed us the website has been moved to devops.worker.htb. I tried to go to that domain, but came back with the following error. 


![Image10](/img/worker10.png)



I needed to add the domains to /etc/hosts. This will be used by the browser to map hostnames before DNS can be referenced. 

![Image11](/img/worker11.png)


Oh look we have a log in prompt now!
![Image12](/img/worker12.png)


By using nathen's credentials, I was able to access the Azure DevOps App

![Image13](/img/worker13.png)

There were 3 members: Administrator, Nathalie Henley and restorer

![Image14](/img/worker14.png)


Work Items shows emails of employees, guests, and admins


![Image15](/img/worker15.png)

![Image16](/img/worker16.png)


Pipelines has websites that have been built. After adding each of these website into /etc/hosts I visited them and found every website except for cartoon.worker.htb is a HTML5 UP template. Not much to do from there.

Spectral.worker.htb has a repo within the Devops environment. I believe I have access to upload files to the repo. Let's try a shell. 

![Image17](/img/worker17.png)


Located in `/opt/useful/SecLists/Web-Shells/FuzzDB/nc.exe` of the HTB pwnbox is a reverse shell. 

![Image18](/img/worker18.png)

![Image19](/img/worker19.png)

![Image20](/img/worker20.png)

![Image21](/img/worker21.png)



But nothing happened

![Image22](/img/worker22.png)


In many of the writeups I used for reference they used .aspx shells before uploading nc.exe. 

ASPX  (Active Server Page Extended) files are used by Microsoft's ASP.NET framework.  Because I am attacking a Microsoft IIS server, this is the best option. I need this shell first so I can run the command to initiate nc.exe's callback. 

![Image23](/img/worker23.png)

I modified the .aspx file to contain the IP of my local computer and the port I will listen to.



![Image24](/img/worker24.png)

![Image25](/img/worker25.png)

![Image26](/img/worker26.png)

The file was successfully uploaded!
When the correct Url was visited, I received access to a shell.

![Image27](/img/worker27.png)

HTB objectives are to find a user.txt file on a user and to access Administrator/Desktop/root.txt. Therefore, when I enter a HTB system, I will check all the users. In this case, I see the two custom users are "restorer" and "robisl". "restorer" contained a note that said HTB uses this user to prevent cheating. Therefore, I had to focus my efforts on robisl.


![Image28](/img/worker28.png)

[This writeup](https://tyr4n7.io/htb-writeup-worker/#) showed me a very cool trick for getting SVN passwords 

`wmic logicaldisk get caption` to see what drives this device contains 

![Image29](/img/worker29.png)

Check each of these drives for "svnrepos"
![Image30](/img/worker30.png)


The command `type passwd` will cat the passwd file containing all the SVN credentials, including robisl's

![Image31](/img/worker31.png)

We now have credentials which can be used to access the user with evil-winrm

`Evil-winrm -I 10.129.75.178 -u <username> -p <password>`

![Image32](/img/worker32.png)


When I tried to add robsil to the administrator group from the shell, access was denied. 

![Image33](/img/worker33.png)

I logged into robisl's Azure account to see if there was any way I could escalate privileges.  

![Image34](/img/worker34.png)

Thankfully, the Pipeline feature in Azure DevOps allows a user to run test code. With this feature, I was able to run a command on the host with the following steps.  

![Image35](/img/worker35.png)

![Image36](/img/worker36.png)

![Image37](/img/worker37.png)


Just like with the shell earlier, I had to make this a pull request to run it, but I can self-approve the merges.

![Image38](/img/worker38.png)

![Image39](/img/worker39.png)

![Image40](/img/worker40.png)

![Image41](/img/worker41.png)


The action was successful! Robisl was immediately added to the admin group. 

![Image42](/img/worker42.png)

However, access to view the juice was denied until I logged out and back in using evil-winrm.

![Image43](/img/worker43.png)


After logging in and out


![Image44](/img/worker44.png)