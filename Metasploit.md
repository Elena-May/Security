# Metasploit
## Metasploit Terminology
### Exploit
An exploit is the means by which an attacker or pen tester takes advantage of a flaw in the system.

In metasploit this is a module that can exploit a systems vulnerabilty.

### Payload
Code that we want the system to execute and that is to be selected and delivered by the Framework.
What the exploit will try and plant on the system.
Files that are left on the attacked system
Give the attacker access or control over the system 

### Shellcode 
Set of instructions used as a payload when exploitation occurs.

### Module
A piece of software that can be used by the Metasploit Framework.
Auxileries, exploits, payloads, encoders, nops, post - 6 types of modules

### Auxileries 
More specific types of testing 
scanners, crawlers, sniffers
Becoming very popular
Primary use is to scan for vulnerabilities and to perform DoS attacks

### Encoders
Help to get around antivirus software and intrusion detection 

### nop 
No operation 
A nop causes the systems processer or CPU to do nothing for an entire clock cycle
They are good for allowing you to get a system to run a specific file or code after you have exploited the buffer overflow 
They allow you to remote execute code on the target machine 

## posts
Short for post exploitation
Give you extra pieces of functionality for after you have exploited a system 


### Listener
A component within Metasploit that waits for an incoming connection.
After the target machine has been exploited, it may call the attacking machine over the Internet. The listener handles that connection, waiitng on the attacking machine to be contacted by the exploited system.

## Metasploit Interfaces
### MSFconsole
All-in-one interface.
Use msfconsole to do everything, including launching an explout, loading auxilary modulem performing enumeration, creating listeners and running mass exploitation against an entire network 

### MSFconsole Commands
cd /opt/metasploit/msf3/
msfconsole

help (followed by the command you are interested in)

- Armitage ans MSFcli are two other interfaces

## Metasploit Utilities
Utilities are direct interfaces to particular features of the Framework

### MSFpayload
Generate shellcode, excutables and much more for use in exploits outside of the Framework 
Can generate the shellcode in lots of different langauges depending on what you want to use it for 

### MSFencode
Used to encode the code from payload so it has less bugs and is less detectable?


