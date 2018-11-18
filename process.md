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
| SMB | Enum4linux | Network Share Scanner |
| Various | NetCat | Network Connection Utility |

Of course these are just a sampling of some tools to start, and very focused on web exploitation. 

{:.mbtablestyle}



        
