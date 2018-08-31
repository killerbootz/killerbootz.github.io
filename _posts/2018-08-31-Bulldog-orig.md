---
layout: post
title: Bulldog Original Walkthrough
published: true
---

After completing Bulldog-2 I had to take a shot at the original [Bulldog-1](https://www.vulnhub.com/entry/bulldog-1,211/). How did it fare to the sequel? Let's find out!


 
      ****Spoiler Alert****          ****Spoiler Alert****




Ennumeration of this box initially showed some interesting things to keep in mind:

![Bulldog NmapOutput]({{ site.url }}/images/Bulldog/nmap.JPG)

The first as seen above is that well-known services (like SSH), will not always show up on their associated port, and can be assigned at the admin's whimsy. Although that doesn't mean it's any less detectable.
The second thing was that it appears that there are two instances of the same web server running. 

Upon going to either web port we are presented with the following base page:

![Bulldog Notice]({{ site.url }}/images/Bulldog/basepage.JPG)

Both Nikto & Dirb revealed a "/dev" directory supposedly put there by one of the staff. This included a link to a webshell, you have to be logged into the Django admin page, discovered at "/admin". The Dev page shows the team members (a.k.a. usernames), and a quick look at the source, shows SHA1 hashes left behind:

![Bulldog DevHash]({{ site.url }}/images/Bulldog/dev_hashes.JPG)

After feeding the hashes through John-the-Ripper I had a hit fairly quickly for the Nick account:

![Bulldog NickPass]({{ site.url }}/images/Bulldog/nickpass.JPG)

From here we can login as Nick to the Django admin page, then jump to the webshell link in the Dev page and bob's your uncle:

![Bulldog Webshell]({{ site.url }}/images/Bulldog/postauth_webshell.JPG)

Perfect. Let's see what happens when we enter a command we're not supposed to:

![Bulldog Caught]({{ site.url }}/images/Bulldog/caught.JPG)

Ya got me.

Ok, let's see if we can pipe our evil commands to the accepted ones to bypass getting caught:

![Bulldog Bypass]({{ site.url }}/images/Bulldog/piped_bypass.JPG)

Nice, now we can make a quick Bash reverse shell and upload it using wget to /tmp. Change the  permissions and run it to gain a low privilege shell:

![Bulldog Rshell]({{ site.url }}/images/Bulldog/rshell.JPG)


Ok, so quick look around. No exceptionally low-hanging fruit for a quick win. Hmm, I do see some hashes for users (including an admin account) in a SQLite db file in the Django home directory:

![Bulldog DB]({{ site.url }}/images/Bulldog/db_bak.JPG)

Maybe I'll run HashCat against those in a few.  
There's also another user account "bulldogadmin" here. And a hidden admin directory:

![Bulldog Hidden]({{ site.url }}/images/Bulldog/hidden_bulldogadmin.JPG)

Hmm, running that app gets me nowhere. Copying it out and running it gets me nowhere as well. This stumped me for awhile and caused me to look around some more. I found another hidden AV directory with a Python script owned by root, but it didn't look like anything was set to run it. This would have been a good avenue since it was writable.  
I ended up going back to the customPermissionApp thinking there was something here I was missing. I ran strings against it to see if it would reveal anything and BAM!

![Bulldog CustomApp]({{ site.url }}/images/Bulldog/customApp.JPG)

This does look like a SUPER ultimate PASSWORD, (but not so hard to get). 

I decided to try this on a few different accounts via SSH, but it ended up being the password for the "django" account:

![Bulldog DPass]({{ site.url }}/images/Bulldog/django_superpass.JPG)

From here it's just a matter of checking sudo privileges, elevating, and grabbing our flag:

![Bulldog Elevate]({{ site.url }}/images/Bulldog/elevate_flag.JPG)

This was very fun, and I think I may even go back to look for the second escalation point (hmm those SQLite hashes or AV file may have something to do with it...)


Time for some coffee  
  .-~~-.
,|`-__-'|
||      |
`|      |
  `-__-'
