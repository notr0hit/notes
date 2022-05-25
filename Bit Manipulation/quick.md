## Bit Manipulation

Suppose we have a number n and we want to 

1. To turn on ith bit &rarr; ```(n | (1 << i))```

2. To turn off ith bit &rarr; ```(n & ~(1 << i))```
3. To toggle ith bit &rarr; ```(n ^ (1 << i))```

4. To check if ith bit is on or off
``` cpp
    if (n & (1 << i) == 0)
        // ith bit is off
    else
        // its bit is on
```
Proof:
X = a1b 
here X and a is any binary number,
b is 000... only
Eg. X = 10101**1**000 
Now -X = 2's compliment of X
-X = (X)' + 1
-X = (a1b)' + 1
-X = a' 0 b' + 1
-X = a' 0 (111...) + 1
-X = a' 1 000...
-X = a' 1 000...
**-X = a'1b**

So, X & -X =0000.. 1 000..

5. Right Most Significant BitMask (RMSB)

``` cpp
// 0011010 -> here RMS Bit is at 2nd position from LSB
Say we have a number n = 10110100
Then RMSB = 00000100
So, RMSB  = (n & (~n + 1)) = (n & -n)
```

**Kernighan's Algorithm**

Used to count number of set bits in a number
``` cpp
int n = 10 // 1010
int Setbits = 0;
while (n != 0) {
	int rmsb = (n & -n); // 0010
	n -= rmsb; // n = 1010-0010 = 1000
            // Basically we are turning of RMS bit
	Setbits++;
}
```

**when a is a signed int and negative, it seems that**
`a << 1`
is not well defined in C++ standard
*so do* 
``` cpp
(unsigned int) a << 1
```
