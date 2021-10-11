### What is Regex?
//Todo: look for two complex examples of regex in use and tear them apart
> Here are the two most important resources describing **regex** that I've seen:  
> - Syntax: [re2](https://github.com/google/re2/wiki/Syntax)
> - Concept: [russ cox](https://swtch.com/~rsc/regexp/regexp1.html)

So, what is `regex`?  
By my understanding, a regex is a construct (a programming item) that matches strings. An `re` is used to describe as string charccter by character.
The simplest `re` is a `literal` string character. A `literal` `re` matches  itself simple. No complications.  

But it gets more complex when we have two `re`s. When we have `e1e2` side by side, we want to match the strings `s` and `t` joined toether by it.
When we have `e1|e2`, it means that we're good with either `s` matching it or `t` matching it.  

> In `go`, put `re`s in `` so you don't have to escape `\`. Those strings in `` are called _raw strings_ . In `rust`, do the same by putting the strings inside r""

`re` uses _metacharacters_ as operators. There are many of them, and they are really all there is to learn about `re`. We'll go through them one by one.

`.` is the ,ost general metacharacter. It matches anything except *newline*, i.e. `\n` (or `\ln` in windows). This exception may be removed if we turn
 on the `s` flag. `.` will begin to match everything including *newline*
 
 
##### Repetition of `re`s
- `*`: 0 or more
- `+`: 1 or more
- `?`: 0 or `
> reps are quite easy to handle. They aren't more complex than that

##### Precedence of operators
Precedence means the same thing it means in Math. It helps to estaplish some form of flow or order of operations. WHich one must come before  
the other? Ops with weaker precedence come later than ops with stronger precedenceFrom lowest to highest.
 > alternation < concatenation < repetition 
 >    > e.g. `e1|e2e3*` goes this way: `e3*` gets evaluated, then the result has to be concatenated to `e2`, which is then `orred` against `e1`.

According to the `re2` docs, the above suffices for the original *unix* `egrep`. It suffices to describe any *regular language*, which it loosely describes as a
set of strngs tjat can be matched in one fell swoop using only a fixed size of memory.

## SubSections

### Flags
|flag |description|
|-----|------------|
|`i`|make `re` case-insensitive|
|`m`|this flag is powerful. Naturally, `^` and `$` would macth the beginning and end of a string or text, but `m` makes them matc the beginning and end of a new line if the string is multi-lined (i.e. it has `\n` characters in it)|
|`s`| it lets `.` match newline charcater|
|U|powerful too. makes all repetitions ungreedy. i.e. `x*` becomes `x*?`, `x+` becomes `x+?`, `x?` becomes `x??` etc.|

> **now what is greediness?** In repetitions, a greedy `re` will keep matching until the instruction fails, while a non-greedy will stopmatching after the first condiction of match is fulfilled
>
> For instance: `<.*>` would probably be expected to match `<br>` in the following string: `<br> My name is muyi </br>`, but no, it will match everything because of greed.
> It is able to match everything in this case because every character except `\n` is matched by `.`, so apparently, even the first `>` matches, and so it continues until the last, thats when the match is broken.
> A simpler example is the `re`: `h.+l` which will match the entire string `hell` and only the substring `hell` in `hello`, but if what you want is just
> `hel`, you have to make your `re`: `h.+?l`, which will prefer to stop at the first instance of the repetition rather than the last.


### Composites
|`re`|description|
|---|------|
|`xy`|`x`followed immediatly by  `y`, a concatenation|
|`x\|y`|an alternation, more like if-else|

### Repetitions
|`re`|description|
|------|------|
|`x*`|zero or more `x`, greedy|
|`x+`|one or more `x`, greedy|
|`x?`|zero or one `x`, reedy|
|`x*?`|zero or more `x`, non-greedy|
|`x+?`|one or more `x`, non-greedy|
|`x??`|zero or one `x`, non-greedy|
|||
|`x{n}`|exactly `n` repetitions of `x`|
|`x{n,}`|at least `n` repetitions of `x`, greedy|
|`x{n,m}`|anything between `n` and `m` repetitions of `x, greedy |
|`x{n,}?`|at least `n` repetitions of `x`, non-greedy|
|`x{n,m}?`|between `n` and `m` repetitions of `x, non-greedy|


### Grouping
`Group`s are very powerful in `re` because of several reasons:
- you can name or index a group and use it to match substrings of a string. cool
- You can create different semantics using different sets of `metacharacters` within the group.

Everything inside an un-escaped `()` pair is a group. The following are the semantics it supports
|construct|semantics|
|----|------|
|`(re)`|captures a substring, a submatch, and the substring it indexed from left to right, this particular one being one of the indexed substrings|
|`(?P<name>re)`|It needs a name to name the index key, so its a submatch indexed by key (name). The `re` that is matched stays just in front of the closing angular bracket|
|`(?:re)`|non-capturing group, means that it  matches without capturing, in essence, the matched characters don't get stored, but are useful only to indicate their presence|
|`(?flags)`|set flags withing current group. I've seen this prepended at the beginning of an `re` that hs several subgroups, and I take it to mean that even the entire regex is a group as well, so I can put it inside a subgroup to set the flag for that group or generally outside at the beginning. Non-capturing|
|`(?flags:re)`|set this flag whenever you're evaluating this particular `re`. Non-capturing|

> To use flags: syntax is xyz for setting x, y, and z. Or -xyz for clearing three flags you set previously inside a subgroup. Also, r xy-z to set xy, and clear z.


### Empty strings
|`re`|description|
|-----|----|
|||
