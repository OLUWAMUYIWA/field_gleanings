### So what are macros?

I'll be dealing mostly with MBE's (Macros by example)
They're the simplest kind of macros in `Rust`

So, what are they?
- First, they're syntax extentions?

Like, seriously, what are they?
First you/I need to understand the process of parsing.
So Lexing comes before parsing. Lexing is where the `Lexer` (hahahaha) takes the souce file, which is in *Rust* a file containing `utf-8`-encoded text.
The lexer takes the source and creates from it a series of `lexemes` or `tokens` which simply means the smallest units thet are acceptable in the language.
`Lexemes` may include, by my knowledge, String literals, integer/float literals (and all others), identifiers (how doe sit understand an identifier?), keywords (both reserved and used)
`Lexemes` also include Symbols that are understood by the language. Symbols like arentheses, square brackets, curlies, dot, all the operators (+. -, etc), comma, etc. Just about anything the  language understands as its smallest indivisible units.
After `lexing`, the compiler/interpreter then takes the `lexemes` and its grammar and from those two build an `AST` (Abstract Syntax tree).

Now, `vkrill` says this is where we need to understand what a token tree is. I need to go back to understand that.

#### When do I need `Macros`?
I'd say for two reasons: 
1. Functions are not as powerful as MBE's. so we use them to get around the limitations of functions
    - use different patterns to allow a macro act like a function but with variable input and output parameters. We won't need to use generics and bind by traits. Instead, we'll use pattern-matching
2. We also use them to write once constructs that we would have had to write several times e.g. implememtations of traits, creation and `impl`s of `items` that are quite similar and writing each by hand would be tedious
    - Tests too
