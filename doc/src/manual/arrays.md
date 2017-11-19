[](# [Multi-dimensional Arrays](@id man-multi-dim-arrays))
# [多次元配列](@id man-multi-dim-arrays)


```@raw html
<!--
Julia, like most technical computing languages, provides a first-class array implementation. Most
technical computing languages pay a lot of attention to their array implementation at the expense
of other containers. Julia does not treat arrays in any special way. The array library is implemented
almost completely in Julia itself, and derives its performance from the compiler, just like any
other code written in Julia. As such, it's also possible to define custom array types by inheriting
from `AbstractArray.` See the [manual section on the AbstractArray interface](@ref man-interface-array) for more details
on implementing a custom array type.

An array is a collection of objects stored in a multi-dimensional grid. In the most general case,
an array may contain objects of type `Any`. For most computational purposes, arrays should contain
objects of a more specific type, such as [`Float64`](@ref) or [`Int32`](@ref).
-->
```
Juliaでは、大抵の技術計算用の言語と同様に、ファーストクラスの配列を実装しています。
ほとんどの技術計算用の言語は、他のコンテナをないがしろにして配列の実装に多くの注意を払っています。
Juliaは配列を特別扱いしません。
配列のライブラリーは、Juliaだけでほぼ完全に実装されており、Juliaで書かれた他のコードと同様に、コンパイラーがパフォーマンスをだせるようになっています。
そして、`AbstractArray`を継承して、独自の配列の型を定義することも可能です。
独自の配列型の実装の詳細については、[マニュアルのAbstractArrayインタフェースに関する部分]（@ ref man-interface-array）を参照してください。

配列は、多次元グリッドに格納されたオブジェクトのコレクションです。
大抵の一般的な場合には、配列には`Any`型のオブジェクトが含まれていてもかまいません。
しかし殆どの計算用途には、配列は、[`Float64`](@ref) や[`Int32`](@ref)のように型により特定すべきです。


```@raw html
<!--
In general, unlike many other technical computing languages, Julia does not expect programs to
be written in a vectorized style for performance. Julia's compiler uses type inference and generates
optimized code for scalar array indexing, allowing programs to be written in a style that is convenient
and readable, without sacrificing performance, and using less memory at times.

In Julia, all arguments to functions are passed by reference. Some technical computing languages
pass arrays by value, and this is convenient in many cases. In Julia, modifications made to input
arrays within a function will be visible in the parent function. The entire Julia array library
ensures that inputs are not modified by library functions. User code, if it needs to exhibit similar
behavior, should take care to create a copy of inputs that it may modify.
-->
```
一般に、他の多くの技術計算用の言語とは異なり、Juliaでは、パフォーマンスをあげたいからといって、プログラムをベクトル化したスタイルで書く必要はありません。
Juliaのコンパイラは型推論を使用し、スカラーによる配列のインデックス参照に最適化されたコードを生成するので、パフォーマンスを犠牲にすることなく、また少ないメモリ量で使用することができます。

Juliaでは、すべての関数の引数は参照渡しです。技術計算用の言語のなかには配列を値渡しするものもあり、多くの場合に便利です。
Juliaでは、関数内で入力用の配列に加えられた変更が、親関数から参照できます。
ライブラリ関数によって入力が変更されないことが、Juliaの配列ライブラリ全体で保証されています。
ユーザーのコードも同様の動作をさせる必要がある場合は、変更用に入力のコピーを作成するよう注意してください。

[](## Arrays)
## 配列

[](### Basic Functions)
### 基本的な関数


```@raw html
<!--
| Function               | Description                                                                      |
| :--------------------- | :------------------------------------------------------------------------------- |
| [`eltype(A)`](@ref)    | the type of the elements contained in `A`                                        |
| [`length(A)`](@ref)    | the number of elements in `A`                                                    |
| [`ndims(A)`](@ref)     | the number of dimensions of `A`                                                  |
| [`size(A)`](@ref)      | a tuple containing the dimensions of `A`                                         |
| [`size(A,n)`](@ref)    | the size of `A` along dimension `n`                                              |
| [`indices(A)`](@ref)   | a tuple containing the valid indices of `A`                                      |
| [`indices(A,n)`](@ref) | a range expressing the valid indices along dimension `n`                         |
| [`eachindex(A)`](@ref) | an efficient iterator for visiting each position in `A`                          |
| [`stride(A,k)`](@ref)  | the stride (linear index distance between adjacent elements) along dimension `k` |
| [`strides(A)`](@ref)   | a tuple of the strides in each dimension                                         |
-->
```
| 関数                   | 説明                       　　　　　　　　                    |
| :--------------------- | :------------------------------------------------------------|
| [`eltype(A)`](@ref)    | `A`に含まれる要素の型。                  　　　　　　           |
| [`length(A)`](@ref)    | `A`の要素の数                             　　　　　　　       |
| [`ndims(A)`](@ref)     | `A`の次元の数         　　　　　　　                           |
| [`size(A)`](@ref)      | `A`の次元を表すタプル            　　　　　                    |
| [`size(A,n)`](@ref)    | 次元`n`における `A`のサイズ            　　　　                |
| [`indices(A)`](@ref)   | `A`の妥当なインデックスを表すタプル              　　           |
| [`indices(A,n)`](@ref) | 次元`n`の `A`の妥当なインデックスを表す範囲          　　　     |
| [`eachindex(A)`](@ref) | `A`の各位置に訪れるイテレータ                      　　　　     |
| [`stride(A,k)`](@ref)  | 次元`k`におけるストライド(隣接する要素間の線形インデックスの距離) |
| [`strides(A)`](@ref)   | 各次元のストライドのタプル                             　　　  |

[](### Construction and Initialization)
### 生成と初期化


```@raw html
<!--
Many functions for constructing and initializing arrays are provided. In the following list of
such functions, calls with a `dims...` argument can either take a single tuple of dimension sizes
or a series of dimension sizes passed as a variable number of arguments. Most of these functions
also accept a first input `T`, which is the element type of the array. If the type `T` is
omitted it will default to [`Float64`](@ref).
-->
```
配列の生成と初期化用に多くの関数が用意されています。
下記の関数のリストにある、引数に`dims...`を使う関数呼び出しは、次元のサイズを表す1個のタプル、または可変引数で渡される次元サイズの数列のいずれかを取ることができます。
これらの関数のほとんどは、配列の要素型である`T`を入力のはじめに受けとります。
型`T`が省略された場合、デフォルト の[`Float64`](@ref)になります。

```@raw html
<!--
| Function                           | Description                                                                                                                                                                                                                                  |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`Array{T}(dims...)`](@ref)        | an uninitialized dense [`Array`](@ref)                                                                                                                                                                                                       |
| [`zeros(T, dims...)`](@ref)        | an `Array` of all zeros                                                                                                                                                                                                                      |
| [`zeros(A)`](@ref)                 | an array of all zeros with the same type, element type and shape as `A`                                                                                                                                                                      |
| [`ones(T, dims...)`](@ref)         | an `Array` of all ones                                                                                                                                                                                                                       |
| [`ones(A)`](@ref)                  | an array of all ones with the same type, element type and shape as `A`                                                                                                                                                                       |
| [`trues(dims...)`](@ref)           | a [`BitArray`](@ref) with all values `true`                                                                                                                                                                                                  |
| [`trues(A)`](@ref)                 | a `BitArray` with all values `true` and the same shape as `A`                                                                                                                                                                                |
| [`falses(dims...)`](@ref)          | a `BitArray` with all values `false`                                                                                                                                                                                                         |
| [`falses(A)`](@ref)                | a `BitArray` with all values `false` and the same shape as `A`                                                                                                                                                                               |
| [`reshape(A, dims...)`](@ref)      | an array containing the same data as `A`, but with different dimensions                                                                                                                                                                      |
| [`copy(A)`](@ref)                  | copy `A`                                                                                                                                                                                                                                     |
| [`deepcopy(A)`](@ref)              | copy `A`, recursively copying its elements                                                                                                                                                                                                   |
| [`similar(A, T, dims...)`](@ref)   | an uninitialized array of the same type as `A` (dense, sparse, etc.), but with the specified element type and dimensions. The second and third arguments are both optional, defaulting to the element type and dimensions of `A` if omitted. |
| [`reinterpret(T, A)`](@ref)        | an array with the same binary data as `A`, but with element type `T`                                                                                                                                                                         |
| [`rand(T, dims...)`](@ref)         | an `Array` with random, iid [^1] and uniformly distributed values in the half-open interval ``[0, 1)``                                                                                                                                       |
| [`randn(T, dims...)`](@ref)        | an `Array` with random, iid and standard normally distributed values                                                                                                                                                                         |
| [`eye(T, n)`](@ref)                | `n`-by-`n` identity matrix                                                                                                                                                                                                                   |
| [`eye(T, m, n)`](@ref)             | `m`-by-`n` identity matrix                                                                                                                                                                                                                   |
| [`linspace(start, stop, n)`](@ref) | range of `n` linearly spaced elements from `start` to `stop`                                                                                                                                                                                 |
| [`fill!(A, x)`](@ref)              | fill the array `A` with the value `x`                                                                                                                                                                                                        |
| [`fill(x, dims...)`](@ref)         | an `Array` filled with the value `x`                                                                                                                                                                                                         |
-->
```

| 関数                          | 説明                                                                                                                                                                                                                                  |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`Array{T}(dims...)`](@ref)        | 初期化されていない密な [`Array`](@ref)                                                                                                                                                                                                       |
| [`zeros(T, dims...)`](@ref)        | すべてが0の `配列`                                                                                                                                                                                                                       |
| [`zeros(A)`](@ref)                 | 要素の型やシェイプが`A`である、すべてが同じ型の0である配列                                                                                                                                                                      |
| [`ones(T, dims...)`](@ref)         | すべてが1の `配列`                                                                                                                                                                                                                       |
| [`ones(A)`](@ref)                  |  要素の型やシェイプが`A`である、すべてが同じ型の1である配列                                                                                                                                                                       |
| [`trues(dims...)`](@ref)           | すべての値が`true`である[`BitArray`](@ref)                                                                                                                                                                                                  |
| [`trues(A)`](@ref)                 | すべての値が`true`でシェイプが`A`と同じ`BitArray`                                                                                                                                       |
| [`falses(dims...)`](@ref)          | すべての値が`false`である`BitArray`                                                                                                                                                                                                         |
| [`falses(A)`](@ref)                | すべての値が`false`でシェイプが`A`と同じ`BitArray``                                                                                                                                                                               |
| [`reshape(A, dims...)`](@ref)      | `A`と同じデータだが次元の違う配列                                                                                                                                                                      |
| [`copy(A)`](@ref)                  | `A`をコピーする                                                                                                                                                                                                                                     |
| [`deepcopy(A)`](@ref)              | `A`をコピーする（再帰的にその要素もコピーする）                                                                                                                                               |
| [`similar(A, T, dims...)`](@ref)   | `A`(密・疎など)と型が同じだが初期化されていない配列、要素の型や次元が指定されたもの。2番目と3番目の引数は共に省略可能で、省略した時のデフォルト値は`A`の要素の型と次元になる。 |
| [`reinterpret(T, A)`](@ref)        | `A`とバイナリデータが同じだが、要素の型が`T`である配列                                                                                                                                                          |
| [`rand(T, dims...)`](@ref)         | 半開区間``[0, 1)``で独立同分布、一様分布の乱数の`配列`                                                                                                                                       |
| [`randn(T, dims...)`](@ref)        | 独立同分布、標準正規分布の乱数の`配列`                                                                                                                                        |
| [`eye(T, n)`](@ref)                | `n`×`n` の単位行列                                                                                                                                                                                                                   |
| [`eye(T, m, n)`](@ref)             | `m`×`n` の単位行列                                                                                                                                                                                                                       |
| [`linspace(start, stop, n)`](@ref) | `start`から`stop`まで`n`個の要素が等間隔にある範囲                                                                                                                                                                                 |
| [`fill!(A, x)`](@ref)              | 配列`A`を値`x`で埋める                                                                                                                                                                                                        |
| [`fill(x, dims...)`](@ref)         | 値が`x`で埋まった`配列`                                                     

```@raw html
<!--
[^1]: *iid*, independently and identically distributed.
-->
```
[^1]: *iid*, 独立同分布


```@raw html
<!--
The syntax `[A, B, C, ...]` constructs a 1-d array (vector) of its arguments. If all
arguments have a common [promotion type](@ref conversion-and-promotion) then they get
converted to that type using `convert()`.
-->
```
構文`[A, B, C, ...]`は、その引数からなる1次元配列（ベクトル）を生成します。
すべての引数が共通の[昇格した型](@ref conversion-and-promotion)を持つ場合、これらの引数は`convert()`を使用してその型に変換されます。

[](### Concatenation)
### 連結


```@raw html
<!--
Arrays can be constructed and also concatenated using the following functions:
-->
```
配列は、以下の関数を使用して生成・連結することもできます。

```@raw html
<!--
| Function               | Description                                          |
| :--------------------- | :--------------------------------------------------- |
| [`cat(k, A...)`](@ref) | concatenate input n-d arrays along the dimension `k` |
| [`vcat(A...)`](@ref)   | shorthand for `cat(1, A...)`                         |
| [`hcat(A...)`](@ref)   | shorthand for `cat(2, A...)`                         |
-->
```
| 関数                   | 説明                                          |
| :--------------------- | :------------------------------------------- |
| [`cat(k, A...)`](@ref) | 入力した n次元の配列を次元kに沿って連結する      |
| [`vcat(A...)`](@ref)   | `cat(1, A...)`の簡略表記                      |
| [`hcat(A...)`](@ref)   | `cat(2, A...)`の簡略表記                      |


```@raw html
<!--
Scalar values passed to these functions are treated as 1-element arrays.

The concatenation functions are used so often that they have special syntax:
-->
```
これらの関数に渡されるスカラー値は、1要素の配列として扱われます。

連結関数はよく使用されて、特殊な構文もあります。

```@raw html
<!--
| Expression        | Calls             |
| :---------------- | :---------------- |
| `[A; B; C; ...]`  | [`vcat()`](@ref)  |
| `[A B C ...]`     | [`hcat()`](@ref)  |
| `[A B; C D; ...]` | [`hvcat()`](@ref) |
-->
```

| 式                | 関数呼び出し        |
| :---------------- | :---------------- |
| `[A; B; C; ...]`  | [`vcat()`](@ref)  |
| `[A B C ...]`     | [`hcat()`](@ref)  |
| `[A B; C D; ...]` | [`hvcat()`](@ref) |


```@raw html
<!--
[`hvcat()`](@ref) concatenates in both dimension 1 (with semicolons) and dimension 2 (with spaces).
-->
```
[`hvcat()`](@ref) は1次元方向（セミコロンを使って）と2次元方向（空白を使って）の両方向に連結します。

[](### Typed array initializers)
### 型指定での配列の初期化


```@raw html
<!--
An array with a specific element type can be constructed using the syntax `T[A, B, C, ...]`. This
will construct a 1-d array with element type `T`, initialized to contain elements `A`, `B`, `C`,
etc. For example `Any[x, y, z]` constructs a heterogeneous array that can contain any values.

Concatenation syntax can similarly be prefixed with a type to specify the element type of the
result.
-->
```

`T[A, B, C, ...]`という構文を使って、要素の型を指定して配列を生成できます。
これは、要素の型が`T`の1次元配列を生成して、要素が`A`, `B`, `C`などとなるように初期化します。
例えば`Any[x, y, z]`とすると、任意の値を含むことができる型の不均質な配列を生成します。

連結構文にも同様に型の接頭辞を付けて、演算結果の要素の型を指定することができます。


```jldoctest
julia> [[1 2] [3 4]]
1×4 Array{Int64,2}:
 1  2  3  4

julia> Int8[[1 2] [3 4]]
1×4 Array{Int8,2}:
 1  2  3  4
```

[](### Comprehensions)
### 内包表記


```@raw html
<!--
Comprehensions provide a general and powerful way to construct arrays. Comprehension syntax is
similar to set construction notation in mathematics:
-->
```
内包表記は、配列を生成する一般的かつ強力な方法です。
内包表記構文は、数学でつかう集合を生成する表記法と似ています。



```
A = [ F(x,y,...) for x=rx, y=ry, ... ]
```


```@raw html
<!--
The meaning of this form is that `F(x,y,...)` is evaluated with the variables `x`, `y`, etc. taking
on each value in their given list of values. Values can be specified as any iterable object, but
will commonly be ranges like `1:n` or `2:(n-1)`, or explicit arrays of values like `[1.2, 3.4, 5.7]`.
The result is an N-d dense array with dimensions that are the concatenation of the dimensions
of the variable ranges `rx`, `ry`, etc. and each `F(x,y,...)` evaluation returns a scalar.

The following example computes a weighted average of the current element and its left and right
neighbor along a 1-d grid. :
-->
```
この形式は、`F(x,y,...)`が変数`x`、`y`などが、与えられたリストから値をそれぞれ取るものとして、評価されることを意味します。
値は、任意のイテラブルオブジェクトによって指定することができますが、通常は`1:n`や`2:(n-1)`のような範囲か、`[1.2, 3.4, 5.7]`のように明示的に値をいれた配列をつかいます。
その演算結果は、N次元の密な配列で、 `rx`、`ry`などの可変範囲の次元を連結した次元となり、各`F(x,y,...)` の評価はスカラーとします。

次の例では、1次元グリッドに沿った現在の要素とその両隣との加重平均を計算します。

```julia-repl
julia> x = rand(8)
8-element Array{Float64,1}:
 0.843025
 0.869052
 0.365105
 0.699456
 0.977653
 0.994953
 0.41084
 0.809411

julia> [ 0.25*x[i-1] + 0.5*x[i] + 0.25*x[i+1] for i=2:length(x)-1 ]
6-element Array{Float64,1}:
 0.736559
 0.57468
 0.685417
 0.912429
 0.8446
 0.656511
```


```@raw html
<!--
The resulting array type depends on the types of the computed elements. In order to control the
type explicitly, a type can be prepended to the comprehension. For example, we could have requested
the result in single precision by writing:
-->
```
結果の配列の型は、計算した要素の型次第です。
型を明示的に制御するために、内包表記の前に型を付けることができます。
たとえば、次のように書いて結果を単精度とすることができます。



```julia
Float32[ 0.25*x[i-1] + 0.5*x[i] + 0.25*x[i+1] for i=2:length(x)-1 ]
```

[](### Generator Expressions)
### ジェネレータ式


```@raw html
<!--
Comprehensions can also be written without the enclosing square brackets, producing an object
known as a generator. This object can be iterated to produce values on demand, instead of allocating
an array and storing them in advance (see [Iteration](@ref)). For example, the following expression
sums a series without allocating memory:
-->
```
また、内包表記は角括弧を使わずに書くと、ジェネレータと呼ばれるオブジェクトを作成することもできます。
このオブジェクトは、必要に応じて値を反復して生成することができ、事前に配列をメモリを割り当てて格納したりしません（[Iteration](@ref)参照）。
たとえば、次の式は、メモリの割り当てをせずに数列の和を算出します。

```jldoctest
julia> sum(1/n^2 for n=1:1000)
1.6439345666815615
```


```@raw html
<!--
When writing a generator expression with multiple dimensions inside an argument list, parentheses
are needed to separate the generator from subsequent arguments:
-->
```

多次元のジェネレータ式を引数リスト内に書く場合、ジェネレータと後に続く引数を区切るには括弧が必要です。

```julia-repl
julia> map(tuple, 1/(i+j) for i=1:2, j=1:2, [1:4;])
ERROR: syntax: invalid iteration specification
```


```@raw html
<!--
All comma-separated expressions after `for` are interpreted as ranges. Adding parentheses lets
us add a third argument to `map`:
-->
```
カンマで区切られた`for`式の後はすべて範囲として解釈されます。
括弧を追加して、`map`の3番目の引数を追加できます。

```jldoctest
julia> map(tuple, (1/(i+j) for i=1:2, j=1:2), [1 3; 2 4])
2×2 Array{Tuple{Float64,Int64},2}:
 (0.5, 1)       (0.333333, 3)
 (0.333333, 2)  (0.25, 4)
```


```@raw html
<!--
Ranges in generators and comprehensions can depend on previous ranges by writing multiple `for`
keywords:
-->
```
ジェネレータと内包表記の範囲は、複数の`for` キーワードを記述した時、前の範囲に依存する場合があります。

```jldoctest
julia> [(i,j) for i=1:3 for j=1:i]
6-element Array{Tuple{Int64,Int64},1}:
 (1, 1)
 (2, 1)
 (2, 2)
 (3, 1)
 (3, 2)
 (3, 3)
```


```@raw html
<!--
In such cases, the result is always 1-d.

Generated values can be filtered using the `if` keyword:
-->
```
そうした場合、結果は常に1次元です。

生成された値は、`if`キーワードを使ってフィルタリングできます。

```jldoctest
julia> [(i,j) for i=1:3 for j=1:i if i+j == 4]
2-element Array{Tuple{Int64,Int64},1}:
 (2, 2)
 (3, 1)
```

[](### [Indexing](@id man-array-indexing))
### [インデックスによる参照](@id man-array-indexing)


```@raw html
<!--
The general syntax for indexing into an n-dimensional array A is:
-->
```
n次元配列Aに対するインデックスによる参照の一般的な構文は次のとおりです。

```
X = A[I_1, I_2, ..., I_n]
```


```@raw html
<!--
where each `I_k` may be a scalar integer, an array of integers, or any other
[supported index](@ref man-supported-index-types). This includes
[`Colon`](@ref) (`:`) to select all indices within the entire dimension,
ranges of the form `a:c` or `a:b:c` to select contiguous or strided
subsections, and arrays of booleans to select elements at their `true` indices.

If all the indices are scalars, then the result `X` is a single element from the array `A`. Otherwise,
`X` is an array with the same number of dimensions as the sum of the dimensionalities of all the
indices.
-->
```
各`I_k`はスカラー整数、整数の配列、又は他の[利用可能なインデックス](@ref man-supported-index-types)で構いません。
この例として、次元全体のすべてのインデックスを選択する[`Colon`](@ref) (`:`)、
 `a:c` や`a:b:c`といった形の連続した範囲や飛び飛びの部分列になった範囲、
および`true`インデックスに当たる要素を選択するブール値の配列などがあります。

すべてのインデックスがスカラーの場合、結果`X`は配列`A`の単一の要素になります。
それ以外の場合 `X`は、すべてのインデックスの次元の合計と同じ次元の数の配列になります。
``


```@raw html
<!--
If all indices are vectors, for example, then the shape of `X` would be `(length(I_1), length(I_2), ..., length(I_n))`,
with location `(i_1, i_2, ..., i_n)` of `X` containing the value `A[I_1[i_1], I_2[i_2], ..., I_n[i_n]]`.
If `I_1` is changed to a two-dimensional matrix, then `X` becomes an `n+1`-dimensional array of
shape `(size(I_1, 1), size(I_1, 2), length(I_2), ..., length(I_n))`. The matrix adds a dimension.
The location `(i_1, i_2, i_3, ..., i_{n+1})` contains the value at `A[I_1[i_1, i_2], I_2[i_3], ..., I_n[i_{n+1}]]`.
All dimensions indexed with scalars are dropped. For example, the result of `A[2, I, 3]` is an
array with size `size(I)`. Its `i`th element is populated by `A[2, I[i], 3]`.

As a special part of this syntax, the `end` keyword may be used to represent the last index of
each dimension within the indexing brackets, as determined by the size of the innermost array
being indexed. Indexing syntax without the `end` keyword is equivalent to a call to `getindex`:
-->
```

すべてのインデックスが、ベクトルである場合、例えば、`X`のシェイプが`(length(I_1), length(I_2), ..., length(I_n))`で、`X`の`(i_1, i_2, ..., i_n)`に当たる値が、`A[I_1[i_1], I_2[i_2], ..., I_n[i_n]]`の場合を考えます。
`I_1`を2次元の行列に変更すると、`X`はn+1次元の配列で、シェイプが`(size(I_1, 1), size(I_1, 2), length(I_2), ..., length(I_n))`になります。
行列の次元が増えます。
`(i_1, i_2, i_3, ..., i_{n+1})`の場所に当たる値が、`A[I_1[i_1, i_2], I_2[i_3], ..., I_n[i_{n+1}]]`になります。
スカラでインデックス付けされたすべての次元は削除されます。
たとえば、`A[2, I, 3]`の結果はサイズ`size(I)`の配列です。
`i`番目の要素は、値が`A[2, I[i], 3]`になります。

この構文の特別な部分として、`end`キーワードを、各次元の最後のインデックスを表すために、角括弧の中で利用できます。
これは、一番内側のインデックス付けされた配列のサイズから決めることができるからです。
`end`キーワードを使わないインデックスによる参照は、`getindex`による呼び出しと同等です。


```
X = getindex(A, I_1, I_2, ..., I_n)
```


```@raw html
<!--
Example:
-->
```
例：

```jldoctest
julia> x = reshape(1:16, 4, 4)
4×4 Base.ReshapedArray{Int64,2,UnitRange{Int64},Tuple{}}:
 1  5   9  13
 2  6  10  14
 3  7  11  15
 4  8  12  16

julia> x[2:3, 2:end-1]
2×2 Array{Int64,2}:
 6  10
 7  11

julia> x[1, [2 3; 4 1]]
2×2 Array{Int64,2}:
  5  9
 13  1
```


```@raw html
<!--
Empty ranges of the form `n:n-1` are sometimes used to indicate the inter-index location between
`n-1` and `n`. For example, the [`searchsorted()`](@ref) function uses this convention to indicate
the insertion point of a value not found in a sorted array:
-->
```

 `n:n-1`といった形の空の範囲は、`n-1`と`n`の間のインデックス間の位置を示すために使用されることが時折あります。
 たとえば、[`searchsorted()`](@ref)関数はこの規則を使用して、整列した配列で値が見つからない時に挿入する場所を示します。



```jldoctest
julia> a = [1,2,5,6,7];

julia> searchsorted(a, 3)
3:2
```

[](### Assignment)
### 代入


```@raw html
<!--
The general syntax for assigning values in an n-dimensional array A is:
-->
```
n次元配列`A`に値を代入する一般的な構文は次のとおりです。
```
A[I_1, I_2, ..., I_n] = X
```


```@raw html
<!--

where each `I_k` may be a scalar integer, an array of integers, or any other
[supported index](@ref man-supported-index-types). This includes
[`Colon`](@ref) (`:`) to select all indices within the entire dimension,
ranges of the form `a:c` or `a:b:c` to select contiguous or strided
subsections, and arrays of booleans to select elements at their `true` indices.

If `X` is an array, it must have the same number of elements as the product of the lengths of
the indices: `prod(length(I_1), length(I_2), ..., length(I_n))`. The value in location `I_1[i_1], I_2[i_2], ..., I_n[i_n]`
of `A` is overwritten with the value `X[i_1, i_2, ..., i_n]`. If `X` is not an array, its value
is written to all referenced locations of `A`.

Just as in [Indexing](@ref man-array-indexing), the `end` keyword may be used
to represent the last index of each dimension within the indexing brackets, as
determined by the size of the array being assigned into. Indexed assignment
syntax without the `end` keyword is equivalent to a call to
[`setindex!()`](@ref):

-->
```
各`I_k`はスカラー整数、整数の配列、又は他の[利用可能なインデックス](@ref man-supported-index-types)で構いません。
この例として、次元全体のすべてのインデックスを選択する[`Colon`](@ref) (`:`)、
 `a:c` や`a:b:c`といった形の連続した範囲や飛び飛びの部分列になった範囲、
および`true`インデックスに当たる要素を選択するブール値の配列などがあります。

`X`が配列の場合、インデックスの長さの積と同じ数の要素数でなければなりません。つまり`prod(length(I_1), length(I_2), ..., length(I_n))`です。
`A`の`I_1[i_1], I_2[i_2], ..., I_n[i_n]`の位置の値は、`X[i_1, i_2, ..., i_n]`の値で上書きされます。
`X`が配列ではない場合、`A`のすべての参照されている位置に値が書き込まれます。

 [インデックスによる参照](@ref man-array-indexing)と同様に、`end`キーワードを、各次元の最後のインデックスを表すために、角括弧の中で利用できます。
これは、代入される配列のサイズから決まるからです。
`end`キーワードを使わないインデックスによる参照は、[`setindex!()`](@ref)による呼び出しと同等です。


```
setindex!(A, X, I_1, I_2, ..., I_n)
```


```@raw html
<!--
Example:
-->
```
例

```jldoctest
julia> x = collect(reshape(1:9, 3, 3))
3×3 Array{Int64,2}:
 1  4  7
 2  5  8
 3  6  9

julia> x[1:2, 2:3] = -1
-1

julia> x
3×3 Array{Int64,2}:
 1  -1  -1
 2  -1  -1
 3   6   9
```

[](### [Supported index types](@id man-supported-index-types))
### [利用可能なインデックス型](@id man-supported-index-types)


```@raw html
<!--
In the expression `A[I_1, I_2, ..., I_n]`, each `I_k` may be a scalar index, an
array of scalar indices, or an object that represents an array of scalar
indices and can be converted to such by [`to_indices`](@ref):
-->
```

`A[I_1, I_2, ..., I_n]`という式で、各`I_k`は、スカラーインデックスであったり、スカラーインデックスの配列であったり、またはスカラーインデックスの配列を表すオブジェクトで[`to_indices`](@ref)を使ってそれに変換できるものであったりします。



```@raw html
<!--
1. A scalar index. By default this includes:
    * Non-boolean integers
    * `CartesianIndex{N}`s, which behave like an `N`-tuple of integers spanning multiple dimensions (see below for more details)
2. An array of scalar indices. This includes:
    * Vectors and multidimensional arrays of integers
    * Empty arrays like `[]`, which select no elements
    * `Range`s of the form `a:c` or `a:b:c`, which select contiguous or strided subsections from `a` to `c` (inclusive)
    * Any custom array of scalar indices that is a subtype of `AbstractArray`
    * Arrays of `CartesianIndex{N}` (see below for more details)
3. An object that represents an array of scalar indices and can be converted to such by [`to_indices`](@ref). By default this includes:
    * [`Colon()`](@ref) (`:`), which represents all indices within an entire dimension or across the entire array
    * Arrays of booleans, which select elements at their `true` indices (see below for more details)
-->
```

1. スカラーインデックス。デフォルトでは以下のようなものがあります。
    *非ブール整数
    *`CartesianIndex{N}`:多次元にまたがるN個の整数の組のように振る舞います（詳細は下記参照）
2. スカラーインデックスの配列。以下のようなものがあります。
    *ベクトルや整数の多次元配列
    *`[]`のような空の配列:要素がない
    *`a:c`や`a:b:c`のような形をした`範囲`:`a`から`c`(両端を含む)までの連続した、または飛び飛びの要素
    *スカラーインデックスの独自の配列:AbstractArrayのサブタイプ
    *`CartesianIndex{N}`の配列:（詳細は下記参照）
3. スカラーインデックスの配列を表すオブジェクトで[`to_indices`](@ref)を使ってそれに変換できるものにはデフォルトでは以下のものあります。
    *[`Colon()`](@ref) (`:`):次元全体または配列全体にわたるすべてのインデックスを表します
    *ブール値の配列:インデックスが`true`の要素を選択する（詳細は下記を参照）



[](#### Cartesian indices)
#### 直積インデックス


```@raw html
<!--
The special `CartesianIndex{N}` object represents a scalar index that behaves
like an `N`-tuple of integers spanning multiple dimensions.  For example:
-->
```
特殊なオブジェクトである`CartesianIndex{N}`は、多次元にまたがる`N`個の整数の組のように動作するスカラーインデックスを表します。例えば、

```jldoctest cartesianindex
julia> A = reshape(1:32, 4, 4, 2);

julia> A[3, 2, 1]
7

julia> A[CartesianIndex(3, 2, 1)] == A[3, 2, 1] == 7
true
```


```@raw html
<!--
Considered alone, this may seem relatively trivial; `CartesianIndex` simply
gathers multiple integers together into one object that represents a single
multidimensional index. When combined with other indexing forms and iterators
that yield `CartesianIndex`es, however, this can lead directly to very elegant
and efficient code. See [Iteration](@ref) below, and for some more advanced
examples, see [this blog post on multidimensional algorithms and
iteration](https://julialang.org/blog/2016/02/iteration).

Arrays of `CartesianIndex{N}` are also supported. They represent a collection
of scalar indices that each span `N` dimensions, enabling a form of indexing
that is sometimes referred to as pointwise indexing. For example, it enables
accessing the diagonal elements from the first "page" of `A` from above:
-->
```
単体だけを考える時は、これは比較的簡単に思えるかもしれません。
`CartesianIndex`は1つの多次元インデックスを表す1つのオブジェクトに複数の整数をまとめているだけです。
しかし、`CartesianIndex`を他のインデックスフォームやイテレータと組み合わせると直ちに、非常にエレガントで効率的なコードとなる可能性があります。以下の[繰返し](@ref)を参照してください。
より高度な例については、[多次元アルゴリズムと繰返しに関するこのブログ記事](https://julialang.org/blog/2016/02/iteration)を参照してください。

`CartesianIndex{N}`の配列も利用可能です。
これらは、それぞれがN次元にまたがるスカラーインデックスの集合を表し、ポイントワイズインデックスとも呼ばれるインデックスの形式が可能になります。
たとえば、前述の`A`の最初の「ページ」から対角線要素にアクセスすることができます。



```jldoctest cartesianindex
julia> page = A[:,:,1]
4×4 Array{Int64,2}:
 1  5   9  13
 2  6  10  14
 3  7  11  15
 4  8  12  16

julia> page[[CartesianIndex(1,1),
             CartesianIndex(2,2),
             CartesianIndex(3,3),
             CartesianIndex(4,4)]]
4-element Array{Int64,1}:
  1
  6
 11
 16
```


```@raw html
This can be expressed much more simply with [dot broadcasting](@ref man-vectorized)
and by combining it with a normal integer index (instead of extracting the
first `page` from `A` as a separate step). It can even be combined with a `:`
to extract both diagonals from the two pages at the same time:
-->
```
これは、[ドットブロードキャスト](@ref man-vectorized)を通常の整数インデックスと組み合わせて使うと、（`A`から最初の`ページ`を別の処理として抽出するよりも）もっと単純に表現できます。
さらに、`:`と組み合わせて 、同時に2つのページから両方の対角線を抽出することさえもできます。



```jldoctest cartesianindex
julia> A[CartesianIndex.(indices(A, 1), indices(A, 2)), 1]
4-element Array{Int64,1}:
  1
  6
 11
 16

julia> A[CartesianIndex.(indices(A, 1), indices(A, 2)), :]
4×2 Array{Int64,2}:
  1  17
  6  22
 11  27
 16  32
```


```@raw html
<!--
!!! warning

    `CartesianIndex` and arrays of `CartesianIndex` are not compatible with the
    `end` keyword to represent the last index of a dimension. Do not use `end`
    in indexing expressions that may contain either `CartesianIndex` or arrays thereof.
-->
```
!!!警告
    `CartesianIndex`と`CartesianIndex`の配列は、次元の最後のインデックスを表すキーワード`end`と一緒に使うことができません。
    `end`を`CartesianIndex`やその配列を含む可能性のある式のインデックス表現として使用しないでください。



[](#### Logical indexing)
#### 論理インデックスによる参照


```@raw html
<!--
Often referred to as logical indexing or indexing with a logical mask, indexing
by a boolean array selects elements at the indices where its values are `true`.
Indexing by a boolean vector `B` is effectively the same as indexing by the
vector of integers that is returned by [`find(B)`](@ref). Similarly, indexing
by a `N`-dimensional boolean array is effectively the same as indexing by the
vector of `CartesianIndex{N}`s where its values are `true`. A logical index
must be a vector of the same length as the dimension it indexes into, or it
must be the only index provided and match the size and dimensionality of the
array it indexes into. It is generally more efficient to use boolean arrays as
indices directly instead of first calling [`find()`](@ref).
-->
```

論理的なインデックスまたは論理的なマスクによるインデックスと呼ばれることが多いが、ブール値配列によるインデックスは、その値が`true`である索引の要素を選択します。
ブール値ベクトル`B`によるインデックスは、[`find(B)`](@ref)によって返される整数ベクトルによるインデックスと事実上同じです。
同様に、N次元のブール値配列によるインデックスは、`CartesianIndex{N}`でその値が`true`のベクトルによるインデックスと事実上同じです。
論理インデックスは、インデックスする対象の次元と同じ長さのベクトルでなければならず、また、インデックス対象の配列の唯一のインデックスであり、インデックス対象の配列のサイズと次元に一致していなければなりません。
通常、ブール値配列を直接インデックスとして使用する方が、[`find()`](@ref)を最初に呼び出すより効率的です。


```jldoctest
julia> x = reshape(1:16, 4, 4)
4×4 Base.ReshapedArray{Int64,2,UnitRange{Int64},Tuple{}}:
 1  5   9  13
 2  6  10  14
 3  7  11  15
 4  8  12  16

julia> x[[false, true, true, false], :]
2×4 Array{Int64,2}:
 2  6  10  14
 3  7  11  15

julia> mask = map(ispow2, x)
4×4 Array{Bool,2}:
  true  false  false  false
  true  false  false  false
 false  false  false  false
  true   true  false   true

julia> x[mask]
5-element Array{Int64,1}:
  1
  2
  4
  8
 16
```

[](### Iteration)
### 繰返し


```@raw html
<!--
The recommended ways to iterate over a whole array are
-->
```
配列全体に対して繰返し処理を行う推奨方法は次のとおりです。

```julia
for a in A
    # Do something with the element a
end

for i in eachindex(A)
    # Do something with i and/or A[i]
end
```


```@raw html
<!--
The first construct is used when you need the value, but not index, of each element. In the second
construct, `i` will be an `Int` if `A` is an array type with fast linear indexing; otherwise,
it will be a `CartesianIndex`:
-->
```
1番目の例は、各要素の値ではなくインデックスを必要とするときに使用されます。
2番目の例で、`A`が高速リニアインデックスを持つ配列型の時、`i`は、`Int`型 です。
それ以外の場合は、`CartesianIndex`になります。


```jldoctest
julia> A = rand(4,3);

julia> B = view(A, 1:3, 2:3);

julia> for i in eachindex(B)
           @show i
       end
i = CartesianIndex{2}((1, 1))
i = CartesianIndex{2}((2, 1))
i = CartesianIndex{2}((3, 1))
i = CartesianIndex{2}((1, 2))
i = CartesianIndex{2}((2, 2))
i = CartesianIndex{2}((3, 2))
```


```@raw html
<!--
In contrast with `for i = 1:length(A)`, iterating with `eachindex` provides an efficient way to
iterate over any array type.
-->
```

`for i = 1:length(A)`と比較すると、`eachindex`を使った繰返しは、任意の配列型に対して効率的な繰返し処理が可能です。

[](### Array traits)
### 配列のトレイト


```@raw html
<!--
If you write a custom [`AbstractArray`](@ref) type, you can specify that it has fast linear indexing using
-->
```
独自の[`AbstractArray`](@ref)型を記述する場合は、以下のように高速リニアインデックスを使って指定できます。

```julia
Base.IndexStyle(::Type{<:MyArray}) = IndexLinear()
```


```@raw html
<!--
This setting will cause `eachindex` iteration over a `MyArray` to use integers. If you don't
specify this trait, the default value `IndexCartesian()` is used.
-->
```
この設定では`eachindex`が`MyArray`にたいして行う繰返し処理には整数が使われます。
このトレイトを指定しない場合、デフォルトでは`IndexCartesian()`が使われます。

[](### Array and Vectorized Operators and Functions)
### 配列とベクトル化演算子・関数


```@raw html
<!--
The following operators are supported for arrays:
-->
```


```@raw html
<!--
1. Unary arithmetic -- `-`, `+`
2. Binary arithmetic -- `-`, `+`, `*`, `/`, `\`, `^`
3. Comparison -- `==`, `!=`, `≈` ([`isapprox`](@ref)), `≉`
-->
```


```@raw html
<!--
Most of the binary arithmetic operators listed above also operate elementwise
when one argument is scalar: `-`, `+`, and `*` when either argument is scalar,
and `/` and `\` when the denominator is scalar. For example, `[1, 2] + 3 == [4, 5]`
and `[6, 4] / 2 == [3, 2]`.

Additionally, to enable convenient vectorization of mathematical and other operations,
Julia [provides the dot syntax](@ref man-vectorized) `f.(args...)`, e.g. `sin.(x)`
or `min.(x,y)`, for elementwise operations over arrays or mixtures of arrays and
scalars (a [Broadcasting](@ref) operation); these have the additional advantage of
"fusing" into a single loop when combined with other dot calls, e.g. `sin.(cos.(x))`.
-->
```


```@raw html
<!--
Also, *every* binary operator supports a [dot version](@ref man-dot-operators)
that can be applied to arrays (and combinations of arrays and scalars) in such
[fused broadcasting operations](@ref man-vectorized), e.g. `z .== sin.(x .* y)`.

Note that comparisons such as `==` operate on whole arrays, giving a single boolean
answer. Use dot operators like `.==` for elementwise comparisons. (For comparison
operations like `<`, *only* the elementwise `.<` version is applicable to arrays.)

Also notice the difference between `max.(a,b)`, which `broadcast`s [`max()`](@ref)
elementwise over `a` and `b`, and `maximum(a)`, which finds the largest value within
`a`. The same relationship holds for `min.(a,b)` and `minimum(a)`.
-->
```

[](### Broadcasting)
### ブロードキャスト


```@raw html
<!--
It is sometimes useful to perform element-by-element binary operations on arrays of different
sizes, such as adding a vector to each column of a matrix. An inefficient way to do this would
be to replicate the vector to the size of the matrix:
-->
```

```julia-repl
julia> a = rand(2,1); A = rand(2,3);

julia> repmat(a,1,3)+A
2×3 Array{Float64,2}:
 1.20813  1.82068  1.25387
 1.56851  1.86401  1.67846
```


```@raw html
<!--
This is wasteful when dimensions get large, so Julia offers [`broadcast()`](@ref), which expands
singleton dimensions in array arguments to match the corresponding dimension in the other array
without using extra memory, and applies the given function elementwise:
-->
```

```julia-repl
julia> broadcast(+, a, A)
2×3 Array{Float64,2}:
 1.20813  1.82068  1.25387
 1.56851  1.86401  1.67846

julia> b = rand(1,2)
1×2 Array{Float64,2}:
 0.867535  0.00457906

julia> broadcast(+, a, b)
2×2 Array{Float64,2}:
 1.71056  0.847604
 1.73659  0.873631
```


```@raw html
<!--
[Dotted operators](@ref man-dot-operators) such as `.+` and `.*` are equivalent
to `broadcast` calls (except that they fuse, as described below). There is also a
[`broadcast!()`](@ref) function to specify an explicit destination (which can also
be accessed in a fusing fashion by `.=` assignment), and functions [`broadcast_getindex()`](@ref)
and [`broadcast_setindex!()`](@ref) that broadcast the indices before indexing. Moreover, `f.(args...)`
is equivalent to `broadcast(f, args...)`, providing a convenient syntax to broadcast any function
([dot syntax](@ref man-vectorized)). Nested "dot calls" `f.(...)` (including calls to `.+` etcetera)
[automatically fuse](@ref man-dot-operators) into a single `broadcast` call.

Additionally, [`broadcast()`](@ref) is not limited to arrays (see the function documentation),
it also handles tuples and treats any argument that is not an array, tuple or `Ref` (except for `Ptr`) as a "scalar".
-->
```

```jldoctest
julia> convert.(Float32, [1, 2])
2-element Array{Float32,1}:
 1.0
 2.0

julia> ceil.((UInt8,), [1.2 3.4; 5.6 6.7])
2×2 Array{UInt8,2}:
 0x02  0x04
 0x06  0x07

julia> string.(1:3, ". ", ["First", "Second", "Third"])
3-element Array{String,1}:
 "1. First"
 "2. Second"
 "3. Third"
```

[](### Implementation)
### 実装


```@raw html
<!--
The base array type in Julia is the abstract type `AbstractArray{T,N}`. It is parametrized by
the number of dimensions `N` and the element type `T`. `AbstractVector` and `AbstractMatrix` are
aliases for the 1-d and 2-d cases. Operations on `AbstractArray` objects are defined using higher
level operators and functions, in a way that is independent of the underlying storage. These operations
generally work correctly as a fallback for any specific array implementation.

The `AbstractArray` type includes anything vaguely array-like, and implementations of it might
be quite different from conventional arrays. For example, elements might be computed on request
rather than stored. However, any concrete `AbstractArray{T,N}` type should generally implement
at least [`size(A)`](@ref) (returning an `Int` tuple), [`getindex(A,i)`](@ref) and [`getindex(A,i1,...,iN)`](@ref getindex);
mutable arrays should also implement [`setindex!()`](@ref). It is recommended that these operations
have nearly constant time complexity, or technically Õ(1) complexity, as otherwise some array
functions may be unexpectedly slow. Concrete types should also typically provide a [`similar(A,T=eltype(A),dims=size(A))`](@ref)
method, which is used to allocate a similar array for [`copy()`](@ref) and other out-of-place
operations. No matter how an `AbstractArray{T,N}` is represented internally, `T` is the type of
object returned by *integer* indexing (`A[1, ..., 1]`, when `A` is not empty) and `N` should be
the length of the tuple returned by [`size()`](@ref).
-->
```


```@raw html
<!--
`DenseArray` is an abstract subtype of `AbstractArray` intended to include all arrays that are
laid out at regular offsets in memory, and which can therefore be passed to external C and Fortran
functions expecting this memory layout. Subtypes should provide a method [`stride(A,k)`](@ref)
that returns the "stride" of dimension `k`: increasing the index of dimension `k` by `1` should
increase the index `i` of [`getindex(A,i)`](@ref) by [`stride(A,k)`](@ref). If a pointer conversion
method [`Base.unsafe_convert(Ptr{T}, A)`](@ref) is provided, the memory layout should correspond
in the same way to these strides.

The [`Array`](@ref) type is a specific instance of `DenseArray` where elements are stored in column-major
order (see additional notes in [Performance Tips](@ref man-performance-tips)). `Vector` and `Matrix` are aliases for
the 1-d and 2-d cases. Specific operations such as scalar indexing, assignment, and a few other
basic storage-specific operations are all that have to be implemented for [`Array`](@ref), so
that the rest of the array library can be implemented in a generic manner.
-->
```


```@raw html
<!--
`SubArray` is a specialization of `AbstractArray` that performs indexing by reference rather than
by copying. A `SubArray` is created with the [`view()`](@ref) function, which is called the same
way as [`getindex()`](@ref) (with an array and a series of index arguments). The result of [`view()`](@ref)
looks the same as the result of [`getindex()`](@ref), except the data is left in place. [`view()`](@ref)
stores the input index vectors in a `SubArray` object, which can later be used to index the original
array indirectly.  By putting the [`@views`](@ref) macro in front of an expression or
block of code, any `array[...]` slice in that expression will be converted to
create a `SubArray` view instead.

`StridedVector` and `StridedMatrix` are convenient aliases defined to make it possible for Julia
to call a wider range of BLAS and LAPACK functions by passing them either [`Array`](@ref) or
`SubArray` objects, and thus saving inefficiencies from memory allocation and copying.

The following example computes the QR decomposition of a small section of a larger array, without
creating any temporaries, and by calling the appropriate LAPACK function with the right leading
dimension size and stride parameters.
-->
```

```julia-repl
julia> a = rand(10,10)
10×10 Array{Float64,2}:
 0.561255   0.226678   0.203391  0.308912   …  0.750307  0.235023   0.217964
 0.718915   0.537192   0.556946  0.996234      0.666232  0.509423   0.660788
 0.493501   0.0565622  0.118392  0.493498      0.262048  0.940693   0.252965
 0.0470779  0.736979   0.264822  0.228787      0.161441  0.897023   0.567641
 0.343935   0.32327    0.795673  0.452242      0.468819  0.628507   0.511528
 0.935597   0.991511   0.571297  0.74485    …  0.84589   0.178834   0.284413
 0.160706   0.672252   0.133158  0.65554       0.371826  0.770628   0.0531208
 0.306617   0.836126   0.301198  0.0224702     0.39344   0.0370205  0.536062
 0.890947   0.168877   0.32002   0.486136      0.096078  0.172048   0.77672
 0.507762   0.573567   0.220124  0.165816      0.211049  0.433277   0.539476

julia> b = view(a, 2:2:8,2:2:4)
4×2 SubArray{Float64,2,Array{Float64,2},Tuple{StepRange{Int64,Int64},StepRange{Int64,Int64}},false}:
 0.537192  0.996234
 0.736979  0.228787
 0.991511  0.74485
 0.836126  0.0224702

julia> (q,r) = qr(b);

julia> q
4×2 Array{Float64,2}:
 -0.338809   0.78934
 -0.464815  -0.230274
 -0.625349   0.194538
 -0.527347  -0.534856

julia> r
2×2 Array{Float64,2}:
 -1.58553  -0.921517
  0.0       0.866567
```

[](## Sparse Matrices)
## 疎な行列


```@raw html
<!--
[Sparse matrices](https://en.wikipedia.org/wiki/Sparse_matrix) are matrices that contain enough
zeros that storing them in a special data structure leads to savings in space and execution time.
Sparse matrices may be used when operations on the sparse representation of a matrix lead to considerable
gains in either time or space when compared to performing the same operations on a dense matrix.
-->
```

[](### Compressed Sparse Column (CSC) Storage)
### Compressed Sparse Column (CSC) Storage


```@raw html
<!--
In Julia, sparse matrices are stored in the [Compressed Sparse Column (CSC) format](https://en.wikipedia.org/wiki/Sparse_matrix#Compressed_sparse_column_.28CSC_or_CCS.29).
Julia sparse matrices have the type `SparseMatrixCSC{Tv,Ti}`, where `Tv` is the type of the stored
values, and `Ti` is the integer type for storing column pointers and row indices.:
-->
```

```julia
struct SparseMatrixCSC{Tv,Ti<:Integer} <: AbstractSparseMatrix{Tv,Ti}
    m::Int                  # Number of rows
    n::Int                  # Number of columns
    colptr::Vector{Ti}      # Column i is in colptr[i]:(colptr[i+1]-1)
    rowval::Vector{Ti}      # Row indices of stored values
    nzval::Vector{Tv}       # Stored values, typically nonzeros
end
```


```@raw html
<!--
The compressed sparse column storage makes it easy and quick to access the elements in the column
of a sparse matrix, whereas accessing the sparse matrix by rows is considerably slower. Operations
such as insertion of previously unstored entries one at a time in the CSC structure tend to be slow. This is
because all elements of the sparse matrix that are beyond the point of insertion have to be moved
one place over.

All operations on sparse matrices are carefully implemented to exploit the CSC data structure
for performance, and to avoid expensive operations.
-->
```


```@raw html
<!--
If you have data in CSC format from a different application or library, and wish to import it
in Julia, make sure that you use 1-based indexing. The row indices in every column need to be
sorted. If your `SparseMatrixCSC` object contains unsorted row indices, one quick way to sort
them is by doing a double transpose.

In some applications, it is convenient to store explicit zero values in a `SparseMatrixCSC`. These
*are* accepted by functions in `Base` (but there is no guarantee that they will be preserved in
mutating operations). Such explicitly stored zeros are treated as structural nonzeros by many
routines. The [`nnz()`](@ref) function returns the number of elements explicitly stored in the
sparse data structure, including structural nonzeros. In order to count the exact number of actual
values that are nonzero, use [`countnz()`](@ref), which inspects every stored element of a sparse
matrix.
-->
```

[](### Sparse matrix constructors)
### 疎な行列のコンストラクタ


```@raw html
<!--
The simplest way to create sparse matrices is to use functions equivalent to the [`zeros()`](@ref)
and [`eye()`](@ref) functions that Julia provides for working with dense matrices. To produce
sparse matrices instead, you can use the same names with an `sp` prefix:
-->
```

```jldoctest
julia> spzeros(3,5)
3×5 SparseMatrixCSC{Float64,Int64} with 0 stored entries

julia> speye(3,5)
3×5 SparseMatrixCSC{Float64,Int64} with 3 stored entries:
  [1, 1]  =  1.0
  [2, 2]  =  1.0
  [3, 3]  =  1.0
```


```@raw html
<!--
The [`sparse()`](@ref) function is often a handy way to construct sparse matrices. It takes as
its input a vector `I` of row indices, a vector `J` of column indices, and a vector `V` of stored
values. `sparse(I,J,V)` constructs a sparse matrix such that `S[I[k], J[k]] = V[k]`.
-->
```

```jldoctest sparse_function
julia> I = [1, 4, 3, 5]; J = [4, 7, 18, 9]; V = [1, 2, -5, 3];

julia> S = sparse(I,J,V)
5×18 SparseMatrixCSC{Int64,Int64} with 4 stored entries:
  [1 ,  4]  =  1
  [4 ,  7]  =  2
  [5 ,  9]  =  3
  [3 , 18]  =  -5
```


```@raw html
<!--
The inverse of the [`sparse()`](@ref) function is [`findn()`](@ref), which retrieves the inputs
used to create the sparse matrix.
-->
```

```jldoctest sparse_function
julia> findn(S)
([1, 4, 5, 3], [4, 7, 9, 18])

julia> findnz(S)
([1, 4, 5, 3], [4, 7, 9, 18], [1, 2, 3, -5])
```


```@raw html
<!--
Another way to create sparse matrices is to convert a dense matrix into a sparse matrix using
the [`sparse()`](@ref) function:
-->
```

```jldoctest
julia> sparse(eye(5))
5×5 SparseMatrixCSC{Float64,Int64} with 5 stored entries:
  [1, 1]  =  1.0
  [2, 2]  =  1.0
  [3, 3]  =  1.0
  [4, 4]  =  1.0
  [5, 5]  =  1.0
```


```@raw html
<!--
You can go in the other direction using the [`full()`](@ref) function. The [`issparse()`](@ref)
function can be used to query if a matrix is sparse.
-->
```

```jldoctest
julia> issparse(speye(5))
true
```

[](### Sparse matrix operations)
### 疎な行列の演算



```@raw html
<!--
Arithmetic operations on sparse matrices also work as they do on dense matrices. Indexing of,
assignment into, and concatenation of sparse matrices work in the same way as dense matrices.
Indexing operations, especially assignment, are expensive, when carried out one element at a time.
In many cases it may be better to convert the sparse matrix into `(I,J,V)` format using [`findnz()`](@ref),
manipulate the values or the structure in the dense vectors `(I,J,V)`, and then reconstruct
the sparse matrix.
-->
```

[](### Correspondence of dense and sparse methods)
### Correspondence of dense and sparse methods


```@raw html
<!--
The following table gives a correspondence between built-in methods on sparse matrices and their
corresponding methods on dense matrix types. In general, methods that generate sparse matrices
differ from their dense counterparts in that the resulting matrix follows the same sparsity pattern
as a given sparse matrix `S`, or that the resulting sparse matrix has density `d`, i.e. each matrix
element has a probability `d` of being non-zero.

Details can be found in the [Sparse Vectors and Matrices](@ref) section of the standard library
reference.
-->
```


```@raw html
<!--
| Sparse                     | Dense                  | Description                                                                                                                                                           |
| :------------------------- | :--------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`spzeros(m,n)`](@ref)     | [`zeros(m,n)`](@ref)   | Creates a *m*-by-*n* matrix of zeros. ([`spzeros(m,n)`](@ref) is empty.)                                                                                              |
| [`spones(S)`](@ref)        | [`ones(m,n)`](@ref)    | Creates a matrix filled with ones. Unlike the dense version, [`spones()`](@ref) has the same sparsity pattern as *S*.                                                 |
| [`speye(n)`](@ref)         | [`eye(n)`](@ref)       | Creates a *n*-by-*n* identity matrix.                                                                                                                                 |
| [`full(S)`](@ref)          | [`sparse(A)`](@ref)    | Interconverts between dense and sparse formats.                                                                                                                       |
| [`sprand(m,n,d)`](@ref)    | [`rand(m,n)`](@ref)    | Creates a *m*-by-*n* random matrix (of density *d*) with iid non-zero elements distributed uniformly on the half-open interval ``[0, 1)``.                            |
| [`sprandn(m,n,d)`](@ref)   | [`randn(m,n)`](@ref)   | Creates a *m*-by-*n* random matrix (of density *d*) with iid non-zero elements distributed according to the standard normal (Gaussian) distribution.                  |
| [`sprandn(m,n,d,X)`](@ref) | [`randn(m,n,X)`](@ref) | Creates a *m*-by-*n* random matrix (of density *d*) with iid non-zero elements distributed according to the *X* distribution. (Requires the `Distributions` package.) |
-->
```
