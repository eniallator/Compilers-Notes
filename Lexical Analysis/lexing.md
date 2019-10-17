# Lexing

1. src
2. lexical analysis
3. Syntax analysis
4. Semantic analysis
5. Intermediate code generation
6. Optimisation
7. Code generation
8. Translated program

## From Strings To ASTs

- The purposes of lexing and parsing is twofold
  - Convert the input from strings to an abstract syntax tree which is convenient for code generation
  - Check syntactic correctness
- Possible to go from strings to ASTs in one step, but it's complicated so we do 2 steps
  - Lexical analysis
  - Syntactical analysis
- Another example of divide and conquer
- Uses automata

## Lexical analysis

- The purpose of lexing is to prepare the input for the parser
- Involves several relate jobs
  - Removing irrelevant detail (e.g whitespace/comments)
  - Tokenisation
- Tokens are useful for syntactical correctness
- Example types of tokens:
  - Keywords
  - Identifiers
  - Integers
  - Floats
  - Binary operators
  - Left/right brackets
  - Token boundaries
  - Etc.

### Breaking Down The Process

- Start with string
- Split string on boundaries into a list
- Classify the list into a list of tokens

#### Why Bother

- We need to make an AST
- Check syntactic correctness
- Both are much easier with a token list rather than a string

### Example

- Take this raw string:

```text
\t IF ( x > 1 \n) \t\t\n THEN \tz := -3.01 \n
```

- This gives rise to the following token list
  - Keyword: `IF`
  - LeftBracket
  - Indent: `x`
  - Binop: `>`
  - Int: `1`
  - RightBracket
  - Keyword: `THEN`
  - Identifier: `z`
  - Binop: `:=`
  - Unop: `-`
  - Float: `3.01`
- Just about anything can be lexed, however the syntax will be analysed later
- Some strings can be described by more than 1 token
  - E.g "THEN" is both an identifier and a keyword
- There needs to be a priority for certain tokens to come first
- Another problem: `IFTHEN`
  - Contains 2 keywords
  - Usually answer to this problem is to just use the longest match
- The lexer can only classify strings by looking ahead
- Should only look ahead by 1 character, since it's inefficient to do it

## Goal Of Lexical Analysis

- Get rid of any irrelevant information
  - Use regex for the description of the lexical structure of the language
- Give a rough classification of the input to simplify the next stage
  - From the description derive a scanning algorithm which determines the token class of each - use FSA for this

## Regex

- Describe the strings of how each token class will be
- An alphabet is any set of characters
- A string over an alphabet A is a finite sequence of elements from A
- A language over an alphabet A is a set of strings over A. Examples over `{a, b, c, ..., z}`:
  - `∅`, the empty language
  - `{""}`
  - `{"hellomynameisfartin"}`
  - `{"hellomynameisfartin", "hellomynameistoooom"}`
  - `{"hellomynameisfartin", "hellomynameistoooom", ""}`

### 7 Kinds

- Empty set `∅` is a language without anything in it
- Epsilon `ε` is a language with an empty string in it
- Alphabet characters is a language consisting only of single character strings going over all of the characters of the alphabet
- Alternatives is a language that's a union of two sets
- Concatenation is a language that's concatenating any string from 1 regular expression and then any string from another regex
- Star is a language that has the same regular expression concatenated 0 or more times (0 being the empty string)
- Brackets is a way of grouping regex `lang((R)) = lang(R)`

#### Examples

- A = {0, 1}. what language is `'1'*`?
  - Any amount of 1s
- A = {0, 1}. what language is `('0'|'1')*1`
  - any odd binary number
- Binary number at most one 0 in it?
  - `'1'*('0'|ε)'1'*`
- Binary number with 4 digits exactly?
  - `('0'|'1')('0'|'1')('0'|'1')('0'|'1')`
- Alphabet A = {-, 0, 1, ... , 9}

### Abbreviations

- Instead of `'f' 'o' 'o'`, write `"foo"`
- Instead of `RR*`, u can use `R`<sup>`+`</sup>
- `R?` means `R|ε`
- `('0'|'1'|'2')` can be `[0-2]`
