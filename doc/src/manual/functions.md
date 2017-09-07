# [Functions](@id man-functions)
# 関数

```@raw html
<!--
In Julia, a function is an object that maps a tuple of argument values to a return value. Julia
functions are not pure mathematical functions, in the sense that functions can alter and be affected
by the global state of the program. The basic syntax for defining functions in Julia is:
-->
```

Juliaにおいて、関数は、引数の値のタプルを受け取り、戻り値を返すオブジェクトです。
関数は、プログラムのグローバルな状態を変更したり、逆に、グローバルな状態に、挙動が影響を受けたりします。
そういう意味で、純粋に数学的ではありません。
Juliaで関数を定義する基本構文は次のとおりです。

```jldoctest
julia> function f(x,y)
           x + y
       end
f (generic function with 1 method)
```

```@raw html
<!--
There is a second, more terse syntax for defining a function in Julia. The traditional function
declaration syntax demonstrated above is equivalent to the following compact "assignment form":
-->
```

もっと簡潔に関数を定義する第二の構文もあります。上に示したような、従来の関数を宣言する構文と、つぎのような、コンパクトな「代入方式」とは同等です。




```jldoctest fofxy
julia> f(x,y) = x + y
f (generic function with 1 method)
```

```@raw html
<!--
In the assignment form, the body of the function must be a single expression, although it can
be a compound expression (see [Compound Expressions](@ref man-compound-expressions)). Short, simple function definitions
are common in Julia. The short function syntax is accordingly quite idiomatic, considerably reducing
both typing and visual noise.
-->
```


代入方式では、関数の本体は単一の式でなければなりませんが、その式は複合式( [複合式](@ref man-compound-expressions)を参照)でもかまいません。
こうした短くて簡単な関数定義は、Juliaではよく使われます。短い関数構文を多用するほど、文字数が大幅に少なくなり、見た目もすっきりします。

```@raw html
<!--
A function is called using the traditional parenthesis syntax:
-->
```

関数は、従来の括弧を使う構文で呼び出されます。


```jldoctest fofxy
julia> f(2,3)
5
```

```@raw html
<!--
Without parentheses, the expression `f` refers to the function object, and can be passed around
like any value:
-->
```

括弧をつけない式`f`は、関数オブジェクトを表し、他の値と同じように受け渡しができます。



```jldoctest fofxy
julia> g = f;

julia> g(2,3)
5
```

```@raw html
<!--
As with variables, Unicode can also be used for function names:
-->
```

変数名と同様に関数名にもUnicodeを使用できます。

```jldoctest
julia> ∑(x,y) = x + y
∑ (generic function with 1 method)

julia> ∑(2, 3)
5
```

## Argument Passing Behavior
## 引数渡しの振る舞い

```@raw html
<!--
Julia function arguments follow a convention sometimes called "pass-by-sharing", which means that
values are not copied when they are passed to functions. Function arguments themselves act as
new variable *bindings* (new locations that can refer to values), but the values they refer to
are identical to the passed values. Modifications to mutable values (such as `Array`s) made within
a function will be visible to the caller. This is the same behavior found in Scheme, most Lisps,
Python, Ruby and Perl, among other dynamic languages.
-->
```

Juliaの関数の引数は、時折「共有渡し」と呼ばれる方式に従っていて、関数へ引き渡す際に、値の複写を行いません。
関数の引数は、新しい変数を *束縛* (新しい値を参照)したように振る舞いますが、参照先は渡された値と同一です。
関数内で(配列のような)可変な値を変更すると、呼び出す側にも影響します。
こうした挙動は,Scheme,Lispの大部分,Python,Ruby,Perl,その他の動的言語と同様です。


　

## The `return` Keyword
## `return`　キーワード

```@raw html
<!--
The value returned by a function is the value of the last expression evaluated, which, by default,
is the last expression in the body of the function definition. In the example function, `f`, from
the previous section this is the value of the expression `x + y`. As in C and most other imperative
or functional languages, the `return` keyword causes a function to return immediately, providing
an expression whose value is returned:
-->
```

関数の返す値は、最後に評価された式の値で、デフォルトでは関数定義の本体内で、最後尾の式の値になります。
例えば、前節に出てきた `f` という関数の戻り値は 、式 `x + y` の値です。
C言語ほかの命令型言語や、関数型言語と同様に、`return` キーワードを使うと、指定された式の値がすぐに返されます。


```julia
function g(x,y)
    return x * y
    x + y
end
```

```@raw html
<!--
Since function definitions can be entered into interactive sessions, it is easy to compare these
definitions:
-->
```

対話セッションでも関数の定義を入力できるので、簡単に定義の比較ができます。


```jldoctest
julia> f(x,y) = x + y
f (generic function with 1 method)

julia> function g(x,y)
           return x * y
           x + y
       end
g (generic function with 1 method)

julia> f(2,3)
5

julia> g(2,3)
6
```

```@raw html
<!--
Of course, in a purely linear function body like `g`, the usage of `return` is pointless since
the expression `x + y` is never evaluated and we could simply make `x * y` the last expression
in the function and omit the `return`. In conjunction with other control flow, however, `return`
is of real use. Here, for example, is a function that  computes the hypotenuse length of a right
triangle with sides of length `x` and `y`, avoiding overflow:
-->
```

もちろん、`g` のように直線的に順次処理を行う関数で、`return` を使っても仕方がありません。
というのも、`x + y` という式は決して評価されないので、単に `x * y` という式で終わって、`return` を削ればいいからです。
けれども、実行順序を制御する場合、`return`は役に立ちます。
例えば、直角三角形の斜辺を、その他の二辺 `x` ,`y`から、桁あふれをさけながら、計算する場合を考えてみましょう。



```jldoctest
julia> function hypot(x,y)
           x = abs(x)
           y = abs(y)
           if x > y
               r = y/x
               return x*sqrt(1+r*r)
           end
           if y == 0
               return zero(x)
           end
           r = x/y
           return y*sqrt(1+r*r)
       end
hypot (generic function with 1 method)

julia> hypot(3, 4)
5.0
```

```@raw html
<!--
There are three possible points of return from this function, returning the values of three different
expressions, depending on the values of `x` and `y`. The `return` on the last line could be omitted
since it is the last expression.
-->
```

この関数には、値を返せる場所が３か所ありますが、３つの異なる式のどの値が返されるのかは、`x` と `y` の
値に応じて変化します。
最終行の `return` は最後尾の式なので省略可能です。


## Operators Are Functions
## 演算子は関数

```@raw html
<!--
In Julia, most operators are just functions with support for special syntax. (The exceptions are
operators with special evaluation semantics like `&&` and `||`. These operators cannot be functions
since [Short-Circuit Evaluation](@ref) requires that their operands are not evaluated before evaluation
of the operator.) Accordingly, you can also apply them using parenthesized argument lists, just
as you would any other function:
-->
```

Juliaでは、ほとんどの演算子は、特殊な構文をとっていても、単なる関数です。
（例外は評価戦略が特殊な演算子で、`&&`や`||`などがあります。これらの演算子は関数たりえません。というのは、[短絡評価](@ref)は被演算子が評価されていなくても、演算の評価をおこなうからです。）したがって、演算子は、他の関数と同様に、括弧でくくった引数のリストに対して、適用することがきます。



```jldoctest
julia> 1 + 2 + 3
6

julia> +(1,2,3)
6
```

```@raw html
<!--
The infix form is exactly equivalent to the function application form -- in fact the former is
parsed to produce the function call internally. This also means that you can assign and pass around
operators such as [`+()`](@ref) and [`*()`](@ref) just like you would with other function values:
-->
```

中置方式は関数適用方式とまったく同等です。じつのところ、中置方式は構文解析後に、内部的に関数呼び出しを行っているのです。 そのため、[`+()`](@ref) や [`*()`](@ref)　といった演算子も、他の関数値と同様に、代入や受け渡しを、行うことができます。


```jldoctest
julia> f = +;

julia> f(1,2,3)
6
```

```@raw html
<!--
Under the name `f`, the function does not support infix notation, however.
-->
```

しかしながら、`f` のような名前の関数は、中置記法に対応していません。



## Operators With Special Names

## 特殊な名前の演算子


```@raw html
<!--
A few special expressions correspond to calls to functions with non-obvious names. These are:
-->
```

特殊な式のなかには、あまり知られていない名前で関数呼び出しのできるものが、いくつかあります。

```@raw html
<!--


| Expression        | Calls                  |
|:----------------- |:--------------------- |
| `[A B C ...]`     | [`hcat()`](@ref)       |
| `[A; B; C; ...]`  | [`vcat()`](@ref)       |
| `[A B; C D; ...]` | [`hvcat()`](@ref)      |
| `A'`              | [`ctranspose()`](@ref) |
| `A.'`             | [`transpose()`](@ref)  |
| `1:n`             | [`colon()`](@ref)      |
| `A[i]`            | [`getindex()`](@ref)   |
| `A[i]=x`          | [`setindex!()`](@ref)  |

-->
```

| 式                | 関数呼び出し            |
|:----------------- |:---------------------- |
| `[A B C ...]`     | [`hcat()`](@ref)       |
| `[A; B; C; ...]`  | [`vcat()`](@ref)       |
| `[A B; C D; ...]` | [`hvcat()`](@ref)      |
| `A'`              | [`ctranspose()`](@ref) |
| `A.'`             | [`transpose()`](@ref)  |
| `1:n`             | [`colon()`](@ref)      |
| `A[i]`            | [`getindex()`](@ref)   |
| `A[i]=x`          | [`setindex!()`](@ref)  |



```@raw html
<!--

These functions are included in the `Base.Operators` module even though they do not have operator-like
names.

-->
```

こういった関数は名前が演算子らしくなくても、`Base.Operators`　モジュールに含まれています。


## [Anonymous Functions](@id man-anonymous-functions)
## 無名関数

```@raw html
<!--

Functions in Julia are [first-class objects](https://en.wikipedia.org/wiki/First-class_citizen):
they can be assigned to variables, and called using the standard function call syntax from the
variable they have been assigned to. They can be used as arguments, and they can be returned as
values. They can also be created anonymously, without being given a name, using either of these
syntaxes:

-->
```

Julia　において、関数は、 [第一級オブジェクト](https://en.wikipedia.org/wiki/First-class_citizen)です:
関数は、変数に代入したり、代入した変数から標準的な構文で呼び出したりできます。
関数の引数としても、戻り値としても使うことができます。
また、以下の構文のいずれかを使用して、名前をつけずに無名で、生成することができます。


```jldoctest
julia> x -> x^2 + 2x - 1
(::#1) (generic function with 1 method)

julia> function (x)
           x^2 + 2x - 1
       end
(::#3) (generic function with 1 method)
```

```@raw html
<!--

This creates a function taking one argument `x` and returning the value of the polynomial `x^2 +
2x - 1` at that value. Notice that the result is a generic function, but with a compiler-generated
name based on consecutive numbering.

-->
```

このような構文で、引数が  `x` 1つ、 戻り値が多項式 `x^2 + 2x -1` になる関数が生成されます。
こうして生成されるのは総称関数ですが、コンパイラが順に番号付けした名前がつけられる点に注意しましょう。　

```@raw html
<!--

The primary use for anonymous functions is passing them to functions which take other functions
as arguments. A classic example is [`map()`](@ref), which applies a function to each value of
an array and returns a new array containing the resulting values:

-->
```

無名関数の主な用途は、別の関数を引数とする関数に渡すことでしょう。
定番の例としては、[`map()`](@ref) があります。
この関数は、 配列の各要素に関数を適用したものを、新しい配列として返します。



```jldoctest
julia> map(round, [1.2,3.5,1.7])
3-element Array{Float64,1}:
 1.0
 4.0
 2.0
```

```@raw html
<!--


This is fine if a named function effecting the transform one wants already exists to pass as the
first argument to [`map()`](@ref). Often, however, a ready-to-use, named function does not exist.
In these situations, the anonymous function construct allows easy creation of a single-use function
object without needing a name:

-->
```

 配列に対して実行したい変換が、名前つきの関数として存在していて、[`map()`](@ref)の1番目の引数に使える場合は、問題ありません。
 ですが、多くの場合、すぐ使える名前付きの関数なんて存在しません。
そんな時、無名関数を使えば、使い捨ての関数オブジェクトを名前もつけずに簡単に作成できます。



```jldoctest
julia> map(x -> x^2 + 2x - 1, [1,3,-1])
3-element Array{Int64,1}:
  2
 14
 -2
```

```@raw html
<!--

An anonymous function accepting multiple arguments can be written using the syntax `(x,y,z)->2x+y-z`.
A zero-argument anonymous function is written as `()->3`. The idea of a function with no arguments
may seem strange, but is useful for "delaying" a computation. In this usage, a block of code is
wrapped in a zero-argument function, which is later invoked by calling it as `f()`.

-->
```
無名関数が複数の引数をとる場合は次のように書きます。`(x,y,z)->2x+y-z`
引数が０個の場合はこう書きます。 `()->3`
引数のない関数というのは、奇妙に感じるかもしれませんが、計算を"遅らせる"場合に便利です。
この記法では、コードのブロックを引数のない関数で囲んでおき、あとから`f()`のように呼び出します。


## Multiple Return Values
## 複数の戻り値


```@raw html
<!--

In Julia, one returns a tuple of values to simulate returning multiple values. However, tuples
can be created and destructured without needing parentheses, thereby providing an illusion that
multiple values are being returned, rather than a single tuple value. For example, the following
function returns a pair of values:

-->
```

Juliaでは、複数の値を返したい時は、疑似的に値のタプルを返します。
しかし、タプルは括弧を使わなくても、生成したり、分解したりできるので、1組のタプルではなく、複数の値が返された、と錯覚を起こすでしょう。
以下の例では関数は値のペアを返します。


```jldoctest foofunc
julia> function foo(a,b)
           a+b, a*b
       end
foo (generic function with 1 method)
```

```@raw html
<!--

If you call it in an interactive session without assigning the return value anywhere, you will
see the tuple returned:

-->
```

対話セッションで戻り値をどこにも代入しない場合は、タプルが返されます。


```jldoctest foofunc
julia> foo(2,3)
(5, 6)
```

```@raw html
<!--

A typical usage of such a pair of return values, however, extracts each value into a variable.
Julia supports simple tuple "destructuring" that facilitates this:

-->
```

しかし値のペアを返す典型的な用法は、それぞれの値を取り出して、別の変数に割り当てる場合でしょう。
Juliaでは、次のように簡単にタプルの`分割`をすることができます。

```jldoctest foofunc
julia> x, y = foo(2,3)
(5, 6)

julia> x
5

julia> y
6
```

```@raw html
<!--

You can also return multiple values via an explicit usage of the `return` keyword:

-->
```
キーワード `return` を使って、明示的に複数の値を返すこともできます。


```julia
function foo(a,b)
    return a+b, a*b
end
```

```@raw html
<!--
This has the exact same effect as the previous definition of `foo`.
-->
```

この書き方は以前の `foo` の定義とまったく同等の効力をもちます。

## Varargs Functions
## 可変引数関数

```@raw html
<!--

It is often convenient to be able to write functions taking an arbitrary number of arguments.
Such functions are traditionally known as "varargs" functions, which is short for "variable number
of arguments". You can define a varargs function by following the last argument with an ellipsis:

-->
```

関数の引数が任意の個数でも受け取れると便利なことが、よくあります。
そういった関数は伝統的に"可変引数"関数と呼ばれます。
"vararg"(可変引数)は"variable number of arguments"(可変個の引数)の略です。
可変引数関数を定義するには、最後の引数の後に...(省略記号)を付けます。



```jldoctest barfunc
julia> bar(a,b,x...) = (a,b,x)
bar (generic function with 1 method)
```

```@raw html
<!--

The variables `a` and `b` are bound to the first two argument values as usual, and the variable
`x` is bound to an iterable collection of the zero or more values passed to `bar` after its first
two arguments:

-->
```

変数 `a`,`b`には、通常通り、最初の2つの引数の値が束縛されます。
変数 `x` に束縛されるのは、長さ０個以上のイテラブル・コレクションで、関数`bar`に始めの2つの引数の後に続けて渡されたものです。


```jldoctest barfunc
julia> bar(1,2)
(1, 2, ())

julia> bar(1,2,3)
(1, 2, (3,))

julia> bar(1, 2, 3, 4)
(1, 2, (3, 4))

julia> bar(1,2,3,4,5,6)
(1, 2, (3, 4, 5, 6))
```

```@raw html
<!--
In all these cases, `x` is bound to a tuple of the trailing values passed to `bar`.
-->
```

以上のすべてのケースで、'x'には、`bar`に渡されて残ったものがタプルとして束縛されます。


```@raw html
<!--
It is possible to constrain the number of values passed as a variable argument; this will be discussed
later in [Parametrically-constrained Varargs methods](@ref).
-->
```

可変引数として渡される値の個数に制約をかけることも可能です。
これは、あとで[パラメートリック制約付き可変引数メソッド](@ref)で検討します。

```@raw html
<!--
On the flip side, it is often handy to "splice" the values contained in an iterable collection
into a function call as individual arguments. To do this, one also uses `...` but in the function
call instead:
-->
```

その一方で、イテラブル・コレクションと関数の引数を"継ぎ合わせる"ことも、便利なのでよく行われます。これは、関数呼び出しの中で、`...`使っておこないます。


```jldoctest barfunc
julia> x = (3, 4)
(3, 4)

julia> bar(1,2,x...)
(1, 2, (3, 4))
```

```@raw html
<!--
In this case a tuple of values is spliced into a varargs call precisely where the variable number
of arguments go. This need not be the case, however:
-->
```

この場合は、タプルと関数の引数の数がちょうど同じ状態で継ぎ合わされています。
`...`を使う必要がないですね。



```jldoctest barfunc
julia> x = (2, 3, 4)
(2, 3, 4)

julia> bar(1,x...)
(1, 2, (3, 4))

julia> x = (1, 2, 3, 4)
(1, 2, 3, 4)

julia> bar(x...)
(1, 2, (3, 4))
```

```@raw html
<!--
Furthermore, the iterable object spliced into a function call need not be a tuple:
-->
```
さらに、関数呼び出しと継ぎ合わせるイテラブル・オブジェクトはタプルである必要はありません。


```jldoctest barfunc
julia> x = [3,4]
2-element Array{Int64,1}:
 3
 4

julia> bar(1,2,x...)
(1, 2, (3, 4))

julia> x = [1,2,3,4]
4-element Array{Int64,1}:
 1
 2
 3
 4

julia> bar(x...)
(1, 2, (3, 4))
```

```@raw html
<!--
Also, the function that arguments are spliced into need not be a varargs function (although it
often is):
-->
```

また、継ぎ合わせる関数のほうも、可変引数関数でなくてもかまいません。(よくあります)

```jldoctest
julia> baz(a,b) = a + b;

julia> args = [1,2]
2-element Array{Int64,1}:
 1
 2

julia> baz(args...)
3

julia> args = [1,2,3]
3-element Array{Int64,1}:
 1
 2
 3

julia> baz(args...)
ERROR: MethodError: no method matching baz(::Int64, ::Int64, ::Int64)
Closest candidates are:
  baz(::Any, ::Any) at none:1
```

```@raw html
<!-
As you can see, if the wrong number of elements are in the spliced container, then the function
call will fail, just as it would if too many arguments were given explicitly.
-->
```
ご覧のように、継ぎ合わせるコンテナの要素の数が間違っていると、明示した引数が多すぎる場合と同じように、関数の呼び出しは失敗します。


## Optional Arguments
## 省略可能引数

```@raw html
<!-

In many cases, function arguments have sensible default values and therefore might not need to
be passed explicitly in every call. For example, the library function [`parse(T, num, base)`](@ref)
interprets a string as a number in some base. The `base` argument defaults to `10`. This behavior
can be expressed concisely as:
-->
```

多くの場合、関数の引数には適切なデフォルト値があるので、すべての呼び出しで、値を明示的に渡す必要はないかもしれません。
例えば、ライブラリ関数の [`parse(T, num, base)`](@ref) は文字列をある基数の数値として解釈します。
基数のデフォルト値は `10` です。この振る舞いは簡潔に以下のように記述できます。


```julia
function parse(type, num, base=10)
    ###
end
```

```@raw html
<!-

With this definition, the function can be called with either two or three arguments, and `10`
is automatically passed when a third argument is not specified:

-->
```

この定義では、関数を２個または3個の引数で呼び出すことができて、
３番目の引数を指定しない場合は、'10'が自動的に引き渡されます。


```jldoctest
julia> parse(Int,"12",10)
12

julia> parse(Int,"12",3)
5

julia> parse(Int,"12")
12
```

```@raw html
<!-

Optional arguments are actually just a convenient syntax for writing multiple method definitions
with different numbers of arguments (see [Note on Optional and keyword Arguments](@ref)).

-->
```

省略可能な引数は、実際には、引数の個数の違う複数のメソッドの定義を書くための便利な構文なのです。
( [省略可能な引数とキーワード引数の注意点](@ref) を参照).

## Keyword Arguments
## キーワード引数

```@raw html
<!-

Some functions need a large number of arguments, or have a large number of behaviors. Remembering
how to call such functions can be difficult. Keyword arguments can make these complex interfaces
easier to use and extend by allowing arguments to be identified by name instead of only by position.

-->
```

関数の中には、多数の引数が必要なものや、様々な挙動をとるものがあります。
そんな関数の呼び出し方を覚えるのは難しいものです。
キーワード引数は、引数の指定を位置ではなく、名前でおこなって、複雑な入力方法を、使いやすく、拡張するものです。



```@raw html
<!-

For example, consider a function `plot` that plots a line. This function might have many options,
for controlling line style, width, color, and so on. If it accepts keyword arguments, a possible
call might look like `plot(x, y, width=2)`, where we have chosen to specify only line width. Notice
that this serves two purposes. The call is easier to read, since we can label an argument with
its meaning. It also becomes possible to pass any subset of a large number of arguments, in any
order.
-->
```
例えば、`plot`という線を描く関数を考えてみましょう。
この関数には、たくさんのオプションがあって、線のスタイル、幅、色などを操作することができます。
キーワード引数を使えば、`plot(x, y, width=2)` のように書いて、線の幅だけを指定する、といったことができるでしょう。
キーワード引数には、２つの目的で役に立ちます。１つは、関数呼び出しの意図がわかりやすくなります。引数に意味をラベル付けできるからです。もう１つは、たくさんの引数の中から、設定する引数を選んでどの順番で指定してもいいのです。

```@raw html
<!-

Functions with keyword arguments are defined using a semicolon in the signature:
-->
```
キーワード引数関数を使う関数は、関数を宣言する行で、セミコロンを使って定義します。

```julia
function plot(x, y; style="solid", width=1, color="black")
    ###
end
```
```@raw html
<!-
When the function is called, the semicolon is optional: one can either call `plot(x, y, width=2)`
or `plot(x, y; width=2)`, but the former style is more common. An explicit semicolon is required
only for passing varargs or computed keywords as described below.
-->
```
関数呼び出しの際に、セミコロンは必ずしも必要ありません。
 `plot(x, y, width=2)` と  `plot(x, y; width=2)` どちらでもよいのですが、ふつうは１つ目の書き方をします。
 後述の可変引数や計算済みのキーワードを使う場合には、明示的にセミコロンを使わなければなりません。



```@raw html
<!-
Keyword argument default values are evaluated only when necessary (when a corresponding keyword
argument is not passed), and in left-to-right order. Therefore default expressions may refer to
prior keyword arguments.
-->
```
キーワード引数のデフォルト値は、（対応する値が渡されなくて）必要になった時だけ、左から右の順番で評価されます。
そのため、デフォルトの式は評価済みのキーワード引数を参照していてもかまいません。


```@raw html
<!-
The types of keyword arguments can be made explicit as follows:
-->
```
キーワード引数の型は以下のように明示的に指定できます。

```julia
function f(;x::Int64=1)
    ###
end
```

```@raw html
<!-
Extra keyword arguments can be collected using `...`, as in varargs functions:
-->
```
余分なキーワード引数は、可変引数関数で使う`...` をつけて、まとめることができます。

```julia
function f(x; y=0, kwargs...)
    ###
end
```

```@raw html
<!-
Inside `f`, `kwargs` will be a collection of `(key,value)` tuples, where each `key` is a symbol.
Such collections can be passed as keyword arguments using a semicolon in a call, e.g. `f(x, z=1; kwargs...)`.
-->
```
`f`の内部では、`kwargs`は、`key` がシンボルであるタプル`(key,value)` のコレクションになります。
このコレクションはセミコロンを使って  `f(x, z=1; kwargs...)` のように渡すことができます。


```@raw html
<!-
Dictionaries can also be used for this purpose.
-->
```
こういった場合に辞書もまた使われます。

```@raw html
<!-
One can also pass `(key,value)` tuples, or any iterable expression (such as a `=>` pair) that
can be assigned to such a tuple, explicitly after a semicolon. For example, `plot(x, y; (:width,2))`
and `plot(x, y; :width => 2)` are equivalent to `plot(x, y, width=2)`. This is useful in situations
where the keyword name is computed at runtime.
-->
```

`(key,value)` といったタプルや、タプルに代入できる（`=>`を使ったペアなど)イテラブルな式などは、明示的に書いたセミコロンの後に続けて渡すことができます。
`plot(x, y; (:width,2))` や  `plot(x, y; :width => 2)` などと定義は `plot(x, y, width=2)` 
と同等です。こういった書き方は実行時にキーワード名を算出する場合に役立ちます。  


```@raw html
<!-
The nature of keyword arguments makes it possible to specify the same argument more than once.
For example, in the call `plot(x, y; options..., width=2)` it is possible that the `options` structure
also contains a value for `width`. In such a case the rightmost occurrence takes precedence; in
this example, `width` is certain to have the value `2`.
-->
```
キーワード引数の性質上、同じ引数に複数回、値を指定できるようになっています。
例えば、`plot(x, y; options..., width=2)` という関数呼び出しは、`options`の中に`width`の値も設定されています。
この場合は、右端の出現が優先されます。`width` の値は `2` に設定されます。



## Evaluation Scope of Default Values
## デフォルト値を評価する際のスコープ

```@raw html
<!-
Optional and keyword arguments differ slightly in how their default values are evaluated. When
optional argument default expressions are evaluated, only *previous* arguments are in scope. In
contrast, *all* the arguments are in scope when keyword arguments default expressions are evaluated.
For example, given this definition:
-->
```
省略可能引数とキーワード引数は式のデフォルト値が評価される方法が少し違います。
省略可能引数が評価される際、スコープに存在するのは、既に評価された引数だけです。
一方、キーワード引数が評価される際には、すべての引数がスコープ内に存在します。
次式のような定義を例に挙げます。


```julia
function f(x, a=b, b=1)
    ###
end
```

```@raw html
<!-
the `b` in `a=b` refers to a `b` in an outer scope, not the subsequent argument `b`. However,
if `a` and `b` were keyword arguments instead, then both would be created in the same scope and
the `b` in `a=b` would refer to the subsequent argument `b` (shadowing any `b` in an outer scope),
which would result in an undefined variable error (since the default expressions are evaluated
left-to-right, and `b` has not been assigned yet).
-->
```
`a=b` の中の `b` は、スコープ外を参照していて、次の引数の `b`とは、解釈されません。
 しかし `a` と `b` がキーワード引数なら、両方とも同じスコープに生成されて``、`a=b` の中の `b` は、次の引数の `b` 
 を参照するでしょう（スコープ外の`b`はシャドーイングされます）。
 この結果、変数の未定義エラーが発生するでしょう。(初期値は左から右へ評価されるため、`b`にはまだ何も代入されていません)


## Do-Block Syntax for Function Arguments
## 関数の引数に対するDoブロック構文


```@raw html
<!-
Passing functions as arguments to other functions is a powerful technique, but the syntax for
it is not always convenient. Such calls are especially awkward to write when the function argument
requires multiple lines. As an example, consider calling [`map()`](@ref) on a function with several
cases:
-->
```
関数を別の関数へ渡すことは強力な技法ですが、いつも便利な構文で実行できるわけではありません。
特に関数の引数が何行にも渡る場合は、書くのが厄介でしょう。
例として、[`map()`](@ref) を呼び出すいくつかのケースを考えてみましょう。


```julia
map(x->begin
           if x < 0 && iseven(x)
               return 0
           elseif x == 0
               return 1
           else
               return x
           end
       end,
    [A, B, C])
```

```@raw html
<!-
Julia provides a reserved word `do` for rewriting this code more clearly:
-->
```
Juliaの予約語である`do`を使って、もっと明解に書きかえてみましょう。

```julia
map([A, B, C]) do x
    if x < 0 && iseven(x)
        return 0
    elseif x == 0
        return 1
    else
        return x
    end
end
```

```@raw html
<!-
The `do x` syntax creates an anonymous function with argument `x` and passes it as the first argument
to [`map()`](@ref). Similarly, `do a,b` would create a two-argument anonymous function, and a
plain `do` would declare that what follows is an anonymous function of the form `() -> ...`.
-->
```
`do x` という構文では `x` を引数にとる無名関数が生成されて、[`map()`](@ref) の第一引数として渡されます。
同様に `do a,b` と書くと、引数が2個の無名関数が生成され、また、単に  `do` と書くと  `() -> ...` という形式の無名関数が生成されます。

```@raw html
<!-
How these arguments are initialized depends on the "outer" function; here, [`map()`](@ref) will
sequentially set `x` to `A`, `B`, `C`, calling the anonymous function on each, just as would happen
in the syntax `map(func, [A, B, C])`.
-->
```
こうした引数が、どうのように初期化されるかは、`外側の`関数によって、異なります。
ここで取り上げた [`map()`](@ref) では、順番に `x` に対して `A`, `B`, `C` が代入されて、その都度、無名関数が
呼び出されます。ちょうど`map(func, [A, B, C])`という構文を使ったときと同じです。

```@raw html
<!-
This syntax makes it easier to use functions to effectively extend the language, since calls look
like normal code blocks. There are many possible uses quite different from [`map()`](@ref), such
as managing system state. For example, there is a version of [`open()`](@ref) that runs code ensuring
that the opened file is eventually closed:
-->
```
この構文を使うと、簡単に関数を使って、言語を効率的に拡張できます。通常のコードブロックと同様に関数を呼び出せるのですから。
また[`map()`](@ref) と全く違う使い方もたくさんあって、システムの状態を管理するようなこともできます。
たとえば、[`open()`](@ref) を使って、コードの実行後にに必ず開いたファイルを閉じるように保証するように書くことができます。



```julia
open("outfile", "w") do io
    write(io, data)
end
```
```@raw html
<!-
This is accomplished by the following definition:
-->
```
次のように定義すれば、実行できます。

```julia
function open(f::Function, args...)
    io = open(args...)
    try
        f(io)
    finally
        close(io)
    end
end
```

```@raw html
<!-
Here, [`open()`](@ref) first opens the file for writing and then passes the resulting output stream
to the anonymous function you defined in the `do ... end` block. After your function exits, [`open()`](@ref)
will make sure that the stream is properly closed, regardless of whether your function exited
normally or threw an exception. (The `try/finally` construct will be described in [Control Flow](@ref).)
-->
```
ここでは、 [`open()`](@ref)　は書き込み用のファイルを開き、出力ストリームを `do ... end` ブロックで定義された無名関数に渡します。この無名関数が終了した後に、 [`open()`](@ref) はストリームが適切に閉じられたかどうかを確認します。これは無名関数が正常に終了しても、例外を投げても行われます。
( `try/finally` については [実行制御](@ref) に記述しています)
-->

```@raw html
<!-
With the `do` block syntax, it helps to check the documentation or implementation to know how
the arguments of the user function are initialized.
-->
```
 `do`ブロック構文は、マニュアルや実装をチェックして、ユーザー定義関数の引数がどのように初期化されているかを理解するのに役立ちます。

## [Dot Syntax for Vectorizing Functions](@id man-vectorized)
##ベクトル化関数に対するDot構文

```@raw html
<!-
In technical-computing languages, it is common to have "vectorized" versions of functions, which
simply apply a given function `f(x)` to each element of an array `A` to yield a new array via
`f(A)`. This kind of syntax is convenient for data processing, but in other languages vectorization
is also often required for performance: if loops are slow, the "vectorized" version of a function
can call fast library code written in a low-level language. In Julia, vectorized functions are
*not* required for performance, and indeed it is often beneficial to write your own loops (see
[Performance Tips](@ref man-performance-tips)), but they can still be convenient. Therefore, *any* Julia function
`f` can be applied elementwise to any array (or other collection) with the syntax `f.(A)`.
For example `sin` can be applied to all elements in the vector `A`, like so:
-->
```
技術計算用のプログラム言語では、関数のベクトル化"したバージョンを作ることがよくあります。
既存の関数 `f(x)`を配列Aの各要素に単に適用して新しい配列を作る`f(A)`という関数を作るといった具合に。
こういった類の構文はデータ処理用に便利なものですが、Julia以外のプログラミング言語ではベクトル化はパフォーマンスをあげるために必要となることがよくあります。ループ処理が遅い場合には、低水準のプログラム言語で書かれた高速なベクトル版の関数を呼び出して使うというように。
Juliaではパフォーマンスをよくするために関数をベクトル化する必要は *ありません* 。
実際に、自分でループを書いたほうがよいこともよくあります([パフォーマンス ティップス](@ref man-performance-tips)参照)。それでも、ベクトル化は便利なので、Juliaでは *すべての* 関数 `f` は、　配列（その他のコレクション)に要素ごとに適用する `f.(A)` という構文が用意されています。
例えば、ベクトル`A`のすべての要素に`sin`関数を適用することができます。


```jldoctest
julia> A = [1.0, 2.0, 3.0]
3-element Array{Float64,1}:
 1.0
 2.0
 3.0

julia> sin.(A)
3-element Array{Float64,1}:
 0.841471
 0.909297
 0.14112
```
```@raw html
<!-
Of course, you can omit the dot if you write a specialized "vector" method of `f`, e.g. via `f(A::AbstractArray) = map(f, A)`,
and this is just as efficient as `f.(A)`. But that approach requires you to decide in advance
which functions you want to vectorize.
-->
```
もちろん、"ベクトル版"のメソッド `f`を `f(A::AbstractArray) = map(f, A)`のように書いて、dotを省略することもできます。この場合、効率は　`f.(A)`という書き方と変わりません。しかし、こういった手法では、事前にどの関数をベクトル化するかを
決める必要があります。

```@raw html
<!-
More generally, `f.(args...)` is actually equivalent to `broadcast(f, args...)`, which allows
you to operate on multiple arrays (even of different shapes), or a mix of arrays and scalars (see
[Broadcasting](@ref)). For example, if you have `f(x,y) = 3x + 4y`, then `f.(pi,A)` will return
a new array consisting of `f(pi,a)` for each `a` in `A`, and `f.(vector1,vector2)` will return
a new vector consisting of `f(vector1[i],vector2[i])` for each index `i` (throwing an exception
if the vectors have different length).
-->
```
さらに、大概の場合は、`f.(args...)` と `broadcast(f, args...)`は実質的に同等であり、複数の配列(形が違っていてもかまいません)の場合や、配列とスカラーを混合した場合([ブロードキャスト](@ref)参照)も操作可能です。
例えば `f(x,y) = 3x + 4y`と定義してから`f.(pi,A)` と書くと、 `A`の各要素`a`を使って `f(pi,a)`と書けるような
新規の配列が返ってくるし、 
`f.(vector1,vector2)` と書くとインデックス`i`を使って各要素が`f(vector1[i],vector2[i])`と書けるような
新規のベクトルが返ってきます。(ベクトルの長さが違うときは例外が発生します)

```jldoctest
julia> f(x,y) = 3x + 4y;

julia> A = [1.0, 2.0, 3.0];

julia> B = [4.0, 5.0, 6.0];

julia> f.(pi, A)
3-element Array{Float64,1}:
 13.4248
 17.4248
 21.4248

julia> f.(A, B)
3-element Array{Float64,1}:
 19.0
 26.0
 33.0
```

```@raw html
<!-
Moreover, *nested* `f.(args...)` calls are *fused* into a single `broadcast` loop. For example,
`sin.(cos.(X))` is equivalent to `broadcast(x -> sin(cos(x)), X)`, similar to `[sin(cos(x)) for x in X]`:
there is only a single loop over `X`, and a single array is allocated for the result. [In contrast,
`sin(cos(X))` in a typical "vectorized" language would first allocate one temporary array for
`tmp=cos(X)`, and then compute `sin(tmp)` in a separate loop, allocating a second array.] This
loop fusion is not a compiler optimization that may or may not occur, it is a *syntactic guarantee*
whenever nested `f.(args...)` calls are encountered. Technically, the fusion stops as soon as
a "non-dot" function call is encountered; for example, in `sin.(sort(cos.(X)))` the `sin` and `cos`
loops cannot be merged because of the intervening `sort` function.
-->
```
さらに `f.(args...)`という書き方を*ネスト*すると、単一の`ブロードキャスト`ループに`融合`します。
例えば、`sin.(cos.(X))` は `broadcast(x -> sin(cos(x)), X)`と同等で、 `[sin(cos(x)) for x in X]`と同様です。
この時`X`に対して1重のループを行うだけで戻り値用の配列１つ分のメモリが確保されるだけです。
[これとは対照的に、典型的なベクトル化されたプログラミング言語で`sin(cos(X))`と書くと、はじめに一時的な配列にメモリを割り当てて`tmp=cos(X)`を計算を行い、次に`sin(tmp)`の計算を別のループで行い別の配列にメモリを割り当てます。]
このループ融合は、コンパイラの最適化によって、左右されるものではなく、 `f.(args...)`がネストして呼び出されたときに、必ず行われるように"構文的に保証されて" います。しくみとしては、ループ融合は"ドットを使わない"関数呼び出しに出会った時点で停止します。例えば、 `sin.(sort(cos.(X)))`と書くと、 `sin` と `cos`は`sort`をはさんでいるために、融合されることはありません。

```@raw html
<!-
Finally, the maximum efficiency is typically achieved when the output array of a vectorized operation
is *pre-allocated*, so that repeated calls do not allocate new arrays over and over again for
the results ([Pre-allocating outputs](@ref):). A convenient syntax for this is `X .= ...`, which
is equivalent to `broadcast!(identity, X, ...)` except that, as above, the `broadcast!` loop is
fused with any nested "dot" calls. For example, `X .= sin.(Y)` is equivalent to `broadcast!(sin, X, Y)`,
overwriting `X` with `sin.(Y)` in-place. If the left-hand side is an array-indexing expression,
e.g. `X[2:end] .= sin.(Y)`, then it translates to `broadcast!` on a `view`, e.g. `broadcast!(sin, view(X, 2:endof(X)), Y)`,
so that the left-hand side is updated in-place.
-->
```
最後に、効率が最大限に達する典型例は、ベクトル化された演算の出力用の配列が、*もともと割り当てられている*場合で、関数呼び出しを何回繰り返しても、新しく演算結果の配列にメモリを割り当てることが、ありません([出力の事前割り当て](@ref):)。




```@raw html
<!-
Since adding dots to many operations and function calls in an expression
can be tedious and lead to code that is difficult to read, the macro
[`@.`](@ref @__dot__) is provided to convert *every* function call,
operation, and assignment in an expression into the "dotted" version.
-->
```
式の中に出てくる多くの演算子にdotをつけて関数呼び出しを行うのはバカバカし、コードが読みづらくなります。
[`@.`](@ref @__dot__)マクロを使うと、式中の、関数呼び出し、演算子、代入すべてに対して"ドット"を付加します。

```jldoctest
julia> Y = [1.0, 2.0, 3.0, 4.0];

julia> X = similar(Y); # pre-allocate output array

julia> @. X = sin(cos(Y)) # equivalent to X .= sin.(cos.(Y))
4-element Array{Float64,1}:
  0.514395
 -0.404239
 -0.836022
 -0.608083
```
```@raw html
<!-
Binary (or unary) operators like `.+` are handled with the same mechanism:
they are equivalent to `broadcast` calls and are fused with other nested "dot" calls.
 `X .+= Y` etcetera is equivalent to `X .= X .+ Y` and results in a fused in-place assignment;
 see also [dot operators](@ref man-dot-operators).
-->
```
 `.+` のような二項(または単項)演算子は同じ仕組みで扱われます。これらは`ブロードキャスト`呼び出しと同等で、
 他のネストされた"dot"呼び出しと融合されます。
  `X .+= Y` などの書き方は `X .= X .+ Y`と同等で、上書きによる代入が融合されて行われます。
 ([dot演算子](@ref man-dot-operators)参照)

## Further Reading
## 関連項目
```@raw html
<!-
We should mention here that this 
is far from a complete picture of defining functions. Julia has
a sophisticated type system and allows multiple dispatch on argument types. None of the examples
given here provide any type annotations on their arguments, meaning that they are applicable to
all types of arguments. The type system is described in [Types](@ref man-types) and defining a function
in terms of methods chosen by multiple dispatch on run-time argument types is described in [Methods](@ref).
-->
```
この章の説明は関数定義の全体像からはほど遠いものです。
Juliaは洗練された型システムを持ち、引数の型による多重ディスパッチを行うことができます。
引数に対して型注釈をつける例も示していません。型注釈はすべての引数の型に対して適応可能な意味づけをします。
型システムは [型](@ref man-types)に記述されています。
実行時の引数の型による多重ディスパッチを行い、その際に選択されるメソッドを使って関数を定義する方法は、 [メソッド](@ref)に記述されています。