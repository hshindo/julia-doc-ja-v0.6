[](# Variables)
# 変数

```@raw html
<!--
A variable, in Julia, is a name associated (or bound) to a value. It's useful when you want to
store a value (that you obtained after some math, for example) for later use. For example:
-->
```
Juliaにおける変数は、値に関連（または紐づく）する名前になります。これは、
計算によって得た値などを後に使用するために保存する際に便利です。以下は例です。

```julia-repl
# Assign the value 10 to the variable x
julia> x = 10
10

# Doing math with x's value
julia> x + 1
11

# Reassign x's value
julia> x = 1 + 1
2

# You can assign values of other types, like strings of text
julia> x = "Hello World!"
"Hello World!"
```

```@raw html
<!--
Julia provides an extremely flexible system for naming variables. Variable names are case-sensitive,
and have no semantic meaning (that is, the language will not treat variables differently based
on their names).
-->
```
Juliaでは変数名を柔軟に設定することができます。変数名は大文字と小文字が区別され、言語的な意味を持ちません。

```jldoctest
julia> x = 1.0
1.0

julia> y = -3
-3

julia> Z = "My string"
"My string"

julia> customary_phrase = "Hello world!"
"Hello world!"

julia> UniversalDeclarationOfHumanRightsStart = "人人生而自由，在尊严和权利上一律平等。"
"人人生而自由，在尊严和权利上一律平等。"
```

```@raw html
<!--
Unicode names (in UTF-8 encoding) are allowed:
-->
```
Unicode（UTF-8）の変数名を使用することができます。

```jldoctest
julia> δ = 0.00001
1.0e-5

julia> 안녕하세요 = "Hello"
"Hello"
```
```@raw html
<!--
In the Julia REPL and several other Julia editing environments, you can type many Unicode math
symbols by typing the backslashed LaTeX symbol name followed by tab. For example, the variable
name `δ` can be entered by typing `\delta`-*tab*, or even `α̂₂` by `\alpha`-*tab*-`\hat`-
*tab*-`\_2`-*tab*. (If you find a symbol somewhere, e.g. in someone else's code,
that you don't know how to type, the REPL help will tell you: just type `?` and
then paste the symbol.)
-->
```
REPLおよび他のJuliaの編集環境では、バックスラッシュを伴うLaTex記号とタブを
入力することで、Unicode数学記号を入力することが可能です。例えば、 変数名
`δ` は、`\delta`-*tab* とすることで入力できます。 また、 `α̂₂`
は、`\alpha`-*tab*-`\hat`-*tab*-`\_2`-*tab* とすることで入力できます。

```@raw html
<!--
Julia will even let you redefine built-in constants and functions if needed:
-->
```
Juliaでは、必要であればビルトインの定数や関数を再定義することができます。

```jldoctest
julia> pi
π = 3.1415926535897...

julia> pi = 3
WARNING: imported binding for pi overwritten in module Main
3

julia> pi
3

julia> sqrt(100)
10.0

julia> sqrt = 4
WARNING: imported binding for sqrt overwritten in module Main
4
```
```@raw html
<!--
However, this is obviously not recommended to avoid potential confusion.
-->
```
しかし、これは混乱を避ける目的で、推奨されていません。

[](## Allowed Variable Names)
## 使用可能な変数名

```@raw html
<!--
Variable names must begin with a letter (A-Z or a-z), underscore, or a subset of Unicode code
points greater than 00A0; in particular, [Unicode character categories](http://www.fileformat.info/info/unicode/category/index.htm)
Lu/Ll/Lt/Lm/Lo/Nl (letters), Sc/So (currency and other symbols), and a few other letter-like characters
(e.g. a subset of the Sm math symbols) are allowed. Subsequent characters may also include ! and
digits (0-9 and other characters in categories Nd/No), as well as other Unicode code points: diacritics
and other modifying marks (categories Mn/Mc/Me/Sk), some punctuation connectors (category Pc),
primes, and a few other characters.
-->
```
変数名は、文字（AからZまたはaからz）、アンダースコア、もしくは00A0よりも大きなUnicodeの
サブセットの符号点である必要があります。特に、[Unicode文字カテゴリ](http://www.fileformat.info/info/unicode/category/index.htm)
のLu/Ll/Lt/Lm/Lo/Nl （文字）、
Sc/So（通貨とその他の記号）、その他の記号（Sm数学記号のサブセットなど）を使用することができます。
！や数字（0から9とNd･Noカテゴリ内のその他の記号）、その他のUnicode符号点
（発音区別符号およびその他の修飾文字（Mn/Mc/Me/Skカテゴリ）、句読点コネクタ（Pcカテゴリ）、
プライム記号、その他の文字）を使用することができます。

```@raw html
<!--
Operators like `+` are also valid identifiers, but are parsed specially. In some contexts, operators
can be used just like variables; for example `(+)` refers to the addition function, and `(+) = f`
will reassign it. Most of the Unicode infix operators (in category Sm), such as `⊕`, are parsed
as infix operators and are available for user-defined methods (e.g. you can use `const ⊗ = kron`
to define `⊗` as an infix Kronecker product).
-->
```
例えば `+`
のような演算子も変数名として有効な識別子ですが、異なった解析がされます。ある文脈では、
演算子は変数のように使用することができます。例えば、 `(+)`
は足し算を意味し、 `(+) = f` は 再割り当てを行います。 `⊕`
などのUnicode中置演算子（Smカテゴリ）のほとんどは、
中置演算子として解析され、ユーザ定義のメソッド（ `⊕`
をクロネッカー積として定義するために `const ⊗ = kron` を
使用する等）として使用することが可能です。

```@raw html
<!--
The only explicitly disallowed names for variables are the names of built-in statements:
-->
```
ビルトインステートメントの名前のみを変数名として使用することはできません

```julia-rep
julia> else = false
ERROR: syntax: unexpected "else"

julia> try = "No"
ERROR: syntax: unexpected "="
```

Some Unicode characters are considered to be equivalent in identifiers.
Different ways of entering Unicode combining characters (e.g., accents)
are treated as equivalent (specifically, Julia identifiers are NFC-normalized).
The Unicode characters `ɛ` (U+025B: Latin small letter open e)
and `µ` (U+00B5: micro sign) are treated as equivalent to the corresponding
Greek letters, because the former are easily accessible via some input methods.

[](## Stylistic Conventions)
## 文体表記

```@raw html
<!--
While Julia imposes few restrictions on valid names, it has become useful to adopt the following
conventions:
-->
```
Juliaは変数名にいくつかの制限を設けていますが、以下の表記法を使用するのに便利になっています。

[](  * Names of variables are in lower case.)
[](  * Word separation can be indicated by underscores (`'_'`), but use of underscores is discouraged)
[](    unless the name would be hard to read otherwise.)
[](  * Names of `Type`s and `Module`s begin with a capital letter and word separation is shown with upper)
[](    camel case instead of underscores.)
[](  * Names of `function`s and `macro`s are in lower case, without underscores.)
[](  * Functions that write to their arguments have names that end in `!`. These are sometimes called)
[](    "mutating" or "in-place" functions because they are intended to produce changes in their arguments)
[](    after the function is called, not just return a value.)

*   変数名を小文字で表記する。
*   単語の区切りはアンダースコア（ `'_'`
    ）で表すことができますが、変数名が読みにくい場合の除き使用は推奨されていません。
*   `型`と
    `モジュール名`は大文字で始まり、単語の区切りはアンダースコアの代わりにキャメルケースで表記されます。
*   `関数`や
    `マクロ`の名前はアンダースコアを含まない小文字で表記されます。
*   引数に書き込む関数名は `!`
    で終わります。これらの関数は、関数が呼び出された際に戻り値を返すだけでなく引数に変更を加えるため、「変異」または「in-place」と呼ばれています。

For more information about stylistic conventions, see the [Style Guide](@ref).
