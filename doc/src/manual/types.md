[](# [Types](@id man-types))
# [型](@id man-types)


```@raw html
<!--
Type systems have traditionally fallen into two quite different camps: static type systems, where
every program expression must have a type computable before the execution of the program, and
dynamic type systems, where nothing is known about types until run time, when the actual values
manipulated by the program are available. Object orientation allows some flexibility in statically
typed languages by letting code be written without the precise types of values being known at
compile time. The ability to write code that can operate on different types is called polymorphism.
All code in classic dynamically typed languages is polymorphic: only by explicitly checking types,
or when objects fail to support operations at run-time, are the types of any values ever restricted.
-->
```
型システムは伝統的にまったく異なる2つの陣営に分類されてきました。静的型システムと動的型システムです。
静的型システムでは、すべてのプログラムの式は、実行前に型が算出可能でなくてはなりません。
動的型システムでは、実行時に処理するデータが利用可能になるまで、型についてなにもわかりません。
静的型システムでもオブジェクト指向だと少し柔軟で、コンパイル時に値の正確な型がわからなくても
プログラムを書くことができます。
異なる型であっても動作するプログラムが書ける能力を多相性といいます。
古典的な動的型付言語はすべて多相的です。
型に制約が生じるのは、明示的に型チェックをする場合か、オブジェクトが実行時に操作を維持できなくなった場合
くらいのものでしょう。

```@raw html
<!--
Julia's type system is dynamic, but gains some of the advantages of static type systems by making
it possible to indicate that certain values are of specific types. This can be of great assistance
in generating efficient code, but even more significantly, it allows method dispatch on the types
of function arguments to be deeply integrated with the language. Method dispatch is explored in
detail in [Methods](@ref), but is rooted in the type system presented here.
-->
```
Juliaの型システムは動的ですが、静的型付システムの利点も、ある程度備えています。ある種の値には、
型を判定できるのです。これは、効率的なコードを生成するのに大いに役立ち、さらに重要なのは、
関数の引数の型に対してメソッド・ディスパッチが可能になり、Julia言語に深く統合されていることです。
メソッド・ディスパッチは[メソッド](@ref)で詳説しますが、ここに示した型システムに基づいているのです。


```@raw html
<!--
The default behavior in Julia when types are omitted is to allow values to be of any type. Thus,
one can write many useful Julia programs without ever explicitly using types. When additional
expressiveness is needed, however, it is easy to gradually introduce explicit type annotations
into previously "untyped" code. Doing so will typically increase both the performance and robustness
of these systems, and perhaps somewhat counterintuitively, often significantly simplify them.
-->
```
Juliaで、型を省略した時は、値は任意の型として許容されるのが、デフォルトの動作です。
つまり、型をわざわざ指定しなくても、Juliaなら有用なプログラムが沢山かけるのです。
しかし必要に応じて、以前は**型を指定していない**コードに徐々に型注釈を加えていくことも簡単にできるのです。
そうすると、一般的には、システムのパフォーマンスと堅牢性が向上し、おそらく直観に反して、しばしば非常に単純化されます。

```@raw html
<!--
Describing Julia in the lingo of [type systems](https://en.wikipedia.org/wiki/Type_system), it
is: dynamic, nominative and parametric. Generic types can be parameterized, and the hierarchical
relationships between types are [explicitly declared](https://en.wikipedia.org/wiki/Nominal_type_system),
rather than [implied by compatible structure](https://en.wikipedia.org/wiki/Structural_type_system).
One particularly distinctive feature of Julia's type system is that concrete types may not subtype
each other: all concrete types are final and may only have abstract types as their supertypes.
While this might at first seem unduly restrictive, it has many beneficial consequences with surprisingly
few drawbacks. It turns out that being able to inherit behavior is much more important than being
able to inherit structure, and inheriting both causes significant difficulties in traditional
object-oriented languages. Other high-level aspects of Julia's type system that should be mentioned
up front are:
-->
```
Juliaのことを[型システム](https://en.wikipedia.org/wiki/Type_system) の術語を使って記述すると,動的、公称的、パラメータ的です。
ジェネリック型はパラメータをとることができ、型の階層関係は、[明示的に宣言](https://en.wikipedia.org/wiki/Nominal_type_system)するのであって、 
[互換構造から推論](https://en.wikipedia.org/wiki/Structural_type_system)するわけではありません。
Juliaの型システムの際立った特徴は、具象型が互いのサブタイプとなってはいけない点です。
すべての具象型は最終的であり、抽象型のスーパータイプを持てるのみです。　
はじめは、この制約は不当に厳しく思えるかも知れませんが、多くの利点があり、欠点はほとんどないのです。
振る舞いを継承できるほうが、構造を継承できるよりも重要であり、両方できるようにすると、従来のオブジェクト指向言語では、深刻な困難が生じることが判明しています。
他に、Juliaの型システムの言及すべき高水準な側面は、

```@raw html
<!--
  * There is no division between object and non-object values: all values in Julia are true objects
    having a type that belongs to a single, fully connected type graph, all nodes of which are equally
    first-class as types.
  * There is no meaningful concept of a "compile-time type": the only type a value has is its actual
    type when the program is running. This is called a "run-time type" in object-oriented languages
    where the combination of static compilation with polymorphism makes this distinction significant.
  * Only values, not variables, have types -- variables are simply names bound to values.
  * Both abstract and concrete types can be parameterized by other types. They can also be parameterized
    by symbols, by values of any type for which [`isbits()`](@ref) returns true (essentially, things
    like numbers and bools that are stored like C types or structs with no pointers to other objects),
    and also by tuples thereof. Type parameters may be omitted when they do not need to be referenced
    or restricted.
-->
```
   * オブジェクトと非オブジェクトで値の区別がありません。すべての値は型を持つ真のオブジェクトです。その型はすべて連結している一つの型のグラフに属し、すべてのノードが型として等しく第一級です。
  * "コンパイル時の型"という考え方は全く無意味です。すべての値は、実行時に実際にとるただ一つの型を持ちます。これはオブジェクト指向言語では「実行時型」と呼ばれ、多相型と静的コンパイルを組み合わせるときは、この違いは重要になります。
  * 値のみが型を持ち、変数は型を持ちません。変数は値に束縛された単なる名前です。 
  * 抽象型と具象型の両方とも、他の型によってパラメータ化できます。また、シンボル、[`isbits()`](@ref) が、真の値を返す型である任意の値（本質的に数やブール値のような、他のオブジェクトへのポインタを持たないCの型や構造体に格納されるもの）、そういったもののタプルなどにによってパラメータ化することもできます。型パラメータは、参照や制限をする必要がない場合は省略することができます。


```@raw html
<!--
Julia's type system is designed to be powerful and expressive, yet clear, intuitive and unobtrusive.
Many Julia programmers may never feel the need to write code that explicitly uses types. Some
kinds of programming, however, become clearer, simpler, faster and more robust with declared types.
-->
```
Juliaの型システムは、強力で表現力豊かではあるが、わかりやすく、直感的で邪魔にならないように設計されています。多くのJuliaプログラマは、型を明示してコードを書く必要性を決して感じないかもしれません。しかし、プログラムの種類によっては、型を宣言すると、より明快単純で、速く、堅牢になります。

[](## Type Declarations)
## 型宣言


```@raw html
<!--
The `::` operator can be used to attach type annotations to expressions and variables in programs.
There are two primary reasons to do this:

1. As an assertion to help confirm that your program works the way you expect,
2. To provide extra type information to the compiler, which can then improve performance in some
   cases
-->
```
`::`オペレータは、プログラム内で、式や変数に型注釈を付けるために使用することができます。
これには主に2つの理由があります。

1.    プログラムに期待する動作を表明して、動作確認に役立てる。
2.    コンパイラに追加の型情報を提供して、場合によってはパフォーマンスを向上させる。



```@raw html
<!--
When appended to an expression computing a value, the `::` operator is read as "is an instance
of". It can be used anywhere to assert that the value of the expression on the left is an instance
of the type on the right. When the type on the right is concrete, the value on the left must have
that type as its implementation -- recall that all concrete types are final, so no implementation
is a subtype of any other. When the type is abstract, it suffices for the value to be implemented
by a concrete type that is a subtype of the abstract type.  the type assertion is not true,
an exception is thrown, otherwise, the left-hand value is returned:
-->
```
計算式に追加した`::`演算子は、 "is an instance of"と読むことができます。
`::`演算子はどこでも使用できて、演算子の左側の式の値が、右側の型のインスタンスであることを表明します。
演算子の右側が具象型の場合、左側の値はその型の実装でなければなりません。
すべて具象型は最終であるため、その実装が他の具象型の実装とはならないことを思い出してください。
抽象型の場合は、その抽象型のサブタイプである具象型によって値が実装されていれば十分です。
型表明が真でない場合は例外が投げられ、そうでない場合は左辺の値が返されます。

```jldoctest
julia> (1+2)::AbstractFloat
ERROR: TypeError: typeassert: expected AbstractFloat, got Int64

julia> (1+2)::Int
3
```


```@raw html
<!--
This allows a type assertion to be attached to any expression in-place.

When appended to a variable on the left-hand side of an assignment, or as part of a `local` declaration,
the `::` operator means something a bit different: it declares the variable to always have the
specified type, like a type declaration in a statically-typed language such as C. Every value
assigned to the variable will be converted to the declared type using [`convert()`](@ref):
-->
```
`::`を使えば、プログラム内の任意の式に型表明を差し込むことができます。

代入文の左側の変数や、`local`宣言の一部に追加されると、::演算子の意味が少し変わります。
つまり、C言語のような静的型付きの型宣言のように、変数が常に指定した型を持つという宣言になります。
変数に代入される値はすべて、 [`convert()`](@ref) を使用して宣言された型に変換されます。

```jldoctest
julia> function foo()
           x::Int8 = 100
           x
       end
foo (generic function with 1 method)

julia> foo()
100

julia> typeof(ans)
Int8
```


```@raw html
<!--
This feature is useful for avoiding performance "gotchas" that could occur if one of the assignments
to a variable changed its type unexpectedly.

This "declaration" behavior only occurs in specific contexts:
-->
```
この機能は、想定外の変数の型の変更をおこなうような代入がある場合に、発生する可能性のあるパフォーマンスの「落とし穴」を回避するのに役立ちます。

この「宣言」の動作は、特定のコンテキストでのみ発生します。


```julia
local x::Int8  # in a local declaration
x::Int8 = 10   # as the left-hand side of an assignment
```


```@raw html
<!--
and applies to the whole current scope, even before the declaration. Currently, type declarations
cannot be used in global scope, e.g. in the REPL, since Julia does not yet have constant-type
globals.

Declarations can also be attached to function definitions:
-->
```

宣言の前であっても、現在のスコープ全体に適用されます。
型宣言はREPLなどのグローバルスコープでは使用できません。
というのも、現時点では、Juliaには
まだ定数型のグローバル変数がないからです。

宣言を関数定義に差し込むこともできます。


```julia
function sinc(x)::Float64
    if x == 0
        return 1
    end
    return sin(pi*x)/(pi*x)
end
```


```@raw html
<!--
Returning from this function behaves just like an assignment to a variable with a declared type:
the value is always converted to `Float64`.
-->
```

この関数の戻り値は、宣言された型を持つ変数への代入と同様に処理されます。値は常に`Float64`に変換されます。

[](## Abstract Types)
## 抽象型


```@raw html
<!--
Abstract types cannot be instantiated, and serve only as nodes in the type graph, thereby describing
sets of related concrete types: those concrete types which are their descendants. We begin with
abstract types even though they have no instantiation because they are the backbone of the type
system: they form the conceptual hierarchy which makes Julia's type system more than just a collection
of object implementations.
-->
```
抽象型はインスタンス化することはできません。抽象型とその子孫である具象型を関連付けて型のグラフを書いた時に、ノードとして役に立つだけです。
型の説明は抽象型から始めましょう。インスタンス化はできないですが、型システムのバックボーンだからです。
抽象型によって形成される概念的な階層が、Juliaの型システムを、単にオブジェクトを実装した寄せ集め以上のものにしています。

```@raw html
<!--
Recall that in [整数と浮動小数点数](@ref), we introduced a variety of concrete
types of numeric values: [`Int8`](@ref), [`UInt8`](@ref), [`Int16`](@ref), [`UInt16`](@ref),
[`Int32`](@ref), [`UInt32`](@ref), [`Int64`](@ref), [`UInt64`](@ref), [`Int128`](@ref),
[`UInt128`](@ref), [`Float16`](@ref), [`Float32`](@ref), and [`Float64`](@ref). Although
they have different representation sizes, `Int8`, `Int16`, `Int32`, `Int64` and `Int128`
all have in common that they are signed integer types. Likewise `UInt8`, `UInt16`, `UInt32`,
`UInt64` and `UInt128` are all unsigned integer types, while `Float16`, `Float32` and
`Float64` are distinct in being floating-point types rather than integers. It is common for
a piece of code to make sense, for example, only if its arguments are some kind of integer,
but not really depend on what particular *kind* of integer. For example, the greatest common
denominator algorithm works for all kinds of integers, but will not work for floating-point
numbers. Abstract types allow the construction of a hierarchy of types, providing a context
into which concrete types can fit. This allows you, for example, to easily program to any type
that is an integer, without restricting an algorithm to a specific type of integer.
-->
```

[整数と浮動小数点数](@ref) で導入した、さまざまな数値の具象型を思い出してください。
[`Int8`](@ref)、 [`UInt8`](@ref)、 [`Int16`](@ref)、 [`UInt16`](@ref)、 [`Int32`](@ref)、 [`UInt32`](@ref)、 [`Int64`](@ref)、 [`UInt64`](@ref)、 [`Int128`](@ref)、
[`UInt128`](@ref)、 [`Float16`](@ref)、 [`Float32`](@ref)、 [`Float64`](@ref) です。
これらは表現できる数値の範囲は異なりますが、Int8、Int16、Int32、Int64、Int128 はすべて、符号付き整数型であるという点で共通しています。
同様にUInt8、UInt16、UInt32、 UInt64、UInt128はすべて、符号なし整数型であり、
一方で、Float16、Float32、Float64は、整数ではなく浮動小数点型に区別されます。
コードの断片が意味を成す、ということはよくあります。
たとえば、関数の引数を特定の種類の整数にだけ指定していても、本当はその特定の**種類**の整数に依存していない場合です。
たとえば、最大公約数を求めるアルゴリズムはすべての種類の整数で機能しますが、浮動小数点数では機能しません。
抽象型によって、型を階層にまとめて、適合する具象型のコンテキストと考えることができます。
これにより、あるアルゴリズムを特定の整数型に制限することなく、任意の整数型に対して簡単にプログラムすることができます。



```@raw html
<!--
Abstract types are declared using the `abstract type` keyword. The general syntaxes for declaring an
abstract type are:
-->
```
抽象型は`abstract type`キーワードを使用して宣言されます。抽象型を宣言する一般的な構文は次のとおりです。


```
abstract type «name» end
abstract type «name» <: «supertype» end
```


```@raw html
<!--
The `abstract type` keyword introduces a new abstract type, whose name is given by `«name»`. This
name can be optionally followed by `<:` and an already-existing type, indicating that the newly
declared abstract type is a subtype of this "parent" type.
-->
```
`abstract type`キーワードは、その名前を`«name»`とする、新たな抽象型を導入します。
この名前の後に`<:`と既存の型を続けて、新たに宣言された抽象型がこの「親」の型のサブタイプであることを示すことができます。



```@raw html
<!--
When no supertype is given, the default supertype is `Any` -- a predefined abstract type that
all objects are instances of and all types are subtypes of. In type theory, `Any` is commonly
called "top" because it is at the apex of the type graph. Julia also has a predefined abstract
"bottom" type, at the nadir of the type graph, which is written as `Union{}`. It is the exact
opposite of `Any`: no object is an instance of `Union{}` and all types are supertypes of `Union{}`.

Let's consider some of the abstract types that make up Julia's numerical hierarchy:
-->
```
スーパータイプが指定されていない場合、デフォルトのスーパータイプは`Any`です。
`Any`は事前に定義された抽象型で、すべてのオブジェクトが`Any`のインスタンスであり、すべての型が`Any`のサブタイプです。
型理論で`Any`は、それは型のグラフの頂点にあるため、一般的に「トップ」と呼ばれます。
またJuliaには、事前に定義された抽象型の「底」があり、型のグラフの最下位にあって、`Union{}`と書きます。
すべてのオブジェクトが`Union{}`のインスタンスではなく、すべての型が`Union{}`のスーパータイプです。

Juliaの数的な階層を構成する抽象型をいくつか考えてみましょう。
```julia
abstract type Number end
abstract type Real     <: Number end
abstract type AbstractFloat <: Real end
abstract type Integer  <: Real end
abstract type Signed   <: Integer end
abstract type Unsigned <: Integer end
```


```@raw html
<!--
The [`Number`](@ref) type is a direct child type of `Any`, and [`Real`](@ref) is its child.
In turn, `Real` has two children (it has more, but only two are shown here; we'll get to
the others later): [`Integer`](@ref) and [`AbstractFloat`](@ref), separating the world into
representations of integers and representations of real numbers. Representations of real
numbers include, of course, floating-point types, but also include other types, such as
rationals. Hence, `AbstractFloat` is a proper subtype of `Real`, including only
floating-point representations of real numbers. Integers are further subdivided into
[`Signed`](@ref) and [`Unsigned`](@ref) varieties.
-->
```
[`Number`](@ref) 型は`Any`の直下の子の型であり、[`Real`](@ref) は[`Number`](@ref) の子です。`Real`には子が２つあります（もっとありますが、ここでとりあげるのは2つだけです。後で他のものにも触れます）。
[`Integer`](@ref) と [`AbstractFloat`](@ref) は数の世界を整数の表現と実数の表現に分けます。
実数の表現には、もちろん浮動小数点型が含まれますが、有理数などの他の型も含まれます。
したがって、 `AbstractFloat` は`Real`の真のサブタイプで、実数のうち浮動小数点数のみを含みます。
整数はさらに[`Signed`](@ref) と [`Unsigned`](@ref) に細分されます。



```@raw html
<!--
The `<:` operator in general means "is a subtype of", and, used in declarations like this, declares
the right-hand type to be an immediate supertype of the newly declared type. It can also be used
in expressions as a subtype operator which returns `true` when its left operand is a subtype of
its right operand:
-->
```
通常、`<:`演算子は、"サブタイプ"を意味し、宣言で使用されると、右側の型が新しく宣言する型の直のスーパータイプであることを意味します。
`<:`演算子は、式の中で、左辺が右辺のサブタイプであるときに真を返すサブタイプ演算子として使用することもできます。

```jldoctest
julia> Integer <: Number
true

julia> Integer <: AbstractFloat
false
```


```@raw html
<!--
An important use of abstract types is to provide default implementations for concrete types. To
give a simple example, consider:
-->
```

抽象型の重要な用途は、具象型のデフォルトの実装を提供することです。簡単な例を挙げてみましょう。


```julia
function myplus(x,y)
    x+y
end
```


```@raw html
<!--
The first thing to note is that the above argument declarations are equivalent to `x::Any` and
`y::Any`. When this function is invoked, say as `myplus(2,5)`, the dispatcher chooses the most
specific method named `myplus` that matches the given arguments. (See [Methods](@ref) for more
information on multiple dispatch.)
-->
```

まず注意すべきことは、上記は `x::Any`かつ`y::Any`と引数の宣言をするのと同等なことです。
この関数がたとえば`myplus(2,5)`のように呼び出されると、ディスパッチャは`myplus`と名がつくメソッドの中で指定された引数にもっとも一致するものを選択します。多重ディスパッチの詳細については、メソッドを参照してください。

```@raw html
<!--
Assuming no method more specific than the above is found, Julia next internally defines and compiles
a method called `myplus` specifically for two `Int` arguments based on the generic function given
above, i.e., it implicitly defines and compiles:
-->
```
上記のメソッドより適切なメソッドが見つからない場合、次にJuliaは内部で、`myplus`という名のメソッドを、上記の総称関数に、2つの引数を`Int`に指定した上で、定義とコンパイルをします。つまり、暗黙的に定義とコンパイルをおこないます。


```julia
function myplus(x::Int,y::Int)
    x+y
end
```


```@raw html
<!--
and finally, it invokes this specific method.

Thus, abstract types allow programmers to write generic functions that can later be used as the
default method by many combinations of concrete types. Thanks to multiple dispatch, the programmer
has full control over whether the default or more specific method is used.
-->
```
最後に、この特定のメソッドを呼び出します。

このように、抽象型を使用すると、プログラマは多くの具象型の組み合わせに対応したデフォルトのメソッドとして後で使用できる総称関数を、記述できます。
多重ディスパッチのおかげで、プログラマは、デフォルトのメソッドとより特化したメソッドのどちらを使用するかを完全に制御できます。



```@raw html
<!--
An important point to note is that there is no loss in performance if the programmer relies on
a function whose arguments are abstract types, because it is recompiled for each tuple of argument
concrete types with which it is invoked. (There may be a performance issue, however, in the case
of function arguments that are containers of abstract types; see [Performance Tips](@ref man-performance-tips).)
-->
```
注意すべき重要な点は、引数が抽象型である関数をプログラマが使っても、パフォーマンスが落ちないことです。
というのも関数は、呼び出される時の具象型の引数のタプルそれぞれに対して、再コンパイルされるからです。
（ただし、抽象型のコンテナである関数の引数の場合は、パフォーマンスの問題がある可能性があります（[パフォーマンスのヒント]（@ ref man-performance-tips）を参照してください）。

[](## Primitive Types)
## 原始型


```@raw html
<!--
A primitive type is a concrete type whose data consists of plain old bits. Classic examples of primitive
types are integers and floating-point values. Unlike most languages, Julia lets you declare your
own primitive types, rather than providing only a fixed set of built-in ones. In fact, the standard
primitive types are all defined in the language itself:
-->
```
原始型は、データが普通のビットで構成される具象型です。
原始型の定番の例は、整数と浮動小数点値です。
ほとんどの言語とは異なり、Juliaでは標準装備の決まった原始型が提供されているだけではなく、独自の原始型を宣言できます。
実際、標準装備の原始型はすべてJulia自体で定義されています。


```julia
primitive type Float16 <: AbstractFloat 16 end
primitive type Float32 <: AbstractFloat 32 end
primitive type Float64 <: AbstractFloat 64 end

primitive type Bool <: Integer 8 end
primitive type Char 32 end

primitive type Int8    <: Signed   8 end
primitive type UInt8   <: Unsigned 8 end
primitive type Int16   <: Signed   16 end
primitive type UInt16  <: Unsigned 16 end
primitive type Int32   <: Signed   32 end
primitive type UInt32  <: Unsigned 32 end
primitive type Int64   <: Signed   64 end
primitive type UInt64  <: Unsigned 64 end
primitive type Int128  <: Signed   128 end
primitive type UInt128 <: Unsigned 128 end
```


```@raw html
<!--
The general syntaxes for declaring a primitive type are:
-->
```
原始型を宣言するための一般的な構文は次のとおりです。


```
primitive type «name» «bits» end
primitive type «name» <: «supertype» «bits» end
```


```@raw html
<!--
The number of bits indicates how much storage the type requires and the name gives the new type
a name. A primitive type can optionally be declared to be a subtype of some supertype. If a supertype
is omitted, then the type defaults to having `Any` as its immediate supertype. The declaration
of [`Bool`](@ref) above therefore means that a boolean value takes eight bits to store, and has
[`Integer`](@ref) as its immediate supertype. Currently, only sizes that are multiples of
8 bits are supported. Therefore, boolean values, although they really need just a single bit,
cannot be declared to be any smaller than eight bits.
-->
```
ビット数は、その型が格納にどれだけ必要とするかを示し、新しい型の名前に使われます。
原始型は、宣言にどのスーパータイプのサブタイプであるかを付加することができます。
スーパータイプが省略されている場合、その型はデフォルトでは、`Any`が直接のスーパータイプになります。
したがって、上記の[`Bool`](@ref) 宣言は、ブール値が格納するために8ビットを要し、[`Integer`](@ref) が直接のスーパータイプであることを意味しています 。
現在は、8ビットの倍数であるサイズのみがサポートされています。
したがって、ブール値は本当に必要なのは1ビットだけですが、8ビットより小さい宣言にはできません。



```@raw html
<!--
The types [`Bool`](@ref), [`Int8`](@ref) and [`UInt8`](@ref) all have identical representations:
they are eight-bit chunks of memory. Since Julia's type system is nominative, however, they
are not interchangeable despite having identical structure. A fundamental difference between
them is that they have different supertypes: [`Bool`](@ref)'s direct supertype is [`Integer`](@ref),
[`Int8`](@ref)'s is [`Signed`](@ref), and [`UInt8`](@ref)'s is [`Unsigned`](@ref). All other
differences between [`Bool`](@ref), [`Int8`](@ref), and [`UInt8`](@ref) are matters of
behavior -- the way functions are defined to act when given objects of these types as
arguments. This is why a nominative type system is necessary: if structure determined type,
which in turn dictates behavior, then it would be impossible to make [`Bool`](@ref) behave
any differently than [`Int8`](@ref) or [`UInt8`](@ref).
-->
```
 [`Bool`](@ref)、 [`Int8`](@ref)、 [`UInt8`](@ref) の型はすべて同一の表現を持っています。これらは8ビットのメモリの塊です。
 しかし、Juliaの型システムは公称的であるため、同一の構造を持っていても互換性はありません。
 これらの基本的な違いは、スーパータイプが異なることです。
  [`Bool`](@ref) の直接のスーパータイプは [`Integer`](@ref) 、[`Int8`](@ref) は[`Signed`](@ref)、[`UInt8`](@ref)は [`Unsigned`](@ref)です。
 [`Bool`](@ref)、 [`Int8`](@ref)、[`UInt8`](@ref) のその他の違いはすべて挙動に関するものです。
 これらの型のオブジェクトが引数として与えられたときに、関数がどのように動作するよう定義されているかということです。
 これが公称的な型システムが必要な理由です。もしも構造決定型であった場合、型の構造から、挙動が順番に決まってしまうので、[`Bool`](@ref) が
 [`Int8`](@ref) や [`UInt8`](@ref) と異なる挙動をとることは不可能になるでしょう。

[](## Composite Types)
## 複合型

```@raw html
<!--
[Composite types](https://en.wikipedia.org/wiki/Composite_data_type) are called records, structs,
or objects in various languages. A composite type is a collection of named fields,
an instance of which can be treated as a single value. In many languages, composite types are
the only kind of user-definable type, and they are by far the most commonly used user-defined
type in Julia as well.
-->
```
[複合型](https://en.wikipedia.org/wiki/Composite_data_type) は、レコード、構造体、オブジェクトなど、様々な言語で呼ばれます。
複合型は、名前付きフィールドのコレクションであり、そのインスタンスは単一の値として扱うことができます。
多くの言語では、複合型のみがユーザーの定義義可能な型であり、Juliaでも最も一般的に使用されるユーザ定義型です。

```@raw html
<!--
In mainstream object oriented languages, such as C++, Java, Python and Ruby, composite types also
have named functions associated with them, and the combination is called an "object". In purer
object-oriented languages, such as Ruby or Smalltalk, all values are objects whether they are
composites or not. In less pure object oriented languages, including C++ and Java, some values,
such as integers and floating-point values, are not objects, while instances of user-defined composite
types are true objects with associated methods. In Julia, all values are objects, but functions
are not bundled with the objects they operate on. This is necessary since Julia chooses which
method of a function to use by multiple dispatch, meaning that the types of *all* of a function's
arguments are considered when selecting a method, rather than just the first one (see [Methods](@ref)
for more information on methods and dispatch). Thus, it would be inappropriate for functions to
"belong" to only their first argument. Organizing methods into function objects rather than having
named bags of methods "inside" each object ends up being a highly beneficial aspect of the language
design.
-->
```
C++、Java、Python、Rubyなどの主流のオブジェクト指向言語では、複合型にはそれらに関連付けられた名前付き関数があり、その組み合わせを「オブジェクト」と呼びます。
RubyやSmalltalkのような、より純粋なオブジェクト指向言語では、すべての値は複合型であろうとなかろうとオブジェクトです。
より純粋でないオブジェクト指向言語（C ++やJavaなど）では、整数や浮動小数点値などの一部の値はオブジェクトではないですが、
ユーザー定義の複合型のインスタンスは、関連付けられたメソッドを持つ真のオブジェクトです。
Juliaでは、すべての値がオブジェクトですが、関数は操作対象のオブジェクトに紐づけされていません。
これは、Juliaが多重ディスパッチで使用する関数のメソッドを選択するために必要です。
つまり、メソッドを選択するときには、**すべての**関数の引数の型が考慮され、最初の引数だけではないからです（メソッドとディスパッチの詳細については、[メソッド](@ref)を参照してください）。
したがって、関数が最初の引数だけに「属する」のは不適切です。
各オブジェクトの "内側"にたくさんの名前のついたメソッドをいれるのではなく、メソッド群を編成して関数オブジェクトにすると、言語設計上、非常に有益です。

```@raw html
<!--
Composite types are introduced with the `struct` keyword followed by a block of field names, optionally
annotated with types using the `::` operator:
-->
```
複合型を定義するには、`struct`キーワードの後ろにフィールド名のブロックを置き、必要に応じて`::`演算子を使って型注釈します。


```jldoctest footype
julia> struct Foo
           bar
           baz::Int
           qux::Float64
       end
```


```@raw html
<!--
Fields with no type annotation default to `Any`, and can accordingly hold any type of value.

New objects of type `Foo` are created by applying the `Foo` type object like a function
to values for its fields:
-->
```
型注釈のないフィールドは、デフォルトでは`Any`型で、任意の型の値を保持することができます。

型`Foo`の新しいオブジェクトは、型オブジェクト`Foo`を関数のように、そのフィールドの値に適用して作成します。


```jldoctest footype
julia> foo = Foo("Hello, world.", 23, 1.5)
Foo("Hello, world.", 23, 1.5)

julia> typeof(foo)
Foo
```


```@raw html
<!--
When a type is applied like a function it is called a *constructor*. Two constructors are generated
automatically (these are called *default constructors*). One accepts any arguments and calls
[`convert()`](@ref) to convert them to the types of the fields, and the other accepts arguments
that match the field types exactly. The reason both of these are generated is that this makes
it easier to add new definitions without inadvertently replacing a default constructor.

Since the `bar` field is unconstrained in type, any value will do. However, the value for `baz`
must be convertible to `Int`:
-->
```
型は関数のように適用されるとき、**コンストラクタ**と呼ばれます。
2つのコンストラクタが自動的に生成されます（これらは**デフォルトコンストラクタ**と呼ばれます）。
一方はどんな引数でも受け入れ、[`convert()`](@ref) を呼び出してフィールドの型に変換します。
もう一方はフィールド型と完全に一致する引数を受け取ります。
これらの両方が生成されるのは、不注意にデフォルトのコンストラクタを置き換えることなく、新しい定義を追加することが容易になるからです。

`bar`フィールドは型の制約はないので、値は何でも構いません。ただし、`baz`の値は`Int`に変換可能である必要があります。

```jldoctest footype
julia> Foo((), 23.5, 1)
ERROR: InexactError()
Stacktrace:
 [1] convert(::Type{Int64}, ::Float64) at ./float.jl:679
 [2] Foo(::Tuple{}, ::Float64, ::Int64) at ./none:2
```


```@raw html
<!--
You may find a list of field names using the `fieldnames` function.
-->
```
`fieldnames`関数を使用して、フィールド名のリストを見つけることができます。


```jldoctest footype
julia> fieldnames(foo)
3-element Array{Symbol,1}:
 :bar
 :baz
 :qux
```


```@raw html
<!--
You can access the field values of a composite object using the traditional `foo.bar` notation:
-->
```
伝統的な`foo.bar`表記法を使用して、複合型のオブジェクトのフィールド値にアクセスできます。


```jldoctest footype
julia> foo.bar
"Hello, world."

julia> foo.baz
23

julia> foo.qux
1.5
```


```@raw html
<!--
Composite objects declared with `struct` are *immutable*; they cannot be modified
after construction. This may seem odd at first, but it has several advantages:

  * It can be more efficient. Some structs can be packed efficiently into arrays, and in some cases the
    compiler is able to avoid allocating immutable objects entirely.
  * It is not possible to violate the invariants provided by the type's constructors.
  * Code using immutable objects can be easier to reason about.
-->
```
`struct`で宣言された複合オブジェクトは**不変**です。生成後に変更することはできません。
これは最初は奇妙に見えるかもしれませんが、いくつかの利点があります：

* より効率的にすることができます。構造体には効率的に配列に詰め込めるものもあり、場合によっては、不変オブジェクト全体にメモリを割り当てることを避けることができます。
* 型のコンストラクタの不変性に違反することはできません。
* 不変オブジェクトを使用するコードは、わかりやすくなります。





```@raw html
<!--
An immutable object might contain mutable objects, such as arrays, as fields. Those contained
objects will remain mutable; only the fields of the immutable object itself cannot be changed
to point to different objects.

Where required, mutable composite objects can be declared with the keyword `mutable struct`, to be
discussed in the next section.

Composite types with no fields are singletons; there can be only one instance of such types:
-->
```
不変オブジェクトには、配列などの可変なオブジェクトがフィールドとして含まれることがあります。
含まれているオブジェクトは可変のままです。
自身が不変オブジェクトであるフィールドのみが、別のオブジェクトを指すことを禁止できます。

必要に応じて、可変な複合オブジェクトをキーワード`mutable struct`で宣言することができます（次のセクションで説明します）。

フィールドのない複合型はシングルトンです。そのような型のインスタンスは1つだけです。

```jldoctest
julia> struct NoFields
       end

julia> NoFields() === NoFields()
true
```


```@raw html
<!--
The `===` function confirms that the "two" constructed instances of `NoFields` are actually one
and the same. Singleton types are described in further detail [below](@ref man-singleton-types).

There is much more to say about how instances of composite types are created, but that discussion
depends on both [Parametric Types](@ref) and on [Methods](@ref), and is sufficiently important
to be addressed in its own section: [Constructors](@ref man-constructors).
-->
```
`===`関数は、`NoFields`の**2つの**生成されたインスタンスが、実際には同一であることを確認します。
シングルトン型については、[以下](@ref man-singleton-types) で詳しく説明します。

複合型のインスタンスがどのように作成されるかについてははるかに多くのことが言えますが、その議論は[パラメトリック型](@ref) と [メソッド](@ref)との両方に依存し、独自のセクションで解説するほど十分重要です：[コンストラクタ](@ref man-constructors)。

[](## Mutable Composite Types)
## 可変複合型


```@raw html
<!--
If a composite type is declared with `mutable struct` instead of `struct`, then instances of
it can be modified:
-->
```
`struct`の代わりに`mutable struct`で複合型を宣言すると、インスタンスは変更可能になります。


```jldoctest
 bartype
julia> mutable struct Bar
           baz
           qux::Float64
       end
julia> bar = Bar("Hello", 1.5);

julia> bar.qux = 2.0
2.0

julia> bar.baz = 1//2
1//2
```


```@raw html
<!--
In order to support mutation, such objects are generally allocated on the heap, and have
stable memory addresses.
A mutable object is like a little container that might hold different values over time,
and so can only be reliably identified with its address.
In contrast, an instance of an immutable type is associated with specific field values ---
the field values alone tell you everything about the object.
In deciding whether to make a type mutable, ask whether two instances
with the same field values would be considered identical, or if they might need to change independently
over time. If they would be considered identical, the type should probably be immutable.
-->
```
変更可能なオブジェクトは、一般にヒープ上に割り当てられ、メモリアドレスが安定しています。
変更可能なオブジェクトは、時間とともに値の変わる可能性がある小さなコンテナと似ていて、アドレスだけで確実に識別できます。
対照的に、不変型のインスタンスは、特定のフィールド値に関連付けられています。
フィールド値だけで、オブジェクトに関するすべての情報が表示されます。
型を変更可能にするかどうかを決めるには、同じフィールド値を持つ2つのインスタンスは同一だと考えられるか、あるいは時間がたてば互いに独立に変更する必要があるかどうかを考えます。
それらが同一であると考えられるならば、その型はおそらく不変にすべきです。

```@raw html
<!--
To recap, two essential properties define immutability in Julia:

  * An object with an immutable type is passed around (both in assignment statements and in function
    calls) by copying, whereas a mutable type is passed around by reference.
  * It is not permitted to modify the fields of a composite immutable type.
-->
```
要約、Juliaにおいて不変性を特徴づける2つの重要な特性：

* 不変型のオブジェクトは、代入文と関数呼び出しの両方でコピーによって受け渡されますが、変更可能な型は参照渡しされます。
* 不変な複合型のフィールドを変更することはできません。



```@raw html
<!--
It is instructive, particularly for readers whose background is C/C++, to consider why these two
properties go hand in hand.  If they were separated, i.e., if the fields of objects passed around
by copying could be modified, then it would become more difficult to reason about certain instances
of generic code.  For example, suppose `x` is a function argument of an abstract type, and suppose
that the function changes a field: `x.isprocessed = true`.  Depending on whether `x` is passed
by copying or by reference, this statement may or may not alter the actual argument in the calling
routine.  Julia sidesteps the possibility of creating functions with unknown effects in this scenario
by forbidding modification of fields of objects passed around by copying.
-->
```
C/C++を知っている読者にとって、これらの2つの特性がなぜ関連しているのかを検討するのは有益です。
それらが分離されている場合、つまり、コピーによって渡されたオブジェクトのフィールドが変更された場合、ジェネリックコードの特定のインスタンスについて推論するのがより困難になります。
たとえば`x`は抽象型の関数の引数で、関数がフィールドを変更したとします。
`x.isprocessed = true`
x`の渡し方がコピーか参照かによって、この文は呼び出し側ルーチンの実際の引数を変更する場合と変更しない場合があります。
Juliaは、こんな場合にどうなるか分からない関数になるのを避けるため、オブジェクトをコピー渡しするときのフィールドの変更を禁止します。

[](## Declared Types)
## 宣言型


```@raw html
<!--
The three kinds of types discussed in the previous three sections are actually all closely related.
They share the same key properties:

  * They are explicitly declared.
  * They have names.
  * They have explicitly declared supertypes.
  * They may have parameters.
-->
```
前述の3つのセクションで説明した3種の型は、実のところ、すべて密接に関連しています。
これらは重要な特徴が共通しています：

* 明示的に宣言されている。
* 名前がある。
* 明示的にスーパータイプを宣言している。
* パラメータを持ちうる。


```@raw html
<!--
Because of these shared properties, these types are internally represented as instances of the
same concept, `DataType`, which is the type of any of these types:
-->
```
これらの共通の特徴から、これらの型は内部的に同じコンセプト`DataType`のインスタンスとして表現されます。
`DataType`はこれらの型のいずれかのことです。

```jldoctest
julia> typeof(Real)
DataType

julia> typeof(Int)
DataType
```


```@raw html
<!--
A `DataType` may be abstract or concrete. If it is concrete, it has a specified size, storage
layout, and (optionally) field names. Thus a bits type is a `DataType` with nonzero size, but
no field names. A composite type is a `DataType` that has field names or is empty (zero size).

Every concrete value in the system is an instance of some `DataType`.
-->
```
`DataType`は抽象型でも具象型でもよい。具象型であれば、特定のサイズ、 記憶領域配置 、および（任意で）フィールド名を持ちます。
したがって、原始型は、サイズが0ではなく、フィールド名を持たない`DataType`です。
複合型は、フィールド名を持つか、空（サイズ0）の`DataType`です。

システムのあらゆる具体的な値は、なんらかの`DataType`のインスタンスです。

[](## Type Unions)
## 型共用体


```@raw html
<!--
A type union is a special abstract type which includes as objects all instances of any of its
argument types, constructed using the special `Union` function:
-->
```
型共用体は特別な抽象型であって、任意の引数の型のインスタンスをオブジェクトとして含み、特殊な関数`Union`を使用して構築されています。

```jldoctest
julia> IntOrString = Union{Int,AbstractString}
Union{AbstractString, Int64}

julia> 1 :: IntOrString
1

julia> "Hello!" :: IntOrString
"Hello!"

julia> 1.0 :: IntOrString
ERROR: TypeError: typeassert: expected Union{AbstractString, Int64}, got Float64
```


```@raw html
<!--
The compilers for many languages have an internal union construct for reasoning about types; Julia
simply exposes it to the programmer.
-->
```
多くの言語のコンパイラには、型についての推論のための内部での共用構造があります。Juliaはそれを単にプログラマに公開しています。

[](## Parametric Types)
## パラメトリック型


```@raw html
<!--
An important and powerful feature of Julia's type system is that it is parametric: types can take
parameters, so that type declarations actually introduce a whole family of new types -- one for
each possible combination of parameter values. There are many languages that support some version
of [generic programming](https://en.wikipedia.org/wiki/Generic_programming), wherein data structures
and algorithms to manipulate them may be specified without specifying the exact types involved.
For example, some form of generic programming exists in ML, Haskell, Ada, Eiffel, C++, Java, C#,
F#, and Scala, just to name a few. Some of these languages support true parametric polymorphism
(e.g. ML, Haskell, Scala), while others support ad-hoc, template-based styles of generic programming
(e.g. C++, Java). With so many different varieties of generic programming and parametric types
in various languages, we won't even attempt to compare Julia's parametric types to other languages,
but will instead focus on explaining Julia's system in its own right. We will note, however, that
because Julia is a dynamically typed language and doesn't need to make all type decisions at compile
time, many traditional difficulties encountered in static parametric type systems can be relatively
easily handled.
-->
```

Juliaの型システムの重要かつ強力な特徴は、パラメトリックであることです。
型はパラメータを取ることができるので、型宣言は実質的に、ありうるパラメータ組み合わせの分だけ、新しい型の族を導入することになります。
[ジェネリック・プログラミング](https://en.wikipedia.org/wiki/Generic_programming)、つまり、正確な型を指定しなくても、データ構造やアルゴリズムを特定することができる言語が、程度の差はあれ、たくさんあります。
たとえば、ML、Haskell、Ada、Eiffel、C ++、Java、C＃、F＃、およびScalaなど、少し挙げるだけでも、ジェネリックプログラミング的なものが存在します。
これらの言語の中には真のパラメトリック多相（ML、Haskell、Scalaなど）に対応するものもあれば、テンプレートベースのジェネリック・プログラミング（C ++、Javaなど）のスタイルに対応するものもあります。
言語によってジェネリック・プログラミングとパラメトリック型が多種多様であるため、Juliaのパラメトリック型を他の言語と比較することはせず、代わりにJuliaのシステムについて説明することに専念します。
ただし、Juliaは動的型付け言語であり、コンパイル時にすべての型決定を行う必要はないため、静的型付け言語で生じる従来の困難は、比較的取り扱い易いなっています。



```@raw html
<!--
All declared types (the `DataType` variety) can be parameterized, with the same syntax in each
case. We will discuss them in the following order: first, parametric composite types, then parametric
abstract types, and finally parametric bits types.
-->
```
すべての宣言型（`DataType`の仲間）は、それぞれ同じ構文でパラメータ化できます。
まず、パラメトリック複合型、パラメトリック抽象型、最後にパラメトリック原始型の順に説明します。[訳注1]_

[](### Parametric Composite Types)
### パラメトリック複合型


```@raw html
<!--
Type parameters are introduced immediately after the type name, surrounded by curly braces:
-->
```

型パラメータは、型名の直後に中括弧で囲んで挿入します。

```jldoctest pointtype
julia> struct Point{T}
           x::T
           y::T
       end
```

```@raw html
<!--
This declaration defines a new parametric type, `Point{T}`, holding two "coordinates" of type
`T`. What, one may ask, is `T`? Well, that's precisely the point of parametric types: it can be
any type at all (or a value of any bits type, actually, although here it's clearly used as a type).
`Point{Float64}` is a concrete type equivalent to the type defined by replacing `T` in the definition
of `Point` with [`Float64`](@ref). Thus, this single declaration actually declares an unlimited
number of types: `Point{Float64}`, `Point{AbstractString}`, `Point{Int64}`, etc. Each of these
is now a usable concrete type:
-->
```

この宣言は、新しいパラメトリック型`Point{T}`を定義し、型`T`の2つの "座標"を保持しています。
`T`ってなんだ？と誰が聞くかもしれません。これがまさしくパラメトリック型のポイントです。
どんな型（または原始型の値、ここでは明らかに型として使われていますが）でもかまいません。
 `Point{Float64}`は、`Point` の定義の中の`T`を [`Float64`](@ref)と置き換えて定義した型と同等な具象型です。
 したがって、一つの宣言が実質的には、`Point{Float64}`、`Point{AbstractString}`、`Point{Int64}`などの無限の宣言に相当します。
 これらの型それぞれが、現在、利用可能な具象型です。


```jldoctest pointtype
julia> Point{Float64}
Point{Float64}

julia> Point{AbstractString}
Point{AbstractString}
```


```@raw html
<!--
The type `Point{Float64}` is a point whose coordinates are 64-bit floating-point values, while
the type `Point{AbstractString}` is a "point" whose "coordinates" are string objects (see [Strings](@ref)).

`Point` itself is also a valid type object, containing all instances `Point{Float64}`, `Point{AbstractString}`,
etc. as subtypes:
-->
```
`Point{Float64}`という型は、座標が64ビット浮動小数点値である点であり、`Point{AbstractString}`は、その「座標」が文字列オブジェクトである「点」です（ [文字列](@ref)を参照）。

`Point`はこれ自体が妥当な型オブジェクトで、Point{Float64}`、 `Point{AbstractString}`などをすべてサブタイプとして含んでいます。


```jldoctest pointtype
julia> Point{Float64} <: Point
true

julia> Point{AbstractString} <: Point
true
```


```@raw html
<!--
Other types, of course, are not subtypes of it:
-->
```
もちろん、他の型はそれのサブタイプではありません：


```jldoctest pointtype
julia> Float64 <: Point
false

julia> AbstractString <: Point
false
```


```@raw html
<!--
Concrete `Point` types with different values of `T` are never subtypes of each other:
-->
```
`T`の値が異なる具象型`Point`は、決して互いにサブタイプではありません。

```jldoctest pointtype
julia> Point{Float64} <: Point{Int64}
false

julia> Point{Float64} <: Point{Real}
false
```


```@raw html
<!--
!!! warning
    This last point is *very* important: even though `Float64 <: Real` we **DO NOT** have `Point{Float64} <: Point{Real}`.
-->
```
!!!警告
 この最後の点は**非常に**重要です。`Float64 <: Real`は成り立つにもかかわらず、`Point{Float64} <: Point{Real}`は成り立ちません。

```@raw html
<!--
In other words, in the parlance of type theory, Julia's type parameters are *invariant*, rather
than being [covariant (or even contravariant)](https://en.wikipedia.org/wiki/Covariance_and_contravariance_%28computer_science%29). This is for practical reasons: while any instance
of `Point{Float64}` may conceptually be like an instance of `Point{Real}` as well, the two types
have different representations in memory:
-->
```
言い換えれば、型理論の用語を使うと、Juliaの型パラメータは [共変（または反変）](https://en.wikipedia.org/wiki/Covariance_and_contravariance_%28computer_science%29)ではなく、不変です。
これは実用的な理由によるものです。
概念的には、`Point{Float64}`のインスタンスは`Point{Real}` のインスタンスと似ていますが、メモリ内での表現は２つの型で異なります。



```@raw html
<!--
  * An instance of `Point{Float64}` can be represented compactly and efficiently as an immediate pair
    of 64-bit values;
  * An instance of `Point{Real}` must be able to hold any pair of instances of [`Real`](@ref).
    Since objects that are instances of `Real` can be of arbitrary size and structure, in
    practice an instance of `Point{Real}` must be represented as a pair of pointers to
    individually allocated `Real` objects.
-->
```

* `Point{Float64}`のインスタンスは、64ビット値の隣接するペアとしてコンパクトかつ効率的に表現できます。
* `Point{Real}` のインスタンスは、どんな[`Real`](@ref)のペアでも保持できる必要があります。というのも、`Real`のインスタンスであるオブジェクトは任意のサイズと構造になる可能性があるので、現実的には、`Point{Real}`のインスタンスは、個別に割り当てられた`Real`オブジェクトへのポインタのペアとして表現する必要があります。



```@raw html
<!--
The efficiency gained by being able to store `Point{Float64}` objects with immediate values is
magnified enormously in the case of arrays: an `Array{Float64}` can be stored as a contiguous
memory block of 64-bit floating-point values, whereas an `Array{Real}` must be an array of pointers
to individually allocated [`Real`](@ref) objects -- which may well be
[boxed](https://en.wikipedia.org/wiki/Object_type_%28object-oriented_programming%29#Boxing)
64-bit floating-point values, but also might be arbitrarily large, complex objects, which are
declared to be implementations of the `Real` abstract type.
-->
```
`Point{Float64}`オブジェクトに、値を直接を格納できることによって得られる効率は、配列の場合、非常に大きくなります。
`Array{Float64}`は、64ビットの浮動小数点値の連続したメモリブロックとして格納されますが、`Array{Real}`は個別に割り当てられた[`Real`](@ref) オブジェクトへのポインタの配列でなければなりません。
-- [ボックス化](https://en.wikipedia.org/wiki/Object_type_%28object-oriented_programming%29#Boxing)されている64ビットの浮動小数点値でも構いませんし、抽象型`Real`の実装と宣言されている任意の大きさの複素数オブジェクトでもかまいません。

```@raw html
<!--
Since `Point{Float64}` is not a subtype of `Point{Real}`, the following method can't be applied
to arguments of type `Point{Float64}`:
-->
```
`Point{Float64}`は、`Point{Real}`のサブタイプではないので、次のメソッドを型`Point{Float64}`の引数に適用することはできません。


```julia
function norm(p::Point{Real})
    sqrt(p.x^2 + p.y^2)
end
```


```@raw html
<!--
A correct way to define a method that accepts all arguments of type `Point{T}` where `T` is
a subtype of [`Real`](@ref) is:
-->
```
`T`が[`Real`](@ref)のサブタイプである`Point{T}`を、すべて引数として受け取るメソッドを定義する正しい方法は次のとおりです。


```julia
function norm(p::Point{<:Real})
    sqrt(p.x^2 + p.y^2)
end
```


```@raw html
<!--
(Equivalently, one could define `function norm{T<:Real}(p::Point{T})` or
`function norm(p::Point{T} where T<:Real)`; see [UnionAll Types](@ref).)

More examples will be discussed later in [Methods](@ref).
-->
```
（同等な定義として、`function norm{T<:Real}(p::Point{T})`や、`function norm(p::Point{T} where T<:Real)`があります。（[UnionAll Types](@ref) を参照。）

より多くの例については、後の [メソッド](@ref) で説明します。

```@raw html
<!--
How does one construct a `Point` object? It is possible to define custom constructors for composite
types, which will be discussed in detail in [Constructors](@ref man-constructors), but in the absence of any special
constructor declarations, there are two default ways of creating new composite objects, one in
which the type parameters are explicitly given and the other in which they are implied by the
arguments to the object constructor.
-->
```
どのようにしてPointオブジェクトを構成しますか？[コンストラクタ]（@ ref man-constructor）で詳しく説明するコンポジット型のカスタムコンストラクタを定義することは可能ですが、特別なコンストラクタ宣言がない場合、新しいコンポジットオブジェクトを作成するデフォルトの方法が2つあります。型パラメータが明示的に与えられ、型のパラメータがオブジェクトコンストラクタへの引数によって暗示されるもの。

```@raw html
<!--
Since the type `Point{Float64}` is a concrete type equivalent to `Point` declared with [`Float64`](@ref)
in place of `T`, it can be applied as a constructor accordingly:
-->
```
型`Point{Float64}`は、`T`の場所に[`Float64`](@ref) を置いて宣言した`Point`と同等の具象型なので、`Point`と同様のコンストラクタとして適用できます。

```jldoctest pointtype
julia> Point{Float64}(1.0, 2.0)
Point{Float64}(1.0, 2.0)

julia> typeof(ans)
Point{Float64}
```


```@raw html
<!--
For the default constructor, exactly one argument must be supplied for each field:
-->
```
デフォルトのコンストラクタでは、各フィールドにちょうど1つの引数を指定する必要があります。



```jldoctest pointtype
julia> Point{Float64}(1.0)
ERROR: MethodError: Cannot `convert` an object of type Float64 to an object of type Point{Float64}
This may have arisen from a call to the constructor Point{Float64}(...),
since type constructors fall back to convert methods.
Stacktrace:
 [1] Point{Float64}(::Float64) at ./sysimg.jl:24

julia> Point{Float64}(1.0,2.0,3.0)
ERROR: MethodError: no method matching Point{Float64}(::Float64, ::Float64, ::Float64)
```


```@raw html
<!--
Only one default constructor is generated for parametric types, since overriding it is not possible.
This constructor accepts any arguments and converts them to the field types.

In many cases, it is redundant to provide the type of `Point` object one wants to construct, since
the types of arguments to the constructor call already implicitly provide type information. For
that reason, you can also apply `Point` itself as a constructor, provided that the implied value
of the parameter type `T` is unambiguous:
-->
```
パラメトリック型に対して、デフォルトのコンストラクタは1つしか生成されません。オーバーライドできないためです。このコンストラクタは任意の引数を受け取り、フィールドの型に変換します。

多くの場合、構築したい`Point`オブジェクトの型を提供することは冗長です。
`Point`コンストラクタ呼び出しの引数の型には、すでに型情報が隠されているからです。
そのため、`Point`のパラメータ型の値`T`が明白である場合は、自身をコンストラクタとして適用することもできます。

```jldoctest pointtype
julia> Point(1.0,2.0)
Point{Float64}(1.0, 2.0)

julia> typeof(ans)
Point{Float64}

julia> Point(1,2)
Point{Int64}(1, 2)

julia> typeof(ans)
Point{Int64}
```


```@raw html
<!--
In the case of `Point`, the type of `T` is unambiguously implied if and only if the two arguments
to `Point` have the same type. When this isn't the case, the constructor will fail with a [`MethodError`](@ref):
-->
```

`Point`の場合、2つの引数が同じ型を持つ場合にのみ、型Tは明白に暗示されます。これが当てはまらない場合、コンストラクタは次のように失敗して、 [`MethodError`](@ref) が発生します。

```jldoctest pointtype
julia> Point(1,2.5)
ERROR: MethodError: no method matching Point(::Int64, ::Float64)
Closest candidates are:
  Point(::T, !Matched::T) where T at none:2
```


```@raw html
<!--
Constructor methods to appropriately handle such mixed cases can be defined, but that will not
be discussed until later on in [Constructors](@ref man-constructors).
-->
```
そのような混合したケースを適切に扱うコンストラクタメソッドは定義できますが、後ほど[コンストラクタ]（@ ref man-constructors）まで議論されることはありません。

[](### Parametric Abstract Types)
### パラメトリック抽象型


```@raw html
<!--
Parametric abstract type declarations declare a collection of abstract types, in much the same
way:
-->
```
パラメトリック抽象型に対しても、ほぼ同じ方法で、抽象型の一群に対して型宣言を行います。

```jldoctest pointytype
julia> abstract type Pointy{T} end
```


```@raw html
<!--
With this declaration, `Pointy{T}` is a distinct abstract type for each type or integer value
of `T`. As with parametric composite types, each such instance is a subtype of `Pointy`:
-->
```

この宣言では`Pointy{T}`は、型または整数値を表す`T`のためのそれぞれ個別の抽象型です。
パラメトリック複合型と同様に、各インスタンスは`Pointy`のサブタイプです。

```jldoctest pointytype
julia> Pointy{Int64} <: Pointy
true

julia> Pointy{1} <: Pointy
true
```


```@raw html
<!--
Parametric abstract types are invariant, much as parametric composite types are:
-->
```
パラメトリックな抽象型は、パラメトリックな複合型と同じように不変です。
```jldoctest pointytype
julia> Pointy{Float64} <: Pointy{Real}
false

julia> Pointy{Real} <: Pointy{Float64}
false
```


```@raw html
<!--
The notation `Pointy{<:Real}` can be used to express the Julia analogue of a
*covariant* type, while `Pointy{>:Int}` the analogue of a *contravariant* type,
but technically these represent *sets* of types (see [UnionAll Types](@ref)).
-->
```
Juliaでは、`Pointy{<:Real}`という表記での版の**共変**型のようなもの、`Pointy{>:Int}`で**反変**型のようなものを表現できます。
しかし、技術的には、これらは型の**集合**を表しています。([UnionAll型](@ref) 参照)

```jldoctest pointytype
julia> Pointy{Float64} <: Pointy{<:Real}
true

julia> Pointy{Real} <: Pointy{>:Int}
true
```


```@raw html
<!--
Much as plain old abstract types serve to create a useful hierarchy of types over concrete types,
parametric abstract types serve the same purpose with respect to parametric composite types. We
could, for example, have declared `Point{T}` to be a subtype of `Pointy{T}` as follows:
-->
```
普通の抽象型は、具象型に対する有益な型の階層を作成するのに役立ちますが、パラメトリックな抽象型はパラメトリックな複合型と同様の目的で使います。
たとえば、次のように`Point{T}`を`Pointy{T}`のサブタイプとして宣言することができます。


```jldoctest pointytype
julia> struct Point{T} <: Pointy{T}
           x::T
           y::T
       end
```


```@raw html
<!--
Given such a declaration, for each choice of `T`, we have `Point{T}` as a subtype of `Pointy{T}`:
-->
```
このような宣言によって、それぞれ選択した`T`に対して、`Point{T}`は`Pointy{T}`のサブタイプとなります。


```jldoctest pointytype
julia> Point{Float64} <: Pointy{Float64}
true

julia> Point{Real} <: Pointy{Real}
true

julia> Point{AbstractString} <: Pointy{AbstractString}
true
```


```@raw html
<!--
This relationship is also invariant:
-->
```
この関係も不変です。

```jldoctest pointytype
julia> Point{Float64} <: Pointy{Real}
false

julia> Point{Float64} <: Pointy{<:Real}
true
```


```@raw html
<!--
What purpose do parametric abstract types like `Pointy` serve? Consider if we create a point-like
implementation that only requires a single coordinate because the point is on the diagonal line
*x = y*:
-->
```

`Pointy`のようなパラメトリック抽象型はなんの役に立つのでしょうか？
対角線**x = y**上にあって、座標がどちらか一つ分かればいい点のようなものを実装する場合を考えましょう。

```jldoctest pointytype
julia> struct DiagPoint{T} <: Pointy{T}
           x::T
       end
```


```@raw html
<!--
Now both `Point{Float64}` and `DiagPoint{Float64}` are implementations of the `Pointy{Float64}`
abstraction, and similarly for every other possible choice of type `T`. This allows programming
to a common interface shared by all `Pointy` objects, implemented for both `Point` and `DiagPoint`.
This cannot be fully demonstrated, however, until we have introduced methods and dispatch in the
next section, [Methods](@ref).
-->
```

ここで`Point{Float64}`と`DiagPoint{Float64}`は共に、抽象型`Pointy{Float64}`の実装で、これは`T`に他の可能な型をとっても同様です。
これにより`Point`と`DiagPoint`のどちらを実装するにも、`Pointy`オブジェクトを共通のインタフェースにするようなプログラミングが可能になります。
しかし、完全な解説は、メソッドとディスパッチを導入する次のセクション[メソッド](@ref) に持ち越します。



```@raw html
<!--
There are situations where it may not make sense for type parameters to range freely over all
possible types. In such situations, one can constrain the range of `T` like so:
-->
```

型のパラメータがとりうる型を自由に認めると、意味を成さない場合があります。
そのような状況では、次のように、`T`の範囲を制限することができます。


```jldoctest realpointytype
julia> abstract type Pointy{T<:Real} end
```


```@raw html
<!--
With such a declaration, it is acceptable to use any type that is a subtype of
[`Real`](@ref) in place of `T`, but not types that are not subtypes of `Real`:
-->
```
この宣言では、`T`の場所には[`Real`](@ref)の任意のサブタイプの使えますが、 `Real`のサブタイプでなければ使えません。

```jldoctest realpointytype
julia> Pointy{Float64}
Pointy{Float64}

julia> Pointy{Real}
Pointy{Real}

julia> Pointy{AbstractString}
ERROR: TypeError: Pointy: in T, expected T<:Real, got Type{AbstractString}

julia> Pointy{1}
ERROR: TypeError: Pointy: in T, expected T<:Real, got Int64
```


```@raw html
<!--
Type parameters for parametric composite types can be restricted in the same manner:
-->
```
パラメトリック複合型の型パラメータも、同じ方法で制限できます
。
```julia
struct Point{T<:Real} <: Pointy{T}
    x::T
    y::T
end
```


```@raw html
<!--
To give a real-world example of how all this parametric type machinery can be useful, here is
the actual definition of Julia's [`Rational`](@ref) immutable type (except that we omit the
constructor here for simplicity), representing an exact ratio of integers:
-->
```
このパラメトリック型という仕組みがどれほど有用であるかの実例として、ここでは整数の比を表すJuliaの[`Rational`](@ref) 不変型の実際の定義を示します。
（単純化のため、ここではコンストラクタを省略します）
```julia
struct Rational{T<:Integer} <: Real
    num::T
    den::T
end
```


```@raw html
<!--
It only makes sense to take ratios of integer values, so the parameter type `T` is restricted
to being a subtype of [`Integer`](@ref), and a ratio of integers represents a value on the
real number line, so any [`Rational`](@ref) is an instance of the [`Real`](@ref) abstraction.
-->
```
整数値の比率を取る時だけ、意味をなすので、パラメータの型`T`は、[`Integer`](@ref)のサブタイプに限定されています。
整数の比は数直線上の値を表現するので、任意の[`Rational`](@ref) は、抽象型 [`Real`](@ref) のインスタンスです。

[](### Tuple Types)
### タプル型



```@raw html
<!--
Tuples are an abstraction of the arguments of a function -- without the function itself. The salient
aspects of a function's arguments are their order and their types. Therefore a tuple type is similar
to a parameterized immutable type where each parameter is the type of one field. For example,
a 2-element tuple type resembles the following immutable type:
-->
```
タプルは関数本体からその引数だけを抜き出したものです。
関数の引数の顕著な側面は、その順序と型です。
したがって、タプル型は、各パラメータが1つのフィールドの型であるパラメトリック複合型に似ています。
たとえば、2要素タプル型は、次の複合型に似ています。

```julia
struct Tuple2{A,B}
    a::A
    b::B
end
```


```@raw html
<!--
However, there are three key differences:

  * Tuple types may have any number of parameters.
  * Tuple types are *covariant* in their parameters: `Tuple{Int}` is a subtype of `Tuple{Any}`. Therefore
    `Tuple{Any}` is considered an abstract type, and tuple types are only concrete if their parameters
    are.
  * Tuples do not have field names; fields are only accessed by index.
-->
```
ただし、3つの重要な違いがあります。 

* タプル型は、任意の数のパラメータを持つことができます。
* タプル型は、そのパラメータと**共変**です。`Tuple{Int}`は`Tuple{Any}`のサブタイプです。したがって `Tuple{Any}`は、抽象型と見なされます。タプル型は、そのパラメータが具象型の場合にのみ具象型です。
* タプルにはフィールド名はありません。フィールドはインデックスによってのみアクセスされます。


```@raw html
<!--
Tuple values are written with parentheses and commas. When a tuple is constructed, an appropriate
tuple type is generated on demand:
-->
```
タプル値は、括弧とカンマで書かれています。タプルが生成されると、必要に応じて適切なタプル型が生成されます。



```jldoctest
julia> typeof((1,"foo",2.5))
Tuple{Int64,String,Float64}
```


```@raw html
<!--
Note the implications of covariance:
-->
```

暗黙的な共変に注意してください。

```jldoctest
julia> Tuple{Int,AbstractString} <: Tuple{Real,Any}
true

julia> Tuple{Int,AbstractString} <: Tuple{Real,Real}
false

julia> Tuple{Int,AbstractString} <: Tuple{Real,}
false
```


```@raw html
<!--
Intuitively, this corresponds to the type of a function's arguments being a subtype of the function's
signature (when the signature matches).
-->
```

直観的には、これは、関数の引数の型が関数のシグネチャのサブタイプであることに相当します（シグネチャが適合する場合）。

[](### Vararg Tuple Types)
### 可変引数タプル型


```@raw html
<!--
The last parameter of a tuple type can be the special type `Vararg`, which denotes any number
of trailing elements:
-->
```
前述のタプル型のパラメータは、特殊な型である`可変引数`として、任意の数の後続の要素を示す型とすることができます。

```jldoctest
julia> mytupletype = Tuple{AbstractString,Vararg{Int}}
Tuple{AbstractString,Vararg{Int64,N} where N}

julia> isa(("1",), mytupletype)
true

julia> isa(("1",1), mytupletype)
true

julia> isa(("1",1,2), mytupletype)
true

julia> isa(("1",1,2,3.0), mytupletype)
false
```


```@raw html
<!--
Notice that `Vararg{T}` corresponds to zero or more elements of type `T`. Vararg tuple types are
used to represent the arguments accepted by varargs methods (see [Varargs Functions](@ref)).

The type `Vararg{T,N}` corresponds to exactly `N` elements of type `T`.  `NTuple{N,T}` is a convenient
alias for `Tuple{Vararg{T,N}}`, i.e. a tuple type containing exactly `N` elements of type `T`.
-->
```
`Vararg{T}`は、0個以上の型`T`に対応することに注意してくださいT。
可変引数タプル型は、varargsメソッドで受け入れられる引数を表すために使用されます（[可変引数関数](@ref)を参照）。

型`Vararg{T,N}`は、ちょうど`N`個の要素`T`に対応します。
`NTuple{N,T}`は`Tuple{Vararg{T,N}}`の便利なエイリアスです。
つまり、ちょうど`N`個の型`T`の要素を含むタプル型です。

[](#### [Singleton Types](@id man-singleton-types))
#### [シングルトン型](@id man-singleton-types)


```@raw html
<!--
There is a special kind of abstract parametric type that must be mentioned here: singleton types.
For each type, `T`, the "singleton type" `Type{T}` is an abstract type whose only instance is
the object `T`. Since the definition is a little difficult to parse, let's look at some examples:
-->
```
ここで言及しなければならないは、特殊なパラメトリック抽象型であるシングルトン型です。
それぞれの型`T`に対して、 "シングルトン型" `Type{T}`は、唯一のインスタンスが`T`のみである抽象型です。。定義を構文解析するのは少し難しいので、いくつかの例を見てみましょう。

```jldoctest
julia> isa(Float64, Type{Float64})
true

julia> isa(Real, Type{Float64})
false

julia> isa(Real, Type{Real})
true

julia> isa(Float64, Type{Real})
false
```


```@raw html
<!--
In other words, [`isa(A,Type{B})`](@ref) is true if and only if `A` and `B` are the same object
and that object is a type. Without the parameter, `Type` is simply an abstract type which has
all type objects as its instances, including, of course, singleton types:
-->
```

言い換えると、[`isa(A,Type{B})`](@ref) は、`A`と`B`が同じオブジェクトであり、そのオブジェクトが型であるのみ真です。パラメータなしでは、`Type`は、単なる抽象型であり、すべての型オブジェクトをそのインスタンスとして持ちます（もちろん、シングルトン型も含みます）。


```jldoctest
julia> isa(Type{Float64}, Type)
true

julia> isa(Float64, Type)
true

julia> isa(Real, Type)
true
```


```@raw html
<!--
Any object that is not a type is not an instance of `Type`:
-->
```
型でないオブジェクトは、`Type`のインスタンスではありません。



```jldoctest
julia> isa(1, Type)
false

julia> isa("foo", Type)
false
```


```@raw html
<!--
Until we discuss [Parametric Methods](@ref) and [conversions](@ref conversion-and-promotion), it is difficult to explain
the utility of the singleton type construct, but in short, it allows one to specialize function
behavior on specific type *values*. This is useful for writing methods (especially parametric
ones) whose behavior depends on a type that is given as an explicit argument rather than implied
by the type of one of its arguments.
-->
```
[Parametric Methods](@ref)と[conversions](@ref conversion-and-promotion)の議論がすむまで、
シングルトン型の有用性を説明するのは困難ですが、要するに、関数の挙動を特定の型の`値`だけに特化することができるのです。
これは、暗黙的ではなく明示的な引数として与えられる型に依存する振る舞いを持つメソッド（特にパラメトリックなもの）を書くのに便利です。

```@raw html
<!--
A few popular languages have singleton types, including Haskell, Scala and Ruby. In general usage,
the term "singleton type" refers to a type whose only instance is a single value. This meaning
applies to Julia's singleton types, but with that caveat that only type objects have singleton
types.
-->
```
人気のある言語の中には、Haskell、Scala、Rubyなど、シングルトン型のあるものがあります。
一般的な使用法では、「シングルトンタイプ」という用語は、インスタンスが単一の値である型を指します。
この意味はJuliaのシングルトン型にも当てはまりますが、型オブジェクトだけがシングルトン型を持つという点に注意してください。

[](### Parametric Primitive Types)
### パラメトリック原始型


```@raw html
<!--
Primitive types can also be declared parametrically. For example, pointers are represented as
primitive types which would be declared in Julia like this:
-->
```
原始型にもパラメータをつけて宣言することができます。
たとえば、ポインタは原始型として表現できて、Juliaでは以下のように宣言します。

```julia
# 32-bit system:
primitive type Ptr{T} 32 end

# 64-bit system:
primitive type Ptr{T} 64 end
```


```@raw html
<!--
The slightly odd feature of these declarations as compared to typical parametric composite types,
is that the type parameter `T` is not used in the definition of the type itself -- it is just
an abstract tag, essentially defining an entire family of types with identical structure, differentiated
only by their type parameter. Thus, `Ptr{Float64}` and `Ptr{Int64}` are distinct types, even though
they have identical representations. And of course, all specific pointer types are subtypes of
the umbrella `Ptr` type:
-->
```
典型的なパラメトリック複合型と比較して、これらの宣言のわずかに奇妙な特徴は、型パラメータ`T`が型自体の定義に使用されていないことです。
つまり、抽象タグであり、本質的には同一の構造を持つ型の族全体を定義し、型パラメータによってのみ区別されます。
このように、`Ptr{Float64}`と`Ptr{Int64}`は、同じ表現を持っているにもかかわらず、型としては異なります。
もちろん、すべての特定のポインタ型は、`Ptr`型のサブタイプです。

```jldoctest
julia> Ptr{Float64} <: Ptr
true

julia> Ptr{Int64} <: Ptr
true
```

[](## UnionAll Types)
## UnionAll型


```@raw html
<!--
We have said that a parametric type like `Ptr` acts as a supertype of all its instances
(`Ptr{Int64}` etc.). How does this work? `Ptr` itself cannot be a normal data type, since without
knowing the type of the referenced data the type clearly cannot be used for memory operations.
The answer is that `Ptr` (or other parametric types like `Array`) is a different kind of type called a
`UnionAll` type. Such a type expresses the *iterated union* of types for all values of some parameter.
-->
```
`Ptr`のようなパラメトリック型はすべてのインスタンス（`Ptr{Int64}`など）のスーパータイプとして機能すると前述しました。
これはどのように作動するでしょうか？
`Ptr`自体は通常のデータ型ではありえません。というのも、参照されるデータの型を知らなければ、明らかに、その型をメモリ操作に使用することができないからです。
答えは、`Ptr`の型（または他の`Array`のようなパラメトリック型）は、`UnionAll`と呼ばれる種類の異なるものです 。
このような型は、いくつかのパラメータのすべての値に対して型の**反復結合**を表現します。

```@raw html
<!--
`UnionAll` types are usually written using the keyword `where`. For example `Ptr` could be more
accurately written as `Ptr{T} where T`, meaning all values whose type is `Ptr{T}` for some value
of `T`. In this context, the parameter `T` is also often called a "type variable" since it is
like a variable that ranges over types.
Each `where` introduces a single type variable, so these expressions are nested for types with
multiple parameters, for example `Array{T,N} where N where T`.
-->
```
UnionAll型は、通常、キーワード`where`を使用して記述されます。
例えば、`Ptr`は、より正確に`Ptr{T} where T`と書くことができ、ある型の値`T`によって`Ptr{T}`と書ける型である値すべてを意味しています。
この文脈では、パラメータ`T`は型にまたがる変数のようなものであるため、しばしば「型変数」と呼ばれます。
それぞれ`where`は一つの型変数を導入しているため、これらの式は複数のパラメータを持つ型に対してネストしています。例えば`Array{T,N} where N where T`のように。



```@raw html
<!--
The type application syntax `A{B,C}` requires `A` to be a `UnionAll` type, and first substitutes `B`
for the outermost type variable in `A`.
The result is expected to be another `UnionAll` type, into which `C` is then substituted.
So `A{B,C}` is equivalent to `A{B}{C}`.
This explains why it is possible to partially instantiate a type, as in `Array{Float64}`: the first
parameter value has been fixed, but the second still ranges over all possible values.
Using explicit `where` syntax, any subset of parameters can be fixed. For example, the type of all
1-dimensional arrays can be written as `Array{T,1} where T`.
-->
```
型の適用構文`A{B,C}`は、`A`がUnionAll型であることが必須で、まず`B`に一番外側の型変数`A`を代入します。
結果は別の`UnionAll`型になると予想され、その中に`C`を代入します。なので`A{B,C}`と`A{B}{C}`は同等です。
これは`Array{Float64}`のように、型を部分的にインスタンス化することができる理由を説明しています。
最初のパラメータ値は固定されていますが、2番目の値はすべての可能な値にまたがっているからです。
明示的な`where`構文を使用すると、パラメータの任意の部分集合を固定できます。
例えば、すべての1次元配列の型は、`Array{T,1} where T`と書くことができます。



```@raw html
<!--
Type variables can be restricted with subtype relations.
`Array{T} where T<:Integer` refers to all arrays whose element type is some kind of
[`Integer`](@ref).
The syntax `Array{<:Integer}` is a convenient shorthand for `Array{T} where T<:Integer`.
Type variables can have both lower and upper bounds.
`Array{T} where Int<:T<:Number` refers to all arrays of [`Number`](@ref)s that are able to
contain `Int`s (since `T` must be at least as big as `Int`).
The syntax `where T>:Int` also works to specify only the lower bound of a type variable,
and `Array{>:Int}` is equivalent to `Array{T} where T>:Int`.
-->
```
型変数は、サブタイプの関係を使って制限することができます。
 `Array{T} where T<:Integer`は、要素の型が[`Integer`](@ref)のなんらかの型になる配列すべてを指しています [`Integer`](@ref)。
 構文`Array{<:Integer}`は`Array{T} where T<:Integer`の便利な簡略表記です。
 型変数は、下限と上限の両方を持つことができます。
  `Array{T} where Int<:T<:Number`は `Int`を含むことができる[`Number`](@ref)のすべての配列を指します（少なくとも、それ以上の大きさでなければなりません）。
  構文`where T>:Int`はまた、型変数の下限のみを指定していて、
  `Array{>:Int}`は、`Array{T} where T>:Int`同等です。

```@raw html
<!--
Since `where` expressions nest, type variable bounds can refer to outer type variables.
For example `Tuple{T,Array{S}} where S<:AbstractArray{T} where T<:Real` refers to 2-tuples
whose first element is some [`Real`](@ref), and whose second element is an `Array` of any
kind of array whose element type contains the type of the first tuple element.
-->
```
`where`式はネストするので、型変数の束縛は外部の型変数を参照できます。
例えば、`Tuple{T,Array{S}} where S<:AbstractArray{T} where T<:Real`は、第1要素は[`Real`](@ref)の何かで、
第2要素は、各要素が第1要素の型を含む型である配列の**配列**である、2要素-タプルを参照します。

```@raw html
<!--
The `where` keyword itself can be nested inside a more complex declaration. For example,
consider the two types created by the following declarations:
-->
```
`where`キーワード自体は、より複雑な宣言の中にネストすることができます。
たとえば、次の宣言で作成される2つの型を考えてみましょう。

```jldoctest
julia> const T1 = Array{Array{T,1} where T, 1}
Array{Array{T,1} where T,1}

julia> const T2 = Array{Array{T,1}, 1} where T
Array{Array{T,1},1} where T
```


```@raw html
<!--
Type `T1` defines a 1-dimensional array of 1-dimensional arrays; each
of the inner arrays consists of objects of the same type, but this type may vary from one inner array to the next.
On the other hand, type `T2` defines a 1-dimensional array of 1-dimensional arrays all of whose inner arrays must have the
same type.  Note that `T2` is an abstract type, e.g., `Array{Array{Int,1},1} <: T2`, whereas `T1` is a concrete type. As a consequence, `T1` can be constructed with a zero-argument constructor `a=T1()` but `T2` cannot.
-->
```
型`T1`は、1次元配列の1次元配列を定義します。
内側の配列は同じ型のオブジェクトで構成されますが、この型は内側の配列ごとに異なる場合があります。
一方、型`T2`は、内側の配列がすべて同じ型でなければならない1次元配列の1次元配列を定義します。
これ`T2`は抽象型であり、`Array{Array{Int,1},1} <: T2`であるのに対して、`T1`は具象型です。
したがって、`T1`は引数のないコンストラクタで`a=T1()`のように構築することはできますが、`T2`ではできません。

```@raw html
<!--
There is a convenient syntax for naming such types, similar to the short form of function
definition syntax:
-->
```
このような型を命名する便利な構文がありますが、これは関数定義の短い形の構文と似ています：

```julia
Vector{T} = Array{T,1}
```


```@raw html
<!--
This is equivalent to `const Vector = Array{T,1} where T`.
Writing `Vector{Float64}` is equivalent to writing `Array{Float64,1}`, and the umbrella type
`Vector` has as instances all `Array` objects where the second parameter -- the number of array
dimensions -- is 1, regardless of what the element type is. In languages where parametric types
must always be specified in full, this is not especially helpful, but in Julia, this allows one
to write just `Vector` for the abstract type including all one-dimensional dense arrays of any
element type.
-->
```
これは`const Vector = Array{T,1} where T`と同等です。
`Vector{Float64}`と書くのは、`Array{Float64,1}`と書くのと同等です。
傘型の `Vector`は、要素の種類に関係なく、2番目のパラメータ（配列の次元数）が1のすべての`Array`オブジェクトをインスタンスとして持ちます。
パラメトリック型を常に完全に指定しなければならない言語では、これは特に有用ではないかもしれません。
しかしJuliaでは、`Vector`と書くだけで、任意の要素型のすべての1次元の密な配列を含む抽象型を表すことができます。

[](## Type Aliases)
## 型エイリアス


```@raw html
<!--
Sometimes it is convenient to introduce a new name for an already expressible type.
This can be done with a simple assignment statement.
For example, `UInt` is aliased to either [`UInt32`](@ref) or [`UInt64`](@ref) as is
appropriate for the size of pointers on the system:
-->
```
すでに表現可能な型に新しい名前をつけると便利な場合が時々あります。
これは簡単な代入文で行うことができます。
たとえば、`UInt`は、システム上のポインタのサイズに応じて、[`UInt32`](@ref) か [`UInt64`](@ref)　の別名となります。

```julia-repl
# 32-bit system:
julia> UInt
UInt32

# 64-bit system:
julia> UInt
UInt64
```


```@raw html
<!--
This is accomplished via the following code in `base/boot.jl`:
-->
```
これは次のコード`base/boot.jl`の中で実行されます：

```julia
if Int === Int64
    const UInt = UInt64
else
    const UInt = UInt32
end
```


```@raw html
<!--
Of course, this depends on what `Int` is aliased to -- but that is predefined to be the correct
type -- either [`Int32`](@ref) or [`Int64`](@ref).
-->
```
もちろん、これは `Int` が[`Int32`](@ref)と [`Int64`](@ref)のどちらのエイリアスであるかに依存しています。
このエイリアスは正しい型になるように事前に定義されています。

```@raw html
<!--
(Note that unlike `Int`, `Float` does not exist as a type alias for a specific sized
[`AbstractFloat`](@ref). Unlike with integer registers, the floating point register sizes
are specified by the IEEE-754 standard. Whereas the size of `Int` reflects the size of a
native pointer on that machine.)
-->
```
（`Int`や`Float`とは異なり、`Float`は、サイズを特定した[`AbstractFloat`](@ref)の型エイリアスとして存在しません。
整数レジスタとは異なり、浮動小数点レジスタのサイズは、IEEE-754規格で規定されています。
一方`Int`のサイズは、そのマシン上のネイティブポインタのサイズを反映しています。）

[](## Operations on Types)
## 型に対する操作


```@raw html
<!--
Since types in Julia are themselves objects, ordinary functions can operate on them. Some functions
that are particularly useful for working with or exploring types have already been introduced,
such as the `<:` operator, which indicates whether its left hand operand is a subtype of its right
hand operand.
-->
```


```@raw html
<!--
The [`isa`](@ref) function tests if an object is of a given type and returns true or false:
-->
```

```jldoctest
julia> isa(1, Int)
true

julia> isa(1, AbstractFloat)
false
```


```@raw html
<!--
The [`typeof()`](@ref) function, already used throughout the manual in examples, returns the type
of its argument. Since, as noted above, types are objects, they also have types, and we can ask
what their types are:
-->
```

```jldoctest
julia> typeof(Rational{Int})
DataType

julia> typeof(Union{Real,Float64,Rational})
DataType

julia> typeof(Union{Real,String})
Union
```


```@raw html
<!--
What if we repeat the process? What is the type of a type of a type? As it happens, types are
all composite values and thus all have a type of `DataType`:
-->
```

```jldoctest
julia> typeof(DataType)
DataType

julia> typeof(Union)
DataType
```


```@raw html
<!--
`DataType` is its own type.

Another operation that applies to some types is [`supertype()`](@ref), which reveals a type's
supertype. Only declared types (`DataType`) have unambiguous supertypes:
-->
```

```jldoctest
julia> supertype(Float64)
AbstractFloat

julia> supertype(Number)
Any

julia> supertype(AbstractString)
Any

julia> supertype(Any)
Any
```


```@raw html
<!--
If you apply [`supertype()`](@ref) to other type objects (or non-type objects), a [`MethodError`](@ref)
is raised:
-->
```

```jldoctest
julia> supertype(Union{Float64,Int64})
ERROR: MethodError: no method matching supertype(::Type{Union{Float64, Int64}})
Closest candidates are:
  supertype(!Matched::DataType) at operators.jl:41
  supertype(!Matched::UnionAll) at operators.jl:46
```

[](## Custom pretty-printing)
## 独自の出力整形


```@raw html
<!--
Often, one wants to customize how instances of a type are displayed.  This is accomplished by
overloading the [`show()`](@ref) function.  For example, suppose we define a type to represent
complex numbers in polar form:
-->
```

```jldoctest polartype
julia> struct Polar{T<:Real} <: Number
           r::T
           Θ::T
       end

julia> Polar(r::Real,Θ::Real) = Polar(promote(r,Θ)...)
Polar
```


```@raw html
<!--
Here, we've added a custom constructor function so that it can take arguments of different
[`Real`](@ref) types and promote them to a common type (see [Constructors](@ref man-constructors)
and [Conversion and Promotion](@ref conversion-and-promotion)).
(Of course, we would have to define lots of other methods, too, to make it act like a
[`Number`](@ref), e.g. `+`, `*`, `one`, `zero`, promotion rules and so on.) By default,
instances of this type display rather simply, with information about the type name and
the field values, as e.g. `Polar{Float64}(3.0,4.0)`.
-->
```


```@raw html
<!--
If we want it to display instead as `3.0 * exp(4.0im)`, we would define the following method to
print the object to a given output object `io` (representing a file, terminal, buffer, etcetera;
see [Networking and Streams](@ref)):
-->
```

```jldoctest polartype
julia> Base.show(io::IO, z::Polar) = print(io, z.r, " * exp(", z.Θ, "im)")
```


```@raw html
<!--
More fine-grained control over display of `Polar` objects is possible. In particular, sometimes
one wants both a verbose multi-line printing format, used for displaying a single object in the
REPL and other interactive environments, and also a more compact single-line format used for
[`print()`](@ref) or for displaying the object as part of another object (e.g. in an array). Although
by default the `show(io, z)` function is called in both cases, you can define a *different* multi-line
format for displaying an object by overloading a three-argument form of `show` that takes the
`text/plain` MIME type as its second argument (see [Multimedia I/O](@ref)), for example:
-->
```

```jldoctest polartype
julia> Base.show{T}(io::IO, ::MIME"text/plain", z::Polar{T}) =
           print(io, "Polar{$T} complex number:\n   ", z)
```


```@raw html
<!--
(Note that `print(..., z)` here will call the 2-argument `show(io, z)` method.) This results in:
-->
```

```jldoctest polartype
julia> Polar(3, 4.0)
Polar{Float64} complex number:
   3.0 * exp(4.0im)

julia> [Polar(3, 4.0), Polar(4.0,5.3)]
2-element Array{Polar{Float64},1}:
 3.0 * exp(4.0im)
 4.0 * exp(5.3im)
```


```@raw html
<!--
where the single-line `show(io, z)` form is still used for an array of `Polar` values.   Technically,
the REPL calls `display(z)` to display the result of executing a line, which defaults to `show(STDOUT, MIME("text/plain"), z)`,
which in turn defaults to `show(STDOUT, z)`, but you should *not* define new [`display()`](@ref)
methods unless you are defining a new multimedia display handler (see [Multimedia I/O](@ref)).
-->
```


```@raw html
<!--
Moreover, you can also define `show` methods for other MIME types in order to enable richer display
(HTML, images, etcetera) of objects in environments that support this (e.g. IJulia).   For example,
we can define formatted HTML display of `Polar` objects, with superscripts and italics, via:
-->
```

```jldoctest polartype
julia> Base.show{T}(io::IO, ::MIME"text/html", z::Polar{T}) =
           println(io, "<code>Polar{$T}</code> complex number: ",
                   z.r, " <i>e</i><sup>", z.Θ, " <i>i</i></sup>")
```


```@raw html
<!--
A `Polar` object will then display automatically using HTML in an environment that supports HTML
display, but you can call `show` manually to get HTML output if you want:
-->
```

```jldoctest polartype
julia> show(STDOUT, "text/html", Polar(3.0,4.0))
<code>Polar{Float64}</code> complex number: 3.0 <i>e</i><sup>4.0 <i>i</i></sup>
```

```@raw html
<p>An HTML renderer would display this as: <code>Polar{Float64}</code> complex number: 3.0 <i>e</i><sup>4.0 <i>i</i></sup></p>
```

[](## "Value types")
## "値型"


```@raw html
<!--
In Julia, you can't dispatch on a *value* such as `true` or `false`. However, you can dispatch
on parametric types, and Julia allows you to include "plain bits" values (Types, Symbols, Integers,
floating-point numbers, tuples, etc.) as type parameters.  A common example is the dimensionality
parameter in `Array{T,N}`, where `T` is a type (e.g., [`Float64`](@ref)) but `N` is just an `Int`.
-->
```


```@raw html
<!--
You can create your own custom types that take values as parameters, and use them to control dispatch
of custom types. By way of illustration of this idea, let's introduce a parametric type, `Val{T}`,
which serves as a customary way to exploit this technique for cases where you don't need a more
elaborate hierarchy.

`Val` is defined as:
-->
```

```jldoctest valtype
julia> struct Val{T}
       end
```


```@raw html
<!--
There is no more to the implementation of `Val` than this.  Some functions in Julia's standard
library accept `Val` types as arguments, and you can also use it to write your own functions.
 For example:
-->
```

```jldoctest valtype
julia> firstlast(::Type{Val{true}}) = "First"
firstlast (generic function with 1 method)

julia> firstlast(::Type{Val{false}}) = "Last"
firstlast (generic function with 2 methods)

julia> firstlast(Val{true})
"First"

julia> firstlast(Val{false})
"Last"
```


```@raw html
<!--
For consistency across Julia, the call site should always pass a `Val`*type* rather than creating
an *instance*, i.e., use `foo(Val{:bar})` rather than `foo(Val{:bar}())`.
-->
```


```@raw html
<!--
It's worth noting that it's extremely easy to mis-use parametric "value" types, including `Val`;
in unfavorable cases, you can easily end up making the performance of your code much *worse*.
 In particular, you would never want to write actual code as illustrated above.  For more information
about the proper (and improper) uses of `Val`, please read the more extensive discussion in [the performance tips](@ref man-performance-tips).
-->
```

[](## [Nullable Types: Representing Missing Values](@id man-nullable-types))
## [Null許容型: 欠損値の表現](@id man-nullable-types)


```@raw html
<!--
In many settings, you need to interact with a value of type `T` that may or may not exist. To
handle these settings, Julia provides a parametric type called [`Nullable{T}`](@ref), which can be thought
of as a specialized container type that can contain either zero or one values. `Nullable{T}` provides
a minimal interface designed to ensure that interactions with missing values are safe. At present,
the interface consists of several possible interactions:
-->
```


```@raw html
<!--
  * Construct a `Nullable` object.
  * Check if a `Nullable` object has a missing value.
  * Access the value of a `Nullable` object with a guarantee that a [`NullException`](@ref)
    will be thrown if the object's value is missing.
  * Access the value of a `Nullable` object with a guarantee that a default value of type
    `T` will be returned if the object's value is missing.
  * Perform an operation on the value (if it exists) of a `Nullable` object, getting a
    `Nullable` result. The result will be missing if the original value was missing.
  * Performing a test on the value (if it exists) of a `Nullable`
    object, getting a result that is missing if either the `Nullable`
    itself was missing, or the test failed.
  * Perform general operations on single `Nullable` objects, propagating the missing data.
-->
```

[](### Constructing [`Nullable`](@ref) objects)
### [`Null許容`](@ref) オブジェクトの生成


```@raw html
<!--
To construct an object representing a missing value of type `T`, use the `Nullable{T}()` function:
-->
```

```jldoctest
julia> x1 = Nullable{Int64}()
Nullable{Int64}()

julia> x2 = Nullable{Float64}()
Nullable{Float64}()

julia> x3 = Nullable{Vector{Int64}}()
Nullable{Array{Int64,1}}()
```


```@raw html
<!--
To construct an object representing a non-missing value of type `T`, use the `Nullable(x::T)`
function:
-->
```

```jldoctest
julia> x1 = Nullable(1)
Nullable{Int64}(1)

julia> x2 = Nullable(1.0)
Nullable{Float64}(1.0)

julia> x3 = Nullable([1, 2, 3])
Nullable{Array{Int64,1}}([1, 2, 3])
```


```@raw html
<!--
Note the core distinction between these two ways of constructing a `Nullable` object:
in one style, you provide a type, `T`, as a function parameter; in the other style, you provide
a single value of type `T` as an argument.
-->
```
[](### Checking if a `Nullable` object has a value)
### `Null許容`オブジェクトが値を持つかどうかを 検査する


```@raw html
<!--
You can check if a `Nullable` object has any value using [`isnull()`](@ref):
-->
```

```jldoctest
julia> isnull(Nullable{Float64}())
true

julia> isnull(Nullable(0.0))
false
```

[](### Safely accessing the value of a `Nullable` object)
### `Null許容`オブジェクトの値に安全にアクセスする

```@raw html
<!--
-->
```


```@raw html
<!--
You can safely access the value of a `Nullable` object using [`get()`](@ref):
-->
```

```jldoctest
julia> get(Nullable{Float64}())
ERROR: NullException()
Stacktrace:
 [1] get(::Nullable{Float64}) at ./nullable.jl:92

julia> get(Nullable(1.0))
1.0
```


```@raw html
<!--
If the value is not present, as it would be for `Nullable{Float64}`, a [`NullException`](@ref)
error will be thrown. The error-throwing nature of the `get()` function ensures that any
attempt to access a missing value immediately fails.
-->
```


```@raw html
<!--
In cases for which a reasonable default value exists that could be used when a `Nullable`
object's value turns out to be missing, you can provide this default value as a second argument
to `get()`:
-->
```

```jldoctest
julia> get(Nullable{Float64}(), 0.0)
0.0

julia> get(Nullable(1.0), 0.0)
1.0
```


```@raw html
<!--
!!! tip
    Make sure the type of the default value passed to `get()` and that of the `Nullable`
    object match to avoid type instability, which could hurt performance. Use [`convert()`](@ref)
    manually if needed.
-->
```

[](### Performing operations on `Nullable` objects)
### `Null許容`オブジェクトの値を効率よく操作する


```@raw html
<!--
`Nullable` objects represent values that are possibly missing, and it
is possible to write all code using these objects by first testing to see if
the value is missing with [`isnull()`](@ref), and then doing an appropriate
action. However, there are some common use cases where the code could be more
concise or clear by using a higher-order function.
-->
```


```@raw html
<!--
The [`map`](@ref) function takes as arguments a function `f` and a `Nullable` value
`x`. It produces a `Nullable`:

 - If `x` is a missing value, then it produces a missing value;
 - If `x` has a value, then it produces a `Nullable` containing
   `f(get(x))` as value.
-->
```


```@raw html
<!--
This is useful for performing simple operations on values that might be missing
if the desired behaviour is to simply propagate the missing values forward.

The [`filter`](@ref) function takes as arguments a predicate function `p`
(that is, a function returning a boolean) and a `Nullable` value `x`.
It produces a `Nullable` value:
-->
```


```@raw html
<!--
 - If `x` is a missing value, then it produces a missing value;
 - If `p(get(x))` is true, then it produces the original value `x`;
 - If `p(get(x))` is false, then it produces a missing value.

-->
```

```@raw html
<!--
In this way, `filter` can be thought of as selecting only allowable
values, and converting non-allowable values to missing values.
-->
```


```@raw html
<!--
While `map` and `filter` are useful in specific cases, by far the most useful
higher-order function is [`broadcast`](@ref), which can handle a wide variety of cases,
including making existing operations work and propagate `Nullable`s. An example
will motivate the need for `broadcast`. Suppose we have a function that computes the
greater of two real roots of a quadratic equation, using the quadratic formula:
-->
```

```jldoctest nullableroot
julia> root(a::Real, b::Real, c::Real) = (-b + √(b^2 - 4a*c)) / 2a
root (generic function with 1 method)
```


```@raw html
<!--
We may verify that the result of `root(1, -9, 20)` is `5.0`, as we expect,
since `5.0` is the greater of two real roots of the quadratic equation.
-->
```


```@raw html
<!--
Suppose now that we want to find the greatest real root of a quadratic
equations where the coefficients might be missing values. Having missing values
in datasets is a common occurrence in real-world data, and so it is important
to be able to deal with them. But we cannot find the roots of an equation if we
do not know all the coefficients. The best solution to this will depend on the
particular use case; perhaps we should throw an error. However, for this
example, we will assume that the best solution is to propagate the missing
values forward; that is, if any input is missing, we simply produce a missing
output.
-->
```


```@raw html
<!--
The `broadcast()` function makes this task easy; we can simply pass the
`root` function we wrote to `broadcast`:
-->
```

```jldoctest nullableroot
julia> broadcast(root, Nullable(1), Nullable(-9), Nullable(20))
Nullable{Float64}(5.0)

julia> broadcast(root, Nullable(1), Nullable{Int}(), Nullable{Int}())
Nullable{Float64}()

julia> broadcast(root, Nullable{Int}(), Nullable(-9), Nullable(20))
Nullable{Float64}()
```


```@raw html
<!--
If one or more of the inputs is missing, then the output of
`broadcast()` will be missing.

There exists special syntactic sugar for the `broadcast()` function
using a dot notation:
-->
```

```jldoctest nullableroot
julia> root.(Nullable(1), Nullable(-9), Nullable(20))
Nullable{Float64}(5.0)
```


```@raw html
<!--
In particular, the regular arithmetic operators can be `broadcast()`
conveniently using `.`-prefixed operators:
-->
```

```jldoctest
julia> Nullable(2) ./ Nullable(3) .+ Nullable(1.0)
Nullable{Float64}(1.66667)
```

[訳注1]:Juliaはver.0.5と0.6の間で型に関する用語を大きく変えており、この訳では`bit type`に`primitive type`の訳である原始型を当てています。