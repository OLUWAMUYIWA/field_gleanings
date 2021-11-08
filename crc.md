# CRC
_Main reference: http://chrisballance.com/wp-content/uploads/2015/10/CRC-Primer.html_  

First, let us talk about the need for CRC
There are many ways to make sure of the integrity of tdata that is transferred to us from a sender over a mdeium, e.e. a network.  
We could hash, and send the hash(e.g. SHA1, SHA256, Blake, etc) along with it; we could send the parity along with it(You count the number of 1s in the data, and check 
if it is even or odd. odd parity goes as one, while even parity goes as 0); we could also use CRC. We use CRC sometimes because it gives us better promises (no guarantees)
  than simple approaches like Parity, and isnt as complex as hashing.
  We could count the number of byyes in the message (or data) perform the addition modulo 256 to make it all fit into a byte. The issue is that there are 256 possible 
  checksums. That limitation means some errors will go unnoticed so long as the errors still computes to an unaltered checksum. This will happen on the average one out of
  256 times as probability tells us. We could do something to  this to mitigate this effect; we could increase the sie of the checksum so that it contains 2 bytes, i.e. 
    2^ 16 (i.e. 65536) possible values instead of 2^8. The problem with this is that it does not help us much. Each coming byte being appended in computing the checksum will likely
    affect just one byte(enphasis on likely). Addition is so simple as operation (or function) that it doesn't spread its effects quite randomly enough. When i add two bytes, there's a high chance that
    the result will remain in the first byte, and if it doesn't it'll be in the second. It's not randomized enough.
      You know what's cool? Division is quite random; far more random than addition. Division will touch every single byte accumulated as you compute the checksum. Division allows each additional byte to
      have effect on the entire checksum register. Width and Chaos are therefore essential for any good checksum.
  So what is CRC? It means Cyclic Redundancy Check
  
  > The basic idea of CRC algorithms is simply to treat the message as an
enormous binary number, to divide it by another fixed binary number,
and to make the remainder from this division the checksum. Upon
receipt of the message, the receiver can perform the same division and
compare the remainder with the "checksum" (transmitted remainder).  ~ Chris Ballance
  
  
  Remember the way division works? Check out 1559/9.:
```
1559 = 0000011000010111 base 2
9 = 1001 base 2
  
          0000000010101101 = 173 = quotient
        __________________
   1001 | 0000011000010111 = 0617 = 1559 = DIVIDEND
DIVISOR   0000.,,....,.,,,
          ----.,,....,.,,,
           0000,,....,.,,,
           0000,,....,.,,,
           ----,,....,.,,,
            0001,....,.,,,
            0000,....,.,,,
            ----,....,.,,,
             0011....,.,,,
             0000....,.,,,
             ----....,.,,,
              0110...,.,,,
              0000...,.,,,
              ----...,.,,,
               1100..,.,,,
               1001..,.,,,
               ====..,.,,,
                0110.,.,,,
                0000.,.,,,
                ----.,.,,,
                 1100,.,,,
                 1001,.,,,
                 ====,.,,,
                  0111.,,,
                  0000.,,,
                  ----.,,,
                   1110,,,
                   1001,,,
                   ====,,,
                    1011,,
                    1001,,
                    ====,,
                     0101,
                     0000,
                     ----
                      1011
                      1001
                      ====
                      0010 = 02 = 2 = REMAINDER
```

Consider how the division above works. As the divisor consumes each bit, it changes the remainder which is passed down and down and shifted to the right a lot of times.
Add an extra byte to the dividend and the remainder will get kicked around about 8 more times, making th checksum register susceptible to greater change
 i.e. having higher entropy.
            
### Polynomial Arithmetic
CRC uses polynomial arithmetic, meaning that the message is treated as a polynomial and each bit is a coefficient of the polynomial variable (say `x`).
The degree of the polynomial ranges from `0` to `n-1` if the length of the message is `n` bits.
`10111` becomes  `1*x^4 + 0*x^3 + 1*x^2 + 1*x^1 + 1*x^0` or `x^4 + x^2 + x^1 + x^0`
                         
So, imagine we want to multiply `1101` by `1011`, we'll have: 
```
(x^3 + x^2 + x^0)(x^3 + x^1 + x^0)
= (x^6 + x^4 + x^3
 + x^5 + x^3 + x^2
 + x^3 + x^1 + x^0) = x^6 + x^5 + x^4 + 3*x^3 + x^2 + x^1 + x^0
Because we know that they are all coefficients of 2, out operations become simpler,  
and we need to perform our additions modulo 2. Interesting things happen
Our polynomial changes: as `3` becomes `2 + 1`, leading `3x^3` to become `2x^3 + 1x^3`,  
and the,`2x^3` turning to `1x^4`, leading to it joining with the existing
 `x^4`, making it `2x^4`, which also trickles down (or up?) to the existing `x^5`,  
making it `2x^5`, and itself joing with the `x^6` to become `x^7`
 In the end, we have: 
 x^7 + x^3 + x^2 + x^1 + x^0
```
  
But this is not how CRC works. CRC uses `pulynomial arithmetic mod 2`. We don't presume that we know the value of `x` as we did in the above. So we cannot carry anything. Nothing flows up or down.
All operations are limited to the to interacting bits. This is interesting, isnt it, because addition in this case would definitely look like normal binary addition with no carries.
And remember how binary addition in half-adders are done. The `sum` id the `XOR` of the bits, while the `carry` is the `AND` of the bits. Here, the `carry` is useless. 
Remember the `XOR` table: 


>"The reader should note the similarity between polynomial
   arithmetic and multiple-precision arithmetic (Section 4.3.1), where
   the radix b is substituted for x. The chief difference is that the
   coefficient u_k of x^k in polynomial arithmetic bears little or no
   relation to its neighboring coefficients x^{k-1} [and x^{k+1}], so
   the idea of "carrying" from one place to another is absent. In fact
   polynomial arithmetic modulo b is essentially identical to multiple
   precision arithmetic with radix b, except that all carries are
   suppressed." ~Donald Knuth  
     
Somethin interesting happens when we do binary arithmetic with no carries: bot `+` and `-` operations are the same. They are `XOR`
```
   0+0=0
   0+1=1
   1+0=1
   1+1=0
```

```
   0-0=0
   0-1=1
   1-0=1
   1-1=0
```

```
   1010 = 1010 + 0011
   1010 = 1010 - 0011
```

What then is multiplication in this case? Multiplication is a number of shif operation (the number being the length of the second bit string). Those bit shifts are added together 
the same way we added the with `XOR` arithmetic. The bit strings added are either the entire first number or a bit string of the same length but all zeros depending
on the value of the consecutive bits in the second number, which could either be `0` or `1`.
Consider:
```
        1101
      x 1011
        ----
        1101
       1101.
      0000..
     1101...
     -------
     1111111 
     -------
```


What then is division? Division must define when a number `goes into` another. Generally, we say that a polynomial `b` goes into another polynomial `a` if the highest power of  
`b` is <= the highest power of `a` and the coefficient of that elemt with the higest power in `b` is <= that of `a`. This is not the way a mathematician will put it, but please manage. Forgive.

 e.g. ```
             1100001010
       _______________
10011 | 11010110110000
        10011,,.,,....
        -----,,.,,....
         10011,.,,....
         10011,.,,....
         -----,.,,....
          00001.,,....
          00000.,,....
          -----.,,....
           00010,,....
           00000,,....
           -----,,....
            00101,....
            00000,....
            -----,....
             01011....
             00000....
             -----....
              10110...
              10011...
              -----...
               01010..
               00000..
               -----..
                10100.
                10011.
                -----.
                 01110
                 00000
                 -----
                  1110 = Remainder. ~ source: Tanenbaum81
```
