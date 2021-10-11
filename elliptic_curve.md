## Elliptive Curve Notes

As I read MArtin Kleppman's paper, I'll distill what I learn here.
This might be a little hard to achieve, seing that .md is not (I suppose) very good for mathematical writing. That does not matter anyway. I don't intend to choose any other
  alternative; this is nt the time for me to learn how to do nathematical writing. So, dear keyboard, bear with me. My limitations are entirely my fault.
I syarted reading Marting Kleppman's paper again today. This is perhaps my third time of starting it, and that's not because its not interesting, rather its because 
Ive had other seemingly momentarily promising things to spend my time on. This time is different, Ill see the end.


#### Modular Arithmetic
A set `Zp` is a set of `Z` modulo `p` if `Zp` = `{0, ..., p-1}` or `[`0:p-1]`  
All operations in modulo can be done by either performing each step and reducing modulo or cmputing the entire thing , and then reducing modulo, or even a mixture of these tow. What does matter is that one makes sure that each operation is operating on numbers with the same charactersistics at every step, whether reduced or not. More on this later

#### Groups(Abelian)
Groups have three properties that make them groups:
- Closure
- Commutavitity
- An Identity element
- Inverse Elements

#### Diffi-Hellma Key Exchange
How this works:
I am bob, I need to allow people to be able to encrypt messages for me, which only I can decrypt, how do I achieve this? How do I produce a key they can use and such that they can communicate with me even when there is an eavesdropper?
Groups come in here. Groups have order, which simply means how many members are in a group. Of particular interest to us are **prime-ordered groups**.
So, I pick a `g`, which is a group member, a popular one, selected for reasons I'll later discover in the paper, this `g` is usally called a `generator`,a d for every `group` in popular use, it is specified and generally agreed upon. With this `g` and a totally random integer `j` whose selection is beyond the scope of this subject. The `j` becomes my ***private key*** which nobody is to know other than me. I take this `g` and perform the `*` operator of my `prime-order group` and apply it `j` times, the result is = `g^j`. That `g^j` is my ***public key***. I publish the public key for everyone in the world to know under aninsecure channel. The only guarantee I need is that no one can modify the key in transit. They can see it all they want. Now that I have done that. Alice, who wants to communicate with me takes my `g^j` and computes a `(g^j)^k`, in the same fashion as i computed the other one, i.e. applying th operator on `g^j` *k* times. She takes the result and use ses it as cipher to encrypt her message to me. She appends one other thing that I need: her computation of `g^k`. I would need that too, she does not encrypt that, she only appends it to the message. The message is sent over the network, *TCP* perhaps. On reaching my side, I take the `g^k` that is appended to the message and compute `(g^k)^j` since I have my `j`. The result is same as `(g^k)^k` that was used to encrypt talice's message. I'm done.   

Why does this work?
This works simply if there is a guarantee that anybody, malicioues or otherwise, who knows `g` (because its a convemtion, sort of), `g^k`(because they were eavesdropping), `g^j` (because I published it openly), cannot armed with the combination of these three numbers be reasonably able to compute with some algorithm other that gruesome brute force, the value of either `(g^j)^k` or `(g^k)^k` which are the same because of associativity.
