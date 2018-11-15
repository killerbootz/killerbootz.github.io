---
layout: post
title: SolidState Walkthrough
published: true
---
This is a walkthrough of the CTF machine "SolidState" (originally on HackTheBox), now on [Vulnhub](https://www.vulnhub.com/entry/solidstate_1,261/). There are many writeups like it, and this one is mine.


 
      ****Spoiler Alert****          ****Spoiler Alert****




![SS Web]({{ site.url }}/images/Bulldog2/web.JPG)  

This was a great classic CTF, with some twists and multiple ways to gain a low privilege shell. Let's grab our nmap scan and see what our options are:

![SS Nmap]({{ site.url }}/images/Bulldog2/nmap.JPG)

Ok, so when I see any service that looks like something other than generic (like sendmail or postfix), or bigname (like MS), I tend to think that it's a great place to look for public exploits. This of course is not always the case, but in the case of the JAMES smtpd server it is. When I look this service up on [Exploit-DB](https://www.exploit-db.com/) we see the following:

![SS Exploits]({{ site.url }}/images/Bulldog2/exploits.JPG)  



