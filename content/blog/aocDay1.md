+++
showonlyimage = true
draft = false
image = "img/aocday1.png"
date = "2016-11-05T18:25:22+05:30"
title = "AOC - Day1 - HTTP Cookies"
weight = 0
+++

<!--more-->

Advent of Cyber's first challenge was related to web cookies. 

A web cookie is a packet of data that a computer receives and sends back without changing it. They are used to help browsers track individual users' activity. They allow us to use a website without logging in every time, let us know if we have already clicked a link and help us save time by filling out our remembered name on forms.  

Disclaimer: TryHackMe has released walkthroughs with each of their daily challenges. My walkthroughs are to track my thought process and as I go through these challenges. It is a way for me to deeply understand the material and practice report writing. I publish these walkthroughs to hold myself accountable and because it is super fun to look back on old work to see how far I have grown. Future Shannon, you better be better than this! (also go you keep killing it) 

What is the name of the cookie used for authentication?

	1. Enter given IP into the browser
	2. Inspect page (crtl + shift + i)
	3. View the name of the cookie
Answer: Auth 

![Image1](/img/Day1.1.png)

In what format is the value of this cookie encoded?

Finding the cookie value was easy: 7b22636f6d70616e79223a22546865204265737420466573746976616c20436f6d70616e79222c2022757365726e616d65223a227573657231227d

Figuring out what was used to encode it was a little bit harder. 

Thankfully, CyberChef has a decoder that allows you to check against multiple ciphers and see examples of their encoded results. 

As you can see, Hexadecimal was our winner!

![Image2](/img/Day1.2.png)


Having decoded the cookie, what format is the data stored in?

I thought to myself, is this a dictionary? JavaScript? Well it was kind of both. The answer is JSON. 

JSON stands for JavaScript Object Notation and usually presents itself in name/value pairs. It can also be an ordered list of values like an array or list. 

What is the value of Santa's cookie?

By changing the username to "santa", instead of "user1", and encoding it into Hex, we are able to predict Santa's cookie. 

7b22636f6d70616e79223a22546865204265737420466573746976616c20436f6d70616e79222c2022757365726e616d65223a2273616e7461227d


