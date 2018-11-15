---
layout: post
title: SolidState Walkthrough
published: true
---
This is a walkthrough of the CTF machine "SolidState" (originally on HackTheBox), now on [Vulnhub](https://www.vulnhub.com/entry/solidstate_1,261/). There are many writeups like it, and this one is mine.


 
      ****Spoiler Alert****          ****Spoiler Alert****




![SS Web]({{ site.url }}/images/SolidState/web.JPG)  

This was a great classic CTF, with some twists and multiple ways to gain a low privilege shell. Let's grab our nmap scan and see what our options are:

![SS Nmap]({{ site.url }}/images/SolidState/nmap.JPG)

Ok, so when I see any service that looks like something other than generic (like sendmail or postfix), or bigname (like MS), I tend to think that it's a great place to look for public exploits. This of course is not always the case, but in the case of the JAMES smtpd server it is. When I look this service up on [Exploit-DB](https://www.exploit-db.com/) we see the following:

![SS Exploits]({{ site.url }}/images/SolidState/exploits.JPG)  

Right off the bat I dive into the RCE, but notice that it's an authenticated RCE. Commands are executed in the context of a user that will already have access to the server and I do not have that access....yet.  

Sometimes the best way is the way of patience, and learning (in fact it's usually the best way) and here that means perusing the paper "Exploiting Apache James Server 2.3.2". By looking at this we see that the Apache James server also has a remote administration port (TCP/4555). Hmm. I didn't see that on my initial nmap scan. Let's run an extended TCP scan...  

![SS Nmap_Extend]({{ site.url }}/images/SolidState/nmap_extended_JAMES.JPG)


Nice, hunches do pay off! Using NetCat to connect I was able to confirm that the default password was still set as well. Now let's view the available users configured and change their passwords so we can read their mail:

![SS Nmap_James_Users]({{ site.url }}/images/SolidState/james_admin_users.JPG)  
![SS Nmap_James_Users]({{ site.url }}/images/SolidState/james_admin_resetting_passwords.JPG)



