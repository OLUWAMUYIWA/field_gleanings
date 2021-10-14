### What is Regex?

[comment]: <> (Todo: look for two complex examples of regex in use and tear them apart)
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
|`i`|make `re` case-insensitive, default is false|
|`m`|this flag is powerful. Naturally, `^` and `$` would macth the beginning and end of a string or text, but `m` makes them match the beginning and end of a new line if the string is multi-lined (i.e. it has `\n` characters in it)|
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
|`^`|matchss the beginning of a string. If `m` flsg is set, this character also matches the beginning of a new line|
|`$`|matches the end of a string(or text). It is same as `\z`, **not** `\Z`. If `m` flag is set, the same `$` character matches the end of a line|
|`\A`| beginning of string(or text) anyday regardless of `m` flag's value|
|`\b`|quite cool, slightly complex, but it isn't. It matches an `ascii` word boundary, what this means is that that there's `\w` (ascii word) on one side, and a `\W` i.e. non-ascii on the other side, or an `\A` beginning of a new text or a `\z` meaning end of text on the other side. Remember that characters like `\n` new line are ascii, I guess that's why `$` won't work here|
|`\B`| not an ascii word boundary. What does this even mean? Is it useful? Wel it does, it matches _non-strings_ or *empty strings* that negate the condition stated above|
|`\z`| mentioned above. it means end of text or end of string|


### Escape Sequences
we need escape sequences to match what charcters would have been interpreted as metacharacters if left unescaped
|`re`|description|
|----|-----|
|`\a`|ascii bell character `\007`|
|`\f`|form feed `014`|
|`\*`|needed to escape the metacharacter `*` which stands for anything apart from newline (except when the `s` flag is set, remember?)|
|`\r`|carriage return|
|`\t`|tab space|
|`\123`|octal character code, up to three digits|
|`\x7F`|hex character code, exactly 2 diits, this would be an ascii character, i guess|
|`\x{10FFFF}`|macthes hex character code, standing for character, would be utf-8 I guess|
|`\C`|this matches a single byte, any single byte, even when in utf-8 mode|


### Character Classes
Character classes are usually in between `[` and `]`. And when theyre used,t tyey match only one character. This character can of course be repeated, can be selected from a set of possible characters.
|type|description|
|----|----|
|`x`|the simplest, matches the exact literal, if it is also a metacharacter, you have to escape it|
|`A-Z`|a character range, chooses one that matches. its inclusive|
|`\d`| example of *perl* character class|
|`[:foo:]`|example of ascii character class|
|`\p{Foo}`|names Unicode character class. Class name is `Foo`|
|`\pF`|second type of Unicode character class. This ine is one-lettered|

Now to use named character classes as class elements:
|`re`|descritpion|
|----|-----|
|`[\d]`|digits, any digit|
|`[\D]` or `[^\d]`|Not a digit, notice how a character class element is negated|
|`[^\D]`|`| 	not not digits (≡ \d)|
|`[[:name:]]`| 	named ASCII class inside character class |
|`[^[:name:]]`| 	named ASCII class inside negated character class|
|`[\p{Name}]`| 	named Unicode property inside character class |
|`[^\p{Name}]`| 	named Unicode property inside negated character class|


### Perl Character Casses
All Perl character classes match ascii characters
|`re`|description|
|----|----|
|`\d`|digits. equivalent to `[0-9]`|
|`\D` or `^\d`|not digit. equivalent to `[^0-9]`|
|`\s` |anything space. equivalent to `[\t\f\n ]`, i.e tab, form feed, newline, and space. All ascii one-byte characters|
|`\S`|opposite of `\s`|
|`\w`|ascii word. equivalent to `[0-9a-zA-Z_]`, i.e the character class that consists of the range of digits, lower-case letters, upper-case letters, and `_`|
|`\w`|opposite of `\w`. equivalent to `^\w`|


### ASCII Character Classes



## How To Implement **Regex**
[comment]: <> (I'm reading about Finite state automata so I can understand this more deeply)


