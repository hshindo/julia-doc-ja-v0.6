[](# [Constructors](@id man-constructors))
# [コンストラクタ](@id man-constructors)


```@raw html
<!--
Constructors [^1] are functions that create new objects -- specifically, instances of [Composite Types](@ref).
In Julia, type objects also serve as constructor functions: they create new instances of themselves
when applied to an argument tuple as a function. This much was already mentioned briefly when
composite types were introduced. For example:
-->
```

コンストラクタ[^ 1]は新しいオブジェクトを作成する関数で、特に[複合型](@ref)のインスタンスの場合に使用されます。
Juliaでは、型オブジェクトはコンストラクタ関数としても機能して、引数となるタプルに適用すると、新しいインスタンスが作成されます。
これは、複合型を紹介したときにすでに簡単に触れました。例えば、


```jldoctest footype
julia> struct Foo
           bar
           baz
       end

julia> foo = Foo(1, 2)
Foo(1, 2)

julia> foo.bar
1

julia> foo.baz
2
```


```@raw html
<!--
For many types, forming new objects by binding their field values together is all that is ever
needed to create instances. There are, however, cases where more functionality is required when
creating composite objects. Sometimes invariants must be enforced, either by checking arguments
or by transforming them. [Recursive data structures](https://en.wikipedia.org/wiki/Recursion_%28computer_science%29#Recursive_data_structures_.28structural_recursion.29),
especially those that may be self-referential, often cannot be constructed cleanly without first
being created in an incomplete state and then altered programmatically to be made whole, as a
separate step from object creation. Sometimes, it's just convenient to be able to construct objects
with fewer or different types of parameters than they have fields. Julia's system for object construction
addresses all of these cases and more.
-->
```

多くの型では、インスタンスを作成する際には、各フィールド値を束縛して新しいオブジェクトを作成します。
しかし、複合型のオブジェクトを作成するときにはより多くの機能が必要になる場合があります。
時には、引数を検査したり変換したりして、強制的に不変にする必要があります。
[再帰的なデータ構造](https://en.wikipedia.org/wiki/Recursion_%28computer_science%29#Recursive_data_structures_.28structural_recursion.29)、
特に自己参照可能な構造は、オブジェクト作成とは別の過程として、はじめは不完全な状態で作成してからプログラムによって変更して全体を作らないと、きれいに構築できないことがよくあります。
また時には、実際のフィールドと比べて、パラメータの数が少なかったり、型が異なっていたりするオブジェクトを構築できるのが便利な場合もあります。
Juliaのオブジェクトコンストラクタは、これらのすべてのケースに対応しています。




```@raw html
<!--
[^1]:
    Nomenclature: while the term "constructor" generally refers to the entire function which constructs
    objects of a type, it is common to abuse terminology slightly and refer to specific constructor
    methods as "constructors". In such situations, it is generally clear from context that the term
    is used to mean "constructor method" rather than "constructor function", especially as it is often
    used in the sense of singling out a particular method of the constructor from all of the others.
-->
```
^ 1]：命名法：「コンストラクタ」という用語は、通常、ある型に対して、その型のオブジェクトを構成する関数全体を指しますが、用語を少し拡大解釈し、特定のコンストラクタメソッドも「コンストラクタ」と呼びます。このようにしても、コンストラクタ関数ではなく、コンストラクタメソッド、特にほかのすべてのメソッドの中から特定のコンストラクタメソッドを単独で指して使っている場合は、通常は文脈から分かります

[](## Outer Constructor Methods)
## 外部コンストラクタメソッド



```@raw html
<!--
A constructor is just like any other function in Julia in that its overall behavior is defined
by the combined behavior of its methods. Accordingly, you can add functionality to a constructor
by simply defining new methods. For example, let's say you want to add a constructor method for
`Foo` objects that takes only one argument and uses the given value for both the `bar` and `baz`
fields. This is simple:
-->
```
コンストラクタは、Juliaの他の関数と同じように、全体の挙動はメソッドの挙動の組み合わせで定義されています。
したがって、新しいメソッドを定義するだけで、コンストラクタに機能を追加できます。
たとえば、`Foo`オブジェクトのコンストラクタメソッドを追加するとします。
このオブジェクトは、1個だけの引数の値を、`bar`フィールドと`baz`フィールドの両方に使用する単純なものです。


```jldoctest footype
julia> Foo(x) = Foo(x,x)
Foo

julia> Foo(1)
Foo(1, 1)
```


```@raw html
<!--
You could also add a zero-argument `Foo` constructor method that supplies default values for both
of the `bar` and `baz` fields:
-->
```
また、引数のない`Foo`コンストラクタメソッドを追加して、`bar`フィールドと`baz`フィールドの両方にデフォルト値を設定することもできます。

```jldoctest footype
julia> Foo() = Foo(0)
Foo

julia> Foo()
Foo(0, 0)
```


```@raw html
<!--
Here the zero-argument constructor method calls the single-argument constructor method, which
in turn calls the automatically provided two-argument constructor method. For reasons that will
become clear very shortly, additional constructor methods declared as normal methods like this
are called *outer* constructor methods. Outer constructor methods can only ever create a new instance
by calling another constructor method, such as the automatically provided default ones.
-->
```
ここでは、引数のないコンストラクタメソッドが、1引数のコンストラクタメソッドを呼び出し、続いて、自動的に生成される2引数のコンストラクタメソッドが呼び出されます。
まもなく明らかにする理由から、このような通常のメソッドのように宣言して追加されたコンストラクタメソッドは、**外部**コンストラクタメソッドと呼ばれます。
外部コンストラクタメソッドは、デフォルト値を自動的に提供するメソッドなど、別の既存のコンストラクタメソッドを呼び出す場合に限って、新しいインスタンスを作成することができます。


[](## Inner Constructor Methods)
## 内部コンストラクタメソッド


```@raw html
<!--
While outer constructor methods succeed in addressing the problem of providing additional convenience
methods for constructing objects, they fail to address the other two use cases mentioned in the
introduction of this chapter: enforcing invariants, and allowing construction of self-referential
objects. For these problems, one needs *inner* constructor methods. An inner constructor method
is much like an outer constructor method, with two differences:

1. It is declared inside the block of a type declaration, rather than outside of it like normal methods.
2. It has access to a special locally existent function called `new` that creates objects of the
   block's type.
-->
```
外部コンストラクタメソッドは、オブジェクトを構築するための追加の便利なメソッドを提供するという問題に対処するのに成功しますが、
この章の導入で言及された他の2つのユースケースには対処できません：
不変性の設定と自己参照オブジェクトの構築 これらの問題に対して、内部コンストラクタメソッドが必要です。
内部コンストラクタメソッドは、外部コンストラクタメソッドによく似ていますが、2つの違いがあります。

1. これは型宣言のブロックの中で宣言されていて、通常のメソッドのようにそのブロックの外側で宣言されていません。
2. ブロックの型のオブジェクトを作成する、ローカルに存在する特殊な関数`new`へのアクセスできます。



```@raw html
<!--
For example, suppose one wants to declare a type that holds a pair of real numbers, subject to
the constraint that the first number is not greater than the second one. One could declare it
like this:
-->
```
たとえば、第1の数が第2の数よりも大きくないという制約をつけて、実数のペアを保持する型を宣言したいとします。
これは次のように宣言することができます：

```jldoctest pairtype
julia> struct OrderedPair
           x::Real
           y::Real
           OrderedPair(x,y) = x > y ? error("out of order") : new(x,y)
       end

```


```@raw html
<!--
Now `OrderedPair` objects can only be constructed such that `x <= y`:
-->
```

ここで、`OrderedPair`オブジェクトは、`x <= y`を満たすように構成することができます。


```jldoctest pairtype
julia> OrderedPair(1, 2)
OrderedPair(1, 2)

julia> OrderedPair(2,1)
ERROR: out of order
Stacktrace:
 [1] OrderedPair(::Int64, ::Int64) at ./none:4
```


```@raw html
<!--
If the type were declared `mutable`, you could reach in and directly change the field values to
violate this invariant, but messing around with an object's internals uninvited is considered poor form.
You (or someone else) can also provide additional outer constructor methods at any later point, but
once a type is declared, there is no way to add more inner constructor methods. Since outer constructor
methods can only create objects by calling other constructor methods, ultimately, some inner constructor
must be called to create an object. This guarantees that all objects of the declared type must come into
existence by a call to one of the inner constructor methods provided with the type, thereby giving
some degree of enforcement of a type's invariants.
-->
```
型を`可変`に宣言していれば、フィールド値に直接アクセスして、不変性を破るような変更をすることができますが、オブジェクトの内部に余計なことをしてめちゃくちゃにするのは悪い方法です。
あなた（または他の誰か）は後で外部コンストラクタメソッドを追加することもできますが、型が宣言されると内部コンストラクタメソッドを追加する方法はありません。
外側のコンストラクタメソッドは他のコンストラクタメソッドを呼び出すことによってのみオブジェクトを作成できるため、最終的には、内部コンストラクタを呼び出してオブジェクトを作成する必要があります。
このため、型の宣言されたすべてのオブジェクトが、その型に備わった内部コンストラクタメソッドの1つから生成されることが保証され、型の不変性がある程度強制されます。


```@raw html
<!--
If any inner constructor method is defined, no default constructor method is provided: it is presumed
that you have supplied yourself with all the inner constructors you need. The default constructor
is equivalent to writing your own inner constructor method that takes all of the object's fields
as parameters (constrained to be of the correct type, if the corresponding field has a type),
and passes them to `new`, returning the resulting object:
-->
```
内部コンストラクタメソッドが定義されている場合は、デフォルトのコンストラクタメソッドは提供されません。
必要とするすべての内部コンストラクタはすべて自作することを前提としています。
デフォルトのコンストラクタは、すべてのオブジェクトのフィールドをパラメータ（対応するフィールドに型がある場合は正しい型に制約されている）として取り込み、それらを`new`に渡して、結果のオブジェクトを返す独自の内部コンストラクタメソッドを作成するのと同じです。

```jldoctest
julia> struct Foo
           bar
           baz
           Foo(bar,baz) = new(bar,baz)
       end

```


```@raw html
<!--
This declaration has the same effect as the earlier definition of the `Foo` type without an explicit
inner constructor method. The following two types are equivalent -- one with a default constructor,
the other with an explicit constructor:
-->
```

この宣言は、明示的な内部コンストラクタメソッドを持たない`Foo`型の以前の定義と同じ効果を持ちます。
以下の2つの型は同等です。一つはデフォルトのコンストラクタを持ち、もう一つは明示的なコンストラクタを持っています。


```jldoctest
julia> struct T1
           x::Int64
       end

julia> struct T2
           x::Int64
           T2(x) = new(x)
       end

julia> T1(1)
T1(1)

julia> T2(1)
T2(1)

julia> T1(1.0)
T1(1)

julia> T2(1.0)
T2(1)
```


```@raw html
<!--
It is considered good form to provide as few inner constructor methods as possible: only those
taking all arguments explicitly and enforcing essential error checking and transformation. Additional
convenience constructor methods, supplying default values or auxiliary transformations, should
be provided as outer constructors that call the inner constructors to do the heavy lifting. This
separation is typically quite natural.
-->
```

できるだけ内部コンストラクタメソッドを少なくするのは良い方法だと考えられています。
すべての引数を明示的に取り、必須のエラーチェックと変換を強制するメソッドのみにします。
デフォルト値や補助的な変換を提供する便利なコンストラクタメソッドを追加する時は、外部コンストラクタとして作成し、内部コンストラクタを呼び出して重い作業を行うようにすべきです。
このように分離するのは、一般的には非常に自然なものです。



[](## Incomplete Initialization)
## 不完全な初期化


```@raw html
<!--
The final problem which has still not been addressed is construction of self-referential objects,
or more generally, recursive data structures. Since the fundamental difficulty may not be immediately
obvious, let us briefly explain it. Consider the following recursive type declaration:
-->
```

まだ話の出ていない最後の問題は、自己参照オブジェクト、さらに一般的には再帰的なデータ構造の生成です。
根源的な難しさはすぐには分からないでしょうから、簡単に説明しましょう。次の再帰型宣言を考えてみましょう。


```jldoctest selfrefer
julia> mutable struct SelfReferential
           obj::SelfReferential
       end

```


```@raw html
<!--
This type may appear innocuous enough, until one considers how to construct an instance of it.
If `a` is an instance of `SelfReferential`, then a second instance can be created by the call:
-->
```

この型は、どうやってインスタンス化するかを考えるなければ、問題なく見えるかもしれません。
`a`が`SelfReferential`のインスタンスであれば、呼び出しによって2つ目のインスタンスを作成できます。


```julia-repl
julia> b = SelfReferential(a)
```


```@raw html
<!--
But how does one construct the first instance when no instance exists to provide as a valid value
for its `obj` field? The only solution is to allow creating an incompletely initialized instance
of `SelfReferential` with an unassigned `obj` field, and using that incomplete instance as a valid
value for the `obj` field of another instance, such as, for example, itself.
-->
```
しかし、`obj`フィールドに有効な値として代入するインスタンスが存在しない場合、最初のインスタンスはどうやって作成するのでしょうか？
唯一の解決策は、`obj`フィールドに代入のない初期化の不完全な`SelfReferential`のインスタンスを作成し、別のインスタンス(例えば自分自身)の`obj`フィールドの有効な値としてその不完全なインスタンスを使用することです。



```@raw html
<!--
To allow for the creation of incompletely initialized objects, Julia allows the `new` function
to be called with fewer than the number of fields that the type has, returning an object with
the unspecified fields uninitialized. The inner constructor method can then use the incomplete
object, finishing its initialization before returning it. Here, for example, we take another crack
at defining the `SelfReferential` type, with a zero-argument inner constructor returning instances
having `obj` fields pointing to themselves:
-->
```
初期化の不完全なオブジェクトを作成できるように、Juliaは、引数が型のフィールド数より少なくても`new`関数を呼び出すことができます。
この`new`関数は、未指定のフィールドは初期化されていないままで、オブジェクトが返します。
そして、内部コンストラクタメソッドは不完全なオブジェクトを利用できて、初期化を完了してからオブジェクトを返します。
ここでは、`SelfReferential`型を定義する際にもう1つの亀裂があります。
引数のない内部コンストラクタは、`obj`フィールドが自身を指すインスタンスを返します。


```jldoctest selfrefer2
julia> mutable struct SelfReferential
           obj::SelfReferential
           SelfReferential() = (x = new(); x.obj = x)
       end

```


```@raw html
<!--
We can verify that this constructor works and constructs objects that are, in fact, self-referential:
-->
```
このコンストラクタが動作していることを検証して、実は自己参照型のオブジェクトを作成できます。


```jldoctest selfrefer2
julia> x = SelfReferential();

julia> x === x
true

julia> x === x.obj
true

julia> x === x.obj.obj
true
```


```@raw html
<!--
Although it is generally a good idea to return a fully initialized object from an inner constructor,
incompletely initialized objects can be returned:
-->
```
内部コンストラクタが完全に初期化されたオブジェクトを返すことは一般的には良い考えですが、初期化の不完全なオブジェクトを返すこともできます。


```jldoctest incomplete
julia> mutable struct Incomplete
           xx
           Incomplete() = new()
       end

julia> z = Incomplete();
```


```@raw html
<!--
While you are allowed to create objects with uninitialized fields, any access to an uninitialized
reference is an immediate error:
-->
```

フィールドの初期化されていないオブジェクトを作成することはできますが、参照が初期化されていないのにアクセスすると、即刻エラーが生じます。

```jldoctest incomplete
julia> z.xx
ERROR: UndefRefError: access to undefined reference
```


```@raw html
<!--
This avoids the need to continually check for `null` values. However, not all object fields are
references. Julia considers some types to be "plain data", meaning all of their data is self-contained
and does not reference other objects. The plain data types consist of primitive types (e.g. `Int`)
and immutable structs of other plain data types. The initial contents of a plain data type is
undefined:
-->
```

このため、`null`値を継続的にチェックする必要がなくなります。
ただし、すべてのオブジェクトフィールドが参照であるとは限りません。
Juliaは、いくつかの型を「プレーンデータ」とみなします。
つまり、すべてのデータが自己完結型であり、他のオブジェクトを参照していないことを意味します。
プレーンデータ型は、原始型（例Int：）と他のプレーンデータ型の不変な複合型から構成されます。
プレーンなデータ型は、最初の内容は未定義です：


```julia-repl
julia> struct HasPlain
           n::Int
           HasPlain() = new()
       end

julia> HasPlain()
HasPlain(438103441441)
```


```@raw html
<!--
Arrays of plain data types exhibit the same behavior.

You can pass incomplete objects to other functions from inner constructors to delegate their completion:
-->
```
プレーンデータ型の配列は同じ動作を示します。

不完全なオブジェクトを内部コンストラクタによって他の関数​​に渡し、その完了を委任することができます。


```jldoctest
julia> mutable struct Lazy
           xx
           Lazy(v) = complete_me(new(), v)
       end
```


```@raw html
<!--
As with incomplete objects returned from constructors, if `complete_me` or any of its callees
try to access the `xx` field of the `Lazy` object before it has been initialized, an error will
be thrown immediately.
-->
```
コンストラクタから返される不完全なオブジェクトと同じように、`complete_me`など呼び出し先が初期化する前に`Lazy`オブジェクトの`xx`フィールドにアクセスしようとすると、即座にエラーが投げられます。

[](## Parametric Constructors)
## パラメトリックコンストラクタ


```@raw html
<!--
Parametric types add a few wrinkles to the constructor story. Recall from [Parametric Types](@ref)
that, by default, instances of parametric composite types can be constructed either with explicitly
given type parameters or with type parameters implied by the types of the arguments given to the
constructor. Here are some examples:
-->
```
パラメトリック型は、コンストラクタのストーリーにいくつかの妙案を付け加えます。
[パラメトリック型](@ref) を思い出してください。
デフォルトでは、パラメトリック複合型をインスタンス化する際の型の指定は、明示的に型パラメータを使うこともできるし、引数から暗黙的に推論させることもできます。
いくつか例を示します。


```jldoctest parametric
julia> struct Point{T<:Real}
           x::T
           y::T
       end

julia> Point(1,2) ## implicit T ##
Point{Int64}(1, 2)

julia> Point(1.0,2.5) ## implicit T ##
Point{Float64}(1.0, 2.5)

julia> Point(1,2.5) ## implicit T ##
ERROR: MethodError: no method matching Point(::Int64, ::Float64)
Closest candidates are:
  Point(::T<:Real, !Matched::T<:Real) where T<:Real at none:2

julia> Point{Int64}(1, 2) ## explicit T ##
Point{Int64}(1, 2)

julia> Point{Int64}(1.0,2.5) ## explicit T ##
ERROR: InexactError()
Stacktrace:
 [1] convert(::Type{Int64}, ::Float64) at ./float.jl:679
 [2] Point{Int64}(::Float64, ::Float64) at ./none:2

julia> Point{Float64}(1.0, 2.5) ## explicit T ##
Point{Float64}(1.0, 2.5)

julia> Point{Float64}(1,2) ## explicit T ##
Point{Float64}(1.0, 2.0)
```


```@raw html
<!--
As you can see, for constructor calls with explicit type parameters, the arguments are converted
to the implied field types: `Point{Int64}(1,2)` works, but `Point{Int64}(1.0,2.5)` raises an
[`InexactError`](@ref) when converting `2.5` to [`Int64`](@ref). When the type is implied
by the arguments to the constructor call, as in `Point(1,2)`, then the types of the
arguments must agree -- otherwise the `T` cannot be determined -- but any pair of real
arguments with matching type may be given to the generic `Point` constructor.
-->
```
御覧のように、コンストラクタを明示的に型パラメータを指定して呼び出すと、引数は暗黙的にフィールドの型に変換されます。
`Point{Int64}(1,2)`とすると動作しますが、`Point{Int64}(1.0,2.5)`とすると、 `2.5` を [`Int64`](@ref) に変換する際に、
[`InexactError`](@ref) が発生します。
型が`Point(1,2)`のようにコンストラクタ呼び出しの引数によって暗黙的に指定されている場合、引数の型どうしは一致する必要があります。
そうでなければ、`T`を決定できません。任意の実引数のペアは、型が一致していれば、汎化`Point`コンストラクタに渡すことができます。

`

```@raw html
<!--
What's really going on here is that `Point`, `Point{Float64}` and `Point{Int64}` are all different
constructor functions. In fact, `Point{T}` is a distinct constructor function for each type `T`.
Without any explicitly provided inner constructors, the declaration of the composite type `Point{T<:Real}`
automatically provides an inner constructor, `Point{T}`, for each possible type `T<:Real`, that
behaves just like non-parametric default inner constructors do. It also provides a single general
outer `Point` constructor that takes pairs of real arguments, which must be of the same type.
This automatic provision of constructors is equivalent to the following explicit declaration:
-->
```

ここで実際に見てきたことは、`Point`、`Point{Float64}`、`Point{Int64}`すべてが異なるコンストラクタ関数だということです。
実際、`Point{T}`は、それぞれの型`T`ごとに異なるコンストラクタ関数があります。
明示的に内部コンストラクタを定義しなければ、複合型の宣言`Point{T<:Real}`は 、自動的に内部コンストラクタ`Point{T}`を作成し、`T<:Real`を満たすそれぞれの型に対して、パラメータを使わない内部コンストラクタのように振る舞います。
また、型のそろった実引数のペアをとる単一の汎化外部コンストラクタ`Point`も作成します。
このコンストラクタの自動生成は、次の明示的宣言と同じです。


```jldoctest parametric2
julia> struct Point{T<:Real}
           x::T
           y::T
           Point{T}(x,y) where {T<:Real} = new(x,y)
       end

julia> Point(x::T, y::T) where {T<:Real} = Point{T}(x,y);
```


```@raw html
<!--
Notice that each definition looks like the form of constructor call that it handles.
The call `Point{Int64}(1,2)` will invoke the definition `Point{T}(x,y)` inside the
`type` block.
The outer constructor declaration, on the other hand, defines a
method for the general `Point` constructor which only applies to pairs of values of the same real
type. This declaration makes constructor calls without explicit type parameters, like `Point(1,2)`
and `Point(1.0,2.5)`, work. Since the method declaration restricts the arguments to being of the
same type, calls like `Point(1,2.5)`, with arguments of different types, result in "no method"
errors.
-->
```
各コンストラクタの定義と、それを呼び出す際の形式はように見えることに注意してください。
`Point{Int64}(1,2)`のように呼び出すと、`型`ブロックの中では、定義の`Point{T}(x,y)`が呼び出されます。
一方、外部コンストラクタ宣言は、実数の同じ型の組にのみ適用される汎化コンストラクタ`Point`のメソッドを定義します。
この宣言は、`Point(1,2)` や`Point(1.0,2.5)`のように型パラメータのないコンストラクタ呼び出しを行います。
このメソッドの宣言では、引数が同じ型のものに制限されるため、`Point(1,2.5)`のような異なる型の引数を持つ呼び出しは、 "no method"のエラーがおこります。



```@raw html
<!--
Suppose we wanted to make the constructor call `Point(1,2.5)` work by "promoting" the integer
value `1` to the floating-point value `1.0`. The simplest way to achieve this is to define the
following additional outer constructor method:
-->
```
`Point(1,2.5)`を整数値`1`を浮動小数点値`1.0`に「昇格」させて、コンストラクタ呼び出しとして動作させたいとします。
これを実現する最も簡単な方法は、以下の外部コンストラクタメソッドの定義を追加することです。


```jldoctest parametric2
julia> Point(x::Int64, y::Float64) = Point(convert(Float64,x),y);
```


```@raw html
<!--
This method uses the [`convert()`](@ref) function to explicitly convert `x` to [`Float64`](@ref)
and then delegates construction to the general constructor for the case where both arguments are
[`Float64`](@ref). With this method definition what was previously a [`MethodError`](@ref) now
successfully creates a point of type `Point{Float64}`:
-->
```
このメソッドは、[`convert()`](@ref)関数を使用して 、 `x` を [`Float64`](@ref) に変換し、両方の引数が[`Float64`](@ref)の時に使用できる汎化コンストラクタに委譲します。
こうして、以前は[`MethodError`](@ref)の生じたメソッド定義から、今までは`Point{Float64}`型を正常に作成できるようになりました。


```jldoctest parametric2
julia> Point(1,2.5)
Point{Float64}(1.0, 2.5)

julia> typeof(ans)
Point{Float64}
```


```@raw html
<!--
However, other similar calls still don't work:
-->
```
しかし、他の似たような呼び出しはまだ動作しません：


```jldoctest parametric2
julia> Point(1.5,2)
ERROR: MethodError: no method matching Point(::Float64, ::Int64)
Closest candidates are:
  Point(::T<:Real, !Matched::T<:Real) where T<:Real at none:1
```


```@raw html
<!--
For a more general way to make all such calls work sensibly, see [Conversion and Promotion](@ref conversion-and-promotion).
At the risk of spoiling the suspense, we can reveal here that all it takes is the following outer
method definition to make all calls to the general `Point` constructor work as one would expect:
-->
```
このような呼び出しをうまく動作させる一般的な方法については、[Conversion and Promotion]（@ ref conversion-and-promotion）を参照してください。
今までの話が無駄になるかもしれませんが、白状してしまうと、汎化コンストラクタ`Point`を様々な引数に対して期待どおりに動作させるには、外部メソッドの定義を以下のようにすればいいのです。


```jldoctest parametric2
julia> Point(x::Real, y::Real) = Point(promote(x,y)...);
```


```@raw html
<!--
The `promote` function converts all its arguments to a common type -- in this case [`Float64`](@ref).
With this method definition, the `Point` constructor promotes its arguments the same way that
numeric operators like [`+`](@ref) do, and works for all kinds of real numbers:
-->
```
この`promote`関数はすべての引数を共通の型、この場合は [`Float64`](@ref)に変換します。
このメソッド定義では、`Point`コンストラクタは、算術演算子の [`+`](@ref)と同じように引数を昇格するので、あらゆる種類の実数に対して動作します。


```jldoctest parametric2
julia> Point(1.5,2)
Point{Float64}(1.5, 2.0)

julia> Point(1,1//2)
Point{Rational{Int64}}(1//1, 1//2)

julia> Point(1.0,1//2)
Point{Float64}(1.0, 0.5)
```


```@raw html
<!--
Thus, while the implicit type parameter constructors provided by default in Julia are fairly strict,
it is possible to make them behave in a more relaxed but sensible manner quite easily. Moreover,
since constructors can leverage all of the power of the type system, methods, and multiple dispatch,
defining sophisticated behavior is typically quite simple.
-->
```
したがって、Juliaのデフォルトでは、型パラメータコンストラクタの暗黙的な型の扱いはかなり厳格ですが、それらをより気軽で、しかし理にかなった方法で動作させることは可能です。
さらに、コンストラクタは型システム、メソッド、および多重ディスパッチのすべての機能を活用できるため、洗練された動作を定義するのは通常非常に簡単です。

[](## Case Study: Rational)
## 事例研究: 有理数



```@raw html
<!--
Perhaps the best way to tie all these pieces together is to present a real world example of a
parametric composite type and its constructor methods. To that end, here is the (slightly modified) beginning of [`rational.jl`](https://github.com/JuliaLang/julia/blob/master/base/rational.jl),
which implements Julia's [有理数](@ref):
-->
```
おそらく、これらの要素すべてを結びつける最良の方法は、パラメトリック複合型とそのコンストラクタメソッドの実例を見てみることです。
そこで、 [`rational.jl`](https://github.com/JuliaLang/julia/blob/master/base/rational.jl)　の始めの方でJuliaで使われる[有理数](@ref)を実装している部分を（少し修正していますが）見てみましょう。


```jldoctest rational
julia> struct OurRational{T<:Integer} <: Real
           num::T
           den::T
           function OurRational{T}(num::T, den::T) where T<:Integer
               if num == 0 && den == 0
                    error("invalid rational: 0//0")
               end
               g = gcd(den, num)
               num = div(num, g)
               den = div(den, g)
               new(num, den)
           end
       end

julia> OurRational(n::T, d::T) where {T<:Integer} = OurRational{T}(n,d)
OurRational

julia> OurRational(n::Integer, d::Integer) = OurRational(promote(n,d)...)
OurRational

julia> OurRational(n::Integer) = OurRational(n,one(n))
OurRational

julia> //(n::Integer, d::Integer) = OurRational(n,d)
// (generic function with 1 method)

julia> //(x::OurRational, y::Integer) = x.num // (x.den*y)
// (generic function with 2 methods)

julia> //(x::Integer, y::OurRational) = (x*y.den) // y.num
// (generic function with 3 methods)

julia> //(x::Complex, y::Real) = complex(real(x)//y, imag(x)//y)
// (generic function with 4 methods)

julia> //(x::Real, y::Complex) = x*y'//real(y*y')
// (generic function with 5 methods)

julia> function //(x::Complex, y::Complex)
           xy = x*y'
           yy = real(y*y')
           complex(real(xy)//yy, imag(xy)//yy)
       end
// (generic function with 6 methods)
```


```@raw html
<!--
The first line -- `struct OurRational{T<:Integer} <: Real` -- declares that `OurRational` takes one
type parameter of an integer type, and is itself a real type. The field declarations `num::T`
and `den::T` indicate that the data held in a `OurRational{T}` object are a pair of integers of type
`T`, one representing the rational value's numerator and the other representing its denominator.
-->
```
最初の行 の`struct OurRational{T<:Integer} <: Real` では、`OurRational`という型は、整数型の1つの型パラメータをとり、それ自体は実数型であることを宣言しています。
フィールドの宣言である`num::T` と`den::T`は、`OurRational{T}`オブジェクトに保持されているデータが、有理数の分子と分母を表す型`T`の 整数のペアであることを示しています。



```@raw html
<!--
Now things get interesting. `OurRational` has a single inner constructor method which checks that
both of `num` and `den` aren't zero and ensures that every rational is constructed in "lowest
terms" with a non-negative denominator. This is accomplished by dividing the given numerator and
denominator values by their greatest common divisor, computed using the `gcd` function. Since
`gcd` returns the greatest common divisor of its arguments with sign matching the first argument
(`den` here), after this division the new value of `den` is guaranteed to be non-negative. Because
this is the only inner constructor for `OurRational`, we can be certain that `OurRational` objects are
always constructed in this normalized form.
-->
```
面白くなってきました。
`OurRational`には内部コンストラクタメソッドが1つあって、`num`と`den`の両方が0ではないことを検査し、すべての有理数は、分母が非負の「既約分数」で構成されることを保証します。
これは、与えられた分子と分母の値を、最大公約数で割ることで得られ、最大公約数は`gcd`関数を使って計算されます。
`gcd`関数は、引数の最大公約数を最初の引数（ここでは`den`）に一致する符号で返すため、
割り算の後に得られる新しい`den`の値は非負であることが保証されます。
これは`OurRational`の唯一の内部コンストラクタであるため、`OurRational`オブジェクトは常にこの正規化された形式で構築されていると断言できます。



```@raw html
<!--
`OurRational` also provides several outer constructor methods for convenience. The first is the "standard"
general constructor that infers the type parameter `T` from the type of the numerator and denominator
when they have the same type. The second applies when the given numerator and denominator values
have different types: it promotes them to a common type and then delegates construction to the
outer constructor for arguments of matching type. The third outer constructor turns integer values
into rationals by supplying a value of `1` as the denominator.
-->
```
`OurRational`は利便性のためにいくつかの外部コンストラクタメソッドも提供しています。
一つ目は、分子と分母の型が同じ場合、その型から型パラメータ`T`を推論する「標準」汎化コンストラクタです。
二つ目は、与えられた分子と分母の値の型が異なる場合に適用されます。
それらを共通の型に昇格させ、その後、型の一致する引数に対して動作する外部コンストラクタに生成を委譲します。
三つ目の外部コンストラクタは、分母としての値`1`を供給することによって整数値を有理数に変換します。



```@raw html
<!--
Following the outer constructor definitions, we have a number of methods for the [`//`](@ref)
operator, which provides a syntax for writing rationals. Before these definitions, [`//`](@ref)
is a completely undefined operator with only syntax and no meaning. Afterwards, it behaves just
as described in [有理数](@ref) -- its entire behavior is defined in these few lines.
The first and most basic definition just makes `a//b` construct a `OurRational` by applying the
`OurRational` constructor to `a` and `b` when they are integers. When one of the operands of [`//`](@ref)
is already a rational number, we construct a new rational for the resulting ratio slightly differently;
this behavior is actually identical to division of a rational with an integer.
Finally, applying
[`//`](@ref) to complex integral values creates an instance of `Complex{OurRational}` -- a complex
number whose real and imaginary parts are rationals:
-->
```
外側のコンストラクタ定義に続いて、[`//`](@ref) 演算子のための多数のメソッドがあります。これは、有理数を書くための構文です。
こういった定義がなければ、[`//`](@ref) は、構文だけで完全に意味のない未定義の演算子です。
その後、 [有理数](@ref)説明したような動作をします。その動作は全部、このファイルの数行で定義されています。
第一の、そして最も基本的な定義は、`a`と`b`が整数の時に、これらに`OurRational`コンストラクタを適用して`a//b`を生成します。
 [`//`](@ref)の被演算子の1つが既に有理数である場合、少し違って新しい有理数を比の結果として生成します。
この動作は実際には有理数と整数の除算と同一です。
最後に、 [`//`](@ref)を複素有理数に適用して、`Complex{OurRational}`のインスタンスを作成します。
これは実部と虚部が有理数である複素数をあらわします。

```jldoctest rational
julia> ans = (1 + 2im)//(1 - 2im);

julia> typeof(ans)
Complex{OurRational{Int64}}

julia> ans <: Complex{OurRational}
false
```


```@raw html
<!--
Thus, although the [`//`](@ref) operator usually returns an instance of `OurRational`, if either
of its arguments are complex integers, it will return an instance of `Complex{OurRational}` instead.
The interested reader should consider perusing the rest of [`rational.jl`](https://github.com/JuliaLang/julia/blob/master/base/rational.jl):
it is short, self-contained, and implements an entire basic Julia type.
-->
```
したがって、[`//`](@ref)演算子は通常`OurRational`のインスタンスを返しますが、引数のいずれかが複素数の場合、替わりに`Complex{OurRational}`のインスタンスを返します。
興味のある読者は、[`rational.jl`](https://github.com/JuliaLang/julia/blob/master/base/rational.jl)の残りの部分を熟読するといいでしょう。
短くてファイル内で参照が完結し、Juliaの基本的な型である有理数型全体が実装されています。

[](## [Constructors and Conversion](@id constructors-and-conversion))
## [コンストラクタと変換](@id constructors-and-conversion)


```@raw html
<!--
Constructors `T(args...)` in Julia are implemented like other callable objects: methods are added
to their types. The type of a type is `Type`, so all constructor methods are stored in the method
table for the `Type` type. This mans that you can declare more flexible constructors, e.g. constructors
for abstract types, by explicitly defining methods for the appropriate types.
-->
```
Juliaでは、`T(args...)`というコンストラクタは、他の呼び出し可能なオブジェクトと同じように実装されていて、引数の組の型に対して
メソッドが追加されます。
型の型は`Type`であり、すべてのコンストラクタのメソッドは`Type`型のメソッド・テーブルに格納されます。
つまり、適切な型に対するメソッドを明示的に定義することで、抽象型のコンストラクタなど、より柔軟にコンストラクタを宣言することができます。

```@raw html
<!--
However, in some cases you could consider adding methods to `Base.convert` *instead* of defining
a constructor, because Julia falls back to calling [`convert()`](@ref) if no matching constructor
is found. For example, if no constructor `T(args...) = ...` exists `Base.convert(::Type{T}, args...) = ...`
is called.
-->
```
ただし、コンストラクタを定義する** 替わりに**、`Base.convert`メソッドを追加を考慮したほうがいい場合もあります。
Juliaでは、一致するコンストラクタが見つからない場合には、[`convert()`](@ref) が呼ばれるからです。
たとえば、コンストラクタ`T(args...) = ...`が存在しない場合は、`Base.convert(::Type{T}, args...) = ...` が呼び出されます。



```@raw html
<!--
`convert` is used extensively throughout Julia whenever one type needs to be converted to another
(e.g. in assignment, [`ccall`](@ref), etcetera), and should generally only be defined (or successful)
if the conversion is lossless.  For example, `convert(Int, 3.0)` produces `3`, but `convert(Int, 3.2)`
throws an `InexactError`.  If you want to define a constructor for a lossless conversion from
one type to another, you should probably define a `convert` method instead.
-->
```
Julia全体で、`convert`はあるタイプを別のタイプに変換する必要があるとき（たとえば、代入や[`ccall`](@ref)など）広く使用されます、
一般的には、変換が可逆の時のみ定義すべきで、そうした時のみうまくいくでしょう。
たとえば、`convert(Int, 3.0)`は`3`を生成しますが、`convert(Int, 3.2)`は `InexactError`を投げます。
あるタイプから別のタイプへの可逆変換をするコンストラクタを定義したい場合は、おそらく`convert`メソッドを定義する必要があるでしょう。



```@raw html
<!--
On the other hand, if your constructor does not represent a lossless conversion, or doesn't represent
"conversion" at all, it is better to leave it as a constructor rather than a `convert` method.
For example, the `Array{Int}()` constructor creates a zero-dimensional `Array` of the type `Int`,
but is not really a "conversion" from `Int` to an `Array`.
-->
```
一方、コンストラクタが可逆変換ではない場合、または「変換」を全くつかわない場合は、`convert`メソッドではなくコンストラクタとして残す方がよいでしょう。
例えば、`Array{Int}()`コンストラクタはゼロ次元の`Int`型の`Array`を生成します。しかしこれは、`Int`から`Array`への「変換」ではありません。

[](## Outer-only constructors)

## 外部限定コンストラクタ


```@raw html
<!--
As we have seen, a typical parametric type has inner constructors that are called when type parameters
are known; e.g. they apply to `Point{Int}` but not to `Point`. Optionally, outer constructors
that determine type parameters automatically can be added, for example constructing a `Point{Int}`
from the call `Point(1,2)`. Outer constructors call inner constructors to do the core work of
making an instance. However, in some cases one would rather not provide inner constructors, so
that specific type parameters cannot be requested manually.
-->
```
これまで見てきたように、一般的なパラメトリック型には、型パラメータが既知のときに呼び出される内部コンストラクタがあります。
例えば、`Point`ではなく`Point{Int}`が適用されます。
必要に応じて、型パラメータを自動的に決定する外部コンストラクタを追加することができます。例えば、`Point(1,2)`から`Point{Int}`を呼び出すことができます。
外部のコンストラクタは内部のコンストラクタを呼び出して、インスタンスを作成する中核的な作業を行います。
しかし、場合によっては内部コンストラクタが存在せず、特定の型パラメータを手動で要求することはできません。



```@raw html
<!--
For example, say we define a type that stores a vector along with an accurate representation of
its sum:
-->
```
たとえば、ベクトルを格納し、その合計を正確に表現する型を定義するとします。


```jldoctest
julia> struct SummedArray{T<:Number,S<:Number}
           data::Vector{T}
           sum::S
       end

julia> SummedArray(Int32[1; 2; 3], Int32(6))
SummedArray{Int32,Int32}(Int32[1, 2, 3], 6)
```


```@raw html
<!--
The problem is that we want `S` to be a larger type than `T`, so that we can sum many elements
with less information loss. For example, when `T` is [`Int32`](@ref), we would like `S` to
be [`Int64`](@ref). Therefore we want to avoid an interface that allows the user to construct
instances of the type `SummedArray{Int32,Int32}`. One way to do this is to provide a
constructor only for `SummedArray`, but inside the `type` definition block to suppress
generation of default constructors:
-->
```
問題は、`S`を`T`より大きな型にして、多くの要素の合計を求める際に情報損失を少なくしたいということです。
たとえば、`T` を[`Int32`](@ref)、`S`を [`Int64`](@ref)とします。
よって、ユーザーが`SummedArray{Int32,Int32}`といった型のインスタンスを構築できるようなインターフェイスは避けたいと考えています。
これを行う1つの方法は、`SummedArray`コンストラクタの提供のみを行い、`type`定義ブロックの中でデフォルトのコンストラクタの生成を抑止することです。


```jldoctest
julia> struct SummedArray{T<:Number,S<:Number}
           data::Vector{T}
           sum::S
           function SummedArray(a::Vector{T}) where T
               S = widen(T)
               new{T,S}(a, sum(S, a))
           end
       end

julia> SummedArray(Int32[1; 2; 3], Int32(6))
ERROR: MethodError: no method matching SummedArray(::Array{Int32,1}, ::Int32)
Closest candidates are:
  SummedArray(::Array{T,1}) where T at none:5
```


```@raw html
<!--
This constructor will be invoked by the syntax `SummedArray(a)`. The syntax `new{T,S}` allows
specifying parameters for the type to be constructed, i.e. this call will return a `SummedArray{T,S}`.
`new{T,S}` can be used in any constructor definition, but for convenience the parameters
to `new{}` are automatically derived from the type being constructed when possible.
-->
```
このコンストラクタは`SummedArray(a)`という構文によって呼び出されます。
`new{T,S}`という構文で、構築する型のパラメータを指定できます。
つまり、この呼び出しは`SummedArray{T,S}`を返します。
 `new{T,S}`を任意のコンストラクタ定義で使用することができますが、便宜上、`new{}`に対するパラメータは、可能な場合は、構築される型から自動的に派生させます。