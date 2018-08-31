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

