[](# [Strings](@id man-strings))
# [文字列](@id man-strings)

```@raw html
<!--
Strings are finite sequences of characters. Of course, the real trouble comes when one asks what
a character is. The characters that English speakers are familiar with are the letters `A`, `B`,
`C`, etc., together with numerals and common punctuation symbols. These characters are standardized
together with a mapping to integer values between 0 and 127 by the [ASCII](https://en.wikipedia.org/wiki/ASCII)
standard. There are, of course, many other characters used in non-English languages, including
variants of the ASCII characters with accents and other modifications, related scripts such as
Cyrillic and Greek, and scripts completely unrelated to ASCII and English, including Arabic, Chinese,
Hebrew, Hindi, Japanese, and Korean. The [Unicode](https://en.wikipedia.org/wiki/Unicode) standard
tackles the complexities of what exactly a character is, and is generally accepted as the definitive
standard addressing this problem. Depending on your needs, you can either ignore these complexities
entirely and just pretend that only ASCII characters exist, or you can write code that can handle
any of the characters or encodings that one may encounter when handling non-ASCII text. Julia
makes dealing with plain ASCII text simple and efficient, and handling Unicode is as simple and
efficient as possible. In particular, you can write C-style string code to process ASCII strings,
and they will work as expected, both in terms of performance and semantics. If such code encounters
non-ASCII text, it will gracefully fail with a clear error message, rather than silently introducing
corrupt results. When this happens, modifying the code to handle non-ASCII data is straightforward.
-->
```

文字列は有限の記号の連続を意味します。ここでいう記号とは一般的な意味のそれとは少し違います。
英語話者にとって馴染みのある記号は `A` 、`B` 、`C` 、などの文字と、アラビア数字や句読記号です。これらの文字は、[ASCII](https://ja.wikipedia.org/wiki/ASCII) 規格
において、0から127の番号をそれぞれ割り振られて標準化されています。もちろん、ラテン文字だけが世界で使われているわけではありません。アクセント記号やその他の変形ASCII文字や、
英語の言語体系に関連を持つロシア語やギリシャ語、および英語の言語体系に関連を持たないアラビア語、中国語、ヘブライ語、ヒンディー語、韓国語、そして日本語などは
それぞれ独自の文字を有しています。 [Unicode](https://ja.wikipedia.org/wiki/Unicode) 規格は、ある文字が一体何なのかという複雑な問題に取り組んでおり、また、
この問題に対処する決定的な規格として一般的に認識されています。あなたの必要に応じて、これらの複雑な問題を気にせずASCII文字だけを使用したり、非ASCIIテキストを扱う際に
見かける様々な文字やエンコードを使用したコードを書くことができます。JuliaはASCIIテキストの取り扱いをシンプルかつ効率的にし、Unicodeの取り扱いも同様に可能な限りシンプル
かつ効率的にします。特に、ASCIIの文字列を処理するためにC言語スタイルに文字列コードを書くことができ、パフォーマンスと乞うっ文的な意味の両面で期待通りの動作をします。
JuliaはASCII以外のテキストが検出された場合、エラーをただ出力するのではなく、明確なエラーメッセージを出力して処理を終了します。この場合、非ASCIIデータを処理できるように
修正することは簡単です。

```@raw html
<!--
There are a few noteworthy high-level features about Julia's strings:

  * The built-in concrete type used for strings (and string literals) in Julia is [`String`](@ref).
    This supports the full range of [Unicode](https://en.wikipedia.org/wiki/Unicode) characters via
    the [UTF-8](https://en.wikipedia.org/wiki/UTF-8) encoding. (A [`transcode()`](@ref) function is
    provided to convert to/from other Unicode encodings.)
  * All string types are subtypes of the abstract type `AbstractString`, and external packages define
    additional `AbstractString` subtypes (e.g. for other encodings).  If you define a function expecting
    a string argument, you should declare the type as `AbstractString` in order to accept any string
    type.
  * Like C and Java, but unlike most dynamic languages, Julia has a first-class type representing
    a single character, called `Char`. This is just a special kind of 32-bit primitive type whose numeric
    value represents a Unicode code point.
  * As in Java, strings are immutable: the value of an `AbstractString` object cannot be changed.
    To construct a different string value, you construct a new string from parts of other strings.
  * Conceptually, a string is a *partial function* from indices to characters: for some index values,
    no character value is returned, and instead an exception is thrown. This allows for efficient
    indexing into strings by the byte index of an encoded representation rather than by a character
    index, which cannot be implemented both efficiently and simply for variable-width encodings of
    Unicode strings.
-->
```

Juliaには、文字列に関係する特筆すべき高度な機能があります。

  * Juliaにおける文字列を扱うビルトインの型は [`String`](@ref) です。これは、[UTF-8](https://ja.wikipedia.org/wiki/UTF-8) エンコーディング下で
    全ての [Unicode](https://ja.wikipedia.org/wiki/Unicode) をサポートします。 (Unicodeエンコードの変換を行うために [`transcode()`](@ref) 関数が
    提供されていす。)
  * すべての文字列型は抽象型 `AbstractString` のサブタイプであり、外部パッケージは追加の `AbstractString` のサブタイプ（例えばその他のエンコーディング用）
    を定義します。文字列の引数を扱う関数を定義する場合は、任意の文字列型を使用するために `AbstractString` を型として宣言する必要があります。
  * C言語やJavaと同様に、しかしほとんどの動的言語とは異なり、Juliaには `Char` と呼ばれる1文字を表す第1級型があります。
    これは数値がUnicodeコードポイントを表す特殊な種類の32ビットのビット型です。
  * Javaと同様に、文字列は変更不可能です。つまり `AbstractString` オブジェクトの値は変更できません。異なる文字列の値を作成したい場合は、他の文字列の一部から新しく作成するのが良いでしょう。
  * 概念的には、文字列はインデックスから文字への部分的な関数です。インデックス値によっては文字列は返されず、例外が出力されます。これは、Unicode文字列
    の可変幅のエンコードに対して効率的かつシンプルに実装できない文字列インデックスではなく、エンコードされた表現のバイトインデックスによる文字列
    への効率的なインデックスを可能にします。

[](## [Characters](@id man-characters))
## [文字](@id man-characters)

```@raw html
<!--
A `Char` value represents a single character: it is just a 32-bit primitive type with a special literal
representation and appropriate arithmetic behaviors, whose numeric value is interpreted as a
[Unicode code point](https://en.wikipedia.org/wiki/Code_point). Here is how `Char` values are
input and shown:
-->
```

`Char` 値は単体の文字を表します。これは特殊なリテラル表現と適切な算術演算を持つ32ビットのプリミティブタイプであり、
数値は [Unicodeコードポイント](https://ja.wikipedia.org/wiki/符号点) として解釈されます。
`Char` 値の入力と表示方法は以下の通りです。:

```jldoctest
julia> 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> typeof(ans)
Char
```

[](You can convert a `Char` to its integer value, i.e. code point, easily:)

`Char` を整数値（コードポイント）へと簡単に変換することができます。:

```jldoctest
julia> Int('x')
120

julia> typeof(ans)
Int64
```

```@raw html
<!--
On 32-bit architectures, [`typeof(ans)`](@ref) will be [`Int32`](@ref). You can convert an
integer value back to a `Char` just as easily:
-->
```

32ビットのアーキテクチャ上では、 [`typeof(ans)`](@ref) は [`Int32`](@ref) を返します。
また、整数値を簡単に `Char` へと変換し直すこともできます。:

```jldoctest
julia> Char(120)
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)
```

```@raw html
<!--
Not all integer values are valid Unicode code points, but for performance, the `Char()` conversion
does not check that every character value is valid. If you want to check that each converted value
is a valid code point, use the [`isvalid()`](@ref) function:
-->
```

全ての整数値が有効なUnicodeコードポイントではありません。しかしパフォーマンスの為に、Juliaは `Char()` の呼び出しの際に
引数が有効なUnicodeコードポイントであるかをチェックしません。
もし変換された値が有効なコードポイントであるか確認したい場合は、 [`isvalid()`](@ref) 関数を使用してください。:

```jldoctest
julia> Char(0x110000)
'\U110000': Unicode U+110000 (category Cn: Other, not assigned)

julia> isvalid(Char, 0x110000)
false
```

```@raw html
<!--
As of this writing, the valid Unicode code points are `U+00` through `U+d7ff` and `U+e000` through
`U+10ffff`. These have not all been assigned intelligible meanings yet, nor are they necessarily
interpretable by applications, but all of these values are considered to be valid Unicode characters.

You can input any Unicode character in single quotes using `\u` followed by up to four hexadecimal
digits or `\U` followed by up to eight hexadecimal digits (the longest valid value only requires
six):
-->
```

現時点では、有効なコードポイントは `U+00` から `U+d7ff`　までと `U+e000` から `U+10ffff` までとなっています。
これら全てのコードポイントに記号が割り当てられているわけではなく、また必ずしもアプリケーションによっては解釈できないものもありますが、
これら全てのコードポイントは有効なUnicode文字としてみなされます。

シングルクオーテンションで囲まれたエリアの中では、`\u` に続けて最大4桁の16進数を入力するか、`\U`に続けて最大8桁の16進数（6桁から有効）を入力することで、
Unicode文字を入力することができます。:


```jldoctest
julia> '\u0'
'\0': ASCII/Unicode U+0000 (category Cc: Other, control)

julia> '\u78'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> '\u2200'
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> '\U10ffff'
'\U10ffff': Unicode U+10ffff (category Cn: Other, not assigned)
```

```@raw html
<!--
Julia uses your system's locale and language settings to determine which characters can be printed
as-is and which must be output using the generic, escaped `\u` or `\U` input forms. In addition
to these Unicode escape forms, all of [C's traditional escaped input forms](https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes)
can also be used:
-->
```

Juliaは、システムのロケールと言語設定を加味して、どの文字が支障なく出力できるか、そしてどの文字が `\u` または `\U` を使用してエスケープされた状態で出力すべきか判断します。
これらのUnicodeエスケープ方式に加えて、 [C言語のエスケープ形式](https://en.wikipedia.org/wiki/C_syntax#C¥Backslash_escapes) （訳注:ウィキペディア英語版）を用いた入力も可能です。:

```jldoctest
julia> Int('\0')
0

julia> Int('\t')
9

julia> Int('\n')
10

julia> Int('\e')
27

julia> Int('\x7f')
127

julia> Int('\177')
127

julia> Int('\xff')
255
```

[](You can do comparisons and a limited amount of arithmetic with `Char` values:)

`Char` 値を使用して限定的な算術演算や `Char` 同士の比較を行うことができます。:

```jldoctest
julia> 'A' < 'a'
true

julia> 'A' <= 'a' <= 'Z'
false

julia> 'A' <= 'X' <= 'Z'
true

julia> 'x' - 'a'
23

julia> 'A' + 1
'B': ASCII/Unicode U+0042 (category Lu: Letter, uppercase)
```

[](## String Basics)
## 文字列の基礎

[](String literals are delimited by double quotes or triple double quotes:)

文字列リテラルはダブルクオーテーションまたは3つのダブルクオーテーションで区切られます。:

```jldoctest helloworldstring
julia> str = "Hello, world.\n"
"Hello, world.\n"

julia> """Contains "quote" characters"""
"Contains \"quote\" characters"
```

[](If you want to extract a character from a string, you index into it:)

文字列から文字を抽出する場合はその文字列にインデックスを添える必要があります。

```jldoctest helloworldstring
julia> str[1]
'H': ASCII/Unicode U+0048 (category Lu: Letter, uppercase)

julia> str[6]
',': ASCII/Unicode U+002c (category Po: Punctuation, other)

julia> str[end]
'\n': ASCII/Unicode U+000a (category Cc: Other, control)
```

```@raw html
<!--
All indexing in Julia is 1-based: the first element of any integer-indexed object is found at
index 1. (As we will see below, this does not necessarily mean that the last element is found
at index `n`, where `n` is the length of the string.)

In any indexing expression, the keyword `end` can be used as a shorthand for the last index (computed
by [`endof(str)`](@ref)). You can perform arithmetic and other operations with `end`, just like
a normal value:
-->
```

Julia のインデックスは全て1から始まります。つまり、整数インデックスを持つオブジェクトの最初の要素のインデックスは1になります。
（しかし、下にある例が示すように、文字列が持つ最後の要素のインデックスが文字列の長さ `n` を持つとは限りません。）

どのインデックス表現でも、キーワード `end` は（[`endof(str)`](@ref) で求められる）インデックスの末尾の省略形として使うことができます。
`end` は通常の値と同様に、算術演算やその他の処理を行うことができます。


```jldoctest helloworldstring
julia> str[end-1]
'.': ASCII/Unicode U+002e (category Po: Punctuation, other)

julia> str[end÷2]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```

[](Using an index less than 1 or greater than `end` raises an error:)
1未満または `end` よりも大きい値のインデックスを使用した場合、エラーが出力されます。:

```jldoctest helloworldstring
julia> str[0]
ERROR: BoundsError: attempt to access "Hello, world.\n"
  at index [0]
[...]

julia> str[end+1]
ERROR: BoundsError: attempt to access "Hello, world.\n"
  at index [15]
[...]
```

[](You can also extract a substring using range indexing:)
範囲のインデックスを使用することで、文字列の一部分を抽出することができます。:

```jldoctest helloworldstring
julia> str[4:9]
"lo, wo"
```

[](Notice that the expressions `str[k]` and `str[k:k]` do not give the same result:)
`str[k]` と `str[k:k]` は同じ結果にならないことに留意してください。:

```jldoctest helloworldstring
julia> str[6]
',': ASCII/Unicode U+002c (category Po: Punctuation, other)

julia> str[6:6]
","
```

```@raw html
<!--
The former is a single character value of type `Char`, while the latter is a string value that
happens to contain only a single character. In Julia these are very different things.
-->
```

前者の記法では一文字のみを取得することが想定されているので、前者は `Char` 型になります。
Juliaでは `Char` 型と `String` 型はとても異なったものです。

[](## Unicode and UTF-8)
## UnicodeおよびUTF-8

```@raw html
<!--
Julia fully supports Unicode characters and strings. As [discussed above](@ref man-characters), in character
literals, Unicode code points can be represented using Unicode `\u` and `\U` escape sequences,
as well as all the standard C escape sequences. These can likewise be used to write string literals:
-->
```

JuliaはUnicode文字とUnicode文字列をサポートします。[上記](@ref man-characters)の通り、
文字列リテラルでは、Unicodeコードポイントは、C言語の標準的なエスケープ方法と同様に、Unicodeは `\u` および `\U` の
エスケープを使用することで表すことができます。これらは同様に文字列リテラルを書くために使用することができます。:

```jldoctest unicodestring
julia> s = "\u2200 x \u2203 y"
"∀ x ∃ y"
```

```@raw html
<!--
Whether these Unicode characters are displayed as escapes or shown as special characters depends
on your terminal's locale settings and its support for Unicode. String literals are encoded using
the UTF-8 encoding. UTF-8 is a variable-width encoding, meaning that not all characters are encoded
in the same number of bytes. In UTF-8, ASCII characters -- i.e. those with code points less than
0x80 (128) -- are encoded as they are in ASCII, using a single byte, while code points 0x80 and
above are encoded using multiple bytes -- up to four per character. This means that not every
byte index into a UTF-8 string is necessarily a valid index for a character. If you index into
a string at such an invalid byte index, an error is thrown:
-->
```

これらのUnicode文字がエスケープとして出力されるか特殊な文字として出力されるかは、あなたの端末のロケール設定とそのUnicodeのサポート
の状況に依存します。文字列リテラルはUTF-8エンコードを使用してエンコードされます。UTF-8は可変幅のエンコードであり、
全ての文字が同じバイト数でエンコードされるわけではありません。UTF-8では、ASCII、つまり0x80(128)以下のコードポイントの文字は
1バイトでASCIIとしてエンコードされ、0x80以上のコードポイントは、1文字あたり最大4バイトのマルチバイトでエンコードされます。
これは、必ずしも全てのUTF-8文字列のインデックスバイトが文字に対して有効なインデックスではないことを意味します。
このような無効なバイトインデックスで文字列にインデックスを付けた場合、例外が投げられます。:

```jldoctest unicodestring
julia> s[1]
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> s[2]
ERROR: UnicodeError: invalid character index
[...]

julia> s[3]
ERROR: UnicodeError: invalid character index
[...]

julia> s[4]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```

```@raw html
<!--
In this case, the character `∀` is a three-byte character, so the indices 2 and 3 are invalid
and the next character's index is 4; this next valid index can be computed by [`nextind(s,1)`](@ref),
and the next index after that by `nextind(s,4)` and so on.
-->
```

この場合、`∀` は3バイト文字であるため、インデックス2と3は無効であり、次の有効なインデックスは4になります。
また、この場合、次の有効なインデックスは [`nextind(s,1)`](@ref) で求めることができ、その次の有効なインデックスは `nextind(s,4)` で計算でき、
その次は...と求めることができます。

```@raw html
<!--
Because of variable-length encodings, the number of characters in a string (given by [`length(s)`](@ref))
is not always the same as the last index. If you iterate through the indices 1 through [`endof(s)`](@ref)
and index into `s`, the sequence of characters returned when errors aren't thrown is the sequence
of characters comprising the string `s`. Thus we have the identity that `length(s) <= endof(s)`,
since each character in a string must have its own index. The following is an inefficient and
verbose way to iterate through the characters of `s`:
-->
```

可変長インデックスなので、文字列内の文字数（ [`length(s)`](@ref) により取得可能）は、
常に末尾のインデックスの数と同一というわけではありません。インデックス1から [`endof(s)`](@ref) まで繰り返し処理を行い、 `s`
にインデックスする場合、エラーが出力されなければ文字列 `s` を構成する一連の文字列が返されます。
したがって、文字列内のそれぞれの文字は固有のインデックスを持つため、 `length(s) <= endof(s)` を使用して識別ができます。
下の例は `s` という文字を反復処理する非効率的で冗長的な方法です。:

```jldoctest unicodestring
julia> for i = 1:endof(s)
           try
               println(s[i])
           catch
               # ignore the index error
           end
       end
∀

x

∃

y
```

```@raw html
<!--
The blank lines actually have spaces on them. Fortunately, the above awkward idiom is unnecessary
for iterating through the characters in a string, since you can just use the string as an iterable
object, no exception handling required:
-->
```

空白行には実際にはスペースがあります。幸運なことに反復可能なオブジェクトとして文字列を使うことができ、それにより例外処理が不要になるため、
上の例のような冗長的な記述は不要です。:

```jldoctest unicodestring
julia> for c in s
           println(c)
       end
∀

x

∃

y
```

```@raw html
<!--
Julia uses the UTF-8 encoding by default, and support for new encodings can be added by packages.
For example, the [LegacyStrings.jl](https://github.com/JuliaArchive/LegacyStrings.jl) package
implements `UTF16String` and `UTF32String` types. Additional discussion of other encodings and
how to implement support for them is beyond the scope of this document for the time being. For
further discussion of UTF-8 encoding issues, see the section below on [byte array literals](@ref man-byte-array-literals).
The [`transcode()`](@ref) function is provided to convert data between the various UTF-xx encodings,
primarily for working with external data and libraries.
-->
```

JuliaはデフォルトでUTF-8エンコーディングを使用し、加えてパッケージで追加できる新しいエンコーディングもサポートしています。
例えば、[LegacyStrings.jl](https://github.com/JuliaArchive/LegacyStrings.jl) パッケージは `UTF16String` および `UTF32String` 型を実装しています。
その他のエンコーディングとその実装方法は当ドキュメントの対象外となります。UTF-8に関するさらなる議論については、以下の[バイト配列リテラル](@ref man-byte-array-literals)
のセクションを参照してください。 [`transcode()`](@ref) 関数は、主に外部データやライブラリを扱うために、様々なUTF-xxエンコーディング間のデータ変換を提供します。

[](## Concatenation)
## 連結

[](One of the most common and useful string operations is concatenation:)

最も一般的で便利な文字列操作の一つが連結です。:

```jldoctest stringconcat
julia> greet = "Hello"
"Hello"

julia> whom = "world"
"world"

julia> string(greet, ", ", whom, ".\n")
"Hello, world.\n"
```

[](Julia also provides `*` for string concatenation:)

Juliaは `*` も文字列連結の記述として提供しています。

```jldoctest stringconcat
julia> greet * ", " * whom * ".\n"
"Hello, world.\n"
```

```@raw html
<!--
While `*` may seem like a surprising choice to users of languages that provide `+` for string
concatenation, this use of `*` has precedent in mathematics, particularly in abstract algebra.
-->
```

`+` を文字列連結の記述としている言語の使用者にとっては Juliaが `*` をそれとするのは奇抜に見えるかもしれませんが、
この `*` の使われ方は数学、特に抽象代数学においては一般的です。

```@raw html
<!--
In mathematics, `+` usually denotes a *commutative* operation, where the order of the operands does
not matter. An example of this is matrix addition, where `A + B == B + A` for any matrices `A` and `B`
that have the same shape. In contrast, `*` typically denotes a *noncommutative* operation, where the
order of the operands *does* matter. An example of this is matrix multiplication, where in general
`A * B != B * A`. As with matrix multiplication, string concatenation is noncommutative:
`greet * whom != whom * greet`. As such, `*` is a more natural choice for an infix string concatenation
operator, consistent with common mathematical use.
-->
```

数学では、`+` はしばしば **可換** 演算（被演算子の順番が関係ない演算）を表します。
例えば行列の加算は、`A + B == B + A` は `A` と `B` が同じサイズで有る限り成り立つ可換演算です。
これに対して `*` はよく **非可換** 演算（被演算子の順番が関係 **ある** 演算）を表します。
例えば行列の乗算は、`A * B != B * A` が基本的に成り立つ非可換演算です。
そして文字列の連結は `greet * whom != whom * greet` であるように行列の乗算と同様、非可換演算です。
これらのことから、 文字列連結演算子としての `*` はより数学においての使われ方に準拠した、自然な選択といえるでしょう。

```@raw html
<!--
More precisely, the set of all finite-length strings *S* together with the string concatenation operator
`*` forms a [free monoid](https://en.wikipedia.org/wiki/Free_monoid) (*S*, `*`). The identity element
of this set is the empty string, `""`. Whenever a free monoid is not commutative, the operation is
typically represented as `\cdot`, `*`, or a similar symbol, rather than `+`, which as stated usually
implies commutativity.
-->
```

より厳密に言えば、全ての有限の長さを持つ文字列の集合 *S* と文字列連結演算子 `*` は [自由モノイド](https://en.wikipedia.org/wiki/Free_monoid) (*S*, `*`) を形成します。
この集合の単位元は空の文字列 `""` です。 自由モノイドが非可換である時、連結演算子は、しばしば可換の状態であることを表す時に用いられる`+` よりも `\cdot` や `*` 、その他の類似した記号で表されることが多いです。



[](## [Interpolation](@id string-interpolation))
## [補間](@id string-interpolation)

```@raw html
<!--
Constructing strings using concatenation can become a bit cumbersome, however. To reduce the need for these
verbose calls to [`string()`](@ref) or repeated multiplications, Julia allows interpolation into string literals
using `$`, as in Perl:
-->
```

しかしながら、連結を使って文字列を構成するのは少々扱いにくくなってしまうかもしれません。
これらの冗長な [`string()`](@ref) もしくは `*` の呼び出しの機会を減らすために、JuliaはPerlのように `$` を使用して文字列リテラルに補間を行うことができます。:

```jldoctest stringconcat
julia> "$greet, $whom.\n"
"Hello, world.\n"
```

```@raw html
<!--
This is more readable and convenient and equivalent to the above string concatenation -- the system
rewrites this apparent single string literal into a concatenation of string literals with variables.

The shortest complete expression after the `$` is taken as the expression whose value is to be
interpolated into the string. Thus, you can interpolate any expression into a string using parentheses:
-->
```

この方法は呼びやすくかつ便利です。システムが単一文字リテラルを変数と文字列リテラルの連結に書き換えるので、上記の文字列連結と動作は変わりません。

`$` の後の最短の完全な式は、値が文字列に補間される式として解釈されます。したがって、括弧を使用して任意の式を文字列に補間することができます。:

```jldoctest
julia> "1 + 2 = $(1 + 2)"
"1 + 2 = 3"
```

```@raw html
<!--
Both concatenation and string interpolation call [`string()`](@ref) to convert objects into string
form. Most non-`AbstractString` objects are converted to strings closely corresponding to how
they are entered as literal expressions:
-->
```

補間と文字列連結はいずれもオブジェクトを文字列形式に変換するために [`string()`](@ref) を呼び出します。
ほとんどの非 `AbstractString` オブジェクトは、どのようにリテラル表現として入力されるかに対応する文字列に変換されます。:

```jldoctest
julia> v = [1,2,3]
3-element Array{Int64,1}:
 1
 2
 3

julia> "v: $v"
"v: [1, 2, 3]"
```

```@raw html
<!--
[`string()`](@ref) is the identity for `AbstractString` and `Char` values, so these are interpolated
into strings as themselves, unquoted and unescaped:
-->
```

[`string()`](@ref) は `AbstractString` および `Char` 値と同一です。そのため、これらはクオーテーションが付かず、
エスケープもされずにそのまま文字列に補間されます。:

```jldoctest
julia> c = 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> "hi, $c"
"hi, x"
```

[](To include a literal `$` in a string literal, escape it with a backslash:)

文字列リテラルにリテラル　`$` を含める場合は、バックスラッシュを使ってエスケープする必要があります。

```jldoctest
julia> print("I have \$100 in my account.\n")
I have $100 in my account.
```

[](## Triple-Quoted String Literals)
## 3つのダブルクオーテーション

```@raw html
<!--
When strings are created using triple-quotes (`"""..."""`) they have some special behavior that
can be useful for creating longer blocks of text. First, if the opening `"""` is followed by a
newline, the newline is stripped from the resulting string.
-->
```

文字列が3つのダブルクオーテーション(`"""..."""`)を使用して作成される場合、長いテキストを作成するのに便利な
特殊な動作をします。まず、始めの `"""` の後ろに改行が続いた場合、結果の文字列から改行は取り除かれます。

```julia
"""hello"""
```

[](is equivalent to)

は以下と同等です。

```julia
"""
hello"""
```

[](but)
しかし

```julia
"""

hello"""
```

```@raw html
<!--
will contain a literal newline at the beginning. Trailing whitespace is left unaltered. They can
contain `"` symbols without escaping. Triple-quoted strings are also dedented to the level of
the least-indented line. This is useful for defining strings within code that is indented. For
example:
-->
```

これは最初にリテラル改行を含みます。末尾の空白は変更されません。これらはエスケープなしで　`"` 記号を含めることができます。
3つのダブルクオーテーションで囲まれた文字列は、最もインデントされていない行合わせでインデントします。これはインデントされたコード内
の文字列を定義するのに便利です。例えば:

```jldoctest
julia> str = """
           Hello,
           world.
         """
"  Hello,\n  world.\n"
```

```@raw html
<!--
In this case the final (empty) line before the closing `"""` sets the indentation level.
-->
```

この場合、閉じる `"""` の前の最後の（空の）行はインデントレベルを定義します。

```@raw html
<!--
Note that line breaks in literal strings, whether single- or triple-quoted, result in a newline
(LF) character `\n` in the string, even if your editor uses a carriage return `\r` (CR) or CRLF
combination to end lines. To include a CR in a string, use an explicit escape `\r`; for example,
you can enter the literal string `"a CRLF line ending\r\n"`.
-->
```

シングルクオートかトリプルクオートかどうかに関係なく、文字列リテラルの改行は、
たとえあなたのエディタがキャリッジリターン `\r` (CR) またはCRLFの組み合わせを行末に使っているとしても、文字列内の改行(LF)文字 `\n` になります。
文字列にCRを含めるためには、明示的に `\r` でエスケープを使用してください。例えば、文字列リテラル `"a CRLF line ending\r\n"` を入力することができます。

[](## Common Operations)
## 共通処理

[](You can lexicographically compare strings using the standard comparison operators:)

文字列を標準比較演算子を使用して比較できます。:

```jldoctest
julia> "abracadabra" < "xylophone"
true

julia> "abracadabra" == "xylophone"
false

julia> "Hello, world." != "Goodbye, world."
true

julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
true
```

```@raw html
<!--
You can search for the index of a particular character using the [`search()`](@ref) function:
-->
```

[`search()`](@ref) 関数を使用して特定の文字のインデックスを検索することができます。:

```jldoctest
julia> search("xylophone", 'x')
1

julia> search("xylophone", 'p')
5

julia> search("xylophone", 'z')
0
```

[](You can start the search for a character at a given offset by providing a third argument:)

3番目の引数を指定することで指定されたオフセットで文字の検索をすることができます。:

```jldoctest
julia> search("xylophone", 'o')
4

julia> search("xylophone", 'o', 5)
7

julia> search("xylophone", 'o', 8)
0
```

```@raw html
<!--
You can use the [`contains()`](@ref) function to check if a substring is contained in a string:
-->
```

[`contains()`](@ref) 関数を使用して、文字列に部分文字列が含まれているか確認することができます。:

```jldoctest
julia> contains("Hello, world.", "world")
true

julia> contains("Xylophon", "o")
true

julia> contains("Xylophon", "a")
false

julia> contains("Xylophon", 'o')
ERROR: MethodError: no method matching contains(::String, ::Char)
Closest candidates are:
  contains(!Matched::Function, ::Any, !Matched::Any) at reduce.jl:664
  contains(::AbstractString, !Matched::AbstractString) at strings/search.jl:378
```

```@raw html
<!--
The last error is because `'o'` is a character literal, and [`contains()`](@ref) is a generic
function that looks for subsequences. To look for an element in a sequence, you must use [`in()`](@ref)
instead.

Two other handy string functions are [`repeat()`](@ref) and [`join()`](@ref):
-->
```

最後のエラーは、`'o'` が文字リテラルであり、 [`contains()`](@ref) は部分列を探す汎用関数であるために起こりました。
文字リテラルを使って文字列ないの要素を探すためには [`in()`](@ref) を使用する必要があります。

その他の2つの便利な文字列関数は [`repeat()`](@ref) および [`join()`](@ref) 関数です。:

```jldoctest
julia> repeat(".:Z:.", 10)
".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

julia> join(["apples", "bananas", "pineapples"], ", ", " and ")
"apples, bananas and pineapples"
```

```@raw html
<!--
Some other useful functions include:

  * [`endof(str)`](@ref) gives the maximal (byte) index that can be used to index into `str`.
  * [`length(str)`](@ref) the number of characters in `str`.
  * [`i = start(str)`](@ref start) gives the first valid index at which a character can be found in `str`
    (typically 1).
  * [`c, j = next(str,i)`](@ref next) returns next character at or after the index `i` and the next valid
    character index following that. With [`start()`](@ref) and [`endof()`](@ref), can be used to iterate
    through the characters in `str`.
  * [`ind2chr(str,i)`](@ref) gives the number of characters in `str` up to and including any at index
    `i`.
  * [`chr2ind(str,j)`](@ref) gives the index at which the `j`th character in `str` occurs.
-->
```

その他の便利な関数を挙げておきます。:

  * [`endof(str)`](@ref) は `str` へのインデックス付けに使用できる最大の（バイト）インデックスを返します。
  * [`length(str)`](@ref) は `str` の文字数を返します。
  * [`i = start(str)`](@ref start) は、`str` にある最初の有効なインデックス(通常は1)を返します。
  * [`c, j = next(str,i)`](@ref next) はインデックス`i` に格納された文字とインデックス `i` 以降で最初の有効なインデックスを返します。
    [`start()`](@ref) や [`endof()`](@ref)　と併用することで、 `str` 内の文字を反復処理することができます。
  * [`ind2chr(str,i)`](@ref) はインデックス1からインデックス`i`までに格納されている文字数を返します。
  * [`chr2ind(str,j)`](@ref) は `str` の `j` 番目の文字が格納されているインデックスの値を返します。

[](## [Non-Standard String Literals](@id non-standard-string-literals))
## [非標準文字列リテラル](@id non-standard-string-literals)

```@raw html
<!--
There are situations when you want to construct a string or use string semantics, but the behavior
of the standard string construct is not quite what is needed. For these kinds of situations, Julia
provides [non-standard string literals](@ref). A non-standard string literal looks like a regular
double-quoted string literal, but is immediately prefixed by an identifier, and doesn't behave
quite like a normal string literal.  Regular expressions, byte array literals and version number
literals, as described below, are some examples of non-standard string literals. Other examples
are given in the [Metaprogramming](@ref) section.
-->
```

文字列を作成したり使用したいけれども、標準の文字列構成の挙動があまり目的に合致していない場合があるかもしれません。
そのような時のために、Juliaは [非標準文字列リテラル](@ref non-standard-string-literals) を提供しています。非標準文字列リテラルは通常のダブルクオーと文字列リテラル
と同じように見えますが、前に識別子が付されていて、通常の文字列リテラルとは異なった挙動をします。下で説明されている正規表現やバイト配列リテラル、
バージョン番号リテラルは非標準文字列リテラルに属します。その他の非標準文字列リテラルの例は [メタプログラミング](@ref Metaprogramming) のセクションで挙げられています。

[](## Regular Expressions)
## 正規表現

```@raw html
<!--
Julia has Perl-compatible regular expressions (regexes), as provided by the [PCRE](http://www.pcre.org/)
library. Regular expressions are related to strings in two ways: the obvious connection is that
regular expressions are used to find regular patterns in strings; the other connection is that
regular expressions are themselves input as strings, which are parsed into a state machine that
can be used to efficiently search for patterns in strings. In Julia, regular expressions are input
using non-standard string literals prefixed with various identifiers beginning with `r`. The most
basic regular expression literal without any options turned on just uses `r"..."`:
-->
```

JuliaはPerlと互換性のある [PCRE](http://www.pcre.prg/) ライブラリが提供する正規表現があります。
正規表現は2つの点で文字列と関連しています: 決定的な関連は正規表現が文字列のパターンを見つけるために用いられる点です。
そしてもう一つは正規表現自体が文字列として入力され、文字列のパターンを効率的に検索するために使用できるステートマシンに解析される点です。
Juliaでは、正規表現は `r` で始まる様々な識別しの接頭辞が付いた非標準文字列リテラルを使用して入力されます。
オプションがない最も基本的な正規表現リテラルは、`r"..."` を使用します。:

```jldoctest
julia> r"^\s*(?:#|$)"
r"^\s*(?:#|$)"

julia> typeof(ans)
Regex
```

```@raw html
<!--
To check if a regex matches a string, use [`ismatch()`](@ref):
-->
```

正規表現が文字列に一致するか確認するためには、 [`ismatch()`](@ref) を使用します。:

```jldoctest
julia> ismatch(r"^\s*(?:#|$)", "not a comment")
false

julia> ismatch(r"^\s*(?:#|$)", "# a comment")
true
```

```@raw html
<!--
As one can see here, [`ismatch()`](@ref) simply returns true or false, indicating whether the
given regex matches the string or not. Commonly, however, one wants to know not just whether a
string matched, but also *how* it matched. To capture this information about a match, use the
[`match()`](@ref) function instead:
-->
```

見て分かる通り、 [`ismatch()`](@ref) は単純に正規表現が文字列と一致するかどうかを示すtrueまたはfalseを返します。
しかし一般的には、文字列が一致するかどうかではなく、どのように一致するかを知りたいと考えるかもしれません。
一致に関する情報を得るためには、代わりに [`match()`](@ref) 関数を使用してください。:

```jldoctest
julia> match(r"^\s*(?:#|$)", "not a comment")

julia> match(r"^\s*(?:#|$)", "# a comment")
RegexMatch("#")
```

```@raw html
<!--
If the regular expression does not match the given string, [`match()`](@ref) returns `nothing`
-- a special value that does not print anything at the interactive prompt. Other than not printing,
it is a completely normal value and you can test for it programmatically:
-->
```

正規表現が文字列と一致しない場合、 [`match()`](@ref) は `nothing` を返します。
これは対話型プロンプトにおいて何も出力しない特別な値です。これは出力しない場合を除いて正常な値であり、プログラムでテストすることができます。:

```julia
m = match(r"^\s*(?:#|$)", line)
if m === nothing
    println("not a comment")
else
    println("blank or comment")
end
```

```@raw html
<!--
If a regular expression does match, the value returned by [`match()`](@ref) is a `RegexMatch`
object. These objects record how the expression matches, including the substring that the pattern
matches and any captured substrings, if there are any. This example only captures the portion
of the substring that matches, but perhaps we want to capture any non-blank text after the comment
character. We could do the following:
-->
```

正規表現が一致する場合、[`match()`](@ref) によって返される値は `RegexMatch` オブジェクトとなります。
このオブジェクトはパターンが一致する部分文字列およびもしあればキャプチャされた部分文字列を含む、どうその正規表現が一致するのかを記録します。
この例では、部分文字列の一部だけを取得していますが、コメント文字後の空白以外のテキストを取得したい場合があるかもしれません。その場合は以下でできます。:

```jldoctest
julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
RegexMatch("# a comment ", 1="a comment")
```

```@raw html
<!--
When calling [`match()`](@ref), you have the option to specify an index at which to start the
search. For example:
-->
```

[`match()`](@ref) を呼び出す時、どのインデックスから検索を開始するかを指定するオプションがあります。例えば:

```jldoctest
julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
RegexMatch("1")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
RegexMatch("2")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",11)
RegexMatch("3")
```

```@raw html
<!--
You can extract the following info from a `RegexMatch` object:

  * the entire substring matched: `m.match`
  * the captured substrings as an array of strings: `m.captures`
  * the offset at which the whole match begins: `m.offset`
  * the offsets of the captured substrings as a vector: `m.offsets`
-->
```

`RegexMatch` オブジェクトから次の情報を抽出することができます。:

  * 全ての一致した部分文字列: `m.match`
  * 文字列の配列としてキャプチャされた部分文字列: `m.captures`
  * 全体の一致が始まるオフセット: `m.offset`
  * ベクトルとしてキャプチャされた部分文字列のオフセット: `m.offsets`

```@raw html
<!--
For when a capture doesn't match, instead of a substring, `m.captures` contains `nothing` in that
position, and `m.offsets` has a zero offset (recall that indices in Julia are 1-based, so a zero
offset into a string is invalid). Here is a pair of somewhat contrived examples:
-->
```

キャプチャが一致しない場合、部分文字列の代わりに、 `m.captures` には `nothing`が格納され、 `m.offsets` には
ゼロオフセットが格納されます。（Juliaは1ベースであり、文字列へのゼロオフセットは無効です。）例:

```jldoctest acdmatch
julia> m = match(r"(a|b)(c)?(d)", "acd")
RegexMatch("acd", 1="a", 2="c", 3="d")

julia> m.match
"acd"

julia> m.captures
3-element Array{Union{SubString{String}, Void},1}:
 "a"
 "c"
 "d"

julia> m.offset
1

julia> m.offsets
3-element Array{Int64,1}:
 1
 2
 3

julia> m = match(r"(a|b)(c)?(d)", "ad")
RegexMatch("ad", 1="a", 2=nothing, 3="d")

julia> m.match
"ad"

julia> m.captures
3-element Array{Union{SubString{String}, Void},1}:
 "a"
 nothing
 "d"

julia> m.offset
1

julia> m.offsets
3-element Array{Int64,1}:
 1
 0
 2
```

```@raw html
<!--
It is convenient to have captures returned as an array so that one can use destructuring syntax
to bind them to local variables:
-->
```

キャプチャが配列として返されることで、ローカル変数に紐付ける再構成構文を使うことができます。:

```jldoctest acdmatch
julia> first, second, third = m.captures; first
"a"
```

```@raw html
<!--
Captures can also be accessed by indexing the `RegexMatch` object with the number or name of the
capture group:
-->
```

キャプチャグループの番号または名前と共に `RegexMatch` オブジェクトをインデックスすることで、取得した値にアクセス
することもできます。:

```jldoctest
julia> m=match(r"(?<hour>\d+):(?<minute>\d+)","12:45")
RegexMatch("12:45", hour="12", minute="45")

julia> m[:minute]
"45"

julia> m[2]
"45"
```

```@raw html
<!--
Captures can be referenced in a substitution string when using [`replace()`](@ref) by using `\n`
to refer to the nth capture group and prefixing the subsitution string with `s`. Capture group
0 refers to the entire match object. Named capture groups can be referenced in the substitution
with `g<groupname>`. For example:
-->
```

[`replace()`](@ref) 関数の引数に `\n` を含めることでn番目のキャプチャグループを参照でき、
`s` で始まる [非標準文字列リテラル](@ref non-standard-string-literals) を含めることで置換文字列でキャプチャを参照できます。
0番目のキャプチャグループはマッチオブジェクト全体を表します。名前付きキャプチャグループは、 `g<groupname>`
での置換で参照できます。例えば:

```jldoctest
julia> replace("first second", r"(\w+) (?<agroup>\w+)", s"\g<agroup> \1")
"second first"
```

```@raw html
<!--
Numbered capture groups can also be referenced as `\g<n>` for disambiguation, as in:
-->
```

番号付きキャプチャグループは、曖昧さ回避の目的上、 `\g<n>` としても参照することができます。:

```jldoctest
julia> replace("a", r".", s"\g<0>1")
"a1"
```

```@raw html
<!--
You can modify the behavior of regular expressions by some combination of the flags `i`, `m`,
`s`, and `x` after the closing double quote mark. These flags have the same meaning as they do
in Perl, as explained in this excerpt from the [perlre manpage](http://perldoc.perl.org/perlre.html#Modifiers):
-->
```

閉じるダブルクオーテーションの後に、フラグ `i`、`m`、`s` 及び `x` の組み合わせによって
正規表現の挙動に変更を加えることができます。これらのフラグは [Perlのマニュアルページ](http://perldoc.perl.org/perlre.html#Modifiers)
から抜粋したように、Perlで使用される場合と同じ意味を持っています。:

```@raw html
<!--
i   Do case-insensitive pattern matching.

    If locale matching rules are in effect, the case map is taken
    from the current locale for code points less than 255, and
    from Unicode rules for larger code points. However, matches
    that would cross the Unicode rules/non-Unicode rules boundary
    (ords 255/256) will not succeed.

m   Treat string as multiple lines.  That is, change "^" and "$"
    from matching the start or end of the string to matching the
    start or end of any line anywhere within the string.

s   Treat string as single line.  That is, change "." to match any
    character whatsoever, even a newline, which normally it would
    not match.

    Used together, as r""ms, they let the "." match any character
    whatsoever, while still allowing "^" and "$" to match,
    respectively, just after and just before newlines within the
    string.

x   Tells the regular expression parser to ignore most whitespace
    that is neither backslashed nor within a character class. You
    can use this to break up your regular expression into
    (slightly) more readable parts. The '#' character is also
    treated as a metacharacter introducing a comment, just as in
    ordinary code.
-->
```

```
i   大文字・小文字を区別しないパターンマッチングを行います。

    もしロケールマッチングルールが有効な場合は、ケースマップはコードポイントが255以下の場合は現在のロケールから、
    コードポイントが255以上の文字の場合はUnicodeルールから取られます。
    しかし、Unicodeルールと非Unicodeルールの境界(コードポイント 255-256間)をまたがるようなマッチは失敗します。

m   文字列を複数行として扱います。つまり、"^" や "$" が文字列の先頭と後尾にのみマッチングするのではなく、
    どの行頭と行末にもマッチングするようになります。

s   文字列を一行として扱います。つまり、"." が通常はマッチしない改行なども含めた全ての文字にマッチするようになります。
    r""ms のようにm と sが同時に使われた場合は、"." は変わらず全ての文字にマッチし、
    "^" や "$" はそれぞれ別々に、文字列内の新しい行の前後にマッチします。

x   正規表現パーサにバックスラッシュ付きもしくは文字列クラス内の空白を除いた大部分の空白を
    無視させます。これを使うことで正規表現を部分ごとに分けて（少しだけ）見やすくすることができます。
    '#' も一般的なコードと同様にコメントを導入するメタ文字として扱われます。
```

```@raw html
<!--
For example, the following regex has all three flags turned on:
-->
```

例として、以下の正規表現は3つのフラグを有効にしています。:

```jldoctest
julia> r"a+.*b+.*?d$"ism
r"a+.*b+.*?d$"ims

julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
RegexMatch("angry,\nBad world")
```

```@raw html
<!--
Triple-quoted regex strings, of the form `r"""..."""`, are also supported (and may be convenient
for regular expressions containing quotation marks or newlines).
-->
```

`r"""..."""` の形で表されるトリプルクオートで囲まれた正規表現文字列もサポートされています
（おそらくクオーテーションや改行を含んだ正規表現はこちらの方が便利かもしれません）。

```@raw html
<!--
## [Byte Array Literals](@id man-byte-array-literals)
-->
```

## [バイト配列リテラル](@id man-byte-array-literals)

```@raw html
<!--
Another useful non-standard string literal is the byte-array string literal: `b"..."`. This form
lets you use string notation to express literal byte arrays -- i.e. arrays of
[`UInt8`](@ref) values. The rules for byte array literals are the following:
<--
```

そのほかの便利な非標準文字列リテラルとして、バイト配列文字列リテラル（`b"..."`）があります。
これらの形式は文字列表記を使用してバイト配列リテラル、つまり [`UInt8`](@ref) 値の配列を表すことができます。
バイト配列リテラルのルールは次の通りです。:

```@raw html
<!--
  * ASCII characters and ASCII escapes produce a single byte.
  * `\x` and octal escape sequences produce the *byte* corresponding to the escape value.
  * Unicode escape sequences produce a sequence of bytes encoding that code point in UTF-8.
-->
```

  * ASCII文字とASCIIエスケープは1バイトを生成します。
  * `\x` と8進数エスケープシーケンスは、エスケープした値に対応するバイトを生成します。
  * Unicodeのエスケープシーケンスは、UTF-8でコードポイントをエンコードする一連のバイトを生成します。

```@raw html
<!--
There is some overlap between these rules since the behavior of `\x` and octal escapes less than
0x80 (128) are covered by both of the first two rules, but here these rules agree. Together, these
rules allow one to easily use ASCII characters, arbitrary byte values, and UTF-8 sequences to
produce arrays of bytes. Here is an example using all three:
-->

`\x` の挙動と0x80(128)未満の8進数エスケープは最初の2つのルールの両方が適用されます。これらのルールの間には重複が存在しますが、
共存可能なので問題ありません。これらのルールにより、ASCII文字や任意のバイト値、そしてUTF-8シーケンスを簡単に使用することができます。
以下の例は上記の3つのルールを全て使っています。

```jldoctest
julia> b"DATA\xff\u2200"
8-element Array{UInt8,1}:
 0x44
 0x41
 0x54
 0x41
 0xff
 0xe2
 0x88
 0x80
```

```@raw html
<!--
The ASCII string "DATA" corresponds to the bytes 68, 65, 84, 65. `\xff` produces the single byte 255.
The Unicode escape `\u2200` is encoded in UTF-8 as the three bytes 226, 136, 128. Note that the
resulting byte array does not correspond to a valid UTF-8 string -- if you try to use this as
a regular string literal, you will get a syntax error:
-->
```

ASCII文字列「DATA」はバイト68、65、84、65にそれぞれ対応します。`\xff` はシングルバイト255を生成します。
Unicodeエスケープ文字 `\u2200` は、UTF-8として3バイト226、136、128としてエンコードされます。
処理結果のバイト配列は有効なUTF-8文字列に対応しません。これを通常の文字列リテラルとして使用した場合、
構文エラーが発生します。

```julia-repl
julia> "DATA\xff\u2200"
ERROR: syntax: invalid UTF-8 sequence
```

```@raw html
<!--
Also observe the significant distinction between `\xff` and `\uff`: the former escape sequence
encodes the *byte 255*, whereas the latter escape sequence represents the *code point 255*, which
is encoded as two bytes in UTF-8:
-->
```

`\xff` と `\uff` の重要な違いについても確認してください。前者のエスケープシーケンスは **バイト** 255
をエンコードする一方で、後者のエスケープシーケンスは、UTF-8で2バイトとしてエンコードされた **コードポイント** 255を表します。:

```jldoctest
julia> b"\xff"
1-element Array{UInt8,1}:
 0xff

julia> b"\uff"
2-element Array{UInt8,1}:
 0xc3
 0xbf
```

```@raw html
<!--
In character literals, this distinction is glossed over and `\xff` is allowed to represent the
code point 255, because characters *always* represent code points. In strings, however, `\x` escapes
always represent bytes, not code points, whereas `\u` and `\U` escapes always represent code points,
which are encoded in one or more bytes. For code points less than `\u80`, it happens that the
UTF-8 encoding of each code point is just the single byte produced by the corresponding `\x` escape,
so the distinction can safely be ignored. For the escapes `\x80` through `\xff` as compared to
`\u80` through `\uff`, however, there is a major difference: the former escapes all encode single
bytes, which -- unless followed by very specific continuation bytes -- do not form valid UTF-8
data, whereas the latter escapes all represent Unicode code points with two-byte encodings.
-->
```

文字リテラルでは、文字は **常** にコードポイントを表すため上述の違いは顕在せず、`\xff` はコードポイント255を表します。
しかし文字列では、`\x` エスケープはコードポイントではなく常にバイトを表し、一方で `\u` や　`\U` エスケープは常に
1またはそれ以上のバイト数でエンコードされたコードポイントを表します。`\u80` より小さなコードポイントの場合、
各コードポイントのUTF-8エンコーディングは、対応する `\x` エスケープによって生成される1バイトだけなので、
この区別は無視しても問題ありません。しかし、 `\u80` から　`\uff` までと比較して、`\x80` から `\xff` までの
エスケープには大きな違いがあります。前者は特定の連続なバイトが後にこない限り、有効なUTF-8データを構成しない全てのシングルバイト
をエスケープします。一方で後者は、2バイトのエンコーディングでUnicodeコードポイントを表す全てをエスケープします。

``@raw html
<!--
If this is all extremely confusing, try reading ["The Absolute Minimum Every
Software Developer Absolutely, Positively Must Know About Unicode and Character
Sets"](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/).
It's an excellent introduction to Unicode and UTF-8, and may help alleviate
some confusion regarding the matter.
-->
```

もし上の説明がピンとこない場合は、["The Absolute Minimum Every
Software Developer Absolutely, Positively Must Know About Unicode and Character
Sets"](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/) を読んでみてください。これはUnicodeとUTF-8についての素晴らしいイントロダクションです。
そして、おそらく理解を助けてくれるでしょう。

```@raw html
<!--
## [Version Number Literals](@id man-version-number-literals)
-->
```

## [バージョン番号リテラル](@id man-version-number-literals)

```@raw html
<!--
Version numbers can easily be expressed with non-standard string literals of the form `v"..."`.
Version number literals create `VersionNumber` objects which follow the specifications of [semantic versioning](http://semver.org),
and therefore are composed of major, minor and patch numeric values, followed by pre-release and
build alpha-numeric annotations. For example, `v"0.2.1-rc1+win64"` is broken into major version
`0`, minor version `2`, patch version `1`, pre-release `rc1` and build `win64`. When entering
a version literal, everything except the major version number is optional, therefore e.g.  `v"0.2"`
is equivalent to `v"0.2.0"` (with empty pre-release/build annotations), `v"2"` is equivalent to
`v"2.0.0"`, and so on.

`VersionNumber` objects are mostly useful to easily and correctly compare two (or more) versions.
For example, the constant `VERSION` holds Julia version number as a `VersionNumber` object, and
therefore one can define some version-specific behavior using simple statements as:
-->
```

バージョン番号は、`v"..."` の形の非標準文字列リテラルで簡単に表現することができます。
バージョン番号リテラルは [バージョン管理](http://semver.org) の仕様に従った `VersionNumber` オブジェクトを生成するため、
バージョン番号リテラルはメジャー、マイナー、パッチの数値で構成され、それにプレリリース番号が続き、英数字注釈で構成されます。
例えば、`v"0.2.1-rc1+win64` は、メジャーバージョン `0`、マイナーバージョン `1`、パッチバージョン `1`、プレリリース `rc1`、
そしてビルド `win64` へと分解できます。バージョン番号リテラルを入力するとき、メジャーバージョン番号以外は全て任意です。
`v"0.2"` は `v"0.2.0"`（プレリリースとビルド注釈が空白の場合）と等価です。`v"2"` は `v"2.0.0"` と等価です。

`VersionNumber` オブジェクトは、2つ（またはそれ以上）のバージョンを簡単かつ正確に比較するのにとても役立ちます。
例えば、定数 `VERSION` にはJuliaのバージョン番号が `VersionNumber` オブジェクトとして格納されているため、
単純な文を使用して特定のバージョンに対しての動作を定義することができます。:

```julia
if v"0.2" <= VERSION < v"0.3-"
    # do something specific to 0.2 release series
end
```

```@raw html
<!--
Note that in the above example the non-standard version number `v"0.3-"` is used, with a trailing
`-`: this notation is a Julia extension of the standard, and it's used to indicate a version which
is lower than any `0.3` release, including all of its pre-releases. So in the above example the
code would only run with stable `0.2` versions, and exclude such versions as `v"0.3.0-rc1"`. In
order to also allow for unstable (i.e. pre-release) `0.2` versions, the lower bound check should
be modified like this: `v"0.2-" <= VERSION`.

Another non-standard version specification extension allows one to use a trailing `+` to express
an upper limit on build versions, e.g.  `VERSION > v"0.2-rc1+"` can be used to mean any version
above `0.2-rc1` and any of its builds: it will return `false` for version `v"0.2-rc1+win64"` and
`true` for `v"0.2-rc2"`.

It is good practice to use such special versions in comparisons (particularly, the trailing `-`
should always be used on upper bounds unless there's a good reason not to), but they must not
be used as the actual version number of anything, as they are invalid in the semantic versioning
scheme.

Besides being used for the [`VERSION`](@ref) constant, `VersionNumber` objects are widely used
in the `Pkg` module, to specify packages versions and their dependencies.
-->
```

上記の例では、非標準バージョン番号 `v"0.3-""` が使用され、末尾に `-` が付いています。
この表記は標準のJuliaの拡張機能であり、これは、全てのプレリリースを含めて `0.3` リリースより低い
バージョンであることを示すために使用されています。従って、上記の例ではコードは安定した `0.2` バージョンでのみ実行され、
`v"0.3.0-rc1"` などのバージョンは除外されます。不安定な（プレリリースなど） `0.2` バージョンを許容する場合は、
下限チェックは次のように変更する必要があります。: `v"0.2-" <= VERSION`

別の非標準バージョン番号の仕様拡張では、後続の `+` を使用してビルドバージョンの上限を表すことができます。
例えば、`VERSION > v"0.2-rc1+" は、`0.2-rc1` 以上の全てのバージョンを指定するために使用することができ、
`v"0.2-rc1+win64"` では `false` を返し、 `v"0.2-rc2"` では `true` を返します。

このような特殊なバージョンを比較に使用するのは有益です。
（特に、後続の - は、必要の場合を除き、 常に上限を示すために使用すべきです。）
しかし、それらはバージョンスキームでは無効であるため、 実際のバージョン番号としては使用できません。

`VersionNumber` オブジェクトは[`VERSION`](@ref) 定数に使用される他に、パッケージのバージョン
とその依存関係を指定するために `Pkg` モジュールで広く使用されています。

```@raw html
<!--
## [Raw String Literals](@id man-raw-string-literals)
-->
```

## [生文字列リテラル](@id man-raw-string-literals)

```@raw html
<!--
Raw strings without interpolation or unescaping can be expressed with
non-standard string literals of the form `raw"..."`. Raw string literals create
ordinary `String` objects which contain the enclosed contents exactly as
entered with no interpolation or unescaping. This is useful for strings which
contain code or markup in other languages which use `$` or `\` as special
characters. The exception is quotation marks that still must be
escaped, e.g. `raw"\""` is equivalent to `"\""`.
-->
```

補間や逆エスケープのない生文字列は `raw"..."` の形の非標準文字列リテラルで表すことができます。
生文字列リテラルは補間や逆エスケープの無い入力されたままの中身を格納している通常の `String` オブジェクトを作成します。
これは `$` や `\`を特別な文字として扱う言語のコードやマークアップを格納する文字列を作る時に便利です。
クオーテーションマークは例外的にエスケープする必要があります。例として、 `raw"\""` は `"\""` と等価です。
