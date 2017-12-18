[](# Style Guide)

# スタイルガイド

```@raw html
<!--
The following sections explain a few aspects of idiomatic Julia coding style. None of these rules
are absolute; they are only suggestions to help familiarize you with the language and to help
you choose among alternative designs.
-->
```

このセクションでは、Juliaの慣習的、特徴的ないくつかのコーディングスタイルについて説明します。これから説明するルールを厳守する必要はありません。
コーディングスタイルはあくまでも多くのコードの書き方から一つを選び取ること、そしてJuliaに慣れることを助けるものにすぎません。

[](## Write functions, not just scripts)

## 関数を使おう

```@raw html
<!--
Writing code as a series of steps at the top level is a quick way to get started solving a problem,
but you should try to divide a program into functions as soon as possible. Functions are more
reusable and testable, and clarify what steps are being done and what their inputs and outputs
are. Furthermore, code inside functions tends to run much faster than top level code, due to how
Julia's compiler works.
-->
```

コードをそのままグローバルスコープに書く方法では迅速に目標に取り掛かることができますが、
一旦落ち着いてソースコードを関数に小分けにしてみると良いでしょう。関数はより再利用しやすくコードテストにも向いていて、
かつどの処理が行われ、入出力は何なのかというのも明確にします。加えて、Juliaのコンパイラの働きにより、関数に組み込まれたコードは剥き出しのコードよりも
速く実行される傾向にあります。

```@raw html
<!--
It is also worth emphasizing that functions should take arguments, instead of operating directly
on global variables (aside from constants like [`pi`](@ref)).
-->
```

関数は基本的に引数を取るので、グローバル変数（[`pi`](@ref)などの定数は別です）を使う機会が減ることもメリットです。

[](## Avoid writing overly-specific types)
## 型を限定しすぎないようにしよう

```@raw html
<!--
Code should be as generic as possible. Instead of writing:
-->
```

コードはなるべく柔軟であるべきです。下のように書くのではなく:

```julia
convert(Complex{Float64}, x)
```

```@raw html
<!--
it's better to use available generic functions:
-->
```

利用可能な汎化関数を使うと良いでしょう。

```julia
complex(float(x))
```

```@raw html
<!--
The second version will convert `x` to an appropriate type, instead of always the same type.
-->
```

上の2番目の例のコードは`x`を然るべき型に変換してくれます。

```@raw html
<!--
This style point is especially relevant to function arguments. For example, don't declare an argument
to be of type `Int` or [`Int32`](@ref) if it really could be any integer, expressed with the abstract
type [`Integer`](@ref). In fact, in many cases you can omit the argument type altogether,
unless it is needed to disambiguate from other method definitions, since a
[`MethodError`](@ref) will be thrown anyway if a type is passed that does not support any
of the requisite operations. (This is known as
[duck typing](https://en.wikipedia.org/wiki/Duck_typing).)
-->
```

この全体的であるべきという思想は、特に関数の引数にも当てはまります。例えばある引数がどんな整数にもなり得る場合、
その引数の型は限定的な`Int`ないし[`Int32`](@ref)型ではなく、より広範的な[`Integer`](@ref)抽象型で宣言してください。
実のところ他のメゾッドの定義などで引数の型を厳密に指定したい時以外は引数の型は指定しなくても良い場合が多いです。
というのも、渡されたタイプが必要な演算に対応していない場合は[`MethodError`](@ref)が投げられるからです。
（これは[ダック・タイピング](https://ja.wikipedia.org/wiki/%E3%83%80%E3%83%83%E3%82%AF%E3%83%BB%E3%82%BF%E3%82%A4%E3%83%94%E3%83%B3%E3%82%B0)として知られています。）

```@raw html
<!--
For example, consider the following definitions of a function `addone` that returns one plus its
argument:
-->
```

例として次に示す、引数に1を足した数を返す関数`addone`の定義を見てください:

```julia
addone(x::Int) = x + 1                 # 引数がIntの時のみ動く
addone(x::Integer) = x + oneunit(x)    # 引数が抽象型Integerに属する型の時のみ動く
addone(x::Number) = x + oneunit(x)     # 引数が抽象型Numberに属する型の時のみ動く
addone(x) = x + oneunit(x)             # 引数が+とoneunitをサポートしている型の時動く
```

```@raw html
<!--
The last definition of `addone` handles any type supporting [`oneunit`](@ref) (which returns 1 in
the same type as `x`, which avoids unwanted type promotion) and the [`+`](@ref) function with
those arguments. The key thing to realize is that there is *no performance penalty* to defining
*only* the general `addone(x) = x + oneunit(x)`, because Julia will automatically compile specialized
versions as needed. For example, the first time you call `addone(12)`, Julia will automatically
compile a specialized `addone` function for `x::Int` arguments, with the call to `oneunit`
replaced by its inlined value `1`. Therefore, the first three definitions of `addone` above are
completely redundant with the fourth definition.
-->
```

最後の`addone`の定義は[`oneunit`](@ref)（`x`と（望まない型プロモーションを避けるために）同じ型での1を返す）
と[`+`](@ref)関数をサポートする全ての型をよしなに扱ってくれます。ここで知っておくべきことは、全体的な`addone(x) = x + oneunit(x)`だけ定義した時と
一つの型に特化した定義の`addone`では実行速度に差は **全く** 出ないということです。なぜならJuliaは必要な時に特定化した関数をコンパイルするからです。
例えば、初めて`addone(12)`を呼び出すと、Juliaは`addone`関数を`x::Int`の引数用にコンパイルします。そのため、上三つの定義は四番目の定義と完全に同じと言えます。

[](## Handle excess argument diversity in the caller)

## 過剰な広範性を排除しよう

```@raw html
<!--
Instead of:
-->
```

次のようにではなく:

```julia
function foo(x, y)
    x = Int(x); y = Int(y)
    ...
end
foo(x, y)
```

```@raw html
<!--
use:
-->
```

このように書きましょう:

```julia
function foo(x::Int, y::Int)
    ...
end
foo(Int(x), Int(y))
```

```@raw html
<!--
This is better style because `foo` does not really accept numbers of all types; it really needs
`Int` s.
-->
```

処理を見てみると、`foo`は全ての型の数字を受け付ける訳ではないので、
もともと引数を`Int`型に絞っている上のコードはさらに上のコードよりも良いと言えます。

```@raw html
<!--
One issue here is that if a function inherently requires integers, it might be better to force
the caller to decide how non-integers should be converted (e.g. floor or ceiling). Another issue
is that declaring more specific types leaves more "space" for future method definitions.
-->
```

ここでいくつか注意点があります。一つはもし関数が本質的に整数を要求する場合は、関数内で非整数に対する処理（切り上げや切り捨て）を明記するべきかもしれないこと。
もう一つはより狭い型の指定はメゾッドの定義に空白を齎してしまうということです。

[](## Append `!` to names of functions that modify their arguments)

## 引数に変更を加える関数には最後に`!`を添えよう

```@raw html
<!--
Instead of:
-->
```

こうする代わりに:

```julia
function double(a::AbstractArray{<:Number})
    for i = 1:endof(a)
        a[i] *= 2
    end
    return a
end
```

```@raw html
<!--
use:
-->
```

このように書くと良いでしょう:

```julia
function double!(a::AbstractArray{<:Number})
    for i = 1:endof(a)
        a[i] *= 2
    end
    return a
end
```

```@raw html
<!--
The Julia standard library uses this convention throughout and contains examples of functions
with both copying and modifying forms (e.g., [`sort()`](@ref) and [`sort!()`](@ref)), and others
which are just modifying (e.g., [`push!()`](@ref), [`pop!()`](@ref), [`splice!()`](@ref)).  It
is typical for such functions to also return the modified array for convenience.
-->
```

Juliaのスタンダードライブラリでは全体を通してこの慣習に従っていて、コピーするだけの関数と変更を加える関数の実例もいくつかあります
。[`sort()`](@ref)と[`sort!()`](@ref)、変更を加える関数の例として[`push!()`](@ref)や[`pop!()`](@ref)、[`splice!()`](@ref)などがあります。
変更された配列を返すような関数にもしばしばこの慣習が適用されます。

[](## Avoid strange type `Union`s)

## 違いすぎる型同士の組み合わせを`Union`にしないようにしよう

```@raw html
<!--
Types such as `Union{Function,AbstractString}` are often a sign that some design could be cleaner.
-->
```

`Union{Function,AbstractString}`のような型は、設計に改善点があるサインかもしれません。

[](## Avoid type Unions in fields)

## 合拼型をフィールドに使うのは避けよう

```@raw html
<!--
When creating a type such as:
-->
```

下のような型を作る時:

```julia
mutable struct MyType
    ...
    x::Union{Void,T}
end
```

```@raw html
<!--
ask whether the option for `x` to be `nothing` (of type `Void`) is really necessary. Here are
some alternatives to consider:

  * Find a safe default value to initialize `x` with
  * Introduce another type that lacks `x`
  * If there are many fields like `x`, store them in a dictionary
  * Determine whether there is a simple rule for when `x` is `nothing`. For example, often the field
    will start as `nothing` but get initialized at some well-defined point. In that case, consider
    leaving it undefined at first.
  * If `x` really needs to hold no value at some times, define it as `::Nullable{T}` instead, as this
    guarantees type-stability in the code accessing this field (see [Nullable types](@ref man-nullable-types)).
-->
```

`x`が`nothing`（`Void`に属する）になる必要が本当にあるかもう一度自問してみてください。いくつかの検討すべき代替手段を挙げます:

    * `x`を初期化する安全なデフォルト値を探す
    * `x`を属させるような別の型を考える
    * `x`に似たフィールドが他にあるなら、まとめて辞書に入れる
    * どのような条件下で`x`が`nothing`になるか決める。例えば、しばしばフィールドは最初`nothing`ですが、何かしらの値で初期化されるなど。
    このケースだと最初は未定義のままにしておくのも手です。
    * もし`x`が何の値も持たないべき時があるなら、それを`::Nullable{T}`と定義すればフィールドにアクセスする時にタイプの安定性が保証されます。
    （詳しくは[Null許容型](@ref man-nullable-types)を参照してください。）

[](## Avoid elaborate container types)

```@raw html
<!--
It is usually not much help to construct arrays like the following:
-->
```

次のように配列を構築するのは推奨されません:

```julia
a = Array{Union{Int,AbstractString,Tuple,Array}}(n)
```

```@raw html
<!--
In this case `Array{Any}(n)` is better. It is also more helpful to the compiler to annotate specific
uses (e.g. `a[i]::Int`) than to try to pack many alternatives into one type.
-->
```

この場合では`Array{Any}(n)`と書いた方が良いでしょう。一つの合拼型にたくさんの型を詰め込むよりも、
コンパイラに特定の部分だけアノテートする（`a[i]::Int`など）のも良いでしょう。

[](## Use naming conventions consistent with Julia's `base/`)

## Juliaの`base/`に習った名前付けをしよう

```@raw html
<!--
  * modules and type names use capitalization and camel case: `module SparseArrays`, `struct UnitRange`.
  * functions are lowercase ([`maximum()`](@ref), [`convert()`](@ref)) and, when readable, with multiple
    words squashed together ([`isequal()`](@ref), [`haskey()`](@ref)). When necessary, use underscores
    as word separators. Underscores are also used to indicate a combination of concepts ([`remotecall_fetch()`](@ref)
    as a more efficient implementation of `fetch(remotecall(...))`) or as modifiers ([`sum_kbn()`](@ref)).
  * conciseness is valued, but avoid abbreviation ([`indexin()`](@ref) rather than `indxin()`) as
    it becomes difficult to remember whether and how particular words are abbreviated.
-->
```

  * モジュールと型の名前にはキャピタライゼーションとキャメルケースを使いましょう。例: `module SparseArrays`、`struct UnitRange`。
  * 関数はローワーケースを使いましょう（[`maximum()`](@ref)、[`convert()`](@ref)）。
  読みやすさが損なわれない時は、複数の単語を連結しましょう（[`isequal()`](@ref)、[`haskey()`](@ref)）。
  損なわれる時は、アンダーバーを単語区切りに使ってください。アンダーバーは機能、概念の組み合わせであることを示す時にも使われます
  （[`remotecall_fetch()`](@ref)）。

```@raw html
<!--
If a function name requires multiple words, consider whether it might represent more than one
concept and might be better split into pieces.
-->
```

関数名に複数の単語を使わなければいけないような時は、少なくとも一つのコンセプトを表現しているかと一つ一つの単語の関数に落とし込めないかを検討してみましょう。

[](## Don't overuse try-catch)

## try-catchを多用しないようにしよう

```@raw html
<!--
It is better to avoid errors than to rely on catching them.
-->
```

エラーをキャッチすることで対処するよりも、エラー自体を回避した方が良いでしょう。

[](## Don't parenthesize conditions)

## 条件式を丸括弧で囲まないようにしよう

```@raw html
<!--
Julia doesn't require parens around conditions in `if` and `while`. Write:
-->
```

Juliaでは、`if`や`while`で用いる条件式を丸括弧で囲む必要はありません。下の例にように書きましょう：

```julia
if a == b
```

```@raw html
<!--
instead of:
-->
```

下のように書くことはやめましょう：

```julia
if (a == b)
```

[](## Don't overuse `...`)

## `...`を使いすぎないようにしよう

```@raw html
<!--
Splicing function arguments can be addictive. Instead of `[a..., b...]`, use simply `[a; b]`,
which already concatenates arrays. [`collect(a)`](@ref) is better than `[a...]`, but since `a`
is already iterable it is often even better to leave it alone, and not convert it to an array.
-->
```

[](## Don't use unnecessary static parameters)

```@raw html
<!--
A function signature:
-->
```

```julia
foo(x::T) where {T<:Real} = ...
```

```@raw html
<!--
should be written as:
-->
```

```julia
foo(x::Real) = ...
```

```@raw html
<!--
instead, especially if `T` is not used in the function body. Even if `T` is used, it can be replaced
with [`typeof(x)`](@ref) if convenient. There is no performance difference. Note that this is
not a general caution against static parameters, just against uses where they are not needed.
-->
```

```@raw html
<!--
Note also that container types, specifically may need type parameters in function calls. See the
FAQ [Avoid fields with abstract containers](@ref) for more information.
-->
```

[](## Avoid confusion about whether something is an instance or a type)

```@raw html
<!--
Sets of definitions like the following are confusing:
-->
```

```julia
foo(::Type{MyType}) = ...
foo(::MyType) = foo(MyType)
```

```@raw html
<!--
Decide whether the concept in question will be written as `MyType` or `MyType()`, and stick to
it.
-->
```

```@raw html
<!--
The preferred style is to use instances by default, and only add methods involving `Type{MyType}`
later if they become necessary to solve some problem.
-->
```

```@raw html
<!--
If a type is effectively an enumeration, it should be defined as a single (ideally immutable struct or primitive)
type, with the enumeration values being instances of it. Constructors and conversions can check
whether values are valid. This design is preferred over making the enumeration an abstract type,
with the "values" as subtypes.
-->
```

[](## Don't overuse macros)

```@raw html
<!--
Be aware of when a macro could really be a function instead.
-->
```

```@raw html
<!--
Calling [`eval()`](@ref) inside a macro is a particularly dangerous warning sign; it means the
macro will only work when called at the top level. If such a macro is written as a function instead,
it will naturally have access to the run-time values it needs.
-->
```

[](## Don't expose unsafe operations at the interface level)

```@raw html
<!--
If you have a type that uses a native pointer:
-->
```

```julia
mutable struct NativeType
    p::Ptr{UInt8}
    ...
end
```

```@raw html
<!--
don't write definitions like the following:
-->
```

```julia
getindex(x::NativeType, i) = unsafe_load(x.p, i)
```

```@raw html
<!--
The problem is that users of this type can write `x[i]` without realizing that the operation is
unsafe, and then be susceptible to memory bugs.
-->
```

```@raw html
<!--
Such a function should either check the operation to ensure it is safe, or have `unsafe` somewhere
in its name to alert callers.
-->
```

[](## Don't overload methods of base container types)

```@raw html
<!--
It is possible to write definitions like the following:
-->
```

```julia
show(io::IO, v::Vector{MyType}) = ...
```

```@raw html
<!--
This would provide custom showing of vectors with a specific new element type. While tempting,
this should be avoided. The trouble is that users will expect a well-known type like `Vector()`
to behave in a certain way, and overly customizing its behavior can make it harder to work with.
-->
```

[](## Avoid type piracy)

```@raw html
<!--
"Type piracy" refers to the practice of extending or redefining methods in Base
or other packages on types that you have not defined. In some cases, you can get away with
type piracy with little ill effect. In extreme cases, however, you can even crash Julia
(e.g. if your method extension or redefinition causes invalid input to be passed to a
`ccall`). Type piracy can complicate reasoning about code, and may introduce
incompatibilities that are hard to predict and diagnose.
-->
```

```@raw html
<!--
As an example, suppose you wanted to define multiplication on symbols in a module:
-->
```

```julia
module A
import Base.*
*(x::Symbol, y::Symbol) = Symbol(x,y)
end
```

```@raw html
<!--
The problem is that now any other module that uses `Base.*` will also see this definition.
Since `Symbol` is defined in Base and is used by other modules, this can change the
behavior of unrelated code unexpectedly. There are several alternatives here, including
using a different function name, or wrapping the `Symbol`s in another type that you define.
-->
```

```@raw html
<!--
Sometimes, coupled packages may engage in type piracy to separate features from definitions,
especially when the packages were designed by collaborating authors, and when the
definitions are reusable. For example, one package might provide some types useful for
working with colors; another package could define methods for those types that enable
conversions between color spaces. Another example might be a package that acts as a thin
wrapper for some C code, which another package might then pirate to implement a
higher-level, Julia-friendly API.
-->
```

[](## Be careful with type equality)

```@raw html
<!--
You generally want to use [`isa()`](@ref) and `<:` ([`issubtype()`](@ref)) for testing types,
not `==`. Checking types for exact equality typically only makes sense when comparing to a known
concrete type (e.g. `T == Float64`), or if you *really, really* know what you're doing.
-->
```

[](## Do not write `x->f(x)`)

```@raw html
<!--
Since higher-order functions are often called with anonymous functions, it is easy to conclude
that this is desirable or even necessary. But any function can be passed directly, without being
"wrapped" in an anonymous function. Instead of writing `map(x->f(x), a)`, write [`map(f, a)`](@ref).
-->
```

[](## Avoid using floats for numeric literals in generic code when possible)

```@raw html
<!--
If you write generic code which handles numbers, and which can be expected to run with many different
numeric type arguments, try using literals of a numeric type that will affect the arguments as
little as possible through promotion.
-->
```

```@raw html
<!--
For example,
-->
```

```jldoctest
julia> f(x) = 2.0 * x
f (generic function with 1 method)

julia> f(1//2)
1.0

julia> f(1/2)
1.0

julia> f(1)
2.0
```

```@raw html
<!--
while
-->
```

```jldoctest
julia> g(x) = 2 * x
g (generic function with 1 method)

julia> g(1//2)
1//1

julia> g(1/2)
1.0

julia> g(1)
2
```

```@raw html
<!--
As you can see, the second version, where we used an `Int` literal, preserved the type of the
input argument, while the first didn't. This is because e.g. `promote_type(Int, Float64) == Float64`,
and promotion happens with the multiplication. Similarly, [`Rational`](@ref) literals are less type disruptive
than [`Float64`](@ref) literals, but more disruptive than `Int`s:
-->
```

```jldoctest
julia> h(x) = 2//1 * x
h (generic function with 1 method)

julia> h(1//2)
1//1

julia> h(1/2)
1.0

julia> h(1)
2//1
```

```@raw html
<!--
Thus, use `Int` literals when possible, with `Rational{Int}` for literal non-integer numbers,
in order to make it easier to use your code.
-->
```
