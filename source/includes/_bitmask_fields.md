# Bitmask Fields

This section describes the bitmasks fields returned in response to some api
calls and explains how to decode their data. 

The bitmask is an positive integer who's binary representation can be considered
as a sequence of boolean values. Bitmasks are little-endian; As such the start
of the sequence is the last (or right-most) bit of the byte.

## Encoding



```
 Sun | Mon | Tue | Wed | Thu | Fri | Sat
-----|-----|-----|-----|-----|-----|-----
  X  |  X  |     |  X  |  X  |     |  X
```

Consider the situation where we have a show with a performance on everyday of
the week with the exception of Tuesday and Friday. 


```
 Sun | Mon | Tue | Wed | Thu | Fri | Sat
-----|-----|-----|-----|-----|-----|-----
  1  |  1  |  0  |  1  |  1  |  0  |  1
```

If we put a `1` in the column for every day of the week when the performance is
available, and `0` for everyday when it's not available then we get something
that looks a lot like binary! Indeed as we have 7 bits (one for each day of the
week) we could represent this as the 8bit number 218.


```
  X  | Sat | Fri | Thu | Wed | Tue | Mon | Sun
-----|-----|-----|-----|-----|-----|-----|-----
  0  |  1  |  0  |  1  |  1  |  0  |  1  |  1    ==  91
```

If we pad the table with an additional day of the week (day `X` in this example)
where the value is always 0, reorder the days of the week from right to left
starting at Sunday we can now read this as the 8 bit integer `91`

This reordering is important as it's probably the direction that your language
converts binary data into integers.


## Decoding


Assuming that you have received `91` as the week day mask in a response from the
API and you wanted to see if the event has a performance on a Wednesday. You can
quickly check this a number of ways. 

```
using bitwise OR:

  X  | Sat | Fri | Thu | Wed | Tue | Mon | Sun
-----|-----|-----|-----|-----|-----|-----|-----
  0  |  1  |  0  |  1  |  1  |  0  |  1  |  1    ==             91
  0  |  0  |  0  |  0  |  1  |  0  |  0  |  0    ==              8
  0  |  1  |  0  |  1  |  1  |  0  |  1  |  1    == (91 | 8) == 91

  X  | Sat | Fri | Thu | Wed | Tue | Mon | Sun
-----|-----|-----|-----|-----|-----|-----|-----
  0  |  1  |  0  |  1  |  1  |  0  |  1  |  1    ==             91
  0  |  0  |  0  |  0  |  0  |  1  |  0  |  0    ==              4
  0  |  1  |  0  |  1  |  1  |  0  |  1  |  1    == (91 | 4) == 95

```

Firstly you can `OR` the mask against the correct day of the week in binary. For
example the number you get when you put a `1` in only the Wednesday column is
`8` or `00001000`. When you use `OR` it will give you a `1` if there is a `1` in
that column in the mask or in your day of the week. `00001000 | 01011011 ==
01011011` or `91`. Because the output is the same as the  mask you now know that
there is a `1` in the 4th bit of the mask.  If you wanted to check Tuesday you
could use `4` or `00000100` against the mask, this would return `01011111` or
`95`, the output is different from your mask, therefore we don't have anything
on a tuesday. You can do compound `OR` as well `00001000 | 00000100 == 00001100`
or `12` so 

