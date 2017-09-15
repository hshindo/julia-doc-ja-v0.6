[](# [Scope of Variables](@id scope-of-variables))
# 変数のスコープ

```@raw html
<!--
The *scope* of a variable is the region of code within which a variable is visible. Variable scoping
helps avoid variable naming conflicts. The concept is intuitive: two functions can both have arguments
called `x` without the two `x`'s referring to the same thing. Similarly there are many other cases
where different blocks of code can use the same name without referring to the same thing. The
rules for when the same variable name does or doesn't refer to the same thing are called scope
rules; this section spells them out in detail.
-->
```

変数の**スコープ**は、変数の見えるコードの領域です。
変数のスコープは、変数の名前の衝突を避けるのに役立ちます。
その考え方は直観的です。
２つの関数が、引数に同じ名前の`x`を使っていても、それぞれの`x`が同じものを参照することがないようにできます。
同様に、異なるコードブロックで、同じ名前を使用しても、同じものを参照することのない多くの他のケースがあります。
同じ変数名が、いつ同じものを参照し、いつそうではないかを、決める規則は、スコープ規則と呼ばれます。
このセクションで詳しく説明します。



```@raw html
<!--
Certain constructs in the language introduce *scope blocks*, which are regions of code that are
eligible to be the scope of some set of variables. The scope of a variable cannot be an arbitrary
set of source lines; instead, it will always line up with one of these blocks. There are two
main types of scopes in Julia, *global scope* and *local scope*, the latter can be nested. The
constructs introducing scope blocks are:
-->
```

ある種の言語構文は、**スコープブロック**を採用しています。
これは、変数のスコープとして適切なコードの領域です。
変数のスコープは、ソースコードの勝手な行の範囲を割り当てることはできません。
代わりに、スコープブロックのいずれかを常に割り当てます。
Juliaには**グローバルスコープ**と**ローカルスコープ**の2つの主要なスコープがあり、ローカルスコープはネストすることができます。
スコープブロックを導入する構成要素は次のとおりです。


```@raw html
<!--
| Scope name           | block/construct introducing this kind of scope                                                |
| :------------------- | :-------------------------------------------------------------------------------------------- |
| [Global Scope](@ref) | `module`, `baremodule`, at interactive prompt (REPL)                                          |
| [Local Scope](@ref)  | [Soft Local Scope](@ref): `for`, `while`, comprehensions, try-catch-finally, `let`            |
| [Local Scope](@ref)  | [Hard Local Scope](@ref): functions (either syntax, anonymous & do-blocks), `struct`, `macro` |
-->
```

| スコープ名            | スコープを採用している　ブロック／構成要素                                                    |
| :---------------- | :----------------------------------------------------------------------------- |
| [グローバルスコープ](@ref) | `module`, `baremodule`, 対話セッション (REPL)                                       |
| [ローカルスコープ](@ref)  | [ソフトローカルスコープ](@ref): `for`, `while`, 内包表記, try-catch-finally, `let`        |
| [ローカルスコープ](@ref)  | [ハードローカルスコープ](@ref): 関数 (通常の構文、 無名関数 、 do-ブロック), `struct`, `macro` |



```@raw html
<!--
Notably missing from this table are [begin blocks](@ref man-compound-expressions) and [if blocks](@ref man-conditional-evaluation), which do *not*
introduce new scope blocks. All three types of scopes follow somewhat different rules which will
be explained below as well as some extra rules for certain blocks.
-->
```

注目すべきはこのテーブルにない [begin ブロック]（@ ref man-compound-expressions）と[if ブロック（@ ref man-conditional-evaluation）]です。
これらは新しいスコープブロックを導入しません。
以下、少しずつ規則の違う３つのスコープと、特別な規則のブロックについて説明します。

```@raw html
<!--
Julia uses [lexical scoping](https://en.wikipedia.org/wiki/Scope_%28computer_science%29#Lexical_scoping_vs._dynamic_scoping),
meaning that a function's scope does not inherit from its caller's scope, but from the scope in
which the function was defined. For example, in the following code the `x` inside `foo` refers
to the `x` in the global scope of its module `Bar`:
-->
```

Juliaはレキシカルスコープを使用しています。
つまり、関数のスコープは呼び出し元のスコープを引き継がず、関数が定義されたスコープを引き継ぎます。
たとえば、次のコードでは、`foo`の内側の`x`は、`Bar`モジュールのグローバルスコープ内にある`x`を参照します。



```jldoctest moduleBar
julia> module Bar
           x = 1
           foo() = x
       end;
```

```@raw html
<!--
and not a `x` in the scope where `foo` is used:
-->
```

`foo`が使われるスコープ内の`x`を参照するわけではありません。


```jldoctest moduleBar
julia> import .Bar

julia> x = -1;

julia> Bar.foo()
1
```

```@raw html
<!--
Thus *lexical scope* means that the scope of variables can be inferred from the source code alone.
-->
```

このように、**レキシカルスコープ**では、変数のスコープをソースコードだけで推測できることを意味します。

[](## Global Scope)
## グローバル　スコープ

```@raw html
<!--
*Each module introduces a new global scope*, separate from the global scope of all other modules;
there is no all-encompassing global scope. Modules can introduce variables of other modules into
their scope through the [using or import](@ref modules) statements or through qualified access using the
dot-notation, i.e. each module is a so-called *namespace*. Note that variable bindings can only
be changed within their global scope and not from an outside module.
-->
```
モジュールは、それぞれ、他のモジュールとは異なる新しいグローバルスコープを導入します。
すべてを包括するスコープは存在しません。
モジュールは他のモジュールの変数を自分のスコープに導入可能で、[usingまたはimport]（@ ref モジュール）文を使うとモジュール全体、dot表記を使うと限定的になります。
つまり、各モジュールはいわゆる名前空間です。
変数の束縛が変更されるのは、モジュールのグローバルスコープ内のみで、モジュールでは変わらないことに注意してください。



```jldoctest
julia> module A
           a = 1 # a global in A's scope
       end;

julia> module B
           module C
               c = 2
           end
           b = C.c    # can access the namespace of a nested global scope
                      # through a qualified access
           import ..A # makes module A available
           d = A.a
       end;

julia> module D
           b = a # errors as D's global scope is separate from A's
       end;
ERROR: UndefVarError: a not defined

julia> module E
           import ..A # make module A available
           A.a = 2    # throws below error
       end;
ERROR: cannot assign variables in other modules
```

```@raw html
<!--
Note that the interactive prompt (aka REPL) is in the global scope of the module `Main`.
-->
```
対話セッション（別名REPL）は、`Main`モジュールのグローバルスコープ内にあることに注意してくださいMain。


[](## Local Scope)
## ローカルスコープ

```@raw html
<!--
A new local scope is introduced by most code-blocks, see above table for a complete list.
 A local scope *usually* inherits all the variables from its parent scope, both for reading and
writing. There are two subtypes of local scopes, hard and soft, with slightly different rules
concerning what variables are inherited. Unlike global scopes, local scopes are not namespaces,
thus variables in an inner scope cannot be retrieved from the parent scope through some sort of
qualified access.
-->
```
新しいローカルスコープが、ほとんどのコードブロックによって導入されています。上記の表を参照してください。
ローカルスコープは、**通常は**、親スコープのすべての変数を引き継いで、読み書きができます。
ローカルスコープにはハードとソフトの2つの派生型があり、どの変数が引き継がれるのか、規則がわずかに異なります。
グローバルスコープとは異なり、ローカルスコープは名前空間ではないため、何かしらの宣言などをして、内部スコープの変数に対して親スコープから取り出すことができません。


```@raw html
<!--
The following rules and examples pertain to both hard and soft local scopes. A newly introduced
variable in a local scope does not back-propagate to its parent scope. For example, here the
`z` is not introduced into the top-level scope:
-->
```
次の規則と例は、ハードローカルスコープとソフトローカルスコープの両方に関係します。
ローカルスコープ内に新しく導入された変数は、その親スコープに伝播しません。たとえば、ここで `z`はトップレベルのスコープには導入されていません。


```jldoctest
julia> for i = 1:10
           z = i
       end

julia> z
ERROR: UndefVarError: z not defined
```

```@raw html
<!--
(Note, in this and all following examples it is assumed that their top-level is a global scope
with a clean workspace, for instance a newly started REPL.)

Inside a local scope a variable can be forced to be a local variable using the `local` keyword:
-->
```
（この例と以下のすべては、トップレベルが、新規に起動されたREPLなどの、クリーンなワークスペースを持つグローバルスコープであると想定しています）。

ローカルスコープの内部では、`local`キーワードを使用して変数を強制的にローカル変数にすることができます：


```jldoctest
julia> x = 0;

julia> for i = 1:10
           local x
           x = i + 1
       end

julia> x
0
```

```@raw html
<!--
Inside a local scope a new global variable can be defined using the keyword `global`:
-->
```

ローカルスコープ内で、新しいグローバル変数は、`global`キーワードを使用して定義することができます。

```jldoctest
julia> for i = 1:10
           global z
           z = i
       end

julia> z
10
```



```@raw html
<!--
The location of both the `local` and `global` keywords within the scope block is irrelevant.
The following is equivalent to the last example (although stylistically worse):
-->
```
スコープブロック内の`local`キーワードと`global`キーワード位置両方ともどこでも構いません。
以下は先程の例と同等です（あまりよくないスタイルですが）。



```jldoctest
julia> for i = 1:10
           z = i
           global z
       end

julia> z
10
```

[](### Soft Local Scope)
### ソフトローカルスコープ


```@raw html
<!--
> In a soft local scope, all variables are inherited from its parent scope unless a variable is
> specifically marked with the keyword `local`.
-->
```
> ソフトローカルスコープでは、特に`local`キーワードが設定されていない限り、すべての変数は親スコープから引き継がれます。

```@raw html
<!--
Soft local scopes are introduced by for-loops, while-loops, comprehensions, try-catch-finally-blocks,
and let-blocks. There are some extra rules for [Let Blocks](@ref) and for [For Loops and Comprehensions](@ref).
-->
```
ソフトループスコープは、for-ループ、while-ループ、内包表記、try-catch-finally-ブロック、およびlet-ブロックに導入されています。
[Let ブロック](@ref) と [For ループ と 内包表記](@ref) には、いくつかの追加ルールがあります。

```@raw html
<!--
In the following example the `x` and `y` refer always to the same variables as the soft local
scope inherits both read and write variables:
-->
```
次の例では、softローカルスコープによって読み書き可能な変数として引き継がれた、引き継ぎ元の`x`と`y`を常に参照しています。


```jldoctest
julia> x, y = 0, 1;

julia> for i = 1:10
           x = i + y + 1
       end

julia> x
12
```

```@raw html
<!--
Within soft scopes, the *global* keyword is never necessary, although allowed. The only case
when it would change the semantics is (currently) a syntax error:
-->
```
ソフトスコープ内では、**グローバル**キーワードは必要ではありませんが、使っても構いません。
セマンティクスの変わる唯一のケースは、構文エラーです（現時点）。

```jldoctest
julia> let
           local j = 2
           let
               global j = 3
           end
       end
ERROR: syntax: `global j`: j is local variable in the enclosing scope
```

[](### Hard Local Scope)
### ハードローカルスコープ

```@raw html
<!--
Hard local scopes are introduced by function definitions (in all their forms), struct type definition blocks,
and macro-definitions.
-->
```

```@raw html
<!--
> In a hard local scope, all variables are inherited from its parent scope unless:
>
>   * an assignment would result in a modified *global* variable, or
>   * a variable is specifically marked with the keyword `local`.
-->
```

```@raw html
<!--
Thus global variables are only inherited for reading but not for writing:
-->
```

```jldoctest
julia> x, y = 1, 2;

julia> function foo()
           x = 2        # assignment introduces a new local
           return x + y # y refers to the global
       end;

julia> foo()
4

julia> x
1
```

```@raw html
<!--
An explicit `global` is needed to assign to a global variable:
-->
```

```jldoctest
julia> x = 1;

julia> function foobar()
           global x = 2
       end;

julia> foobar();

julia> x
2
```

```@raw html
<!--
Note that *nested functions* can behave differently to functions defined in the global scope as
they can modify their parent scope's *local* variables:
-->
```

```jldoctest
julia> x, y = 1, 2;

julia> function baz()
           x = 2 # introduces a new local
           function bar()
               x = 10       # modifies the parent's x
               return x + y # y is global
           end
           return bar() + x # 12 + 10 (x is modified in call of bar())
       end;

julia> baz()
22

julia> x, y
(1, 2)
```

```@raw html
<!--
The distinction between inheriting global and local variables for assignment can lead to some
slight differences between functions defined in local vs. global scopes. Consider the modification
of the last example by moving `bar` to the global scope:
-->
```

```jldoctest
julia> x, y = 1, 2;

julia> function bar()
           x = 10 # local
           return x + y
       end;

julia> function quz()
           x = 2 # local
           return bar() + x # 12 + 2 (x is not modified)
       end;

julia> quz()
14

julia> x, y
(1, 2)
```

```@raw html
<!--
Note that above subtlety does not pertain to type and macro definitions as they can only appear
at the global scope. There are special scoping rules concerning the evaluation of default and
keyword function arguments which are described in the [Function section](@ref man-functions).
-->
```

```@raw html
<!--
An assignment introducing a variable used inside a function, type or macro definition need not
come before its inner usage:
-->
```

```jldoctest
julia> f = y -> y + a
(::#1) (generic function with 1 method)

julia> f(3)
ERROR: UndefVarError: a not defined
Stacktrace:
 [1] (::##1#2)(::Int64) at ./none:1

julia> a = 1
1

julia> f(3)
4
```

```@raw html
<!--
This behavior may seem slightly odd for a normal variable, but allows for named functions -- which
are just normal variables holding function objects -- to be used before they are defined. This
allows functions to be defined in whatever order is intuitive and convenient, rather than forcing
bottom up ordering or requiring forward declarations, as long as they are defined by the time
they are actually called. As an example, here is an inefficient, mutually recursive way to test
if positive integers are even or odd:
-->
```

```jldoctest
julia> even(n) = n == 0 ? true : odd(n-1);

julia> odd(n) = n == 0 ? false : even(n-1);

julia> even(3)
false

julia> odd(3)
true
```

```@raw html
<!--
Julia provides built-in, efficient functions to test for oddness and evenness called [`iseven()`](@ref)
and [`isodd()`](@ref) so the above definitions should only be taken as examples.
-->
```

[](### Hard vs. Soft Local Scope)
### ハード vs ソフトローカルスコープ
```@raw html
<!--
Blocks which introduce a soft local scope, such as loops, are generally used to manipulate the
variables in their parent scope. Thus their default is to fully access all variables in their
parent scope.
-->
```

```@raw html
<!--
Conversely, the code inside blocks which introduce a hard local scope (function, type, and macro
definitions) can be executed at any place in a program. Remotely changing the state of global
variables in other modules should be done with care and thus this is an opt-in feature requiring
the `global` keyword.
-->
```

```@raw html
<!--
The reason to allow *modifying local* variables of parent scopes in nested functions is to allow
constructing [closures](https://en.wikipedia.org/wiki/Closure_%28computer_programming%29) which
have a private state, for instance the `state` variable in the following example:
-->
```

```jldoctest
julia> let
           state = 0
           global counter
           counter() = state += 1
       end;

julia> counter()
1

julia> counter()
2
```

```@raw html
<!--
See also the closures in the examples in the next two sections.
-->
```

[](### Let Blocks)
### Let ブロック


```@raw html
<!--
Unlike assignments to local variables, `let` statements allocate new variable bindings each time
they run. An assignment modifies an existing value location, and `let` creates new locations.
This difference is usually not important, and is only detectable in the case of variables that
outlive their scope via closures. The `let` syntax accepts a comma-separated series of assignments
and variable names:
-->
```

```jldoctest
julia> x, y, z = -1, -1, -1;

julia> let x = 1, z
           println("x: $x, y: $y") # x is local variable, y the global
           println("z: $z") # errors as z has not been assigned yet but is local
       end
x: 1, y: -1
ERROR: UndefVarError: z not defined
```

```@raw html
<!--
The assignments are evaluated in order, with each right-hand side evaluated in the scope before
the new variable on the left-hand side has been introduced. Therefore it makes sense to write
something like `let x = x` since the two `x` variables are distinct and have separate storage.
Here is an example where the behavior of `let` is needed:
-->
```

```jldoctest
julia> Fs = Array{Any}(2); i = 1;

julia> while i <= 2
           Fs[i] = ()->i
           i += 1
       end

julia> Fs[1]()
3

julia> Fs[2]()
3
```


```@raw html
<!--
Here we create and store two closures that return variable `i`. However, it is always the same
variable `i`, so the two closures behave identically. We can use `let` to create a new binding
for `i`:
-->
```

```jldoctest
julia> Fs = Array{Any}(2); i = 1;

julia> while i <= 2
           let i = i
               Fs[i] = ()->i
           end
           i += 1
       end

julia> Fs[1]()
1

julia> Fs[2]()
2
```


```@raw html
<!--
Since the `begin` construct does not introduce a new scope, it can be useful to use a zero-argument
`let` to just introduce a new scope block without creating any new bindings:
-->
```

```jldoctest
julia> let
           local x = 1
           let
               local x = 2
           end
           x
       end
1
```


```@raw html
<!--
Since `let` introduces a new scope block, the inner local `x` is a different variable than the
outer local `x`.
-->
```

[](### For Loops and Comprehensions)
### For ループと内包表記

```@raw html
<!--
`for` loops and [Comprehensions](@ref) have the following behavior: any new variables introduced
in their body scopes are freshly allocated for each loop iteration. This is in contrast to `while`
loops which reuse the variables for all iterations. Therefore these constructs are similar to
`while` loops with `let` blocks inside:
-->
```

```jldoctest
julia> Fs = Array{Any}(2);

julia> for j = 1:2
           Fs[j] = ()->j
       end

julia> Fs[1]()
1

julia> Fs[2]()
2
```


```@raw html
<!--
`for` loops will reuse existing variables for its iteration variable:
-->
```

```jldoctest
julia> i = 0;

julia> for i = 1:3
       end

julia> i
3
```


```@raw html
<!--
However, comprehensions do not do this, and always freshly allocate their iteration variables:
-->
```

```jldoctest
julia> x = 0;

julia> [ x for x = 1:3 ];

julia> x
0
```

[](## Constants)
## 定数


```@raw html
<!--
A common use of variables is giving names to specific, unchanging values. Such variables are only
assigned once. This intent can be conveyed to the compiler using the `const` keyword:
-->
```

```jldoctest
julia> const e  = 2.71828182845904523536;

julia> const pi = 3.14159265358979323846;
```


```@raw html
<!--
The `const` declaration is allowed on both global and local variables, but is especially useful
for globals. It is difficult for the compiler to optimize code involving global variables, since
their values (or even their types) might change at almost any time. If a global variable will
not change, adding a `const` declaration solves this performance problem.
-->
```


```@raw html
<!--
Local constants are quite different. The compiler is able to determine automatically when a local
variable is constant, so local constant declarations are not necessary for performance purposes.
-->
```


```@raw html
<!--
Special top-level assignments, such as those performed by the `function` and `struct` keywords,
are constant by default.
-->
```


```@raw html
<!--
Note that `const` only affects the variable binding; the variable may be bound to a mutable object
(such as an array), and that object may still be modified.
-->
```
