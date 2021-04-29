# Grammar

## Source Text

SourceCharacter ::
  - "U+0009"
  - "U+000A"
  - "U+000D"
  - "U+0020–U+FFFF"

## Ignored Tokens

Ignored ::
  - `,`
  - WhiteSpace
  - LineTerminator
  - Comment

WhiteSpace ::
  - "Horizontal Tab (U+0009)"
  - "Space (U+0020)"

LineTerminator ::
  - "New Line (U+000A)"
  - "Carriage Return (U+000D)" [lookahead != "New Line (U+000A)"]
  - "Carriage Return (U+000D)" "New Line (U+000A)"

Comment ::
  - `|` CommentChar* [lookahead != CommentChar]

CommentChar ::
  - SourceCharacter but not LineTerminator

## Lexical Tokens

Token ::
  - Punctuator
  - Name
  - IntValue
  - FloatValue
  - StringValue

Punctuator :: one of `(` `)` `[` `]` `..` `:` `=` 

Name ::
  - NameStart NameContinue* [lookahead != NameContinue]

NameStart ::
  - Letter
  - `_`

NameContinue ::
  - Letter
  - Digit
  - `_`
  - `-` (dash)

Letter :: one of
  - `A` `B` `C` `D` `E` `F` `G` `H` `I` `J` `K` `L` `M`
  - `N` `O` `P` `Q` `R` `S` `T` `U` `V` `W` `X` `Y` `Z`
  - `a` `b` `c` `d` `e` `f` `g` `h` `i` `j` `k` `l` `m`
  - `n` `o` `p` `q` `r` `s` `t` `u` `v` `w` `x` `y` `z`

Digit :: one of
  - `0` `1` `2` `3` `4` `5` `6` `7` `8` `9`

IntValue :: IntegerPart [lookahead != {Digit, `.`, NameStart}]

IntegerPart ::
  - NegativeSign? 0
  - NegativeSign? NonZeroDigit Digit*

NegativeSign :: `-`

NonZeroDigit :: Digit but not `0`

FloatValue ::
  - IntegerPart FractionalPart ExponentPart [lookahead != {Digit, `.`, NameStart}]
  - IntegerPart FractionalPart [lookahead != {Digit, `.`, NameStart}]
  - IntegerPart ExponentPart [lookahead != {Digit, `.`, NameStart}]

FractionalPart :: . Digit+

ExponentPart :: ExponentIndicator Sign? Digit+

ExponentIndicator :: one of `e` `E`

Sign :: one of + -

StringValue ::
  - `""` [lookahead != `"`]
  - `"` StringCharacter+ `"`

StringCharacter ::
  - SourceCharacter but not `"` or `\` or LineTerminator
  - `\u` EscapedUnicode
  - `\` EscapedCharacter

EscapedUnicode :: /[0-9A-Fa-f]{4}/

EscapedCharacter :: one of `"` `\` `/` `b` `f` `n` `r` `t`

## Program Syntax

Program :
  - Expression `..`
  - LetDefinition+ Expression `..`

##### Example

```graffiticode
let addAll = <[hd ...tl] total = 0:
    | Recurse over a list of numbers and return the sum of all element values.
    if len tl
       then fn tl add total hd
       else total
>..

addAll [1 2 3 4 5 6 7 8 9 10]..  | Yields the value 55
```

LetDefinition :
  - `let` Name = Expression `..`

Expression :
  - Name
  - Value
  - ApplyExpr
  - CaseExpr
  - IfExpr

Value :
  - IntValue
  - FloatValue
  - StringValue
  - BooleanValue
  - NullValue
  - EnumValue
  - ListValue
  - RecordValue
  - LambdaValue

BooleanValue : one of `true` `false`

NullValue : `null`

EnumValue : Name but not `true`, `false` or `null`

ListValue :
  - `[` `]`
  - `[` Value+ `]`

RecordValue :
  - `{` `}`
  - `{` Field+ `}`

Field : Name : Value

LambdaValue :
  - `<` `:` Expression `>`
  - `<` Pattern+ `:` Expression `>`

ApplyExpr :
  - Name
  - Name Expression+

CaseExpr :
  - `case` Expression OfExpr+ `end`

##### Example

```graffiticode
case x
  of 10:
    10
  of _:
    add x 1
end
>..
```
OfExpr :
  - `of` Pattern `:` Expression

Pattern :
  - Name
  - ListPattern
  - RecordPattern

ListPattern :
  - `[` RestPattern? `]`
  - `[` Pattern+ RestPattern? `]`

RestPattern :
  - `...` Name

RecordPattern :
  - `{` RestPattern? `}`
  - `{` Name+ RestPattern? `}`

IfExpr :
  - `if` Expression `then` Expression
  - `if` Expression `then` Expression `else` Expression
