[](# Methods)
# メソッド


```@raw html
<!--
Recall from [関数](@ref man-functions) that a function is an object that maps a tuple of arguments to a
return value, or throws an exception if no appropriate value can be returned. It is common for
the same conceptual function or operation to be implemented quite differently for different types
of arguments: adding two integers is very different from adding two floating-point numbers, both
of which are distinct from adding an integer to a floating-point number. Despite their implementation
differences, these operations all fall under the general concept of "addition". Accordingly, in
Julia, these behaviors all belong to a single object: the `+` function.
-->
```
[関数]（@ ref man-functions）の章を思い出してください。関数は、引数のタプルを受け取り、戻り値を返すか、適切な値が返せない場合は例外を投げる、オブジェクトです。
同一の概念である関数や演算が、引数の型が異なると実装がまったく異なる、ということはよくあります。
2つの整数を足すことは、2つの浮動小数点数を足すこととはまったく異なり、両方とも整数に浮動小数点数を足すこととは異なります。
実装の違いにもかかわらず、これらの操作はすべて「足し算」という一般的な概念に当てはまります。
したがって、Juliaでは、これらの挙動はすべて1つのオブジェクト「`+`関数」に属します。



```@raw html
<!--
To facilitate using many different implementations of the same concept smoothly, functions need
not be defined all at once, but can rather be defined piecewise by providing specific behaviors
for certain combinations of argument types and counts. A definition of one possible behavior for
a function is called a *method*. Thus far, we have presented only examples of functions defined
with a single method, applicable to all types of arguments. However, the signatures of method
definitions can be annotated to indicate the types of arguments in addition to their number, and
more than a single method definition may be provided. When a function is applied to a particular
tuple of arguments, the most specific method applicable to those arguments is applied. Thus, the
overall behavior of a function is a patchwork of the behaviors of its various method definitions.
If the patchwork is well designed, even though the implementations of the methods may be quite
different, the outward behavior of the function will appear seamless and consistent.
-->
```
同じ概念でも、多くの異なる実装を、円滑に利用するためには、関数すべてを一度に定義する必要はなくて、引数の型と個数の組み合わせごとに、特定の動作を指定して、区分的に定義できます。
こうした関数の１つのありうる動作の定義は、**メソッド**と呼ばれます。
ここまでは例示した関数は、単一のメソッドで定義されたものだけで、すべての型の引数に適用できます。
しかし、メソッド定義のシグネチャに、型注釈をつけて引数の型と数を指定でき、複数のメソッド定義を提供することができます。
関数が特定の組の引数に適用されるとき、それらの引数に適用可能な最もなメソッドが適用されます。
したがって、関数の全体的な動作は、さまざまなメソッド定義の動作のパッチワークです。
パッチワークがうまく設計されていれば、メソッドの実装がかなり異なっていても、外からは関数の動作は継ぎ目なく一貫しているように見えます。



```@raw html
<!--
The choice of which method to execute when a function is applied is called *dispatch*. Julia allows
the dispatch process to choose which of a function's methods to call based on the number of arguments
given, and on the types of all of the function's arguments. This is different than traditional
object-oriented languages, where dispatch occurs based only on the first argument, which often
has a special argument syntax, and is sometimes implied rather than explicitly written as an argument.
[^1] Using all of a function's arguments to choose which method should be invoked, rather than
just the first, is known as [multiple dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch).
Multiple dispatch is particularly useful for mathematical code, where it makes little sense to
artificially deem the operations to "belong" to one argument more than any of the others: does
the addition operation in `x + y` belong to `x` any more than it does to `y`? The implementation
of a mathematical operator generally depends on the types of all of its arguments. Even beyond
mathematical operations, however, multiple dispatch ends up being a powerful and convenient paradigm
for structuring and organizing programs.
-->
```
実行するメソッドを、関数が適用される際に選択することを、**ディスパッチ**と呼ばれます。
Juliaでは、ディスパッチの過程で、関数のメソッドのどれを呼び出すか、関数のすべての引数の型と個数に基づいて、選択できます。
これは、従来のオブジェクト指向言語とは異なります。従来のオブジェクト指向言語では、通常は最初の引数のみに基づいてディスパッチが行われ、最初の引数を特別視する構文を持ち、引数として明示的にではなく暗示的に記述されることがあります。
[^ 1]
関数の、最初の引数だけではなく、すべての引数を使用して、呼び出すメソッドを選択することは、[多重ディスパッチ](https://en.wikipedia.org/wiki/)として知られています。
多重ディスパッチは特に数学用のコードで有用です。演算がどの引数より「属している」かと不自然なことを考えても、いみがないからです。`x + y` の式の中の足し算は、 `x` に `y`よりも属してると思いますか？
数学的演算子の実装は、一般には、すべての引数の型に依存しています。
しかし、数学的な操作以外でも、多重ディスパッチは、プログラムの構築し組織化する強力で便利なパラダイムです。


```@raw html
<!--
[^1]:
    In C++ or Java, for example, in a method call like `obj.meth(arg1,arg2)`, the object obj "receives"
    the method call and is implicitly passed to the method via the `this` keyword, rather than as
    an explicit method argument. When the current `this` object is the receiver of a method call,
    it can be omitted altogether, writing just `meth(arg1,arg2)`, with `this` implied as the receiving
    object.
-->
```
[^ 1]：例えば、C ++やJavaでは、`obj.meth(arg1,arg2)`のようなメソッド呼び出しでは、オブジェクトobjはメソッド呼び出しを「受け取り」、明示的なメソッドの引数ではなく暗黙のうちに`this`キーワードを介して、メソッドに引き渡されます。`this`オブジェクトがメソッド呼び出しのレシーバである場合は省略できます。`meth(arg1,arg2)`と書くだけで、`this`は受け取るオブジェクトを暗に指します。

[](## Defining Methods)
## メソッドの定義


```@raw html
<!--
Until now, we have, in our examples, defined only functions with a single method having unconstrained
argument types. Such functions behave just like they would in traditional dynamically typed languages.
Nevertheless, we have used multiple dispatch and methods almost continually without being aware
of it: all of Julia's standard functions and operators, like the aforementioned `+` function,
have many methods defining their behavior over various possible combinations of argument type
and count.
-->
```
今までの例では、引数の型に制約のない、単一のメソッドを持つ関数しか定義していませんでした。
そのような関数は、従来の動的型付け言語と同じように動作します。
にもかかわらず、私たちは知らない間に、ほぼ継続的に多重ディスパッチとメソッドを使用してきました。
前述の`+`関数のような、Juliaの標準的な関数と演算子はすべて、引数の型と個数のさまざまな組み合わせで動作を定義された、多くのメソッドを持っています。



```@raw html
<!--
When defining a function, one can optionally constrain the types of parameters it is applicable
to, using the `::` type-assertion operator, introduced in the section on [Composite Types](@ref):
-->
```
関数を定義するときには、[複合型](@ref)に関するセクションで紹介した`::`型表明演算子を使って、適用可能なパラメータの型を、必要に応じて、制限することができます。

```jldoctest fofxy
julia> f(x::Float64, y::Float64) = 2x + y
f (generic function with 1 method)
```


```@raw html
<!--
This function definition applies only to calls where `x` and `y` are both values of type
[`Float64`](@ref):
-->
```
この関数定義は、`x`と`y`の両方の値の型が[`Float64`](@ref)のみ、呼び出しに適用されます。


```jldoctest fofxy
julia> f(2.0, 3.0)
7.0
```


```@raw html
<!--
Applying it to any other types of arguments will result in a [`MethodError`](@ref):
-->
```
これを他の型の引数に適用すると、次のような[`MethodError`](@ref)になります。


```jldoctest fofxy
julia> f(2.0, 3)
ERROR: MethodError: no method matching f(::Float64, ::Int64)
Closest candidates are:
  f(::Float64, !Matched::Float64) at none:1

julia> f(Float32(2.0), 3.0)
ERROR: MethodError: no method matching f(::Float32, ::Float64)
Closest candidates are:
  f(!Matched::Float64, ::Float64) at none:1

julia> f(2.0, "3.0")
ERROR: MethodError: no method matching f(::Float64, ::String)
Closest candidates are:
  f(::Float64, !Matched::Float64) at none:1

julia> f("2.0", "3.0")
ERROR: MethodError: no method matching f(::String, ::String)
```


```@raw html
<!--
As you can see, the arguments must be precisely of type [`Float64`](@ref). Other numeric
types, such as integers or 32-bit floating-point values, are not automatically converted
to 64-bit floating-point, nor are strings parsed as numbers. Because `Float64` is a concrete
type and concrete types cannot be subclassed in Julia, such a definition can only be applied
to arguments that are exactly of type `Float64`. It may often be useful, however, to write
more general methods where the declared parameter types are abstract:
-->
```

これを見て分かるように、引数は正確に[`Float64`](@ref)型のものでなければなりません。
整数や32ビット浮動小数点数などの他の数値型は、自動的には64ビット浮動小数点に変換されず、文字列も数字として解析されません。
`Float64`は具象型で、具象型はJuliaでは、サブクラス化できないので、このような定義は型の正確な引数のみに適用することができます。
しかし、宣言されたパラメータ型が抽象的な時は、より一般的なメソッドを書くと、けっこう役に立ちます。


```jldoctest fofxy
julia> f(x::Number, y::Number) = 2x - y
f (generic function with 2 methods)

julia> f(2.0, 3)
1.0
```


```@raw html
<!--
This method definition applies to any pair of arguments that are instances of [`Number`](@ref).
They need not be of the same type, so long as they are each numeric values. The problem of
handling disparate numeric types is delegated to the arithmetic operations in the
expression `2x - y`.
-->
```
このメソッド定義は[`Number`](@ref)のインスタンスである任意の引数のペアに適用されます。
それらがそれぞれ数値である限り、同じ型である必要はありません。
異なる数値型を処理する問題は、式`2x - y`の算術演算に委譲されます。



```@raw html
<!--
To define a function with multiple methods, one simply defines the function multiple times, with
different numbers and types of arguments. The first method definition for a function creates the
function object, and subsequent method definitions add new methods to the existing function object.
The most specific method definition matching the number and types of the arguments will be executed
when the function is applied. Thus, the two method definitions above, taken together, define the
behavior for `f` over all pairs of instances of the abstract type `Number` -- but with a different
behavior specific to pairs of [`Float64`](@ref) values. If one of the arguments is a 64-bit
float but the other one is not, then the `f(Float64,Float64)` method cannot be called and
the more general `f(Number,Number)` method must be used:
-->
```
複数のメソッドを持つ関数を定義するには、異なる数と型の引数に対して関数を複数回定義するだけです。
最初のメソッド定義では関数オブジェクトを作成され、後続のメソッド定義では既存の関数オブジェクトに新しいメソッドが追加されます。
関数の適用時に、引数の数と型が最も一致するメソッド定義が実行されます。
したがって、上の2つのメソッド定義はまとめて、抽象型`Number`のインスタンスのペアすべてに対して`f`の動作を定義しますが、値[`Float64`](@ref)のペアに固有の動作は異なります。
引数の1つが64ビット浮動小数点数で、もう1つがそうでない場合、この`f(Float64,Float64)`メソッドは呼び出すことができず、より一般的な`f(Number,Number)`メソッドを使用する必要があります。

```jldoctest fofxy
julia> f(2.0, 3.0)
7.0

julia> f(2, 3.0)
1.0

julia> f(2.0, 3)
1.0

julia> f(2, 3)
1
```


```@raw html
<!--
The `2x + y` definition is only used in the first case, while the `2x - y` definition is used
in the others. No automatic casting or conversion of function arguments is ever performed: all
conversion in Julia is non-magical and completely explicit. [Conversion and Promotion](@ref conversion-and-promotion),
however, shows how clever application of sufficiently advanced technology can be indistinguishable
from magic. [^Clarke61]

For non-numeric values, and for fewer or more than two arguments, the function `f` remains undefined,
and applying it will still result in a [`MethodError`](@ref):
-->
```
`2x + y`という定義は、最初の場合にのみ使用されますが、`2x - y`という定義は、他で使用されています。
関数の引数の自動キャストや変換は一度も実行されません。
Juliaでは、すべての変換は魔法ではなく完全に明示的です。
しかし、[Conversion and Promotion]（@ ref conversion-and-promotion）は、十分に高度な技術を巧みに応用すると魔法と区別できないほどになりうることを示しています。[^ Clarke61]

数値以外の値や2つ以上の引数の場合、関数`f`は未定義のままであり、そのまま適用すると次のように[`MethodError`](@ref)がおこります。


```jldoctest fofxy
julia> f("foo", 3)
ERROR: MethodError: no method matching f(::String, ::Int64)
Closest candidates are:
  f(!Matched::Number, ::Number) at none:1

julia> f()
ERROR: MethodError: no method matching f()
Closest candidates are:
  f(!Matched::Float64, !Matched::Float64) at none:1
  f(!Matched::Number, !Matched::Number) at none:1
```


```@raw html
<!--
You can easily see which methods exist for a function by entering the function object itself in
an interactive session:
-->
```
対話セッションで関数オブジェクト自体を入力すると、関数にどんなメソッドが存在するかを簡単に確認できます。


```jldoctest fofxy
julia> f
f (generic function with 2 methods)
```


```@raw html
<!--
This output tells us that `f` is a function object with two methods. To find out what the signatures
of those methods are, use the [`methods()`](@ref) function:
-->
```
この出力は、`f`が2つのメソッドを持つ関数オブジェクトであることを示しています。
これらのメソッドのシグネチャを調べるには、次の[`methods()`](@ref)関数を使用します。

```julia-repl
julia> methods(f)
# 2 methods for generic function "f":
f(x::Float64, y::Float64) in Main at none:1
f(x::Number, y::Number) in Main at none:1
```


```@raw html
<!--

which shows that `f` has two methods, one taking two `Float64` arguments and one taking arguments
of type `Number`. It also indicates the file and line number where the methods were defined: because
these methods were defined at the REPL, we get the apparent line number `none:1`.

In the absence of a type declaration with `::`, the type of a method parameter is `Any` by default,
meaning that it is unconstrained since all values in Julia are instances of the abstract type
`Any`. Thus, we can define a catch-all method for `f` like so:
-->
```

すると表示されるのは、`f`の二つのメソッドで、一方は2つの`Float64`の引数を取り、他方は型が`Number`の引数を取ります。
また、メソッドが定義されたファイルと行番号も表示されます。
これらのメソッドはREPLで定義されているため、見かけの行番号`none:1`が表示されます。

`::`による型宣言がない場合、メソッドのパラメータの型はデフォルトでは`Any`であり、Juliaのすべての値が抽象型`Any`のインスタンスであるため、制約がないことを意味しています 。
したがって、`f`のcatch-allメソッドを以下のように定義することができます。


```jldoctest fofxy
julia> f(x,y) = println("Whoa there, Nelly.")
f (generic function with 3 methods)

julia> f("foo", 1)
Whoa there, Nelly.
```


```@raw html
<!--
This catch-all is less specific than any other possible method definition for a pair of parameter
values, so it will only be called on pairs of arguments to which no other method definition applies.

Although it seems a simple concept, multiple dispatch on the types of values is perhaps the single
most powerful and central feature of the Julia language. Core operations typically have dozens
of methods:
-->
```
このcatch-allは、ある引数のペアに対する他のメソッド定義ほど限定的ではないため、他のメソッド定義が適用されない引数のペアに対してのみ呼び出されます。

単純な考え方のように見えますが、値の型に対する多重ディスパッチは、おそらくJulia言語の最も強力で中心的な機能です。中心となる演算には通常数十種類のメソッドがあります。

```julia-repl
julia> methods(+)
# 180 methods for generic function "+":
+(x::Bool, z::Complex{Bool}) in Base at complex.jl:224
+(x::Bool, y::Bool) in Base at bool.jl:89
+(x::Bool) in Base at bool.jl:86
+(x::Bool, y::T) where T<:AbstractFloat in Base at bool.jl:96
+(x::Bool, z::Complex) in Base at complex.jl:231
+(a::Float16, b::Float16) in Base at float.jl:372
+(x::Float32, y::Float32) in Base at float.jl:374
+(x::Float64, y::Float64) in Base at float.jl:375
+(z::Complex{Bool}, x::Bool) in Base at complex.jl:225
+(z::Complex{Bool}, x::Real) in Base at complex.jl:239
+(x::Char, y::Integer) in Base at char.jl:40
+(c::BigInt, x::BigFloat) in Base.MPFR at mpfr.jl:303
+(a::BigInt, b::BigInt, c::BigInt, d::BigInt, e::BigInt) in Base.GMP at gmp.jl:303
+(a::BigInt, b::BigInt, c::BigInt, d::BigInt) in Base.GMP at gmp.jl:296
+(a::BigInt, b::BigInt, c::BigInt) in Base.GMP at gmp.jl:290
+(x::BigInt, y::BigInt) in Base.GMP at gmp.jl:258
+(x::BigInt, c::Union{UInt16, UInt32, UInt64, UInt8}) in Base.GMP at gmp.jl:315
...
+(a, b, c, xs...) at operators.jl:119
```


```@raw html
<!--
Multiple dispatch together with the flexible parametric type system give Julia its ability to
abstractly express high-level algorithms decoupled from implementation details, yet generate efficient,
specialized code to handle each case at run time.
-->
```
多重ディスパッチと柔軟なパラメトリック型システムによって、Juliaに備わった能力は、実装の詳細から切り離された高水準のアルゴリズムを抽象的に表現し、実行時に各ケースを処理する効率的で特定されたコードを生成することです。

[](## [Method Ambiguities](@id man-ambiguities))
## [メソッドの曖昧さ](@id man-ambiguities)


```@raw html
<!--
It is possible to define a set of function methods such that there is no unique most specific
method applicable to some combinations of arguments:
-->
```
ある種の引数の組み合わせに対しては、最も適合するメソッドが一意に定まらない、関数メソッドの組み合わせを定義することは可能です。

```jldoctest gofxy
julia> g(x::Float64, y) = 2x + y
g (generic function with 1 method)

julia> g(x, y::Float64) = x + 2y
g (generic function with 2 methods)

julia> g(2.0, 3)
7.0

julia> g(2, 3.0)
8.0

julia> g(2.0, 3.0)
ERROR: MethodError: g(::Float64, ::Float64) is ambiguous.
[...]
```


```@raw html
<!--
Here the call `g(2.0, 3.0)` could be handled by either the `g(Float64, Any)` or the `g(Any, Float64)`
method, and neither is more specific than the other. In such cases, Julia raises a [`MethodError`](@ref)
rather than arbitrarily picking a method. You can avoid method ambiguities by specifying an appropriate
method for the intersection case:
-->
```
ここでの関数呼び出し`g(2.0, 3.0)`は、`g(Float64, Any)`と`g(Any, Float64)` のメソッドによって処理可能ですが、どちらがより適合しているとは決められません。
このような場合、Juliaは勝手にメソッドを選択するのではなく、[`MethodError`](@ref)を発生させます。 
共通する場合に適切なメソッドを指定することで、メソッドの曖昧さをなくすことができます。


```jldoctest gofxy
julia> g(x::Float64, y::Float64) = 2x + 2y
g (generic function with 3 methods)

julia> g(2.0, 3)
7.0

julia> g(2, 3.0)
8.0

julia> g(2.0, 3.0)
10.0
```


```@raw html
<!--
It is recommended that the disambiguating method be defined first, since otherwise the ambiguity
exists, if transiently, until the more specific method is defined.

In more complex cases, resolving method ambiguities involves a certain
element of design; this topic is explored further [below](@ref man-method-design-ambiguities).
-->
```
曖昧さのないメソッドを最初に定義することが推奨されます。というのも、一時的であれば、より適合するメソッドが定義されるまで、曖昧さが残るからです。

より複雑なケースでは、メソッドの曖昧さを解決するには一定の設計要素が必要になります。この話題は[below]（@ ref man-method-design-ambiguities）をさらに詳しく解説しています。

[](## Parametric Methods)
## パラメトリックメソッド



```@raw html
<!--
Method definitions can optionally have type parameters qualifying the signature:
-->
```

メソッド定義には、シグネチャを修飾する型パラメータを必要に応じてつけることができます。


```jldoctest same_typefunc
julia> same_type(x::T, y::T) where {T} = true
same_type (generic function with 1 method)

julia> same_type(x,y) = false
same_type (generic function with 2 methods)
```


```@raw html
<!--
The first method applies whenever both arguments are of the same concrete type, regardless of
what type that is, while the second method acts as a catch-all, covering all other cases. Thus,
overall, this defines a boolean function that checks whether its two arguments are of the same
type:
-->
```
第1のメソッドは、両方の引数が同じ具象型の時、どんな型であっても適用します。第2のメソッドは他のすべての場合をカバーするガラクタ入れのように機能するときに適用します。
したがって、これは全体的に、2つの引数が同じ型であるかどうかを検査するブール関数を定義します。



```jldoctest same_typefunc
julia> same_type(1, 2)
true

julia> same_type(1, 2.0)
false

julia> same_type(1.0, 2.0)
true

julia> same_type("foo", 2.0)
false

julia> same_type("foo", "bar")
true

julia> same_type(Int32(1), Int64(2))
false
```


```@raw html
<!--
Such definitions correspond to methods whose type signatures are `UnionAll` types
(see [UnionAll Types](@ref)).

This kind of definition of function behavior by dispatch is quite common -- idiomatic, even --
in Julia. Method type parameters are not restricted to being used as the types of arguments:
they can be used anywhere a value would be in the signature of the function or body of the function.
Here's an example where the method type parameter `T` is used as the type parameter to the parametric
type `Vector{T}` in the method signature:
-->
```

そのような定義はシグネチャがUnionAll型であるメソッドに対応します([全合併型](@ref) 参照)。

Juliaでは、このようなディスパッチによる関数の動作の定義は、まったく共通していて、慣用的でさえあります。メソッドの型パラメータは、引数の型として使用することに限定されず、関数の本体または本体のシグネチャに値がある場所であればどこでも使用できます。メソッドの型パラメータ`T`が、メソッドのシグネチャのパラメトリック型に対する型パラメータ`Vector{T}`として使用される例を次に示します。


```jldoctest
julia> myappend(v::Vector{T}, x::T) where {T} = [v..., x]
myappend (generic function with 1 method)

julia> myappend([1,2,3],4)
4-element Array{Int64,1}:
 1
 2
 3
 4

julia> myappend([1,2,3],2.5)
ERROR: MethodError: no method matching myappend(::Array{Int64,1}, ::Float64)
Closest candidates are:
  myappend(::Array{T,1}, !Matched::T) where T at none:1

julia> myappend([1.0,2.0,3.0],4.0)
4-element Array{Float64,1}:
 1.0
 2.0
 3.0
 4.0

julia> myappend([1.0,2.0,3.0],4)
ERROR: MethodError: no method matching myappend(::Array{Float64,1}, ::Int64)
Closest candidates are:
  myappend(::Array{T,1}, !Matched::T) where T at none:1
```


```@raw html
<!--
As you can see, the type of the appended element must match the element type of the vector it
is appended to, or else a [`MethodError`](@ref) is raised. In the following example, the method type parameter
`T` is used as the return value:
-->
```
ご覧のように、追加された要素の型は、追加されるベクトルの要素の型と一致しなければなりません。でなければ、[`MethodError`](@ref) が生成されます。
次の例では、メソッドの型パラメータ `T`が戻り値として使用されています。


```jldoctest
julia> mytypeof(x::T) where {T} = T
mytypeof (generic function with 1 method)

julia> mytypeof(1)
Int64

julia> mytypeof(1.0)
Float64
```


```@raw html
<!--
Just as you can put subtype constraints on type parameters in type declarations (see [Parametric Types](@ref)),
you can also constrain type parameters of methods:
-->
```
型宣言（[パラメトリック](@ref)型を参照）の型パラメータにサブタイプ制約を入れることができるのと同様に、メソッドの型パラメータも制約することができます。

```jldoctest
julia> same_type_numeric(x::T, y::T) where {T<:Number} = true
same_type_numeric (generic function with 1 method)

julia> same_type_numeric(x::Number, y::Number) = false
same_type_numeric (generic function with 2 methods)

julia> same_type_numeric(1, 2)
true

julia> same_type_numeric(1, 2.0)
false

julia> same_type_numeric(1.0, 2.0)
true

julia> same_type_numeric("foo", 2.0)
ERROR: MethodError: no method matching same_type_numeric(::String, ::Float64)
Closest candidates are:
  same_type_numeric(!Matched::T<:Number, ::T<:Number) where T<:Number at none:1
  same_type_numeric(!Matched::Number, ::Number) at none:1

julia> same_type_numeric("foo", "bar")
ERROR: MethodError: no method matching same_type_numeric(::String, ::String)

julia> same_type_numeric(Int32(1), Int64(2))
false
```


```@raw html
<!--
The `same_type_numeric` function behaves much like the `same_type` function defined above, but
is only defined for pairs of numbers.
-->
```
この`same_type_numeric`関数は、上で定義した関数`same_type`と非常によく似た動作をしますが、数値の組に対してのみ定義されています。

```@raw html
<!--
Parametric methods allow the same syntax as `where` expressions used to write types
(see [UnionAll Types](@ref)).
If there is only a single parameter, the enclosing curly braces (in `where {T}`) can be omitted,
but are often preferred for clarity.
Multiple parameters can be separated with commas, e.g. `where {T, S<:Real}`, or written using
nested `where`, e.g. `where S<:Real where T`.
-->
```

パラメトリックメソッドでは、型の作成に使用される`where`式と同じ構文を使用できます（[全合併型](@ref)を参照）。パラメータが1つしかない場合は、中括弧（`where {T}`）を省略することができますが、わかりやすいようにしばしばつけられます。複数のパラメータは、例えば、カンマで分離できたり(例`where {T, S<:Real}`)、またはネストされた`where`使用して記述されます。（例`where S<:Real where T`）


[](Rdefining Methods)

メソッドの再定義
------------------


```@raw html
<!--
When redefining a method or adding new methods,
it is important to realize that these changes don't take effect immediately.
This is key to Julia's ability to statically infer and compile code to run fast,
without the usual JIT tricks and overhead.
Indeed, any new method definition won't be visible to the current runtime environment,
including Tasks and Threads (and any previously defined `@generated` functions).
Let's start with an example to see what this means:
-->
```

```julia-repl
julia> function tryeval()
           @eval newfun() = 1
           newfun()
       end
tryeval (generic function with 1 method)

julia> tryeval()
ERROR: MethodError: no method matching newfun()
The applicable method may be too new: running in world age xxxx1, while current world is xxxx2.
Closest candidates are:
  newfun() at none:1 (method too new to be called from this world context.)
 in tryeval() at none:1
 ...

julia> newfun()
1
```


```@raw html
<!--
In this example, observe that the new definition for `newfun` has been created,
but can't be immediately called.
The new global is immediately visible to the `tryeval` function,
so you could write `return newfun` (without parentheses).
But neither you, nor any of your callers, nor the functions they call, or etc.
can call this new method definition!
-->
```


```@raw html
<!--
But there's an exception: future calls to `newfun` *from the REPL* work as expected,
being able to both see and call the new definition of `newfun`.

However, future calls to `tryeval` will continue to see the definition of `newfun` as it was
*at the previous statement at the REPL*, and thus before that call to `tryeval`.
-->
```


```@raw html
<!--
You may want to try this for yourself to see how it works.

The implementation of this behavior is a "world age counter".
This monotonically increasing value tracks each method definition operation.
This allows describing "the set of method definitions visible to a given runtime environment"
as a single number, or "world age".
It also allows comparing the methods available in two worlds just by comparing their ordinal value.
In the example above, we see that the "current world" (in which the method `newfun()` exists),
is one greater than the task-local "runtime world" that was fixed when the execution of `tryeval` started.
-->
```


```@raw html
<!--
Sometimes it is necessary to get around this (for example, if you are implementing the above REPL).
Fortunately, there is an easy solution: call the function using [`Base.invokelatest`](@ref):
-->
```

```jldoctest
julia> function tryeval2()
           @eval newfun2() = 2
           Base.invokelatest(newfun2)
       end
tryeval2 (generic function with 1 method)

julia> tryeval2()
2
```


```@raw html
<!--
Finally, let's take a look at some more complex examples where this rule comes into play.
Define a function `f(x)`, which initially has one method:
-->
```

```jldoctest redefinemethod
julia> f(x) = "original definition"
f (generic function with 1 method)
```


```@raw html
<!--
Start some other operations that use `f(x)`:
-->
```

```jldoctest redefinemethod
julia> g(x) = f(x)
g (generic function with 1 method)

julia> t = @async f(wait()); yield();
```


```@raw html
<!--
Now we add some new methods to `f(x)`:
-->
```

```jldoctest redefinemethod
julia> f(x::Int) = "definition for Int"
f (generic function with 2 methods)

julia> f(x::Type{Int}) = "definition for Type{Int}"
f (generic function with 3 methods)
```


```@raw html
<!--
Compare how these results differ:
-->
```

```jldoctest redefinemethod
julia> f(1)
"definition for Int"

julia> g(1)
"definition for Int"

julia> wait(schedule(t, 1))
"original definition"

julia> t = @async f(wait()); yield();

julia> wait(schedule(t, 1))
"definition for Int"
```

[](## Parametrically-constrained Varargs methods)
## パラメトリック制約付き可変引数メソッド


```@raw html
<!--
Function parameters can also be used to constrain the number of arguments that may be supplied
to a "varargs" function ([可変引数関数](@ref)).  The notation `Vararg{T,N}` is used to indicate
such a constraint.  For example:
-->
```

```jldoctest
julia> bar(a,b,x::Vararg{Any,2}) = (a,b,x)
bar (generic function with 1 method)

julia> bar(1,2,3)
ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64)
Closest candidates are:
  bar(::Any, ::Any, ::Any, !Matched::Any) at none:1

julia> bar(1,2,3,4)
(1, 2, (3, 4))

julia> bar(1,2,3,4,5)
ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64, ::Int64, ::Int64)
Closest candidates are:
  bar(::Any, ::Any, ::Any, ::Any) at none:1
```


```@raw html
<!--
More usefully, it is possible to constrain varargs methods by a parameter. For example:
-->
```

```julia
function getindex(A::AbstractArray{T,N}, indexes::Vararg{Number,N}) where {T,N}
```

would be called only when the number of `indexes` matches the dimensionality of the array.

[](## Note on Optional and keyword Arguments)
## 省略可能引数とキーワード引数の注意点


```@raw html
<!--
As mentioned briefly in [関数](@ref man-functions), optional arguments are implemented as syntax for multiple
method definitions. For example, this definition:
-->
```

```julia
f(a=1,b=2) = a+2b
```


```@raw html
<!--
translates to the following three methods:
-->
```

```julia
f(a,b) = a+2b
f(a) = f(a,2)
f() = f(1,2)
```


```@raw html
<!--
This means that calling `f()` is equivalent to calling `f(1,2)`. In this case the result is `5`,
because `f(1,2)` invokes the first method of `f` above. However, this need not always be the case.
If you define a fourth method that is more specialized for integers:
-->
```

```julia
f(a::Int,b::Int) = a-2b
```


```@raw html
<!--
then the result of both `f()` and `f(1,2)` is `-3`. In other words, optional arguments are tied
to a function, not to any specific method of that function. It depends on the types of the optional
arguments which method is invoked. When optional arguments are defined in terms of a global variable,
the type of the optional argument may even change at run-time.
-->
```


```@raw html
<!--
Keyword arguments behave quite differently from ordinary positional arguments. In particular,
they do not participate in method dispatch. Methods are dispatched based only on positional arguments,
with keyword arguments processed after the matching method is identified.
-->
```

[](## Function-like objects)
## 関数のようなオブジェクト


```@raw html
<!--
Methods are associated with types, so it is possible to make any arbitrary Julia object "callable"
by adding methods to its type. (Such "callable" objects are sometimes called "functors.")

For example, you can define a type that stores the coefficients of a polynomial, but behaves like
a function evaluating the polynomial:
-->
```

```jldoctest polynomial
julia> struct Polynomial{R}
           coeffs::Vector{R}
       end

julia> function (p::Polynomial)(x)
           v = p.coeffs[end]
           for i = (length(p.coeffs)-1):-1:1
               v = v*x + p.coeffs[i]
           end
           return v
       end
```


```@raw html
<!--
Notice that the function is specified by type instead of by name. In the function body, `p` will
refer to the object that was called. A `Polynomial` can be used as follows:
-->
```

```jldoctest polynomial
julia> p = Polynomial([1,10,100])
Polynomial{Int64}([1, 10, 100])

julia> p(3)
931
```


```@raw html
<!--
This mechanism is also the key to how type constructors and closures (inner functions that refer
to their surrounding environment) work in Julia, discussed [later in the manual](@ref constructors-and-conversion).
-->
```

[](## Empty generic functions)
## 空のジェネリック関数


```@raw html
<!--
Occasionally it is useful to introduce a generic function without yet adding methods. This can
be used to separate interface definitions from implementations. It might also be done for the
purpose of documentation or code readability. The syntax for this is an empty `function` block
without a tuple of arguments:
-->
```

```julia
function emptyfunc
end
```

[](## [Method design and the avoidance of ambiguities](@id man-method-design-ambiguities))
## [メソッドの設計と曖昧さの回避](@id man-method-design-ambiguities)


```@raw html
<!--
Julia's method polymorphism is one of its most powerful features, yet
exploiting this power can pose design challenges.  In particular, in
more complex method hierarchies it is not uncommon for
[ambiguities](@ref man-ambiguities) to arise.
-->
```


```@raw html
<!--
Above, it was pointed out that one can resolve ambiguities like
-->
```

```julia
f(x, y::Int) = 1
f(x::Int, y) = 2
```


```@raw html
<!--
by defining a method
-->
```

```julia
f(x::Int, y::Int) = 3
```


```@raw html
<!--
This is often the right strategy; however, there are circumstances
where following this advice blindly can be counterproductive. In
particular, the more methods a generic function has, the more
possibilities there are for ambiguities. When your method hierarchies
get more complicated than this simple example, it can be worth your
while to think carefully about alternative strategies.

Below we discuss particular challenges and some alternative ways to resolve such issues.
-->
```

[](### Tuple and NTuple arguments)
### タプルとNタプル引数


```@raw html
<!--
`Tuple` (and `NTuple`) arguments present special challenges. For example,
-->
```

```julia
f(x::NTuple{N,Int}) where {N} = 1
f(x::NTuple{N,Float64}) where {N} = 2
```


```@raw html
<!--
are ambiguous because of the possibility that `N == 0`: there are no
elements to determine whether the `Int` or `Float64` variant should be
called. To resolve the ambiguity, one approach is define a method for
the empty tuple:
-->
```

```julia
f(x::Tuple{}) = 3
```


```@raw html
<!--
Alternatively, for all methods but one you can insist that there is at
least one element in the tuple:
-->
```

```julia
f(x::NTuple{N,Int}) where {N} = 1           # this is the fallback
f(x::Tuple{Float64, Vararg{Float64}}) = 2   # this requires at least one Float64
```

[](### [Orthogonalize your design](@id man-methods-orthogonalize))
### [設計の直交化](@id man-methods-orthogonalize)


```@raw html
<!--
When you might be tempted to dispatch on two or more arguments,
consider whether a "wrapper" function might make for a simpler
design. For example, instead of writing multiple variants:
-->
```

```julia
f(x::A, y::A) = ...
f(x::A, y::B) = ...
f(x::B, y::A) = ...
f(x::B, y::B) = ...
```


```@raw html
<!--
you might consider defining
-->
```

```julia
f(x::A, y::A) = ...
f(x, y) = f(g(x), g(y))
```


```@raw html
<!--
where `g` converts the argument to type `A`. This is a very specific
example of the more general principle of
[orthogonal design](https://en.wikipedia.org/wiki/Orthogonality_(programming)),
in which separate concepts are assigned to separate methods. Here, `g`
will most likely need a fallback definition
-->
```

```julia
g(x::A) = x
```


```@raw html
<!--
A related strategy exploits `promote` to bring `x` and `y` to a common
type:
-->
```

```julia
f(x::T, y::T) where {T} = ...
f(x, y) = f(promote(x, y)...)
```


```@raw html
<!--
One risk with this design is the possibility that if there is no
suitable promotion method converting `x` and `y` to the same type, the
second method will recurse on itself infinitely and trigger a stack
overflow. The non-exported function `Base.promote_noncircular` can be
used as an alternative; when promotion fails it will still throw an
error, but one that fails faster with a more specific error message.
-->
```

[](### Dispatch on one argument at a time)
### 一度に一引数のディスパッチ


```@raw html
<!--
If you need to dispatch on  multiplearguments, and there are many
fallbacks with too many combinations to make it practical to define
all possible variants, then consider introducing a "name cascade"
where (for example) you dispatch on the first argument and then call
an internal method:
-->
```

```julia
f(x::A, y) = _fA(x, y)
f(x::B, y) = _fB(x, y)
```


```@raw html
<!--
Then the internal methods `_fA` and `_fB` can dispatch on `y` without
concern about ambiguities with each other with respect to `x`.
-->
```


```@raw html
<!--
Be aware that this strategy has at least one major disadvantage: in
many cases, it is not possible for users to further customize the
behavior of `f` by defining further specializations of your exported
function `f`. Instead, they have to define specializations for your
internal methods `_fA` and `_fB`, and this blurs the lines between
exported and internal methods.
-->
```

[](### Abstract containers and element types)
### 抽象コンテナと要素の型


```@raw html
<!--
Where possible, try to avoid defining methods that dispatch on
specific element types of abstract containers. For example,
-->
```

```julia
-(A::AbstractArray{T}, b::Date) where {T<:Date}
```


```@raw html
<!--
generates ambiguities for anyone who defines a method
-->
```

```julia
-(A::MyArrayType{T}, b::T) where {T}
```


```@raw html
<!--
The best approach is to avoid defining *either* of these methods:
instead, rely on a generic method `-(A::AbstractArray, b)` and make
sure this method is implemented with generic calls (like `similar` and
`-`) that do the right thing for each container type and element type
*separately*. This is just a more complex variant of the advice to
[orthogonalize](@ref man-methods-orthogonalize) your methods.
-->
```


```@raw html
<!--
When this approach is not possible, it may be worth starting a
discussion with other developers about resolving the ambiguity; just
because one method was defined first does not necessarily mean that it
can't be modified or eliminated.  As a last resort, one developer can
define the "band-aid" method
-->
```

```julia
-(A::MyArrayType{T}, b::Date) where {T<:Date} = ...
```


```@raw html
<!--
that resolves the ambiguity by brute force.
-->
```

[](### Complex method "cascades" with default arguments)
### Complex method "cascades" with default arguments


```@raw html
<!--
If you are defining a method "cascade" that supplies defaults, be
careful about dropping any arguments that correspond to potential
defaults. For example, suppose you're writing a digital filtering
algorithm and you have a method that handles the edges of the signal
by applying padding:
-->
```

```julia
function myfilter(A, kernel, ::Replicate)
    Apadded = replicate_edges(A, size(kernel))
    myfilter(Apadded, kernel)  # now perform the "real" computation
end
```


```@raw html
<!--
This will run afoul of a method that supplies default padding:
-->
```

```julia
myfilter(A, kernel) = myfilter(A, kernel, Replicate()) # replicate the edge by default
```


```@raw html
<!--
Together, these two methods generate an infinite recursion with `A` constantly growing bigger.

The better design would be to define your call hierarchy like this:
-->
```

```julia
struct NoPad end  # indicate that no padding is desired, or that it's already applied

myfilter(A, kernel) = myfilter(A, kernel, Replicate())  # default boundary conditions

function myfilter(A, kernel, ::Replicate)
    Apadded = replicate_edges(A, size(kernel))
    myfilter(Apadded, kernel, NoPad())  # indicate the new boundary conditions
end

# other padding methods go here

function myfilter(A, kernel, ::NoPad)
    # Here's the "real" implementation of the core computation
end
```


```@raw html
<!--
`NoPad` is supplied in the same argument position as any other kind of
padding, so it keeps the dispatch hierarchy well organized and with
reduced likelihood of ambiguities. Moreover, it extends the "public"
`myfilter` interface: a user who wants to control the padding
explicitly can call the `NoPad` variant directly.
-->
```


```@raw html
<!--
[^Clarke61]: Arthur C. Clarke, *Profiles of the Future* (1961): Clarke's Third Law.
-->
```
