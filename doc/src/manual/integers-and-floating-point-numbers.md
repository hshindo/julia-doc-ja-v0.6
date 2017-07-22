[](# Integers and Floating-Point Numbers)
# 整数と浮動小数点数

```@raw html
<!--
Integers and floating-point values are the basic building blocks of arithmetic and computation.
Built-in representations of such values are called numeric primitives, while representations of
integers and floating-point numbers as immediate values in code are known as numeric literals.
For example, `1` is an integer literal, while `1.0` is a floating-point literal; their binary
in-memory representations as objects are numeric primitives.
-->
```
整数と浮動小数点値は、演算処理の基本的要素です。コード内の即値としての整数と浮動小数点値の表現は
数値リテラルとして知られている一方で、ビルトインのこれらの値の表現は数値プリミティブと呼ばれています。
例えば、 `1` は整数リテラルですが、`1.0`
は浮動小数点リテラルです。これらのオブジェクトとしての
インメモリ表現が数値プリミティブです。

```@raw html
<!--
Julia provides a broad range of primitive numeric types, and a full complement of arithmetic and
bitwise operators as well as standard mathematical functions are defined over them. These map
directly onto numeric types and operations that are natively supported on modern computers, thus
allowing Julia to take full advantage of computational resources. Additionally, Julia provides
software support for [任意精度計算](@ref), which can handle operations on numeric
values that cannot be represented effectively in native hardware representations, but at the cost
of relatively slower performance.
-->
```
Juliaは、幅広いプリミティブ数値型を提供し、標準的な数学関数はもちろん算術演算やビット演算子の
補完が定義されています。これらは、現代のコンピュータでサポートされている数値型や演算処理と関連づけます。
これにより、Juliaは演算処理のリソースを最大限活用することができます。さらに、Juliaは、
使用しているハードウェアでは効果的に表現できない数値の演算処理を行うことができる [任意精度計算](@ref) の ソフトウェアサポートを提供します。

```@raw html
<!--
The following are Julia's primitive numeric types:
-->
```
以下はJuliaにおける数値プリミティブ型です。

[](  * **Integer types:**)

[](| Type      | Signed? | Number of bits | Smallest value | Largest value |)
[](|:----------------- |:------- |:-------------- |:-------------- |:------------- |)
[](| [`Int8`](@ref)    | ✓       | 8              | -2^7           | 2^7 - 1       |)
[](| [`UInt8`](@ref)   |         | 8              | 0              | 2^8 - 1       |)
[](| [`Int16`](@ref)   | ✓       | 16             | -2^15          | 2^15 - 1      |)
[](| [`UInt16`](@ref)  |         | 16             | 0              | 2^16 - 1      |)
[](| [`Int32`](@ref)   | ✓       | 32             | -2^31          | 2^31 - 1      |)
[](| [`UInt32`](@ref)  |         | 32             | 0              | 2^32 - 1      |)
[](| [`Int64`](@ref)   | ✓       | 64             | -2^63          | 2^63 - 1      |)
[](| [`UInt64`](@ref)  |         | 64             | 0              | 2^64 - 1      |)
[](| [`Int128`](@ref)  | ✓       | 128            | -2^127         | 2^127 - 1     |)
[](| [`UInt128`](@ref) |         | 128            | 0              | 2^128 - 1     |)
[](| [`Bool`](@ref)    | N/A     | 8              | `false` (0)    | `true` (1)    |)

[](  * **Floating-point types:**)

[](| Type              | Precision                                                                      | Number of bits |)
[](|:----------------- |:------------------------------------------------------------------------------ |:-------------- |)
[](| [`Float16`](@ref) | [half](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)     | 16             |)
[](| [`Float32`](@ref) | [single](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |)
[](| [`Float64`](@ref) | [double](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |)

  * **整数型:**

| 型             　　| 符号の有無|バイト数         | 最小値          | 最大値         |
|:----------------- |:------- |:-------------- |:-------------- |:------------- |
| `Int8`    | ✓       | 8              | -2^7           | 2^7 - 1       |
| `UInt8`   |         | 8              | 0              | 2^8 - 1       |
| `Int16`   | ✓       | 16             | -2^15          | 2^15 - 1      |
| `UInt16`  |         | 16             | 0              | 2^16 - 1      |
| `Int32`   | ✓       | 32             | -2^31          | 2^31 - 1      |
| `UInt32`  |         | 32             | 0              | 2^32 - 1      |
| `Int64`   | ✓       | 64             | -2^63          | 2^63 - 1      |
| `UInt64`  |         | 64             | 0              | 2^64 - 1      |
| `Int128`  | ✓       | 128            | -2^127         | 2^127 - 1     |
| `UInt128` |         | 128            | 0              | 2^128 - 1     |
| `Bool`  | N/A     | 8              | `false` (0)    | `true` (1)    |

  * **浮動小数点型:**

| 型              　| 精度                                                                            | バイト数 　　　　|
|:----------------- |:------------------------------------------------------------------------------ |:-------------- |
| `Float16` | [half](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)     | 16             |
| `Float32` | [single](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |
| `Float64` | [double](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |

```@raw html
<!--
Additionally, full support for [複素数と有理数](@ref) is built on top of these primitive
numeric types. All numeric types interoperate naturally without explicit casting, thanks to a
flexible, user-extensible [type promotion system](@ref conversion-and-promotion).
-->
```

加えて、[複素数と有理数](@ref)
へのサポートは、これらの数値プリミティブ型を踏まえて構築されています。
全ての数値型は、明示的に割り当てることなく自然に相互運用します。:[type promotion system](@ref conversion-and-promotion)


[](## Integers)
## 整数

```@raw html
<!--
Literal integers are represented in the standard manner:
-->
```
整数リテラルは標準的な方法で表現されます。:

```jldoctest
julia> 1
1

julia> 1234
1234
```
```@raw html
<!--
The default type for an integer literal depends on whether the target system has a 32-bit architecture
or a 64-bit architecture:
-->
```
整数リテラルのデフォルトの型は、ターゲットシステムのアーキテクチャが32ビットであるか
64ビットであるかによって異なります。:

```julia-repl
# 32-bit system:
julia> typeof(1)
Int32

# 64-bit system:
julia> typeof(1)
Int64
```
```@raw html
<!--
The Julia internal variable [`Sys.WORD_SIZE`](@ref) indicates whether the target system is 32-bit
or 64-bit:
-->
```
Juliaの内部変数 [`Sys.WORD_SIZE`](@ref)
はターゲットシステムが32ビットまたは64ビットのどちらかであることを示します。:

```julia-repl
# 32-bit system:
julia> Sys.WORD_SIZE
32

# 64-bit system:
julia> Sys.WORD_SIZE
64
```
```@raw html
<!--
Julia also defines the types `Int` and `UInt`, which are aliases for the system's signed and unsigned
native integer types respectively:
-->
```
Juliaは、システムの符号つきまたは符号なしの固有の整数型のエイリアスである
`Int` および `UInt` 型を定義します。:

```julia-repl
# 32-bit system:
julia> Int
Int32
julia> UInt
UInt32

# 64-bit system:
julia> Int
Int64
julia> UInt
UInt64
```
```@raw html
<!--
Larger integer literals that cannot be represented using only 32 bits but can be represented in
64 bits always create 64-bit integers, regardless of the system type:
-->
```
32ビットを使用して表現できないが、64ビットでは表現できるような大きな整数リテラルの場合、
システムタイプに関係なく常に64ビット整数を作成します。:

```jldoctest
# 32-bit or 64-bit system:
julia> typeof(3000000000)
Int64
```
```@raw html
<!--
Unsigned integers are input and output using the `0x` prefix and hexadecimal (base 16) digits
`0-9a-f` (the capitalized digits `A-F` also work for input). The size of the unsigned value is
determined by the number of hex digits used:
-->
```
符号なし整数は、 `0x` プレフィックスおよび16進数の `0-9a-f` （大文字の
`A-F` は入力時のみ使用可能）を
使用して入力および出力されます。符号なしの値の大きさは、使用されている16進数の桁数により決定されます。:

```jldoctest
julia> 0x1
0x01

julia> typeof(ans)
UInt8

julia> 0x123
0x0123

julia> typeof(ans)
UInt16

julia> 0x1234567
0x01234567

julia> typeof(ans)
UInt32

julia> 0x123456789abcdef
0x0123456789abcdef

julia> typeof(ans)
UInt64
```
```@raw html
<!--
This behavior is based on the observation that when one uses unsigned hex literals for integer
values, one typically is using them to represent a fixed numeric byte sequence, rather than just
an integer value.
-->
```
この動作は、整数値に符号なし16進リテラルを使用する際、ただの整数ではなく、
通常固定された数値のバイト列を表すという傾向に基づいています。

```@raw html
<!--
Recall that the variable [`ans`](@ref) is set to the value of the last expression evaluated in
an interactive session. This does not occur when Julia code is run in other ways.
-->
```
前述の通り、変数 [`ans`](@ref)
は対話セッションで最後に出力された式の値がセットされます。
この処理は、Juliaコードがその他の方法で実行された場合には実施されません。

```@raw html
<!--
Binary and octal literals are also supported:
-->
```
2進および8進リテラルについてもサポートされています。:

```jldoctest
julia> 0b10
0x02

julia> typeof(ans)
UInt8

julia> 0o10
0x08

julia> typeof(ans)
UInt8
```
```@raw html
<!--
The minimum and maximum representable values of primitive numeric types such as integers are given
by the [`typemin()`](@ref) and [`typemax()`](@ref) functions:
-->
```
整数のような数値プリミティブ型の最小値と最大値は、 [`typemin()`](@ref) および
[`typemax()`](@ref) 関数により取得が可能です。:

```jldoctest
julia> (typemin(Int32), typemax(Int32))
(-2147483648, 2147483647)

julia> for T in [Int8,Int16,Int32,Int64,Int128,UInt8,UInt16,UInt32,UInt64,UInt128]
           println("$(lpad(T,7)): [$(typemin(T)),$(typemax(T))]")
       end
   Int8: [-128,127]
  Int16: [-32768,32767]
  Int32: [-2147483648,2147483647]
  Int64: [-9223372036854775808,9223372036854775807]
 Int128: [-170141183460469231731687303715884105728,170141183460469231731687303715884105727]
  UInt8: [0,255]
 UInt16: [0,65535]
 UInt32: [0,4294967295]
 UInt64: [0,18446744073709551615]
UInt128: [0,340282366920938463463374607431768211455]
```
```@raw html
<!--
The values returned by [`typemin()`](@ref) and [`typemax()`](@ref) are always of the given argument
type. (The above expression uses several features we have yet to introduce, including [for loops](@ref man-loops),
[Strings](@ref man-strings), and [Interpolation](@ref), but should be easy enough to understand for users
with some existing programming experience.)
-->
```
[`typemin()`](@ref) および [`typemax()`](@ref) の戻り値は常に指定された引数の型となります。
（上記の式は、まだ紹介されていない
[for loops](@ref man-loops)、[Strings](@ref man-strings)、[Interpolation](@ref)
などの機能が含まれていますが、プログラミング経験者にとっては難しいものではないはずです。）

[](### Overflow behavior)
### オーバーフロー処理
```@raw html
<!--
In Julia, exceeding the maximum representable value of a given type results in a wraparound behavior:
-->
```
Juliaでは、指定された型の表現可能な最大値を超えた場合、ワードラップが発生します。:

```jldoctest
julia> x = typemax(Int64)
9223372036854775807

julia> x + 1
-9223372036854775808

julia> x + 1 == typemin(Int64)
true
```
```@raw html
<!--
Thus, arithmetic with Julia integers is actually a form of [modular arithmetic](https://en.wikipedia.org/wiki/Modular_arithmetic).
This reflects the characteristics of the underlying arithmetic of integers as implemented on modern
computers. In applications where overflow is possible, explicit checking for wraparound produced
by overflow is essential; otherwise, the [`BigInt`](@ref) type in [任意精度計算](@ref)
is recommended instead.
-->
```
このように、Juliaにおける整数の演算は、
[合同算術](https://en.wikipedia.org/wiki/Modular_arithmetic) の
形をとります。これは現代のコンピュータで実行される基本演算の特性を反映しています。
オーバーフローを許容するアプリケーションでは、オーバーフローにより発生したワードラップの明示的なチェックは不可欠です。
チェックが難しい場合は、[任意精度計算](@ref) の [`BigInt`](@ref)
型を使用することをお勧めします。

[](### Division errors)
### 助産処理エラー

```@raw html
<!--
Integer division (the `div` function) has two exceptional cases: dividing by zero, and dividing
the lowest negative number ([`typemin()`](@ref)) by -1. Both of these cases throw a [`DivideError`](@ref).
The remainder and modulus functions (`rem` and `mod`) throw a [`DivideError`](@ref) when their
second argument is zero.
-->
```
整数の除算（`div` 関数）には、0で割る、最小の負の数（ [`typemin()`](@ref)
）を−1で割るの2つの 例外的ケースがあります。どちらの結果も [`DivideError`](@ref)
となります。余りおよび絶対値の 関数（`rem` および `mod`
）についても、その第二引数が0の際に [`DivideError`](@ref) となります。

[](## Floating-Point Numbers)
## 浮動小数点数

```@raw html
<!--
Literal floating-point numbers are represented in the standard formats:
-->
```
浮動小数点数リテラルは標準的な形式で表されます。:

```jldoctest
julia> 1.0
1.0

julia> 1.
1.0

julia> 0.5
0.5

julia> .5
0.5

julia> -1.23
-1.23

julia> 1e10
1.0e10

julia> 2.5e-4
0.00025
```

```@raw html
<!--
The above results are all [`Float64`](@ref) values. Literal [`Float32`](@ref) values can be entered by writing an `f` in place of `e`:
-->
```
上記の結果は、全て [`Float64`](@ref) 値です。リテラル [`Float32`](@ref) 値は、 `f`
の代わりに `e` を使用することで入力が可能です。:

```jldoctest
julia> 0.5f0
0.5f0

julia> typeof(ans)
Float32

julia> 2.5f-4
0.00025f0
```

```@raw html
<!--
Values can be converted to [`Float32`](@ref) easily:
-->
```
値は簡単に [`Float32`](@ref) に変換することが可能です。:

```jldoctest
julia> Float32(-1.5)
-1.5f0

julia> typeof(ans)
Float32
```

```@raw html
<!--
Hexadecimal floating-point literals are also valid, but only as [`Float64`](@ref) values:
-->
```
16進数の浮動小数点リテラルも有効ですが、 [`Float64`](@ref)
値としてのみ使用が可能です。:

```jldoctest
julia> 0x1p0
1.0

julia> 0x1.8p3
12.0

julia> 0x.4p-1
0.125

julia> typeof(ans)
Float64
```

```@raw html
<!--
Half-precision floating-point numbers are also supported ([`Float16`](@ref)), but they are
implemented in software and use [`Float32`](@ref) for calculations.
-->
```
半制度浮動小数点数もサポートされていますが（ [`Float16`](@ref)
）、保存形式のみとして使用が可能です。 計算時には、それらは [`Float32`](@ref)
に変換されます。:

```jldoctest
julia> sizeof(Float16(4.))
2

julia> 2*Float16(4.)
Float16(8.0)
```

```@raw html
<!--
The underscore `_` can be used as digit separator:
-->
```
アンダースコア（ `_` ）は桁区切り文字として使用することができます。

```jldoctest
julia> 10_000, 0.000_000_005, 0xdead_beef, 0b1011_0010
(10000, 5.0e-9, 0xdeadbeef, 0xb2)
```

[](### Floating-point zero)
### 浮動小数点における0

```@raw html
<!--
Floating-point numbers have [two zeros](https://en.wikipedia.org/wiki/Signed_zero), positive zero
and negative zero. They are equal to each other but have different binary representations, as
can be seen using the `bits` function: :
-->
```
浮動小数点数には [2つの0](https://en.wikipedia.org/wiki/Signed_zero)
（正の0と負の0）があります。 この2つの0は同じ値ですが、 `bits`
関数を使用した際に見られるように、それぞれ異なるバイナリ表現を持ちます。:

```jldoctest
julia> 0.0 == -0.0
true

julia> bits(0.0)
"0000000000000000000000000000000000000000000000000000000000000000"

julia> bits(-0.0)
"1000000000000000000000000000000000000000000000000000000000000000"
```

[](### Special floating-point values)
### 特殊な浮動小数点値

```@raw html
<!--
There are three specified standard floating-point values that do not correspond to any point on
the real number line:
-->
```
実数直線上に対応しない3つの浮動小数点値が存在します。

[](| `Float16` | `Float32` | `Float64` | Name              | Description                                                     |)
[](|:--------- |:--------- |:--------- |:----------------- |:--------------------------------------------------------------- |)
[](| `Inf16`   | `Inf32`   | `Inf`     | positive infinity | a value greater than all finite floating-point values           |)
[](| `-Inf16`  | `-Inf32`  | `-Inf`    | negative infinity | a value less than all finite floating-point values              |)
[](| `NaN16`   | `NaN32`   | `NaN`     | not a number      | a value not `==` to any floating-point value (including itself) |)

| `Float16` | `Float32` | `Float64` | 名称               | 概要                                                            |
|:--------- |:--------- |:--------- |:----------------- |:--------------------------------------------------------------- |
| `Inf16`   | `Inf32`   | `Inf`     |　正の無限大 　　　　　| 全ての有限の浮動小数点値よりも大きい値                                |
| `-Inf16`  | `-Inf32`  | `-Inf`    | 負の無限大          | 全ての有限の浮動小数点数値よりも小さい値                               |
| `NaN16`   | `NaN32`   | `NaN`     | 非数値             | 浮動小数点値以外の値                                                |

```@raw html
<!--
For further discussion of how these non-finite floating-point values are ordered with respect
to each other and other floats, see []([数値の比較](@ref)). By the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008),
these floating-point values are the results of certain arithmetic operations:
-->
```
どのように非有限浮動小数点値がお互いに、およびその他の浮動値に対して順序付けられているかについては、
[]([数値の比較](@ref)) を参照してください。 [IEEE
754規格](https://en.wikipedia.org/wiki/IEEE_754-2008)
では、これらの浮動小数点値は特定の演算処理の結果として取得されます。:

```jldoctest
julia> 1/Inf
0.0

julia> 1/0
Inf

julia> -5/0
-Inf

julia> 0.000001/0
Inf

julia> 0/0
NaN

julia> 500 + Inf
Inf

julia> 500 - Inf
-Inf

julia> Inf + Inf
Inf

julia> Inf - Inf
NaN

julia> Inf * Inf
Inf

julia> Inf / Inf
NaN

julia> 0 * Inf
NaN
```

```@raw html
<!--
The [`typemin()`](@ref) and [`typemax()`](@ref) functions also apply to floating-point types:
-->
```
[`typemin()`](@ref) および [`typemax()`](@ref) 関数は浮動小数点型に対しても使用が可能です。

```jldoctest
julia> (typemin(Float16),typemax(Float16))
(-Inf16, Inf16)

julia> (typemin(Float32),typemax(Float32))
(-Inf32, Inf32)

julia> (typemin(Float64),typemax(Float64))
(-Inf, Inf)
```

[](### Machine epsilon)
### 計算機エプシロン

```@raw html
<!--
Most real numbers cannot be represented exactly with floating-point numbers, and so for many purposes
it is important to know the distance between two adjacent representable floating-point numbers,
which is often known as [machine epsilon](https://en.wikipedia.org/wiki/Machine_epsilon).
-->
```
ほとんどの実数は、浮動小数点数で正確に表現することができないため、[計算機イプシロン](https://en.wikipedia.org/wiki/Machine_epsilon)
として知られる隣接する２つの浮動小数点数の距離を理解することは、多くの用途のために重要となります。

```@raw html
<!--
Julia provides [`eps()`](@ref), which gives the distance between `1.0` and the next larger representable
floating-point value:
-->
```
Juliaは、 `1.0` と次に大きい浮動小数点値の間の距離を取得する [`eps()`](@ref)
関数を提供しています。:

```jldoctest
julia> eps(Float32)
1.1920929f-7

julia> eps(Float64)
2.220446049250313e-16

julia> eps() # same as eps(Float64)
2.220446049250313e-16
```
```@raw html
<!--
These values are `2.0^-23` and `2.0^-52` as [`Float32`](@ref) and [`Float64`](@ref) values, respectively. The
[`eps()`](@ref) function can also take a floating-point value as an argument, and gives the absolute
difference between that value and the next representable floating point value. That is, `eps(x)`
yields a value of the same type as `x` such that `x + eps(x)` is the next representable floating-point
value larger than `x`:
-->
```
これらはそれぞれ [`Float32`](@ref) および [`Float64`](@ref) 値として `2.0^-23` および
`2.0^-52` と なります。 [`eps()`](@ref)
関数は、浮動小数点値を引数として使用したり、ある値と次の浮動小数点値との絶対差を
得ることができます。つまり、 `eps(x)` は `x`
と同様の型の値を生成し、`x + eps(x)` は `x` よりも
大きい次の浮動小数点値を生成します。:

```jldoctest
julia> eps(1.0)
2.220446049250313e-16

julia> eps(1000.)
1.1368683772161603e-13

julia> eps(1e-27)
1.793662034335766e-43

julia> eps(0.0)
5.0e-324
```
```@raw html
<!--
The distance between two adjacent representable floating-point numbers is not constant, but is
smaller for smaller values and larger for larger values. In other words, the representable floating-point
numbers are densest in the real number line near zero, and grow sparser exponentially as one moves
farther away from zero. By definition, `eps(1.0)` is the same as `eps(Float64)` since `1.0` is
a 64-bit floating-point value.
-->
```
隣接する2つの浮動小数点数の距離は一定ではありませんが、小さい値ではその距離はより小さく、
大きい値ではより大きくなります。言い換えれば、浮動小数点数は実数線上で0に近い場合に最も高密度になり、
0から離れるにつれてまばらになっていきます。定義上、 `1.0`
は64ビットの浮動小数点値であるため、 `eps(1.0)` は`eps(Float64)`
と同一です。

```@raw html
<!--
Julia also provides the [`nextfloat()`](@ref) and [`prevfloat()`](@ref) functions which return
the next largest or smallest representable floating-point number to the argument respectively:
-->
```
Juliaは、次の最大または最小の浮動小数点数を引数に戻り値として与える
[`nextfloat()`](@ref) および [`prevfloat()`](@ref) 関数を提供しています。:

```jldoctest
julia> x = 1.25f0
1.25f0

julia> nextfloat(x)
1.2500001f0

julia> prevfloat(x)
1.2499999f0

julia> bits(prevfloat(x))
"00111111100111111111111111111111"

julia> bits(x)
"00111111101000000000000000000000"

julia> bits(nextfloat(x))
"00111111101000000000000000000001"
```

```@raw html
<!--
This example highlights the general principle that the adjacent representable floating-point numbers
also have adjacent binary integer representations.
-->
```
この例では、隣接する浮動小数点数は隣接する二進整数を持つという一般的な原則を表しています。


[](### Rounding modes)
### 端数処理モード
```@raw html
<!--
If a number doesn't have an exact floating-point representation, it must be rounded to an appropriate
representable value, however, if wanted, the manner in which this rounding is done can be changed
according to the rounding modes presented in the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008).
-->
```
値が割り切れる浮動小数点数ではない場合、適切な表現可能な値に繰り上げる必要があります。
しかし、必要な場合は、どのように繰り上げするかは [IEEE
754規格](https://en.wikipedia.org/wiki/IEEE_754-2008) の
端数処理モードに準拠して変更が可能です。

```jldoctest
julia> x = 1.1; y = 0.1;

julia> x + y
1.2000000000000002

julia> setrounding(Float64,RoundDown) do
           x + y
       end
1.2
```
```@raw html
<!--
The default mode used is always [`RoundNearest`](@ref), which rounds to the nearest representable
value, with ties rounded towards the nearest value with an even least significant bit.
-->
```
デフォルトでは、偶数の最下位ビットに最も近い値になるよう値を 繰り上げる
[`RoundNearest`](@ref) が常に使用されます。


[](!!! warning)
[](    Rounding is generally only correct for basic arithmetic functions ([`+()`](@ref), [`-()`](@ref),)
[](    [`*()`](@ref), [`/()`](@ref) and [`sqrt()`](@ref)) and type conversion operations. Many other)
[](    functions assume the default [`RoundNearest`](@ref) mode is set, and can give erroneous results)
[](    when operating under other rounding modes.)

!!! warning
    端数処理は通常基本的な算術関数（[`+()`](@ref)、[`-()`](@ref)、[`*()`](@ref)、[`/()`](@ref)、[`sqrt()`](@ref)）および
    型変換処理においてのみ正常に機能します。その他の多くの関数は、デフォルトの [`RoundNearest`](@ref) モードが
    設定されている前提で機能し、その他の端数処理モードでの演算処理では誤った結果を出力する場合があります。

[](### Background and References)
### 背景と考察

```@raw html
<!--
Floating-point arithmetic entails many subtleties which can be surprising to users who are unfamiliar
with the low-level implementation details. However, these subtleties are described in detail in
most books on scientific computation, and also in the following references:
-->
```
浮動小数点数演算は、あまり経験のないユーザには難しいかもしれませんが、多くの応用的な知識が必要です。
しかし、応用的な知識は科学技術計算に関する書籍や以下参考に詳細が記載されています。

[](  * The definitive guide to floating point arithmetic is the [IEEE 754-2008 Standard](http://standards.ieee.org/findstds/standard/754-2008.html);)
[](    however, it is not available for free online.)
[](  * For a brief but lucid presentation of how floating-point numbers are represented, see John D.
    Cook's [article](https://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)
    on the subject as well as his [introduction](https://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)
    to some of the issues arising from how this representation differs in behavior from the idealized
    abstraction of real numbers.)
[](  * Also recommended is Bruce Dawson's [series of blog posts on floating-point numbers](https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/).)
[](  * For an excellent, in-depth discussion of floating-point numbers and issues of numerical accuracy
    encountered when computing with them, see David Goldberg's paper [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf).)
[](  * For even more extensive documentation of the history of, rationale for, and issues with floating-point
    numbers, as well as discussion of many other topics in numerical computing, see the [collected writings](https://people.eecs.berkeley.edu/~wkahan/)
    of [William Kahan](https://en.wikipedia.org/wiki/William_Kahan), commonly known as the "Father
    of Floating-Point". Of particular interest may be [An Interview with the Old Man of Floating-Point](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html).)

  * 浮動小数点演算の最も有効なガイドは [IEEE754-2008規格](http://standards.ieee.org/findstds/standard/754-2008.html)です。しかし、オンライン上で無償で利用することはできません。
  * 浮動小数点数の表現に関するの簡潔かつ明快な説明は、John D. Cookの[記事](http://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)および実数の理想化された抽象化によりどのように表現が異なるかに関するいくつかの問題についての彼の[紹介](http://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)を参照してください。
  * Bruce Dawsonによる浮動小数点数に関する[一連のブログ記事](https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/)もお勧めします。
  * 浮動小数点数に関する高度かつ詳細な議論および演算時の数値の精度の問題については、David Goldbergの論文 [What Every Computer Scientist Should Know About Floating-PointArithmetic](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf)を参照してください。
  * より広範囲な歴史や合理性、浮動小数点数の問題に関する文献、およびその他の数値計算に関する議論については、「浮動小数点の父」として知られる [William Kahan](https://en.wikipedia.org/wiki/William_Kahan)の[論文](https://people.eecs.berkeley.edu/~wkahan/)を参照してください。特に [An Interview with the Old Man of Floating-Point](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html)は特に興味深いものとなっています。


[](## Arbitrary Precision Arithmetic)
## 任意精度計算
```@raw html
<!--
To allow computations with arbitrary-precision integers and floating point numbers, Julia wraps
the [GNU Multiple Precision Arithmetic Library (GMP)](https://gmplib.org) and the [GNU MPFR Library](http://www.mpfr.org),
respectively. The [`BigInt`](@ref) and [`BigFloat`](@ref) types are available in Julia for arbitrary
precision integer and floating point numbers respectively.
-->
```
任意精度の整数と浮動小数点数の計算を可能にするため、Juliaは [GNU
Multiple Precision Arithmetic Library (GMP)](https://gmplib.org) および
[GNU MPFR Library](http://www.mpfr.org)
に対応しています。Juliaでは任意精度整数と浮動小数点数のための The [`BigInt`](@ref)
および [`BigFloat`](@ref) 型を利用いただけます。

```@raw html
<!--
Constructors exist to create these types from primitive numerical types, and [`parse()`](@ref)
can be used to construct them from `AbstractString`s.  Once created, they participate in arithmetic
with all other numeric types thanks to Julia's [type promotion and conversion mechanism](@ref conversion-and-promotion):
-->
```
コンストラクタは数値プリミティブ型からこれらの型を生成し、[`parse()`](@ref) は
`AbstractString`から これらの型を生成する際に使用できます。Juliaの
型プロモーションと変換機構 [type promotion and conversion mechanism](@ref conversion-and-promotion) により、一度生成すると、全ての数値型で演算に対応します。

```jldoctest
julia> BigInt(typemax(Int64)) + 1
9223372036854775808

julia> parse(BigInt, "123456789012345678901234567890") + 1
123456789012345678901234567891

julia> parse(BigFloat, "1.23456789012345678901")
1.234567890123456789010000000000000000000000000000000000000000000000000000000004

julia> BigFloat(2.0^66) / 3
2.459565876494606882133333333333333333333333333333333333333333333333333333333344e+19

julia> factorial(BigInt(40))
815915283247897734345611269596115894272000000000
```
```@raw html
<!--
However, type promotion between the primitive types above and [`BigInt`](@ref)/[`BigFloat`](@ref)
is not automatic and must be explicitly stated.
-->
```
しかし、上記のプリミティブ型と [`BigInt`](@ref) または [`BigFloat`](@ref) 間の型変換は、自動では行われず、 明示的に指定する必要があります。

```jldoctest
julia> x = typemin(Int64)
-9223372036854775808

julia> x = x - 1
9223372036854775807

julia> typeof(x)
Int64

julia> y = BigInt(typemin(Int64))
-9223372036854775808

julia> y = y - 1
-9223372036854775809

julia> typeof(y)
BigInt
```
```@raw html
<!--
The default precision (in number of bits of the significand) and rounding mode of [`BigFloat`](@ref)
operations can be changed globally by calling [`setprecision()`](@ref) and [`setrounding()`](@ref),
and all further calculations will take these changes in account.  Alternatively, the precision
or the rounding can be changed only within the execution of a particular block of code by using
the same functions with a `do` block:
-->
```
デフォルトの精度（仮数のビット数で設定）および [`BigFloat`](@ref)
関数の端数処理は、[`setprecision()`](@ref) および [`setrounding()`](@ref)
を使用することで設定変更が可能です。設定変更後の演算は全てこの変更が反映されます。
また、コード内の特定のブロックのみの精度や端数処理の変更をしたい場合は、
`do` ブロックを併用することで設定が可能です。:

```jldoctest
julia> setrounding(BigFloat, RoundUp) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.100000000000000000000000000000000000000000000000000000000000000000000000000003

julia> setrounding(BigFloat, RoundDown) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.099999999999999999999999999999999999999999999999999999999999999999999999999986

julia> setprecision(40) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.1000000000004
```

[](## [Numeric Literal Coefficients](@id man-numeric-literal-coefficients))
## [数値リテラル係数](@id man-numeric-literal-coefficients)

```@raw html
<!--
To make common numeric formulas and expressions clearer, Julia allows variables to be immediately
preceded by a numeric literal, implying multiplication. This makes writing polynomial expressions
much cleaner:
-->
```
一般的な数値式や表現をより明確にするため、Juliaでは乗算の数値リテラルが変数を
先行することができます。これにより多項式をより明確に記載することができます。:

```jldoctest numeric-coefficients
julia> x = 3
3

julia> 2x^2 - 3x + 1
10

julia> 1.5x^2 - .5x + 1
13.0
```
```@raw html
<!--
It also makes writing exponential functions more elegant:
-->
```
また、指数関数を簡潔に記載することが可能です。:

```jldoctest numeric-coefficients
julia> 2^2x
64
```
```@raw html
<!--
The precedence of numeric literal coefficients is the same as that of unary operators such as
negation. So `2^3x` is parsed as `2^(3x)`, and `2x^3` is parsed as `2*(x^3)`.
-->
```
数値リテラル係数の優先順位は、否定のような単項演算子と同様です。 `2^3x`
は `2^(3x)` として 解析され、 `2x^3` は `2*(x^3)` として解析されます。:

```@raw html
<!--
Numeric literals also work as coefficients to parenthesized expressions:
-->
```
数値リテラルは括弧内の式の係数としての役割も持ちます。:

```jldoctest numeric-coefficients
julia> 2(x-1)^2 - 3(x-1) + 1
3
```

```@raw html
<!--
Additionally, parenthesized expressions can be used as coefficients to variables, implying multiplication
of the expression by the variable:
-->
```
さらに、括弧内の式は、変数の式の乗数を示す変数の係数としても使用できます。:

```jldoctest numeric-coefficients
julia> (x-1)x
6
```

```@raw html
<!--
Neither juxtaposition of two parenthesized expressions, nor placing a variable before a parenthesized
expression, however, can be used to imply multiplication:
-->
```
2つの括弧内の式の並記や括弧を伴う式の前に変数の置くことは、乗算を意味するためには使用することができません。

```jldoctest numeric-coefficients
julia> (x-1)(x+1)
ERROR: MethodError: objects of type Int64 are not callable

julia> x(x+1)
ERROR: MethodError: objects of type Int64 are not callable
```
```@raw html
<!--
Both expressions are interpreted as function application: any expression that is not a numeric
literal, when immediately followed by a parenthetical, is interpreted as a function applied to
the values in parentheses (see [Functions](@ref) for more about functions). Thus, in both of these
cases, an error occurs since the left-hand value is not a function.
-->
```
上記2つの式は関数として解釈されています。括弧を伴う式が直後に続く数値リテラルではない式は、
括弧内の値に適用される関数として解釈されます（詳細は [Functions](@ref)
を参照ください）。
上記の例では、左辺の値が関数ではないため、エラーが発生します。

```@raw html
<!--
The above syntactic enhancements significantly reduce the visual noise incurred when writing common
mathematical formulae. Note that no whitespace may come between a numeric literal coefficient
and the identifier or parenthesized expression which it multiplies.
-->
```
上記の構文の拡張機能は、同様な数式を記載する際の視覚的なノイズを低減します。
数値リテラル係数と乗算識別子や括弧で囲まれた式の間には、空白を入れることができませんので、注意してください。

[](### Syntax Conflicts)
### 構文の競合

```@raw html
<!--
Juxtaposed literal coefficient syntax may conflict with two numeric literal syntaxes: hexadecimal
integer literals and engineering notation for floating-point literals. Here are some situations
where syntactic conflicts arise:
-->
```
並列されたリテラル係数の構文は、16進整数リテラルと浮動小数点リテラルの指数表記の2つの
数値リテラルと競合する場合があります。以下は構文の競合が発生する状況の例です。

[](  * The hexadecimal integer literal expression `0xff` could be interpreted as the numeric literal
    `0` multiplied by the variable `xff`.)
[](  * The floating-point literal expression `1e10` could be interpreted as the numeric literal `1` multiplied
    by the variable `e10`, and similarly with the equivalent `E` form.)
  * 16進整数リテラルの表記 `0xff` は、数値リテラル `0` 掛ける変数 `xff` と解釈される可能性があります。
  * 浮動小数点リテラルの表記 `1e10` は、数値リテラル `1` 掛ける指数関数のような変数 `e10` と解釈される可能性があります

```@raw html
<!--
In both cases, we resolve the ambiguity in favor of interpretation as a numeric literals:
-->
```
上記両ケースでは、優先的に数値リテラルとして解釈されるようにすることで、あいまいさを回避しています。

[](  * Expressions starting with `0x` are always hexadecimal literals.)
[](  * Expressions starting with a numeric literal followed by `e` or `E` are always floating-point literals.)
  * `0x` で始まる式は、常に16進リテラルとして解釈されます。
  * `e` または `E`が続く数値リテラルで始まる式は、浮動小数点リテラルとして解釈されます。

[](## Literal zero and one)
## リテラルの0および1

```@raw html
<!--
Julia provides functions which return literal 0 and 1 corresponding to a specified type or the
type of a given variable.
-->
```
Juliaは、特定の方や特定の変数の型に対応してリテラル0および1を返す関数を提供しています。

[](| Function          | Description                                      |)
[](|:----------------- |:------------------------------------------------ |)
[](| [`zero(x)`](@ref) | Literal zero of type `x` or type of variable `x` |)
[](| [`one(x)`](@ref)  | Literal one of type `x` or type of variable `x`  |)

| 関数          　　　| D概要                                     |
|:----------------- |:------------------------------------------------ |
| [`zero(x)`](@ref) | `x` 型のリテラル0または変数 `x` の型 |
| [`one(x)`](@ref)  | `x` のリテラル1型または変数 `x` の型 |

```@raw html
<!--
These functions are useful in []([数値の比較](@ref)) to avoid overhead from unnecessary
[type conversion](@ref conversion-and-promotion).
-->
```
これらの関数は、 []([数値の比較](@ref)) 時の不要な
[type conversion](@ref conversion-and-promotion)の
オーバーヘッドを回避する際に有効です。

```@raw html
<!--
Examples:
-->
```
例：
```jldoctest
julia> zero(Float32)
0.0f0

julia> zero(1.0)
0.0

julia> one(Int32)
1

julia> one(BigFloat)
1.000000000000000000000000000000000000000000000000000000000000000000000000000000
```
