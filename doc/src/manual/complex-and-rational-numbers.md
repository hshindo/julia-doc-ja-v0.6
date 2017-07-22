[](# Complex and Rational Numbers)

# 複素数と有理数

[](
Julia ships with predefined types representing both complex and rational numbers, and supports
all standard [算術処理と基本的な関数](@ref) on them. [Conversion and Promotion](@ref conversion-and-promotion) are defined
so that operations on any combination of predefined numeric types, whether primitive or composite,
behave as expected.
)

Juliaは、複素数および有理数を表す定義済みの型を提供し、それらに対する[算術処理と基本的な関数](@ref)をサポートしています。
[Conversion and Promotion](@ref conversion-and-promotion)は、定義済みの数値型（プリミティブまたはコンポジットであっても）
が想定通りに処理されるように定義されています。

[](## Complex Numbers)

## 複素数

[](
The global constant [`im`](@ref) is bound to the complex number *i*, representing the principal
square root of -1. It was deemed harmful to co-opt the name `i` for a global constant, since it
is such a popular index variable name. Since Julia allows numeric literals to be [juxtaposed with identifiers as coefficients](@ref man-numeric-literal-coefficients),
this binding suffices to provide convenient syntax for complex numbers, similar to the traditional
mathematical notation:
)

グローバル定数[`im`](@ref)は-1の平方根を表す複素数 *i* に紐付きます。 なぜ定数名を[`i`](@ref)としないのかというと、``i`` は一般的なインデックス変数であるため、
グローバル定数にそのような名前をつけることは混乱を招くと考えられているからです。
Juliaでは数値リテラルに[識別子を伴う係数として並置](@ref man-numeric-literal-coefficients)することができるため、従来の数学的表記法と同様に、
複素数を使用する際に便利な構文を提供することができます。:


```jldoctest
julia> 1 + 2im
1 + 2im
```

[](You can perform all the standard arithmetic operations with complex numbers:)

複素数を使って標準的なすべての算術処理が可能です。:

```jldoctest
julia> (1 + 2im)*(2 - 3im)
8 + 1im

julia> (1 + 2im)/(1 - 2im)
-0.6 + 0.8im

julia> (1 + 2im) + (1 - 2im)
2 + 0im

julia> (-3 + 2im) - (5 - 1im)
-8 + 3im

julia> (-1 + 2im)^2
-3 - 4im

julia> (-1 + 2im)^2.5
2.7296244647840084 - 6.960664459571898im

julia> (-1 + 2im)^(1 + 1im)
-0.27910381075826657 + 0.08708053414102428im

julia> 3(2 - 5im)
6 - 15im

julia> 3(2 - 5im)^2
-63 - 60im

julia> 3(2 - 5im)^-1.0
0.20689655172413796 + 0.5172413793103449im
```

[](The promotion mechanism ensures that combinations of operands of different types just work:)

このプロモーションのシステムにより、異なる型の被演算子の組み合わせが正しく動作することが保証されています。:

```jldoctest
julia> 2(1 - 1im)
2 - 2im

julia> (2 + 3im) - 1
1 + 3im

julia> (1 + 2im) + 0.5
1.5 + 2.0im

julia> (2 + 3im) - 0.5im
2.0 + 2.5im

julia> 0.75(1 + 2im)
0.75 + 1.5im

julia> (2 + 3im) / 2
1.0 + 1.5im

julia> (1 - 3im) / (2 + 2im)
-0.5 - 1.0im

julia> 2im^2
-2 + 0im

julia> 1 + 3/4im
1.0 - 0.75im
```

[](
Note that `3/4im == 3/(4*im) == -(3/4*im)`, since a literal coefficient binds more tightly than
division.
)

リテラル係数は除算よりも厳密に紐付けるため、 ``3/4im == 3/(4*im)== -(3/4*im)`` であることに注意してください。

[](Standard functions to manipulate complex values are provided:)

複雑な値を操作するための標準関数がビルトインで定義されています。:



```jldoctest
julia> z = 1 + 2im
1 + 2im

julia> real(1 + 2im) # real part of z
1

julia> imag(1 + 2im) # imaginary part of z
2

julia> conj(1 + 2im) # complex conjugate of z
1 - 2im

julia> abs(1 + 2im) # absolute value of z
2.23606797749979

julia> abs2(1 + 2im) # squared absolute value
5

julia> angle(1 + 2im) # phase angle in radians
1.1071487177940904
```

[](
As usual, the absolute value ([`abs()`](@ref)) of a complex number is its distance from zero.
[`abs2()`](@ref) gives the square of the absolute value, and is of particular use for complex
numbers where it avoids taking a square root. [`angle()`](@ref) returns the phase angle in radians
(also known as the *argument* or *arg* function). The full gamut of other []([基本的な関数](@ref))
is also defined for complex numbers:
)

従来通り、複素数の絶対値 ([`abs()`](@ref)) は0からの距離を表します。
[`abs2()`](@ref) は絶対値の2乗の値を算出します。これは平方根を避けるために使用されます。
[`angle()`](@ref) は位相角をラジアンで返します。 (引数または *arg* 関数とも呼ばれます。)
複素数に関するその他の [基本的な関数](@ref) が定義されています。:


```jldoctest
julia> sqrt(1im)
0.7071067811865476 + 0.7071067811865475im

julia> sqrt(1 + 2im)
1.272019649514069 + 0.7861513777574233im

julia> cos(1 + 2im)
2.0327230070196656 - 3.0518977991518im

julia> exp(1 + 2im)
-1.1312043837568135 + 2.4717266720048188im

julia> sinh(1 + 2im)
-0.4890562590412937 + 1.4031192506220405im
```

[](
Note that mathematical functions typically return real values when applied to real numbers and
complex values when applied to complex numbers. For example, [`sqrt()`](@ref) behaves differently
when applied to `-1` versus `-1 + 0im` even though `-1 == -1 + 0im`:
)

数学関数は通常、実数値に対して使用された場合は実数値を、複素数値に対して使用された場合は複素数値を返しますので注意してください。
例えば [`sqrt()`](@ref) は、たとえ ``-1 == -1 + 0im`` であっても ``-1`` と ``-1 + 0im`` とが使用された場合では挙動が異なります。:

```jldoctest
julia> sqrt(-1)
ERROR: DomainError:
sqrt will only return a complex result if called with a complex argument. Try sqrt(complex(x)).
Stacktrace:
 [1] sqrt(::Int64) at ./math.jl:431

julia> sqrt(-1 + 0im)
0.0 + 1.0im
```

[](
The [literal numeric coefficient notation](@ref man-numeric-literal-coefficients) does not work when constructing a complex number
from variables. Instead, the multiplication must be explicitly written out:
)

[数値リテラル係数表現](@ref man-numeric-literal-coefficients) は、変数から複素数を
構成する際には機能しません。代わりに、乗算は明示的に記載される必要があります。:

```jldoctest
julia> a = 1; b = 2; a + b*im
1 + 2im
```

[](
However, this is *not* recommended; Use the [`complex()`](@ref) function instead to construct
a complex value directly from its real and imaginary parts:
)

しかしながら、これは推奨されていません。実数部および虚数部から直接、複素数値を構築するのではなく、
[`complex()`](@ref) 関数を使用してください。:

```jldoctest
julia> a = 1; b = 2; complex(a, b)
1 + 2im
```

[](This construction avoids the multiplication and addition operations.)

この場合、乗算および加算を回避することができます。

[](
[`Inf`](@ref) and [`NaN`](@ref) propagate through complex numbers in the real and imaginary parts
of a complex number as described in the [特殊な浮動小数点値](@ref) section:
)

[`Inf`](@ref) および [`NaN`](@ref) は、 [特殊な浮動小数点値](@ref) に説明されているように、
複素数の実数部と虚数部を伝えます。:

```jldoctest
julia> 1 + Inf*im
1.0 + Inf*im

julia> 1 + NaN*im
1.0 + NaN*im
```

[](## Rational Numbers)

## 有理数

[](
Julia has a rational number type to represent exact ratios of integers. Rationals are constructed
using the [`//`](@ref) operator:
)

Juliaには、整数の正確な比率を表す有理数型があります。有理数は [`//`](@ref) 演算子を使用して構成されます。:

```jldoctest
julia> 2//3
2//3
```

[](
If the numerator and denominator of a rational have common factors, they are reduced to lowest
terms such that the denominator is non-negative:
)

有理数の分子と分母が共通の因数を持つ場合、分母が非負であるように最小値まで約分されます。:

```jldoctest
julia> 6//9
2//3

julia> -4//8
-1//2

julia> 5//-15
-1//3

julia> -4//-12
1//3
```

[](
This normalized form for a ratio of integers is unique, so equality of rational values can be
tested by checking for equality of the numerator and denominator. The standardized numerator and
denominator of a rational value can be extracted using the [`numerator()`](@ref) and [`denominator()`](@ref)
functions:
)

整数比に対するこの正規化された形式は固有であるため、有理数の相等は、分子と分母の相等をチェックすることで確認できます。
有理数の標準化された分子と分母は [`numerator()`](@ref) および [`denominator()`](@ref) 関数を使用して得ることができます。:

```jldoctest
julia> numerator(2//3)
2

julia> denominator(2//3)
3
```

[](
Direct comparison of the numerator and denominator is generally not necessary, since the standard
arithmetic and comparison operations are defined for rational values:
)

標準的な算術および比較演算は有理数向けに定義されているため、分子と分母の直接的な比較は通常必要ありません。:

```jldoctest
julia> 2//3 == 6//9
true

julia> 2//3 == 9//27
false

julia> 3//7 < 1//2
true

julia> 3//4 > 2//3
true

julia> 2//4 + 1//6
2//3

julia> 5//12 - 1//4
1//6

julia> 5//8 * 3//12
5//32

julia> 6//5 / 10//7
21//25
```

[](Rationals can be easily converted to floating-point numbers:)

有理数は浮動小数点数に簡単に変換できます。:

```jldoctest
julia> float(3//4)
0.75
```

[](
Conversion from rational to floating-point respects the following identity for any integral values
of `a` and `b`, with the exception of the case `a == 0` and `b == 0`:
)

有理数から浮動小数点数への変換は、 ``a==0`` および ``b==0`` の場合を除き、
整数値 ``a`` および ``b`` の整数値の同一性を考慮します。:

```jldoctest
julia> a = 1; b = 2;

julia> isequal(float(a//b), a/b)
true
```

[](Constructing infinite rational values is acceptable:)

無限有理値を得ることができます。:

```jldoctest
julia> 5//0
1//0

julia> -3//0
-1//0

julia> typeof(ans)
Rational{Int64}
```

[](Trying to construct a [`NaN`](@ref) rational value, however, is not:)

しかしながら、 [`NaN`](@ref) の有理値を得ることはできません。:

```jldoctest
julia> 0//0
ERROR: ArgumentError: invalid rational: zero(Int64)//zero(Int64)
Stacktrace:
 [1] Rational{Int64}(::Int64, ::Int64) at ./rational.jl:8
 [2] //(::Int64, ::Int64) at ./rational.jl:35
```

[](As usual, the promotion system makes interactions with other numeric types effortless:)

これまで同様、プロモーションシステムはその他の数値型との処理を容易にします。:

```jldoctest
julia> 3//5 + 1
8//5

julia> 3//5 - 0.5
0.09999999999999998

julia> 2//7 * (1 + 2im)
2//7 + 4//7*im

julia> 2//7 * (1.5 + 2im)
0.42857142857142855 + 0.5714285714285714im

julia> 3//2 / (1 + 2im)
3//10 - 3//5*im

julia> 1//2 + 2im
1//2 + 2//1*im

julia> 1 + 2//3im
1//1 - 2//3*im

julia> 0.5 == 1//2
true

julia> 0.33 == 1//3
false

julia> 0.33 < 1//3
true

julia> 1//3 - 0.33
0.0033333333333332993
```
