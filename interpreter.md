# Lexing
Programming language helps us communicate with the computer. While plain text is convenient for us humans, they can be cumbersome for computers to interpret.

Hence, our source code undergoes two stages of transformation, from source code, to tokens, and then an abstract syntax tree.

```
flowchart LR
    s[source code] --> t[tokens] --> ast[abstract syntax tree]
```

The first transformation from source code to tokens, is called "lexical analysis", or "lexing" for short. It is done by a lexer.

**Source Code** - human readable programming language
**Tokens** - small, easily categorizable data structure that are fed to the parser

For example, the input `let a = 1 + 2;` can evaludate to the following, where whitespace characters don't show up as tokens.

```
[
    LET,
    IDENTIFIER("a"),
    EQUAL_SIGN,
    INTEGER(1),
    PLUS_SIGN,
    INTEGER(2),
    SEMICOLON
]
```

Note that in some languages like Python, the length of whitespace is significant, so the lexer can't just discard the whitespace and newline characters. Instead, they have to output the whitespace charactrers as tokens for the parser.

Production ready lexer may also attach the line number, column number and filename to a token - to later output useful error messages in the parsing stage. For example, instead of `error - missing semicolon`, it will output

```
error: missing semicolon, line 42, column 23, main.dog
```

## Designing the Token
Before thinking about lexer, we should define the tokens they are going to output. In a typical program, we may have the following elements
```
let a = 1
let b = 2

let add = function(a, b) {
    return x + y;
}

let result = add(a, b)
```

Here, we can broadly categorise what the programming language represent into three groups.

The first group would be fixed values - the **literals**. We have only `1` and `2`.

Next, we have names which identifies a variable - the **identifiers** , we have `a`, `b`, `add` and `result` as identifiers here.

Lastly, the rest are parts of the language that's not value and not variables. They are the language **keywords** - we see `let`, `=`, `,`, `function` and brackets in the above snippet.

We have decided on three broad token types - literrals, identifiers and keywords. With that, we can use the following data structure to represent a single token

```
// token.go

type TokenType string // defined as string, but more performant as int or byte

type Token struct {
    Type TokenType
    Literal string
}
```

Next, we can grab everything we saw above and lump them into different token types.
```
// token.go
const (
    ILLEGAL = "ILLEGAL"
    EOF     = "EOF"

    // identifier, and integer literals
    IDENT = "IDENT"
    INT   = "INT"

    // delimiters and brackets
    SEMICOLON = ";"
    COMMA     = ","

    LPAREN = "("
    RPAREN = ")"
    LBRACE = "{"
    RBRACE = "}"

    // keywords
    FUNCTION = "FUNCTION"
    LET      = "LET"
)
```

Note we have two extra special token types - `EOF` and `ILLEGAL`. Although they do not explicitly appear in the example above, they are still required.

EOF - tells the parser to stop
ILLEGAL - represents an illegal character that does not map to any type.