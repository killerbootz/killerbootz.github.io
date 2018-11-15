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

![SS James_Users]({{ site.url }}/images/SolidState/james_admin_users.JPG)  
![SS James_Users]({{ site.url }}/images/SolidState/john_admin_resetting_passwords.JPG)

Reading these emails for each user can be done fairly easily using NetCat, Telnet or other similar methods, but is clunky. Python POP scripts are a-plenty and one good one is available [here](http://net-informations.com/python/net/pop3.htm "POP3 Pythong Script").  Using this I was able to cycle through the different users emails until I hit paydirt on Mindy's account (a new employee):

![SS MindyEmail]({{ site.url }}/images/SolidState/mindy_email.JPG)  

Perfect, so now we have a password! And we can login! Hey, there's a user flag, cool!

![SS Mindyflag]({{ site.url }}/images/SolidState/mindyFlag.JPG) 

![SS MindyShell]({{ site.url }}/images/SolidState/mndy_shell.JPG)  

....And we're hit with a restricted shell. Not surprising considering this is a new employee account.
![SS MindyRbash]({{ site.url }}/images/SolidState/mindy_rbash.JPG)  

So after clunking around for a bit in the shell trying a couple of different escape attempts, there was no joy. I ended up coming across [this slide](https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells?slide=9 "rbash escape") from SecTalks Melbourne 2016 that detailed using the __-t__ switch to execute commands before the restricted shell is set on the account. Using this method I was able to execute a vi session on the mindy account, set the shell using __:set shell=/bin/bash__ then __:shell__ and I was freeeeeeeeeeee!!  

After completing this CTF I looked at a couple of walkthroughs and noticed that some others took the more-or-less obvious route of going back to the JAMES smtpd RCE from earlier (now that we have credentials it makes execution of commands much easier, this a new shell could be spawned using that method), but either method works.  

PrivEsc took to turning over rocks, and rocks of rocks, but not finding much. After some time I thought about focusing on JAMES smtpd again. Where was it executing from, taking a look through those directories etc. I found it by looking at the processes running under root (one of my privesc steps from earlier), and went to those locations to analyze further.  

What I found was a tmp.py file that was owned by root and looked like it's function was to clean out /tmp. I had noticed before that one of my /tmp files was missing, but thought that it was my imagination. Now it looked like I had the culprit! After some failed attempts at a simple bash-based reverse shell, and other methods I upped my game and converted to one more Python-friendly:

![SS Flag]({{ site.url }}/images/SolidState/flag.JPG)  

Finally, fairly straight-forward, but I learn so much from each of these that none are a gimme.
