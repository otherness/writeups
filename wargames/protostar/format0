"print" 64 char to fill the buffer and overflow 0xdeadbeef to stack value stored next.
This works because both variables are local and stored in stack together one under another.

./format0 $(python -c "print '%64d\xef\xbe\xad\xde'")