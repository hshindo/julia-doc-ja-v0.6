[](# Mathematical Operations and Elementary Functions)
# 算術処理と基本的な関数

```@raw html
<!--
Julia provides a complete collection of basic arithmetic and bitwise operators across all of its
numeric primitive types, as well as providing portable, efficient implementations of a comprehensive
collection of standard mathematical functions.
-->
```
Juliaは、基本的な数値プリミティブ型の全ての算術演算子とビット演算子だけでなく、
可搬的かつ標準的な数学関数の効率的な実装を実現しています。

[](## Arithmetic Operators)
## 算術演算子

```@raw html
<!--
The following [arithmetic operators](https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations)
are supported on all primitive numeric types:
-->
```
[算術演算子](https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations)
は 全ての数値プリミティブ型でサポートされています。

[](| Expression | Name           | Description                            |)
[](|:---------- |:-------------- |:-------------------------------------- |)
[](| `+x`       | unary plus     | the identity operation                 |)
[](| `-x`       | unary minus    | maps values to their additive inverses |)
[](| `x + y`    | binary plus    | performs addition                      |)
[](| `x - y`    | binary minus   | performs subtraction                   |)
[](| `x * y`    | times          | performs multiplication                |)
[](| `x / y`    | divide         | performs division                      |)
[](| `x \ y`    | inverse divide | equivalent to `y / x`                  |)
[](| `x ^ y`    | power          | raises `x` to the `y`th power          |)
[](| `x % y`    | remainder      | equivalent to `rem(x,y)`               |)

| 式 　　　　　| 名   称         |   概要        　　　　　　　　　　　　　　　|
|:---------- |:-------------- |:-------------------------------------- |
| `+x`       | 単一項加法       | 恒等作用素                              |
| `-x`       | 単一項減法       | 値を逆数と関連付ける                      |
| `x + y`    | 二項加法         | 加法を実施                              |
| `x - y`    | 二項減法         | 減法を実施                              |
| `x * y`    | 乗法            | 乗法を実施                              |
| `x / y`    | 除法            | 除法を実施                              |
| `x \ y`    | 逆数除法         | `y / x` と同等                         |
| `x ^ y`    | 累乗             | `x`を`y`分だけ掛ける                    |
| `x % y`    | 余り             | `rem(x,y)`と同等                       |

```@raw html
<!--
as well as the negation on `Bool`(@ref) types:
-->
```
`Bool`(@ref) 型の否定形についても同様。

[](| Expression | Name     | Description                              |)
[](|:---------- |:-------- |:---------------------------------------- |)
[](| `!x`       | negation | changes `true` to `false` and vice versa |)

| 式         | 名称      | 概要                             |
|:---------- |:-------- |:---------------------------------------- |
| `!x`       | 否定形    | `true`を`false` に`false`を`true`に変換する |

```@raw html
<!--
Julia's promotion system makes arithmetic operations on mixtures of argument types "just work"
naturally and automatically. See [Conversion and Promotion](@ref conversion-and-promotion) for details of the promotion
system.
-->
```
Juliaのプロモーションシステムは、複数の引数の型を含む算術演算が自然にかつ自動的に
動作するようにしています。プロモーションシステムの詳細については
man-変換とプロモーションシステムを参照ください。

```@raw html
<!--
Here are some simple examples using arithmetic operators:
-->
```
以下は算術演算の例です。

```jldoctest
julia> 1 + 2 + 3
6

julia> 1 - 2
-1

julia> 3*2/12
0.5
```
```@raw html
<!--
(By convention, we tend to space operators more tightly if they get applied before other nearby
operators. For instance, we would generally write `-x + 2` to reflect that first `x` gets negated,
and then `2` is added to that result.)
-->
```
（慣例的に、他の演算子が適用される場合は、スペースを開けずに記載する傾向があります。
例えば、通常 `-x + 2` と記載しますが、これは最初に `x`
を負の数にし、その後 `2` をその結果に足します。）

[](## Bitwise Operators)
## ビット単位の演算子
```@raw html
<!--
The following [bitwise operators](https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators)
are supported on all primitive integer types:
-->
```
以下の[ビット単位の演算子](https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators)
は 全ての数値プリミティブ型でサポートされています。

[](| Expression | Name                                                                     |)
[](|:---------- |:------------------------------------------------------------------------ |)
[](| `~x`       | bitwise not                                                              |)
[](| `x & y`    | bitwise and                                                              |)
[](| `x \| y`   | bitwise or                                                               |)
[](| `x ⊻ y`    | bitwise xor (exclusive or)                                               |)
[](| `x >>> y`  | [logical shift](https://en.wikipedia.org/wiki/Logical_shift) right       |)
[](| `x >> y`   | [arithmetic shift](https://en.wikipedia.org/wiki/Arithmetic_shift) right |)
[](| `x << y`   | logical/arithmetic shift left                                            |)

| 式         | 名   称                                                                   |
|:---------- |:------------------------------------------------------------------------ |
| `~x`        |    ビット単位の否定                                                         |
| `x & y`     |    ビット単位の論理積                                                        |
| `x | y`     |    ビット単位の論理和                                                        |
| `x $ y`     |    ビット単位の排他的論理和                                                   |
| `x >>> y`   |    [論理右桁送り](https://en.wikipedia.org/wiki/Logical_shift)              |
| `x >> y`    |    [算術右桁送り](https://en.wikipedia.org/wiki/Arithmetic_shift)           |
| `x << y`    |    論理/算術左桁送り                                                         |

```@raw html
<!--
Here are some examples with bitwise operators:
-->
```
以下はビット単位演算子の例です。

```jldoctest
julia> ~123
-124

julia> 123 & 234
106

julia> 123 | 234
251

julia> 123 ⊻ 234
145

julia> xor(123, 234)
145

julia> ~UInt32(123)
0xffffff84

julia> ~UInt8(123)
0x84
```

[](## Updating operators)
## 演算子の更新

```@raw html
<!--
Every binary arithmetic and bitwise operator also has an updating version that assigns the result
of the operation back into its left operand. The updating version of the binary operator is formed
by placing a `=` immediately after the operator. For example, writing `x += 3` is equivalent to
writing `x = x + 3`:
-->
```
全ての二項演算子とビット単位の演算子には、被演算子の処理の結果を代入できる更新機能があります。
二校演算子の更新機能は、演算子の後ろに `=`
を記載することで実行できます。 例えば、 `x += 3` と記載することで
`x = x + 3` と同じ結果となります。:

```jldoctest
julia> x = 1
1

julia> x += 3
4

julia> x
4
```
```@raw html
<!--
The updating versions of all the binary arithmetic and bitwise operators are:
-->
```
更新機能を持つ二項演算子とビット単位の演算子は以下の通りです。:

```
+=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  ⊻=  >>>=  >>=  <<=
```

[](!!! note)
    [](An updating operator rebinds the variable on the left-hand side. As a result, the type of the)
    [](variable may change.)
!!! note
    更新機能の結果は、左辺にある変数の型に紐付きます。これにより、変数の型が変更されることがあります。

    ```jldoctest
    julia> x = 0x01; typeof(x)
    UInt8

    julia> x *= 2 # Same as x = x * 2
    2

    julia> typeof(x)
    Int64
    ```

## [Vectorized "dot" operators](@id man-dot-operators)

For *every* binary operation like `^`, there is a corresponding
"dot" operation `.^` that is *automatically* defined
to perform `^` element-by-element on arrays. For example,
`[1,2,3] ^ 3` is not defined, since there is no standard
mathematical meaning to "cubing" an array, but `[1,2,3] .^ 3`
is defined as computing the elementwise
(or "vectorized") result `[1^3, 2^3, 3^3]`.  Similarly for unary
operators like `!` or `√`, there is a corresponding `.√` that
applies the operator elementwise.

```jldoctest
julia> [1,2,3] .^ 3
3-element Array{Int64,1}:
  1
  8
 27
```

More specifically, `a .^ b` is parsed as the ["dot" call](@ref man-vectorized)
`(^).(a,b)`, which performs a [broadcast](@ref Broadcasting) operation:
it can combine arrays and scalars, arrays of the same size (performing
the operation elementwise), and even arrays of different shapes (e.g.
combining row and column vectors to produce a matrix). Moreover, like
all vectorized "dot calls," these "dot operators" are
*fusing*. For example, if you compute `2 .* A.^2 .+ sin.(A)` (or
equivalently `@. 2A^2 + sin(A)`, using the [`@.`](@ref @__dot__) macro) for
an array `A`, it performs a *single* loop over `A`, computing `2a^2 + sin(a)`
for each element of `A`. In particular, nested dot calls like `f.(g.(x))`
are fused, and "adjacent" binary operators like `x .+ 3 .* x.^2` are
equivalent to nested dot calls `(+).(x, (*).(3, (^).(x, 2)))`.

Furthermore, "dotted" updating operators like `a .+= b` (or `@. a += b`) are parsed
as `a .= a .+ b`, where `.=` is a fused *in-place* assignment operation
(see the [dot syntax documentation](@ref man-vectorized)).

Note the dot syntax is also applicable to user-defined operators.
For example, if you define `⊗(A,B) = kron(A,B)` to give a convenient
infix syntax `A ⊗ B` for Kronecker products ([`kron`](@ref)), then
`[A,B] .⊗ [C,D]` will compute `[A⊗C, B⊗D]` with no additional coding.

[](## Numeric Comparisons)
## 数値の比較

```@raw html
<!--
Standard comparison operations are defined for all the primitive numeric types:
-->
```
標準的な比較演算子は、全ての数値プリミティブ型に定義されています。

[](| Operator                     | Name                     |)
[](|:---------------------------- |:------------------------ |)
[](| [`==`](@ref)                 | equality                 |)
[](| [`!=`](@ref), [`≠`](@ref !=) | inequality               |)
[](| [`<`](@ref)                  | less than                |)
[](| [`<=`](@ref), [`≤`](@ref <=) | less than or equal to    |)
[](| [`>`](@ref)                  | greater than             |)
[](| [`>=`](@ref), [`≥`](@ref >=) | greater than or equal to |)

| 演算子          |        名称                           |
|:-------------- |:------------------------------------ |
| [`==`](@ref)                        | 等しい           |
| [`!=`](@ref), [`≠`](@ref !=)        | 等しくない        |
| [`<`](@ref)                         | より小さい        |
| [`<=`](@ref), [`≤`](@ref <=)        | より大きい        |
| [`>`](@ref)                         | 以下             |
| [`>=`](@ref), [`≥`](@ref >=)        | 以上             |

```@raw html
<!--
Here are some simple examples:
-->
```
以下は使用例です。

```jldoctest
julia> 1 == 1
true

julia> 1 == 2
false

julia> 1 != 2
true

julia> 1 == 1.0
true

julia> 1 < 2
true

julia> 1.0 > 3
false

julia> 1 >= 1.0
true

julia> -1 <= 1
true

julia> -1 <= -1
true

julia> -1 <= -2
false

julia> 3 < -0.5
false
```
```@raw html
<!--
Integers are compared in the standard manner -- by comparison of bits. Floating-point numbers
are compared according to the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008):
-->
```
整数は標準的な方法（ビットの比較）により比較されます。 浮動小数点数は
[IEEE 754規格](https://en.wikipedia.org/wiki/IEEE_754-2008)
に準拠して比較されます。

[](  * Finite numbers are ordered in the usual manner.)
[](  * Positive zero is equal but not greater than negative zero.)
[](  * `Inf` is equal to itself and greater than everything else except `NaN`).
[](  * `-Inf` is equal to itself and less then everything else except `NaN`.)
[](  * `NaN` is not equal to, not less than, and not greater than anything, including itself.)

  * 有限数は標準的な方法で順序付けされます。
  * 正のゼロは負のゼロに等しく、大きくはありません。
  *  `Inf` はそれ自体と等しく、 `NaN` を除く全てよりも大きいです。
  *  `-Inf` はそれ自体と等しく、 `NaN` を除く全てよりも小さいです。

```@raw html
<!--
The last point is potentially surprising and thus worth noting:
-->
```
最後の記述は驚くべきものかもしれませんが、特段意味があるものではありません。

```jldoctest
julia> NaN == NaN
false

julia> NaN != NaN
true

julia> NaN < NaN
false

julia> NaN > NaN
false
```
```@raw html
<!--
and can cause especial headaches with [Arrays](@ref):
-->
```
これは、 配列[Arrays](@ref)と共に理解に苦しむものかもしれません。

```jldoctest
julia> [1 NaN] == [1 NaN]
false
```
```@raw html
<!--
Julia provides additional functions to test numbers for special values, which can be useful in
situations like hash key comparisons:
-->
```
Juliaは、ハッシュ値の比較など、特別な値を比較するための機能を提供します。

[](| Function                | Tests if                  |)
[](|:----------------------- |:------------------------- |)
[](| [`isequal(x, y)`](@ref) | `x` and `y` are identical |)
[](| [`isinf(x)`](@ref)      | `x` is infinite           |)
[](| [`isfinite(x)`](@ref)   | `x` is a finite number    |)
[](| [`isnan(x)`](@ref)      | `x` is not a number       |)

| 関数                     | 検証内容                　　|
|:----------------------- |:------------------------- |
| [`isequal(x, y)`](@ref) | `x` および `y` は一致するか 　|
| [`isfinite(x)`](@ref)   | `x` は有限数であるか    　　　|
| [`isinf(x)`](@ref)      | `x` は無限であるか           |
| [`isnan(x)`](@ref)      | `x` は数字以外であるか      　|


```@raw html
<!--
[`isequal()`](@ref) considers `NaN`s equal to each other:
-->
```
[`isequal()`](@ref) は、 `NaN` 同士は等しいと解釈します。

```jldoctest
julia> isequal(NaN, NaN)
true

julia> isequal([1 NaN], [1 NaN])
true

julia> isequal(NaN, NaN32)
true
```
```@raw html
<!--
`isequal()` can also be used to distinguish signed zeros:
-->
```
`isequal()` は符号付き0を区別するためにも使用することができます。

```jldoctest
julia> -0.0 == 0.0
true

julia> isequal(-0.0, 0.0)
false
```
```@raw html
<!--
Mixed-type comparisons between signed integers, unsigned integers, and floats can be tricky. A
great deal of care has been taken to ensure that Julia does them correctly.
-->
```
符号付き整数、符号がない整数、および浮動小数点数間の複数の型の比較は注意が必要です。
Juliaでは、このような処理を正確に実施できるよう、細心の注意が払われています。

```@raw html
<!--
For other types, `isequal()` defaults to calling [`==()`](@ref), so if you want to define
equality for your own types then you only need to add a [`==()`](@ref) method.  If you define
your own equality function, you should probably define a corresponding [`hash()`](@ref) method
to ensure that `isequal(x,y)` implies `hash(x) == hash(y)`.
-->
```
その他の型については、 `isequal()` はデフォルトで [`==()`](@ref) を呼び出すため、
使用している型の等式を定義したい場合は、[`==()`](@ref)
メソッドを追加することで定義することができます。
もし使用している等式の関数を定義したい場合は、 `isequal(x,y)` が
`hash(x) == hash(y)` を 表すことを保証する [`hash()`](@ref)
メソッドを定義することをお勧めします。

[](### Chaining comparisons)
### 連続した比較

```@raw html
<!--
Unlike most languages, with the [notable exception of Python](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators),
comparisons can be arbitrarily chained:
-->
```
[Pythonのような例外](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators)
を
除いて、ほとんどの言語とは異なり、比較は任意に連続して使用することができます。

```jldoctest
julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
true
```
```@raw html
<!--
Chaining comparisons is often quite convenient in numerical code. Chained comparisons use the
`&&` operator for scalar comparisons, and the [`&`](@ref) operator for elementwise comparisons,
which allows them to work on arrays. For example, `0 .< A .< 1` gives a boolean array whose entries
are true where the corresponding elements of `A` are between 0 and 1.
-->
```
連続した比較は、数値コードで便利です。連続した比較は、スカラー比較に `&&`
演算子を使用し、 要素単位（elementwiseの比較では配列処理を行う [`&`](@ref)
演算子を使用します。例えば、 `0 .< A .< 1` は、 `A` に
対応する要素が0と1の間に存在し、エントリが真であるブール値配列を返します。

```@raw html
<!--
Note the evaluation behavior of chained comparisons:
-->
```
連続した比較の値の処理に注意してください。

```jldoctest
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> v(1) < v(2) <= v(3)
2
1
3
true

julia> v(1) > v(2) <= v(3)
2
1
false
```
```@raw html
<!--
The middle expression is only evaluated once, rather than twice as it would be if the expression
were written as `v(1) < v(2) && v(2) <= v(3)`. However, the order of evaluations in a chained
comparison is undefined. It is strongly recommended not to use expressions with side effects (such
as printing) in chained comparisons. If side effects are required, the short-circuit `&&` operator
should be used explicitly (see [Short-Circuit Evaluation](@ref)).
-->
```
`v(1) < v(2) && v(2) <= v(3)`
と記載されている場合は2度処理されるかのように
思われるかもしれませんが、中間の式は1度だけ処理されます。しかし、連続した比較の処理順は
定義されていません。連続した比較で副次効果のある式（印刷など）を使用しないことを推奨します。
副次効果が必要な場合は、短絡 `&&`
演算子を明示的に使用する必要があります。（ [Short-Circuit Evaluation](@ref) 参照）

[](### Elementary Functions)
### 基本的な関数

```@raw html
<!--
Julia provides a comprehensive collection of mathematical functions and operators. These mathematical
operations are defined over as broad a class of numerical values as permit sensible definitions,
including integers, floating-point numbers, rationals, and complex numbers,
wherever such definitions make sense.
-->
```
Juliaは、包括的な数学関数および演算子を提供します。これらの数学的演算子は、
整数、浮動小数点数、有理数、およびそれらの複合などを許容可能な定義として、
幅広い数値クラスとして定義されています。

Moreover, these functions (like any Julia function) can be applied in "vectorized" fashion to
arrays and other collections with the [dot syntax](@ref man-vectorized) `f.(A)`,
e.g. `sin.(A)` will compute the sine of each element of an array `A`.

[](## Operator Precedence)
## 演算子の優先順位

```@raw html
<!--
Julia applies the following order of operations, from highest precedence to lowest:
-->
```
Juliaは、優先順位の高いものから低いものまで、以下の処理順序を適用しています。

| 累乗       | 演算子                                                                                         |
|:-------------- |:----------------------------------------------------------------------------------------- |
| 構文         | `::` を伴う `.`                                                                             　|
| 累乗 | `^`                                                                                              　 　|
| 分数      | `//`                                                                                            |
| 乗法 | `* / % & \`                                                                                       　　|
| ビットシフト   | `<< >> >>>`                                                                                 |
| 加法      | `+ - \| ⊻`                                                                                      |
| 構文       |  `\|>`を伴う `: ..`                                                                             |
| 比較    | `> < >= <= == === != !== <:`                                                                      |
| 制御フロー   | `\|\|` および　`?` を伴う　`&&`                                                                  |
| 割り当て   | `= += -= *= /= //= \= ^= ÷= %= \|= &= ⊻= <<= >>= >>>=`                                          |

For a complete list of *every* Julia operator's precedence, see the top of this file:
[`src/julia-parser.scm`](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm)

You can also find the numerical precedence for any given operator via the built-in function `Base.operator_precedence`, where higher numbers take precedence:

```jldoctest
julia> Base.operator_precedence(:+), Base.operator_precedence(:*), Base.operator_precedence(:.)
(9, 11, 15)

julia> Base.operator_precedence(:+=), Base.operator_precedence(:(=))  # (Note the necessary parens on `:(=)`)
(1, 1)
```

[](## Numerical Conversions)
## 数値変換

```@raw html
<!--
Julia supports three forms of numerical conversion, which differ in their handling of inexact
conversions.
-->
```
Juliaは、不正確な変換の処理が異なる3つの形式の数値変換をサポートしています。

[](  * The notation `T(x)` or `convert(T,x)` converts `x` to a value of type `T`.)

[](      * If `T` is a floating-point type, the result is the nearest representable value, which could be
        positive or negative infinity.)
[](      * If `T` is an integer type, an `InexactError` is raised if `x` is not representable by `T`.)
[](  * `x % T` converts an integer `x` to a value of integer type `T` congruent to `x` modulo `2^n`,
    where `n` is the number of bits in `T`. In other words, the binary representation is truncated
    to fit.)
[](  * The [Rounding functions](@ref) take a type `T` as an optional argument. For example, `round(Int,x)`
    is a shorthand for `Int(round(x))`.)
  * 表記法 `T(x)` または `convert(T,x)` は、 `x` を `T`型の値に変換します。
    * `T`が浮動小数点型の場合、変換の結果は最も近い表現可能な、正または負の無限大にもなり得る値になります。
    * `T` が整数型の場合、 `x` が `T` で表現できない際は`InexactError` が発生します。
  * `x % T` は、整数 `x` を、 `x` を法とする `2^n` （ `n` は `T`のビット数）に一致する整数型 `T`の値に変換します。言い換えれば、バイナリ表現は値に収まるように切り捨てられます。
  * man-端数処理関数 は、 `T`型をオプション引数として解釈します。例えば、 `round(Int,x)` は`Int(round(x))` の短縮系です。

```@raw html
<!--
The following examples show the different forms.
-->
```
以下の例は異なる形式を表しています。

```jldoctest
julia> Int8(127)
127

julia> Int8(128)
ERROR: InexactError()
Stacktrace:
 [1] Int8(::Int64) at ./sysimg.jl:24

julia> Int8(127.0)
127

julia> Int8(3.14)
ERROR: InexactError()
Stacktrace:
 [1] convert(::Type{Int8}, ::Float64) at ./float.jl:658
 [2] Int8(::Float64) at ./sysimg.jl:24

julia> Int8(128.0)
ERROR: InexactError()
Stacktrace:
 [1] convert(::Type{Int8}, ::Float64) at ./float.jl:658
 [2] Int8(::Float64) at ./sysimg.jl:24

julia> 127 % Int8
127

julia> 128 % Int8
-128

julia> round(Int8,127.4)
127

julia> round(Int8,127.6)
ERROR: InexactError()
Stacktrace:
 [1] trunc(::Type{Int8}, ::Float64) at ./float.jl:651
 [2] round(::Type{Int8}, ::Float64) at ./float.jl:337
```

[](See [Conversion and Promotion](@ref conversion-and-promotion) for how to define your own conversions and promotions.)
独自の変換とプロモーションの定義方法については、[Conversion and Promotion](@ref conversion-and-promotion) を参照してください


[](### Rounding functions)
### 端数処理関数

[](| Function              | Description                      | Return type |)
[](|:--------------------- |:-------------------------------- |:----------- |)
[](| [`round(x)`](@ref)    | round `x` to the nearest integer | `typeof(x)` |)
[](| [`round(T, x)`](@ref) | round `x` to the nearest integer | `T`         |)
[](| [`floor(x)`](@ref)    | round `x` towards `-Inf`         | `typeof(x)` |)
[](| [`floor(T, x)`](@ref) | round `x` towards `-Inf`         | `T`         |)
[](| [`ceil(x)`](@ref)     | round `x` towards `+Inf`         | `typeof(x)` |)
[](| [`ceil(T, x)`](@ref)  | round `x` towards `+Inf`         | `T`         |)
[](| [`trunc(x)`](@ref)    | round `x` towards zero           | `typeof(x)` |)
[](| [`trunc(T, x)`](@ref) | round `x` towards zero           | `T`         |)

| 関数              | 概要                      | 戻り値の型 |
|:--------------------- |:-------------------------------- |:----------- |
| [`round(x)`](@ref)    | `x` を最も近い整数になるよう丸めを行う | `typeof(x)` |
| [`round(T, x)`](@ref) | `x` を最も近い整数になるよう丸めを行う | `T`         |
| [`floor(x)`](@ref)    | `x` を `-Inf` に向かって丸めを行う        | `typeof(x)` |
| [`floor(T, x)`](@ref) | `x` を `-Inf` に向かって丸めを行う         | `T`         |
| [`ceil(x)`](@ref)     | `x` を `+Inf` に向かって丸めを行う         | `typeof(x)` |
| [`ceil(T, x)`](@ref)  | `x` を `+Inf` に向かって丸めを行う         | `T`         |
| [`trunc(x)`](@ref)    | `x` を0に向かって丸めを行う           | `typeof(x)` |
| [`trunc(T, x)`](@ref) | `x` を0に向かって丸めを行う           | `T`         |

[](### Division functions)
### 除算関数

[](| Function              | Description                                                                                               |)
[](|:--------------------- |:--------------------------------------------------------------------------------------------------------- |)
[](| [`div(x,y)`](@ref)    | truncated division; quotient rounded towards zero                                                         |)
[](| [`fld(x,y)`](@ref)    | floored division; quotient rounded towards `-Inf`                                                         |)
[](| [`cld(x,y)`](@ref)    | ceiling division; quotient rounded towards `+Inf`                                                         |)
[](| [`rem(x,y)`](@ref)    | remainder; satisfies `x == div(x,y)*y + rem(x,y)`; sign matches `x`                                       |)
[](| [`mod(x,y)`](@ref)    | modulus; satisfies `x == fld(x,y)*y + mod(x,y)`; sign matches `y`                                         |)
[](| [`mod1(x,y)`](@ref)   | `mod()` with offset 1; returns `r∈(0,y]` for `y>0` or `r∈[y,0)` for `y<0`, where `mod(r, y) == mod(x, y)` |)
[](| [`mod2pi(x)`](@ref)   | modulus with respect to 2pi;  `0 <= mod2pi(x)    < 2pi`                                                   |)
[](| [`divrem(x,y)`](@ref) | returns `(div(x,y),rem(x,y))`                                                                             |)
[](| [`fldmod(x,y)`](@ref) | returns `(fld(x,y),mod(x,y))`                                                                             |)
[](| [`gcd(x,y...)`](@ref) | greatest positive common divisor of `x`, `y`,...                                                          |)
[](| [`lcm(x,y...)`](@ref) | least positive common multiple of `x`, `y`,...                                                            |)

| 関数              | 概要                                                                                               |
|:--------------------- |:--------------------------------------------------------------------------------------------------------- |
| [`div(x,y)`](@ref)    | 0に向かって丸めを行う除算                                                       |
| [`fld(x,y)`](@ref)    | 床関数のように `-Inf` に向かって丸めを行う除算                                                        |
| [`cld(x,y)`](@ref)    | 天井関数のように `+Inf` に向かって丸めを行う除算                                                        |
| [`rem(x,y)`](@ref)    | 余り； `x == div(x,y)*y + rem(x,y)` を満たす；記号は `x` と一致                                |
| [`mod(x,y)`](@ref)    | 剰余演算； `x == fld(x,y)*y + mod(x,y)` を満たす；記号は `y` と一致                                        |
| [`mod1(x,y)`](@ref)   | オフセットが1の `mod()` ； `mod(r, y) == mod(x, y)` の場合、  `y>0` に `r∈(0,y]` を、　 `y<0` に `r∈[y,0)` を返す |
| [`mod2pi(x)`](@ref)   | 2piを法とする； `0 <= mod2pi(x)  < 2pi`                                                  |
| [`divrem(x,y)`](@ref) | `(div(x,y),rem(x,y))` を返す                                                                            |
| [`fldmod(x,y)`](@ref) | `(fld(x,y),mod(x,y))` を返す                                                                            |
| [`gcd(x,y...)`](@ref) | `x` 、 `y` 、...の最大の正の因数                                                          |
| [`lcm(x,y...)`](@ref) | `x` 、 `y` 、...の最小の正の公倍数                                                          |

[](### Sign and absolute value functions)
### 符号と絶対値関数

[](| Function                | Description                                                |)
[](|:----------------------- |:---------------------------------------------------------- |)
[](| [`abs(x)`](@ref)        | a positive value with the magnitude of `x`                 |)
[](| [`abs2(x)`](@ref)       | the squared magnitude of `x`                               |)
[](| [`sign(x)`](@ref)       | indicates the sign of `x`, returning -1, 0, or +1          |)
[](| [`signbit(x)`](@ref)    | indicates whether the sign bit is on (true) or off (false) |)
[](| [`copysign(x,y)`](@ref) | a value with the magnitude of `x` and the sign of `y`      |)
[](| [`flipsign(x,y)`](@ref) | a value with the magnitude of `x` and the sign of `x*y`    |)

| 関数                | 概要                                                |
|:----------------------- |:---------------------------------------------------------- |
| [`abs(x)`](@ref)        | 絶対値 `x` の正の値                 |
| [`abs2(x)`](@ref)       | 絶対値 `x` の2乗                               |
| [`sign(x)`](@ref)       | `x` の符号を示し、 -1、 0、 または +1を返す        |
| [`signbit(x)`](@ref)    | 符号ビットがオン（true）またはオフ（false）であるかを示す  |
| [`copysign(x,y)`](@ref) | 絶対値 `x` と符号 `y` を持つ値      |
| [`flipsign(x,y)`](@ref) | 絶対値 `x` と符号 `x*y` を持つ値    |

[](### Powers, logs and roots)
### 累乗、対数、根

[](| Function                 | Description                                                                |)
[](|:------------------------ |:-------------------------------------------------------------------------- |)
[](| [`sqrt(x)`](@ref), `√x`  | square root of `x`                                                         |)
[](| [`cbrt(x)`](@ref), `∛x`  | cube root of `x`                                                           |)
[](| [`hypot(x,y)`](@ref)     | hypotenuse of right-angled triangle with other sides of length `x` and `y` |)
[](| [`exp(x)`](@ref)         | natural exponential function at `x`                                        |)
[](| [`expm1(x)`](@ref)       | accurate `exp(x)-1` for `x` near zero                                      |)
[](| [`ldexp(x,n)`](@ref)     | `x*2^n` computed efficiently for integer values of `n`                     |)
[](| [`log(x)`](@ref)         | natural logarithm of `x`                                                   |)
[](| [`log(b,x)`](@ref)       | base `b` logarithm of `x`                                                  |)
[](| [`log2(x)`](@ref)        | base 2 logarithm of `x`                                                    |)
[](| [`log10(x)`](@ref)       | base 10 logarithm of `x`                                                   |)
[](| [`log1p(x)`](@ref)       | accurate `log(1+x)` for `x` near zero                                      |)
[](| [`exponent(x)`](@ref)    | binary exponent of `x`                                                     |)
[](| [`significand(x)`](@ref) | binary significand (a.k.a. mantissa) of a floating-point number `x`        |)

| 関数                 | 概要                                                                |
|:------------------------ |:-------------------------------------------------------------------------- |
| [`sqrt(x)`](@ref), `√x`  | `x` の平方根                                                         |
| [`cbrt(x)`](@ref), `∛x`  | `x` の立方根                                                           |
| [`hypot(x,y)`](@ref)     | 長さ `x` および `y` をその他の辺に持つ直角三角形の斜辺 |
| [`exp(x)`](@ref)         | `x` における自然指数関数                                       |
| [`expm1(x)`](@ref)       | 0に近い `x` の正確な `exp(x)-1` の結果                                      |
| [`ldexp(x,n)`](@ref)     | `n` の整数値に対して効率的に計算された `x*2^n`                     |
| [`log(x)`](@ref)         | `x` の自然対数                                                   |
| [`log(b,x)`](@ref)       | `b` を底とした `x` の対数                                                  |
| [`log2(x)`](@ref)        | 2を底とした `x` の対数                                                    |
| [`log10(x)`](@ref)       | 10を底とした `x` の対数                                                   |
| [`log1p(x)`](@ref)       | 0に近い `x` の正確な `log(1+x)` の結果                                      |
| [`exponent(x)`](@ref)    | `x` の2進指数                                                     |
| [`significand(x)`](@ref) | 浮動小数点数 `x` の2進仮数        |

```@raw html
<!--
For an overview of why functions like [`hypot()`](@ref), [`expm1()`](@ref), and [`log1p()`](@ref)
are necessary and useful, see John D. Cook's excellent pair of blog posts on the subject: [expm1, log1p, erfc](https://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/),
and [hypot](https://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/).
-->
```
[`hypot()`](@ref), [`expm1()`](@ref), および [`log1p()`](@ref) などの関数が必要かつ有用な理由については、
[expm1、 log1p、erfc](http://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/)
および
[hypot](http://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/)
に関するJohn D. Cookのブログ記事を参照してください。

[](### Trigonometric and hyperbolic functions)
### 三角関数と双曲線関数

[](All the standard trigonometric and hyperbolic functions are also defined:)
標準的な全ての三角関数と双曲線関数も定義されています。:

```
sin    cos    tan    cot    sec    csc
sinh   cosh   tanh   coth   sech   csch
asin   acos   atan   acot   asec   acsc
asinh  acosh  atanh  acoth  asech  acsch
sinc   cosc   atan2
```
```@raw html
<!--
These are all single-argument functions, with the exception of [atan2](https://en.wikipedia.org/wiki/Atan2),
which gives the angle in [radians](https://en.wikipedia.org/wiki/Radian) between the *x*-axis
and the point specified by its arguments, interpreted as *x* and *y* coordinates.
-->
```
これらはすべて引数が1つの関数ですが、
[atan2](https://en.wikipedia.org/wiki/Atan2) は例外で、角度をx軸と引数で
指定された点の [ラジアン](https://en.wikipedia.org/wiki/Radian)
で表し、x座標とy座標として解釈します。

```@raw html
<!--
Additionally, [`sinpi(x)`](@ref) and [`cospi(x)`](@ref) are provided for more accurate computations
of [`sin(pi*x)`](@ref) and [`cos(pi*x)`](@ref) respectively.
-->
```
さらに、[`sinpi(x)`](@ref) および[`cospi(x)`](@ref)
は、より正確な [`sin(pi*x)`](@ref) および [`cos(pi*x)`](@ref)
の計算のために提供されています。

```@raw html
<!--
In order to compute trigonometric functions with degrees instead of radians, suffix the function
with `d`. For example, [`sind(x)`](@ref) computes the sine of `x` where `x` is specified in degrees.
The complete list of trigonometric functions with degree variants is:
-->
```
ラジアンではなく角度で三角関数を計算するためには、 `d`
を関数の末尾に付与してください。 例えば、[`sind(x)`](@ref)
は、角度で指定された `x` の正弦を計算します。
角度で計算できる三角関数のリストは以下の通りです。:

```
sind   cosd   tand   cotd   secd   cscd
asind  acosd  atand  acotd  asecd  acscd
```

[](### Special functions)
### 特殊な関数

[](| Function                                                      | Description                                                                                                                                                     |)
[](|:------------------------------------------------------------- |:--------------------------------------------------------------------------------------------------------------------------------------------------------------- |)
[](| [`gamma(x)`](@ref)                                            | [gamma function](https://en.wikipedia.org/wiki/Gamma_function) at `x`                                                                                           |)
[](| [`lgamma(x)`](@ref)                                           | accurate `log(gamma(x))` for large `x`                                                                                                                          |)
[](| [`lfact(x)`](@ref)                                            | accurate `log(factorial(x))` for large `x`; same as `lgamma(x+1)` for `x > 1`, zero otherwise                                                                   |)
[](| [`beta(x,y)`](@ref)                                           | [beta function](https://en.wikipedia.org/wiki/Beta_function) at `x,y`                                                                                           |)
[](| [`lbeta(x,y)`](@ref)                                          | accurate `log(beta(x,y))` for large `x` or `y`                                                                                                                  |)

| Function                                                      | Description                                                                                    |
|:------------------------------------------------------------- |:---------------------------------------------------------------------------------------------- |
| [`gamma(x)`](@ref)                                            | `x` における [ガンマ関数](https://en.wikipedia.org/wiki/Gamma_function)                          |
| [`lgamma(x)`](@ref)                                           |  大きな値 `x` における `log(gamma(x))`                                                        |
| [`lfact(x)`](@ref)                                            | 大きな値 `x` における `log(factorial(x))` ； `x > 1` における `lgamma(x+1)` と同様（それ以外の場合は0）  |
| [`beta(x,y)`](@ref)                                           | `x,y` における [ベータ関数](https://en.wikipedia.org/wiki/Beta_function)                          |
| [`lbeta(x,y)`](@ref)                                          | 大きな値 `x` または `y` における正確な `log(beta(x,y))`                                                 |
