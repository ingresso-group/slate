# Bitmask Fields

This section describes the bitmasks fields returned in response to some API
calls and explains how to decode their data. 

The 'bitmask' fields are compact representations of arrays of flags, where
the binary digits of the number represent true and false flags as 0 and 1.

We number our bits in the conventional way with the least significant bit
being bit zero.

The correspondance between bits numbers and actual things is:

1. For weekdays, 0 = Sunday, 1 = Monday, 2 = Tuesday etc...

2. For months 0 is not used, 1 = 1st, 2 = 2nd, 3 = 3rd etc. (Note that this 
means all month bitmasks will be even numbers).

3. For tickets, 0 = first ticket, 1 = second ticket, like an array index

Check check one of the bits in the bitmask you can use the following syntax in
most languages:
`((1 << bit_number) & bitmask)`

For example if the API returns `91` as a weekday bitmask you could make the 
following checks in Python.

There is availability on Sunday:

`>>> ((1 << 0) & 91)`

`1`

There isn't availability on Tuesday:

`>>> ((1 << 2) & 91)`

`0`

There is availability on Saturday:

`>>> ((1 << 6) & 91)`

`1`