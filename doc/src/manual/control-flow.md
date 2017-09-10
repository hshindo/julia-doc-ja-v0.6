# Control Flow
# 制御構造

```@raw html
<!--
Julia provides a variety of control flow constructs:
-->
```
Juliaには、さまざまな制御構造があります。
```@raw html
<!--
  * [Compound Expressions](@ref man-compound-expressions): `begin` and `(;)`.
  * [Conditional Evaluation](@ref man-conditional-evaluation): `if`-`elseif`-`else` and `?:` (ternary operator).
  * [Short-Circuit Evaluation](@ref): `&&`, `||` and chained comparisons.
  * [Repeated Evaluation: Loops](@ref man-loops): `while` and `for`.
  * [Exception Handling](@ref): `try`-`catch`, [`error()`](@ref) and [`throw()`](@ref).
  * [Tasks (aka Coroutines)](@ref man-tasks): [`yieldto()`](@ref).
-->
```
  * [複合式](@ref man-compound-expressions): `begin` および `(;)`。
  * [条件付き評価](@ref man-conditional-evaluation): `if`-`elseif`-`else` および `?:` (三項演算子)。
  * [短絡評価](@ref): `&&`, `||` とおよび比較の連鎖。
  * [繰り返し評価: ループ](@ref man-loops): `while` と `for`。
  * [例外処理](@ref): `try`-`catch`, [`error()`](@ref) および [`throw()`](@ref)。
  * [タスク (コルーチン)の別称](@ref man-tasks): [`yieldto()`](@ref)。
```@raw html
<!--
The first five control flow mechanisms are standard to high-level programming languages. [`Task`](@ref)s
are not so standard: they provide non-local control flow, making it possible to switch between
temporarily-suspended computations. This is a powerful construct: both exception handling and
cooperative multitasking are implemented in Julia using tasks. Everyday programming requires no
direct usage of tasks, but certain problems can be solved much more easily by using tasks.
-->
```
始めの５つは高水準なプログラミング言語に標準的な制御構造の仕組みです。
[タスク]はそれほど標準的ではありません。タスクはローカルだけにとどまらない制御構造で、一時的に保留している計算を
切り替えることができます。
これは、強力な制御構造で、Juliaでは、例外処理や協調的なマルチタスクは、共にタスクを使って実装されています。
日常的なプログラムには、タスクを直接使う必要はないかもしれませんが、ある種の問題には、タスクを使うとはるかに簡単に解くことができます。

## [Compound Expressions](@id man-compound-expressions)
## [複合式]

```@raw html
<!--
Sometimes it is convenient to have a single expression which evaluates several subexpressions
in order, returning the value of the last subexpression as its value. There are two Julia constructs
that accomplish this: `begin` blocks and `(;)` chains. The value of both compound expression constructs
is that of the last subexpression. Here's an example of a `begin` block:
-->
```
いくつかの部分式を並べて単一の式とし、順番に評価した部分式の最後の式の値を戻り値とすると、便利なことが、時々あります。
これを遂行するJuliaの制御構造は2つあります。beginブロック、と、セミコロン(;)連鎖です。
どちらの複合式も、最後の部分式の値が戻り値になります。
beginブロックの例を次に示します。

```jldoctest
julia> z = begin
           x = 1
           y = 2
           x + y
       end
3
```

```@raw html
<!--
Since these are fairly small, simple expressions, they could easily be placed onto a single line,
which is where the `(;)` chain syntax comes in handy:
-->
```
この例では、式がかなり小さくて単純なので、簡単に1行にまとめることができます。こういう場合に便利なのが、セミコロン(;)連鎖構文です。

```jldoctest
julia> z = (x = 1; y = 2; x + y)
3
```
```@raw html
<!--
This syntax is particularly useful with the terse single-line function definition form introduced
in [Functions](@ref). Although it is typical, there is no requirement that `begin` blocks be multiline
or that `(;)` chains be single-line:
-->
```
この構文は、[関数](@ref) の章で紹介した、簡潔で単一行の関数定義形式で使うと、特に便利です。
こういう使い方が典型的ですが、beginブロックは複数行で、セミコロン(;)連鎖は単一行でなければならないといった、制約はありません。

```jldoctest
julia> begin x = 1; y = 2; x + y end
3

julia> (x = 1;
        y = 2;
        x + y)
3
```

## [Conditional Evaluation](@id man-conditional-evaluation)
## [条件付き評価](@id man-conditional-evaluation)

```@raw html
<!--
Conditional evaluation allows portions of code to be evaluated or not evaluated depending on the
value of a boolean expression. Here is the anatomy of the `if`-`elseif`-`else` conditional syntax:
-->
```
条件付き評価では、ブール式の値に応じてコードの一部を評価するかしないかを、制御ができます。
ここで、 `if`-`elseif`-`else`型の条件分岐構文の解剖をしてみましょう：

```julia
if x < y
    println("x is less than y")
elseif x > y
    println("x is greater than y")
else
    println("x is equal to y")
end
```

```@raw html
<!--
If the condition expression `x < y` is `true`, then the corresponding block is evaluated; otherwise
the condition expression `x > y` is evaluated, and if it is `true`, the corresponding block is
evaluated; if neither expression is true, the `else` block is evaluated. Here it is in action:
-->
```
条件式`x < y`が`真`の場合、対応するブロックが評価されます。そうでなければ、条件式`x > y`が評価され、この式が`真`の場合は、対応するブロックが評価されます。いずれの式も真でない場合、elseブロックが評価されます。
例を挙げると、

```jldoctest
julia> function test(x, y)
           if x < y
               println("x is less than y")
           elseif x > y
               println("x is greater than y")
           else
               println("x is equal to y")
           end
       end
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```
```@raw html
<!--
The `elseif` and `else` blocks are optional, and as many `elseif` blocks as desired can be used.
The condition expressions in the `if`-`elseif`-`else` construct are evaluated until the first
one evaluates to `true`, after which the associated block is evaluated, and no further condition
expressions or blocks are evaluated.
-->
```
この、`elseif` および `else`ブロックは省略可能で、`elseif`ブロックは、書いただけ使うことができます。
`if`-`elseif`-`else`構文に出てくる条件文は、`真`になるものがでてくるまで評価されます。
その後、真になる条件文に対応するブロックが評価され、以降は、条件式もブロックも評価されません。

```@raw html
<!--
`if` blocks are "leaky", i.e. they do not introduce a local scope. This means that new variables
defined inside the `if` clauses can be used after the `if` block, even if they weren't defined
before. So, we could have defined the `test` function above as
-->
```
`if`ブロックには「漏れ」があります。つまりローカルスコープを導入していません。
これは、`if`句の中で定義した新しい変数が、たとえ`if`ブロックの前で定義していなくても、
`if`ブロックの後ろで使えるということです。
だから、上記のtest関数は、このようにも定義できます。


```jldoctest
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           else
               relation = "greater than"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(2, 1)
x is greater than y.
```
```@raw html
<!--
The variable `relation` is declared inside the `if` block, but used outside. However, when depending
on this behavior, make sure all possible code paths define a value for the variable. The following
change to the above function results in a runtime error
-->
```
変数`relation`は`if`ブロックの内側で宣言されていますが、外側で使用されています。
ただし、こういった使い方をする場合は、すべてのとりうる分岐で、変数の値が定義されていることを確認してください。
上記の関数を次のように変更すると、ランタイムエラーが発生します。


```jldoctest
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(1,2)
x is less than y.

julia> test(2,1)
ERROR: UndefVarError: relation not defined
Stacktrace:
 [1] test(::Int64, ::Int64) at ./none:7
```
```@raw html
<!--
`if` blocks also return a value, which may seem unintuitive to users coming from many other languages.
This value is simply the return value of the last executed statement in the branch that was chosen,
so
-->
```
`if`ブロックも値を返しますが、他の多くの言語の利用者には直感的ではないようです。
この値は、単に、選択された分岐の中で、最後に実行された文の戻り値です。
```jldoctest
julia> x = 3
3

julia> if x > 0
           "positive!"
       else
           "negative..."
       end
"positive!"
```
```@raw html
<!--
Note that very short conditional statements (one-liners) are frequently expressed using Short-Circuit
Evaluation in Julia, as outlined in the next section.
-->
```
Juliaでは、非常に短い条件文（ワンライナー）には、頻繁に短絡評価が利用される点に注意してください。
次章で説明します。

```@raw html
<!--
Unlike C, MATLAB, Perl, Python, and Ruby -- but like Java, and a few other stricter, typed languages
-- it is an error if the value of a conditional expression is anything but `true` or `false`:
-->
```
条件文に、`true` または `false` 以外の値をいれると、エラーが発生します。
この仕様は、C、MATLAB、PerlやPython、Rubyとは異なりますが、Javaなどの厳格な型付け言語とおなじです。
```jldoctest
julia> if 1
           println("true")
       end
ERROR: TypeError: non-boolean (Int64) used in boolean context
```
```@raw html
<!--
This error indicates that the conditional was of the wrong type: [`Int64`](@ref) rather
than the required [`Bool`](@ref).
-->
```
このエラーは、条件が間違った型であったことを示しています。
適合する型は[`Bool`](@ref)であって [`Int64`](@ref)は不適合です。


```@raw html
<!--
The so-called "ternary operator", `?:`, is closely related to the `if`-`elseif`-`else` syntax,
but is used where a conditional choice between single expression values is required, as opposed
to conditional execution of longer blocks of code. It gets its name from being the only operator
in most languages taking three operands:
-->
```
いわゆる「三項演算子」`?:`は、`if`-`elseif`-`else`構文と密接に関連しています。
前者は一つの式で条件を選択する必要がありますが、後者はもっと長いコードで条件分岐をおこなえます。
「三項演算子」の名前は、多くの他の言語では、3つの被演算子をとる唯一の演算子であることに由来します。

```julia
a ? b : c
```
```@raw html
<!--
The expression `a`, before the `?`, is a condition expression, and the ternary operation evaluates
the expression `b`, before the `:`, if the condition `a` is `true` or the expression `c`, after
the `:`, if it is `false`.
-->
```
`?`の前にある式`a`は条件式で、三項演算は`a`が`真`の時は、`:`の前にある`b`を評価し、`偽`の時は、`:`の後にある`c`を評価します。
```@raw html
<!--
The easiest way to understand this behavior is to see an example. In the previous example, the
`println` call is shared by all three branches: the only real choice is which literal string to
print. This could be written more concisely using the ternary operator. For the sake of clarity,
let's try a two-way version first:
-->
```

この動作を理解する最も簡単な方法は、例を見ることでしょう。
前述の例では`println`を呼び出すのは3つの分岐すべてで共通しています。
唯一の意味のある分岐は、どの文字列のリテラルが印刷されるかです。
これは三項演算子を使ってより簡潔に書くことができます。
わかりやすくするために、まず二分岐版をみてみましょう：


```jldoctest
julia> x = 1; y = 2;

julia> println(x < y ? "less than" : "not less than")
less than

julia> x = 1; y = 0;

julia> println(x < y ? "less than" : "not less than")
not less than
```
```@raw html
<!--
If the expression `x < y` is true, the entire ternary operator expression evaluates to the string
`"less than"` and otherwise it evaluates to the string `"not less than"`. The original three-way
example requires chaining multiple uses of the ternary operator together:
-->
```
式`x < y`が真の場合、三項演算子式全体は、文字列"less than"に評価され、そうでない場合は文字列"not less than" に評価されます。元の三分岐版の例では、三項演算子を複数連鎖させて使用​​する必要があります。
```jldoctest`
julia> test(x, y) = println(x < y ? "x is less than y"    :
                            x > y ? "x is greater than y" : "x is equal to y")
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```
```@raw html
<!--
To facilitate chaining, the operator associates from right to left.

It is significant that like `if`-`elseif`-`else`, the expressions before and after the `:` are
only evaluated if the condition expression evaluates to `true` or `false`, respectively:
-->
```
連鎖を利用する時は、演算子の結合順は右から左です。
`if`-`elseif`-`else`と同様に重要なのは、`:`の前後の式は、それぞれ条件式が`真`・`偽`の時、その時だけ、
評価されることです。

```jldoctest
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> 1 < 2 ? v("yes") : v("no")
yes
"yes"

julia> 1 > 2 ? v("yes") : v("no")
no
"no"
```

## Short-Circuit Evaluation
## 短絡評価

```@raw html
<!--
Short-circuit evaluation is quite similar to conditional evaluation. The behavior is found in
most imperative programming languages having the `&&` and `||` boolean operators: in a series
of boolean expressions connected by these operators, only the minimum number of expressions are
evaluated as are necessary to determine the final boolean value of the entire chain. Explicitly,
this means that:
-->
```
短絡評価は条件付き評価と非常によく似ています。
短絡評価は`&&`や`||`のブール演算子持つほとんどの命令型プログラミング言語で
みられます。ブール式をこれらの演算子で連結した時、式全体のブール値を決定する最小限の式しか評価されません。つまり、


```@raw html
<!--
  * In the expression `a && b`, the subexpression `b` is only evaluated if `a` evaluates to `true`.
  * In the expression `a || b`, the subexpression `b` is only evaluated if `a` evaluates to `false`.
-->
```
*  `a && b`という式では、`b`が評価されるのは、`a`の評価値が`真`の時のみ。
*  `a || b`という式では、`b`が評価されるのは、`a`の評価値が`偽`の時のみ。
```@raw html
<!--
The reasoning is that `a && b` must be `false` if `a` is `false`, regardless of the value of
`b`, and likewise, the value of `a || b` must be true if `a` is `true`, regardless of the value
of `b`. Both `&&` and `||` associate to the right, but `&&` has higher precedence than `||` does.
It's easy to experiment with this behavior:
-->
```
この理由は、`a && b`は`a`が`偽`の時には、`b`の値がどうであれ、`偽`でなければならないから。 同様に`a || b`は`a`が`真`の時には、`b`の値がどうであれ、`真`でなければならないから。`&&` と`||`は共に、右から結合するが、`&&`は`||`より、優先度が高い。これを実験するのは簡単です：

```jldoctest tandf
julia> t(x) = (println(x); true)
t (generic function with 1 method)

julia> f(x) = (println(x); false)
f (generic function with 1 method)

julia> t(1) && t(2)
1
2
true

julia> t(1) && f(2)
1
2
false

julia> f(1) && t(2)
1
false

julia> f(1) && f(2)
1
false

julia> t(1) || t(2)
1
true

julia> t(1) || f(2)
1
true

julia> f(1) || t(2)
1
2
true

julia> f(1) || f(2)
1
2
false
```

```@raw html
<!--
You can easily experiment in the same way with the associativity and precedence of various combinations
of `&&` and `||` operators.
-->
```
同様の方法で、`&&`と`||`演算子のさまざまな組み合わせに対して、結合性と優先度を簡単に試すことができます。

```@raw html
<!--
This behavior is frequently used in Julia to form an alternative to very short `if` statements.
Instead of `if <cond> <statement> end`, one can write `<cond> && <statement>` (which could be
read as: <cond> *and then* <statement>). Similarly, instead of `if ! <cond> <statement> end`,
one can write `<cond> || <statement>` (which could be read as: <cond> *or else* <statement>).

For example, a recursive factorial routine could be defined like this:
-->
```


Juliaでは、if文が非常に短くなる時、代わりに、この動作が頻繁に利用されます。
`if <条件式> <実行文> end`の代わりに、
`<条件式> && <実行文>`と書けます
（`&&`、"の時は" と読み下せます)。
同様に、 `if ! <条件式> <実行文> end`は、
`<条件式> || <実行文>`と書けます
（`||`は"でなければ" と読み下せます)。

たとえば、再帰的な階乗ルーチンは次のように定義できます。


```jldoctest
julia> function fact(n::Int)
           n >= 0 || error("n must be non-negative")
           n == 0 && return 1
           n * fact(n-1)
       end
fact (generic function with 1 method)

julia> fact(5)
120

julia> fact(0)
1

julia> fact(-1)
ERROR: n must be non-negative
Stacktrace:
 [1] fact(::Int64) at ./none:2
```

```@raw html
<!--
Boolean operations *without* short-circuit evaluation can be done with the bitwise boolean operators
introduced in [Mathematical Operations and Elementary Functions](@ref): `&` and `|`. These are
normal functions, which happen to support infix operator syntax, but always evaluate their arguments:
-->
```
短絡評価を使わないブール演算は、[算術処理と基本的な関数](@ref)で紹介した、 `&` と `|`の、ビット単位の論理演算子を使って行うことができます。
これらは通常の関数で、中置演算子の構文を利用できますが、常に引数を評価します。

```jldoctest tandf
julia> f(1) & t(2)
1
2
false

julia> t(1) | t(2)
1
2
true
```

```@raw html
<!--
Just like condition expressions used in `if`, `elseif` or the ternary operator, the operands of
`&&` or `||` must be boolean values (`true` or `false`). Using a non-boolean value anywhere except
for the last entry in a conditional chain is an error:
-->
```
`if`、`elseif`、三項演算子で使用される条件式のと同様に、
`&&`や`||`の被演算子は、ブール値()`true` または `false`)でなければなりません。
条件式の連鎖で最後の項以外のどこかで、非ブール値を使用するとエラーになります。

```jldoctest
julia> 1 && true
ERROR: TypeError: non-boolean (Int64) used in boolean context
```

```@raw html
<!--
On the other hand, any type of expression can be used at the end of a conditional chain. It will
be evaluated and returned depending on the preceding conditionals:
-->
```
一方、条件式の連鎖の最終項では、あらゆる型の式が利用可能です。これは先立つ項の条件に応じて評価され、戻り値となります。

```jldoctest
julia> true && (x = (1, 2, 3))
(1, 2, 3)

julia> false && (x = (1, 2, 3))
false
```

## [Repeated Evaluation: Loops](@id man-loops)
## 繰り返し評価:ループ

```@raw html
<!--
There are two constructs for repeated evaluation of expressions: the `while` loop and the `for`
loop. Here is an example of a `while` loop:
-->
```
式の繰り返し評価には、`while`ループと`for`ループの2種類があります。
`while`ループの例を次に示します。

```jldoctest
julia> i = 1;

julia> while i <= 5
           println(i)
           i += 1
       end
1
2
3
4
5
```

```@raw html
<!--
The `while` loop evaluates the condition expression (`i <= 5` in this case), and as long it remains
`true`, keeps also evaluating the body of the `while` loop. If the condition expression is `false`
when the `while` loop is first reached, the body is never evaluated.

The `for` loop makes common repeated evaluation idioms easier to write. Since counting up and
down like the above `while` loop does is so common, it can be expressed more concisely with a
`for` loop:
-->`
```
 `while` ループは、条件式(この場合は`i <= 5`）を評価し、その式が`真`である間、`while`ループ本体の評価を続けます。
 条件式の評価が`偽`に初めてなると、その後、本体は決して評価されません。

`for`ループは、よくある繰り返し評価を、書きやすい慣用表現にしたものです。
上記の`while`ループのようなカウントアップ・カウントダウンは非常に一般的で、`for`ループでより簡潔に表現できます。

```jldoctest
julia> for i = 1:5
           println(i)
       end
1
2
3
4
5
```

```@raw html
<!--
Here the `1:5` is a `Range` object, representing the sequence of numbers 1, 2, 3, 4, 5. The `for`
loop iterates through these values, assigning each one in turn to the variable `i`. One rather
important distinction between the previous `while` loop form and the `for` loop form is the scope
during which the variable is visible. If the variable `i` has not been introduced in an other
scope, in the `for` loop form, it is visible only inside of the `for` loop, and not afterwards.
You'll either need a new interactive session instance or a different variable name to test this:
-->
```
この`1:5`は、`Range`オブジェクトで、1,2,3,4,5の数列をあらわしています。
`for` ループはこれらの値を反復し、順番に変数`i`に代入します。。
前述の`while`ループ方式と`for`ループ方式のかなり重要な違いの1つは、ループの実行中の変数が見えるスコープです。
`for`ループ方式の時は、変数`i`が、他のスコープに導入されていない場合、変数は  `for`ループの内部でのみ見えて、その後ろでは見えません。
これを試すには、新規の対話セッションか、別の変数名が必要です。

```jldoctest
julia> for j = 1:5
           println(j)
       end
1
2
3
4
5

julia> j
ERROR: UndefVarError: j not defined
```

```@raw html
<!--
See [Scope of Variables](@ref scope-of-variables) for a detailed explanation of variable scope and how it works in
Julia.

In general, the `for` loop construct can iterate over any container. In these cases, the alternative
(but fully equivalent) keyword `in` or `∈` is typically used instead of `=`, since it makes
the code read more clearly:
-->
```
変数のスコープとJuliaでの動作の詳細については、[変数のスコープ]（@ref変数のスコープ）を参照してください。

一般に、`for`ループはどのコンテナに対しても反復処理が可能です。
これらのケースでは、`=`の代替となり、しかし完全に同等なキーワードの`in`や`∈`が広く使われます。というのも、コードがより明確になるからです。

``jldoctest
julia> for i in [1,4,0]
           println(i)
       end
1
4
0

julia> for s ∈ ["foo","bar","baz"]
           println(s)
       end
foo
bar
baz
```

```@raw html
<!--
Various types of iterable containers will be introduced and discussed in later sections of the
manual (see, e.g., [Multi-dimensional Arrays](@ref man-multi-dim-arrays)).

It is sometimes convenient to terminate the repetition of a `while` before the test condition
is falsified or stop iterating in a `for` loop before the end of the iterable object is reached.
This can be accomplished with the `break` keyword:
-->
```
さまざまなタイプのイテラブル・コンテナについて、マニュアルの次章以降で紹介・検討します（例：[多次元配列]（@ ref man-multi-dim-arrays）参照）。

`while`ループで、条件式が偽になる前に繰り返しを終わらせたり、`for`ループで、最後のイテラブル・オブジェクトに達する前に反復を止めたりすると、
便利なことがあります。
これは`break`キーワードで実現できます：


```jldoctest
julia> i = 1;

julia> while true
           println(i)
           if i >= 5
               break
           end
           i += 1
       end
1
2
3
4
5

julia> for i = 1:1000
           println(i)
           if i >= 5
               break
           end
       end
1
2
3
4
5
```

```@raw html
<!--
Without the `break` keyword, the above `while` loop would never terminate on its own, and the `for` loop would iterate up to 1000. These loops are both exited early by using `break`.

In other circumstances, it is handy to be able to stop an iteration and move on to the next one
immediately. The `continue` keyword accomplishes this:
-->
```

`break`キーワードがなければ、上記の`while`ループは決して自身で終了することはなく、`for`ループは1000まで繰り返されます。
これらのループは、共に、`break`を使用すると、早い段階で終了します。

他の状況では、現在の繰り返しを停止してすぐに次回に移ることができれば便利なことがあります。continueキーワードは、これを実現します：



```jldoctest
julia> for i = 1:10
           if i % 3 != 0
               continue
           end
           println(i)
       end
3
6
9
```

```@raw html
<!--
This is a somewhat contrived example since we could produce the same behavior more clearly by
negating the condition and placing the `println` call inside the `if` block. In realistic usage
there is more code to be evaluated after the `continue`, and often there are multiple points from
which one calls `continue`.

Multiple nested `for` loops can be combined into a single outer loop, forming the cartesian product
of its iterables:
-->
```
これは不自然な例です。というのも、条件を否定にしてコールを`if`ブロック内に`println`の呼び出しを置くことで、同じ動作をより明確に書けるからです。
実際に使用するときは、`continue`の後に評価するコードがもっとあって、`continue`を呼び出す箇所も複数ある場合でしょう。

複数のネストしている`for`ループは、組み合わせて1つの外側ループにして、イテラブルの直積を作ることができます。



```jldoctest
julia> for i = 1:2, j = 3:4
           println((i, j))
       end
(1, 3)
(1, 4)
(2, 3)
(2, 4)
```

```@raw html
<!--
A `break` statement inside such a loop exits the entire nest of loops, not just the inner one.
-->
```
ネストした`for`ループ内の`break`文は、内側のループ１つだけでなく、ネスト全体のループを終了します。
## Exception Handling
## 例外処理

```@raw html
<!--
When an unexpected condition occurs, a function may be unable to return a reasonable value to
its caller. In such cases, it may be best for the exceptional condition to either terminate the
program, printing a diagnostic error message, or if the programmer has provided code to handle
such exceptional circumstances, allow that code to take the appropriate action.
-->

予期外の状態が発生した場合、関数は呼び出しに対して、適切な値を返すことができない可能性があります。
そんな例外的な状況に対する最善の策は、プログラムを終了させることかもしれないし、状況を報告するエラーメッセージを出力することかもしれません。また、プログラマが例外的な状況に対応するコードを用意している場合は、そのコードに適切な処置をとらせることかもしれません。


### Built-in `Exception`
### 標準装備の`例外`

```@raw html
<!--
`Exception`s are thrown when an unexpected condition has occurred. The built-in `Exception`s listed
below all interrupt the normal flow of control.
-->
```

`例外`は予想外の状況が発生したときに投げられます。以下に掲載した標準装備の例外は、すべて、通常の制御の流れを中断します。

```@raw html
<!--
| `Exception`                   |
|:----------------------------- |
| [`ArgumentError`](@ref)       |
| [`BoundsError`](@ref)         |
| `CompositeException`          |
| [`DivideError`](@ref)         |
| [`DomainError`](@ref)         |
| [`EOFError`](@ref)            |
| [`ErrorException`](@ref)      |
| [`InexactError`](@ref)        |
| [`InitError`](@ref)           |
| [`InterruptException`](@ref)  |
| `InvalidStateException`       |
| [`KeyError`](@ref)            |
| [`LoadError`](@ref)           |
| [`OutOfMemoryError`](@ref)    |
| [`ReadOnlyMemoryError`](@ref) |
| [`RemoteException`](@ref)     |
| [`MethodError`](@ref)         |
| [`OverflowError`](@ref)       |
| [`ParseError`](@ref)          |
| [`SystemError`](@ref)         |
| [`TypeError`](@ref)           |
| [`UndefRefError`](@ref)       |
| [`UndefVarError`](@ref)       |
| `UnicodeError`                |
-->
```
| `例外`                   |
|:----------------------------- |
| [`ArgumentError`](@ref)       |
| [`BoundsError`](@ref)         |
| `CompositeException`          |
| [`DivideError`](@ref)         |
| [`DomainError`](@ref)         |
| [`EOFError`](@ref)            |
| [`ErrorException`](@ref)      |
| [`InexactError`](@ref)        |
| [`InitError`](@ref)           |
| [`InterruptException`](@ref)  |
| `InvalidStateException`       |
| [`KeyError`](@ref)            |
| [`LoadError`](@ref)           |
| [`OutOfMemoryError`](@ref)    |
| [`ReadOnlyMemoryError`](@ref) |
| [`RemoteException`](@ref)     |
| [`MethodError`](@ref)         |
| [`OverflowError`](@ref)       |
| [`ParseError`](@ref)          |
| [`SystemError`](@ref)         |
| [`TypeError`](@ref)           |
| [`UndefRefError`](@ref)       |
| [`UndefVarError`](@ref)       |
| `UnicodeError`                |



```@raw html
<!--
For example, the [`sqrt()`](@ref) function throws a [`DomainError`](@ref) if applied to a negative
real value:
-->
```
たとえば、 [`sqrt()`](@ref) 関数を負の実数値に適用すると、a [`DomainError`](@ref) が投げられます。

```jldoctest
julia> sqrt(-1)
ERROR: DomainError:
sqrt will only return a complex result if called with a complex argument. Try sqrt(complex(x)).
Stacktrace:
 [1] sqrt(::Int64) at ./math.jl:434
```

```@raw html
<!--
You may define your own exceptions in the following way:
-->
```
独自の例外を次のように定義することができます。

```jldoctest
julia> struct MyCustomException <: Exception end
```
### The [`throw()`](@ref) function
### [`throw()`](@ref)　関数

```@raw html
<!--
Exceptions can be created explicitly with [`throw()`](@ref). For example, a function defined only
for nonnegative numbers could be written to [`throw()`](@ref) a [`DomainError`](@ref) if the argument
is negative:
-->
```
例外は[`throw()`](@ref) 関数を使って、明示的に作成できます。
たとえば、非負の数値のみに定義される関数は、引数が負の場合に[`DomainError`](@ref) を[`throw()`](@ref) 関数として書くことができます。

```jldoctest
julia> f(x) = x>=0 ? exp(-x) : throw(DomainError())
f (generic function with 1 method)

julia> f(1)
0.36787944117144233

julia> f(-1)
ERROR: DomainError:
Stacktrace:
 [1] f(::Int64) at ./none:1
```

```@raw html
<!--
Note that [`DomainError`](@ref) without parentheses is not an exception, but a type of exception.
It needs to be called to obtain an `Exception` object:
-->
```
[`DomainError`](@ref) は括弧が抜けると、例外ではなく、例外の型になる点に注意してください。
`例外`オブジェクトを取得するには、関数呼び出しを行う必要があります。

```jldoctest
julia> typeof(DomainError()) <: Exception
true

julia> typeof(DomainError) <: Exception
false
```

```@raw html
<!--
Additionally, some exception types take one or more arguments that are used for error reporting:
-->
```
さらに、一部の例外の型は、1つ以上の引数をとって、エラーの報告をおこないます。

```jldoctest
julia> throw(UndefVarError(:x))
ERROR: UndefVarError: x not defined
```

```@raw html
<!--
This mechanism can be implemented easily by custom exception types following the way [`UndefVarError`](@ref)
is written:
-->
```
この方法を使うと、以下のように、[`UndefVarError`](@ref)　にならった、独自の例外の型を簡単に実装できます。UndefVarError 。

```jldoctest
julia> struct MyUndefVarError <: Exception
           var::Symbol
       end

julia> Base.showerror(io::IO, e::MyUndefVarError) = print(io, e.var, " not defined")
```

```@raw html
<!--
!!! note
    When writing an error message, it is preferred to make the first word lowercase. For example,
    `size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))`

    is preferred over

    `size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))`.

    However, sometimes it makes sense to keep the uppercase first letter, for instance if an argument
    to a function is a capital letter: `size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))`.
-->
```

!!! 注意点
エラーメッセージを書くときは、最初の単語を小文字にすることをお勧めします。
例えば、 
  `size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))`
のほうが
  `size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))`.
より好ましいです。

しかし、最初の文字を意図的に大文字にする場合もたまにあります。
例えば、
`size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))`.


size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))
### Errors
### エラー

```@raw html
<!--
The [`error()`](@ref) function is used to produce an [`ErrorException`](@ref) that interrupts
the normal flow of control.

Suppose we want to stop execution immediately if the square root of a negative number is taken.
To do this, we can define a fussy version of the [`sqrt()`](@ref) function that raises an error
if its argument is negative:
-->
```
[`error()`](@ref) 関数は、[`ErrorExceptioをn`](@ref) を生成して、通常の制御の流れを中断するために使用されます。

負の数の平方根を取ると即座に実行を停止したいとします。このとき、[`sqrt()`](@ref) 関数を、引数が負の場合にエラーを発生させる、小うるさく定義することができます。




```jldoctest fussy_sqrt
julia> fussy_sqrt(x) = x >= 0 ? sqrt(x) : error("negative x not allowed")
fussy_sqrt (generic function with 1 method)

julia> fussy_sqrt(2)
1.4142135623730951

julia> fussy_sqrt(-1)
ERROR: negative x not allowed
Stacktrace:
 [1] fussy_sqrt(::Int64) at ./none:1
```

```@raw html
<!--
If `fussy_sqrt` is called with a negative value from another function, instead of trying to continue
execution of the calling function, it returns immediately, displaying the error message in the
interactive session:
-->
```
`fussy_sqrt`が、別の関数から負の値で呼び出された場合、関数呼び出しを続行しようとするのではなく、
直ちに関数から抜けて、エラーメッセージを対話セッションに表示します。

```jldoctest fussy_sqrt
julia> function verbose_fussy_sqrt(x)
           println("before fussy_sqrt")
           r = fussy_sqrt(x)
           println("after fussy_sqrt")
           return r
       end
verbose_fussy_sqrt (generic function with 1 method)

julia> verbose_fussy_sqrt(2)
before fussy_sqrt
after fussy_sqrt
1.4142135623730951

julia> verbose_fussy_sqrt(-1)
before fussy_sqrt
ERROR: negative x not allowed
Stacktrace:
 [1] fussy_sqrt at ./none:1 [inlined]
 [2] verbose_fussy_sqrt(::Int64) at ./none:3
```

### Warnings and informational messages
### 警告と情報メッセージ

```@raw html
<!--
Julia also provides other functions that write messages to the standard error I/O, but do not
throw any `Exception`s and hence do not interrupt execution:
-->
```
Juliaには、標準エラー出力にメッセージを書き出す他の関数もありますが、例外を投げないため、実行が中断されません。


```jldoctest
julia> info("Hi"); 1+1
INFO: Hi
2

julia> warn("Hi"); 1+1
WARNING: Hi
2

julia> error("Hi"); 1+1
ERROR: Hi
Stacktrace:
 [1] error(::String) at ./error.jl:21
```

### The `try/catch` statement
### The `try/catch` 文

```@raw html
<!--
The `try/catch` statement allows for `Exception`s to be tested for. For example, a customized
square root function can be written to automatically call either the real or complex square root
method on demand using `Exception`s :
-->
```
try/catch文によって、例外をテストすることができます。
たとえば、`例外`を使って、場合によって、実数の平方根メソッドまたは複素数の平方根メソッドを自動的に呼び出すような、独自の平方根関数を書くことができます。



```jldoctest
julia> f(x) = try
           sqrt(x)
       catch
           sqrt(complex(x, 0))
       end
f (generic function with 1 method)

julia> f(1)
1.0

julia> f(-1)
0.0 + 1.0im
```

```@raw html
<!--
It is important to note that in real code computing this function, one would compare `x` to zero
instead of catching an exception. The exception is much slower than simply comparing and branching.

`try/catch` statements also allow the `Exception` to be saved in a variable. In this contrived
example, the following example calculates the square root of the second element of `x` if `x`
is indexable, otherwise assumes `x` is a real number and returns its square root:
-->
```
注意すべき重要な点は、この関数が実際に計算されるときには、例外を受理するのではなくて、`x`と0を比較する点です。例外は、単なる比較や分岐よりも、はるかに遅いのです。

`try/catch`文では、例外を変数を変数に保存することができます。
無理がありますが、次の例では、`x`がインデックスを持つ場合、`x`の2番目の要素の平方根を計算し、そうでなければ,
`x`を実数と仮定して平方根を返します。

```jldoctest
julia> sqrt_second(x) = try
           sqrt(x[2])
       catch y
           if isa(y, DomainError)
               sqrt(complex(x[2], 0))
           elseif isa(y, BoundsError)
               sqrt(x)
           end
       end
sqrt_second (generic function with 1 method)

julia> sqrt_second([1 4])
2.0

julia> sqrt_second([1 -4])
0.0 + 2.0im

julia> sqrt_second(9)
3.0

julia> sqrt_second(-9)
ERROR: DomainError:
Stacktrace:
 [1] sqrt_second(::Int64) at ./none:7
```

```@raw html
<!--
Note that the symbol following `catch` will always be interpreted as a name for the exception,
so care is needed when writing `try/catch` expressions on a single line. The following code will
*not* work to return the value of `x` in case of an error:
-->
```
`catch`に続くシンボルは常に例外の名前として解釈されるため、try/catch式を1行に記述する際には注意が必要です。
次のコードは、エラーの時には、`x`の値を返しません。


```julia
try bad() catch x end
```

```@raw html
<!--
Instead, use a semicolon or insert a line break after `catch`:
-->
```


代わりに、`catch`の後では、セミコロンを使用するか、改行を挿入します。

```julia
try bad() catch; x end

try bad()
catch
    x
end
```

```@raw html
<!--
The `catch` clause is not strictly necessary; when omitted, the default return value is `nothing`.
-->
```
この`catch`節は厳密には必要ではありません。省略された場合、デフォルトの戻り値は`nothing`です。


```jldoctest
julia> try error() end # Returns nothing
```

```@raw html
<!--
The power of the `try/catch` construct lies in the ability to unwind a deeply nested computation
immediately to a much higher level in the stack of calling functions. There are situations where
no error has occurred, but the ability to unwind the stack and pass a value to a higher level
is desirable. Julia provides the [`rethrow()`](@ref), [`backtrace()`](@ref) and [`catch_backtrace()`](@ref)
functions for more advanced error handling.
-->
```
`try/catch`文が強力なのは、深く入れ子になった計算から、関数呼び出しを重ねたはるかに高いレベルまで、すぐに巻き戻すことができることにあります。エラーが発生していない場合でも、スタックを巻き戻してより高いレベルに値を渡す機能はほしいものです。
Juliaでは [`rethrow()`](@ref), [`backtrace()`](@ref) and [`catch_backtrace()`](@ref) といった さらに高度なエラー処理のための関数が用意されています。

### `finally` Clauses
### `finally` 節

```@raw html
<!--
In code that performs state changes or uses resources like files, there is typically clean-up
work (such as closing files) that needs to be done when the code is finished. Exceptions potentially
complicate this task, since they can cause a block of code to exit before reaching its normal
end. The `finally` keyword provides a way to run some code when a given block of code exits, regardless
of how it exits.

For example, here is how we can guarantee that an opened file is closed:
-->
```

状態の変更やファイルのようなリソースの使用するコードでは、一般的に、コードの終了時に行うべきクリーンアップ作業（ファイルの閉じることなど）が記述されています。
例外があると、正常に終了する前にコードブロックを終了させる可能性があるため、このタスクを複雑にする可能性があります。
この`finally`キーワードは、あるコードブロックが終了する際に、どのように終了しようと、別のあるコードを実行させる手段を提供します。

たとえば、開いているファイルを閉じることを保証する方法は次のとおりです。




```julia
f = open("file")
try
    # operate on file f
finally
    close(f)
end
```

```@raw html
<!--
When control leaves the `try` block (for example due to a `return`, or just finishing normally),
`close(f)` will be executed. If the `try` block exits due to an exception, the exception will
continue propagating. A `catch` block may be combined with `try` and `finally` as well. In this
case the `finally` block will run after `catch` has handled the error.
-->
```

制御が`try`ブロックを離れると（たとえば`return`を使ったり、通常どおり正常終了したりして）、 ブロック`close(f)`が実行されます。
`try`ブロックを例外によって抜ける場合、例外が伝播していきます。`catch`ブロックは`try`や`finally`とくみあわせてもかまいません。この場合、`finally`ブロックは`catch`ブロックがエラーを処理した後に実行されます。



## [Tasks (aka Coroutines)](@id man-tasks)

Tasks are a control flow feature that allows computations to be suspended and resumed in a flexible
manner. This feature is sometimes called by other names, such as symmetric coroutines, lightweight
threads, cooperative multitasking, or one-shot continuations.

When a piece of computing work (in practice, executing a particular function) is designated as
a [`Task`](@ref), it becomes possible to interrupt it by switching to another [`Task`](@ref).
The original [`Task`](@ref) can later be resumed, at which point it will pick up right where it
left off. At first, this may seem similar to a function call. However there are two key differences.
First, switching tasks does not use any space, so any number of task switches can occur without
consuming the call stack. Second, switching among tasks can occur in any order, unlike function
calls, where the called function must finish executing before control returns to the calling function.

This kind of control flow can make it much easier to solve certain problems. In some problems,
the various pieces of required work are not naturally related by function calls; there is no obvious
"caller" or "callee" among the jobs that need to be done. An example is the producer-consumer
problem, where one complex procedure is generating values and another complex procedure is consuming
them. The consumer cannot simply call a producer function to get a value, because the producer
may have more values to generate and so might not yet be ready to return. With tasks, the producer
and consumer can both run as long as they need to, passing values back and forth as necessary.

Julia provides a [`Channel`](@ref) mechanism for solving this problem.
A [`Channel`](@ref) is a waitable first-in first-out queue which can have
multiple tasks reading from and writing to it.

Let's define a producer task, which produces values via the [`put!`](@ref) call.
To consume values, we need to schedule the producer to run in a new task. A special [`Channel`](@ref)
constructor which accepts a 1-arg function as an argument can be used to run a task bound to a channel.
We can then [`take!()`](@ref) values repeatedly from the channel object:

```jldoctest producer
julia> function producer(c::Channel)
           put!(c, "start")
           for n=1:4
               put!(c, 2n)
           end
           put!(c, "stop")
       end;

julia> chnl = Channel(producer);

julia> take!(chnl)
"start"

julia> take!(chnl)
2

julia> take!(chnl)
4

julia> take!(chnl)
6

julia> take!(chnl)
8

julia> take!(chnl)
"stop"
```

```@raw html
<!--
One way to think of this behavior is that `producer` was able to return multiple times. Between
calls to [`put!()`](@ref), the producer's execution is suspended and the consumer has control.
-->
```
```@raw html
<!--
The returned [`Channel`](@ref) can be used as an iterable object in a `for` loop, in which case the
loop variable takes on all the produced values. The loop is terminated when the channel is closed.
-->
```
```jldoctest producer
julia> for x in Channel(producer)
           println(x)
       end
start
2
4
6
8
stop
```

Note that we did not have to explicitly close the channel in the producer. This is because
the act of binding a [`Channel`](@ref) to a [`Task()`](@ref) associates the open lifetime of
a channel with that of the bound task. The channel object is closed automatically when the task
terminates. Multiple channels can be bound to a task, and vice-versa.

While the [`Task()`](@ref) constructor expects a 0-argument function, the [`Channel()`](@ref)
method which creates a channel bound task expects a function that accepts a single argument of
type [`Channel`](@ref). A common pattern is for the producer to be parameterized, in which case a partial
function application is needed to create a 0 or 1 argument [anonymous function](@ref man-anonymous-functions).

For [`Task()`](@ref) objects this can be done either directly or by use of a convenience macro:

```julia
function mytask(myarg)
    ...
end

taskHdl = Task(() -> mytask(7))
# or, equivalently
taskHdl = @task mytask(7)
```

To orchestrate more advanced work distribution patterns, [`bind()`](@ref) and [`schedule()`](@ref)
can be used in conjunction with [`Task()`](@ref) and [`Channel()`](@ref)
constructors to explicitly link a set of channels with a set of producer/consumer tasks.

Note that currently Julia tasks are not scheduled to run on separate CPU cores.
True kernel threads are discussed under the topic of [Parallel Computing](@ref).

### Core task operations

Let us explore the low level construct [`yieldto()`](@ref) to underestand how task switching works.
`yieldto(task,value)` suspends the current task, switches to the specified `task`, and causes
that task's last [`yieldto()`](@ref) call to return the specified `value`. Notice that [`yieldto()`](@ref)
is the only operation required to use task-style control flow; instead of calling and returning
we are always just switching to a different task. This is why this feature is also called "symmetric
coroutines"; each task is switched to and from using the same mechanism.

[`yieldto()`](@ref) is powerful, but most uses of tasks do not invoke it directly. Consider why
this might be. If you switch away from the current task, you will probably want to switch back
to it at some point, but knowing when to switch back, and knowing which task has the responsibility
of switching back, can require considerable coordination. For example, [`put!()`](@ref) and [`take!()`](@ref)
are blocking operations, which, when used in the context of channels maintain state to remember
who the consumers are. Not needing to manually keep track of the consuming task is what makes [`put!()`](@ref)
easier to use than the low-level [`yieldto()`](@ref).

In addition to [`yieldto()`](@ref), a few other basic functions are needed to use tasks effectively.

  * [`current_task()`](@ref) gets a reference to the currently-running task.
  * [`istaskdone()`](@ref) queries whether a task has exited.
  * [`istaskstarted()`](@ref) queries whether a task has run yet.
  * [`task_local_storage()`](@ref) manipulates a key-value store specific to the current task.

### Tasks and events

Most task switches occur as a result of waiting for events such as I/O requests, and are performed
by a scheduler included in the standard library. The scheduler maintains a queue of runnable tasks,
and executes an event loop that restarts tasks based on external events such as message arrival.

The basic function for waiting for an event is [`wait()`](@ref). Several objects implement [`wait()`](@ref);
for example, given a `Process` object, [`wait()`](@ref) will wait for it to exit. [`wait()`](@ref)
is often implicit; for example, a [`wait()`](@ref) can happen inside a call to [`read()`](@ref)
to wait for data to be available.

In all of these cases, [`wait()`](@ref) ultimately operates on a [`Condition`](@ref) object, which
is in charge of queueing and restarting tasks. When a task calls [`wait()`](@ref) on a [`Condition`](@ref),
the task is marked as non-runnable, added to the condition's queue, and switches to the scheduler.
The scheduler will then pick another task to run, or block waiting for external events. If all
goes well, eventually an event handler will call [`notify()`](@ref) on the condition, which causes
tasks waiting for that condition to become runnable again.

A task created explicitly by calling [`Task`](@ref) is initially not known to the scheduler. This
allows you to manage tasks manually using [`yieldto()`](@ref) if you wish. However, when such
a task waits for an event, it still gets restarted automatically when the event happens, as you
would expect. It is also possible to make the scheduler run a task whenever it can, without necessarily
waiting for any events. This is done by calling [`schedule()`](@ref), or using the [`@schedule`](@ref)
or [`@async`](@ref) macros (see [Parallel Computing](@ref) for more details).

### Task states

Tasks have a `state` field that describes their execution status. A [`Task`](@ref) `state` is one of the following
symbols:

| Symbol      | Meaning                                            |
|:----------- |:-------------------------------------------------- |
| `:runnable` | Currently running, or available to be switched to  |
| `:waiting`  | Blocked waiting for a specific event               |
| `:queued`   | In the scheduler's run queue about to be restarted |
| `:done`     | Successfully finished executing                    |
| `:failed`   | Finished with an uncaught exception                |
