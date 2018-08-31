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

Upon going to either web port we are presented with a cute English Bulldog site. The site says it used to be a photography company for these breeds but was recently hacked, presenting the following notice:

![Bulldog Notice]({{ site.url }}/images/Bulldog/basepage.JPG)