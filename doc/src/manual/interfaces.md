[](# Interfaces)
# インターフェース


```@raw html
<!--
A lot of the power and extensibility in Julia comes from a collection of informal interfaces.
 By extending a few specific methods to work for a custom type, objects of that type not only
receive those functionalities, but they are also able to be used in other methods that are written
to generically build upon those behaviors.
-->
```

Juliaに仮に実装されている様々なインターフェースは、この言語の力と拡張性の源となっています。
このインターフェースを独自型に特化したメソッドに拡張すると、その型のオブジェクトでは、特化したメソッドは直接呼び出しても、そのメソッドを利用して汎化的に記述されたメソッドの中でも機能します。



[](## [Iteration](@id man-interface-iteration))
## [繰返し](@id man-interface-iteration)


```@raw html
<!--

| Required methods               |                        | Brief description                                                                     |
|:------------------------------ |:---------------------- |:------------------------------------------------------------------------------------- |
| `start(iter)`                  |                        | Returns the initial iteration state                                                   |
| `next(iter, state)`            |                        | Returns the current item and the next state                                           |
| `done(iter, state)`            |                        | Tests if there are any items remaining                                                |
| **Important optional methods** | **Default definition** | **Brief description**                                                                 |
| `iteratorsize(IterType)`       | `HasLength()`          | One of `HasLength()`, `HasShape()`, `IsInfinite()`, or `SizeUnknown()` as appropriate |
| `iteratoreltype(IterType)`     | `HasEltype()`          | Either `EltypeUnknown()` or `HasEltype()` as appropriate                              |
| `eltype(IterType)`             | `Any`                  | The type the items returned by `next()`                                               |
| `length(iter)`                 | (*undefined*)          | The number of items, if known                                                         |
| `size(iter, [dim...])`         | (*undefined*)          | The number of items in each dimension, if known                                       |

| Value returned by `iteratorsize(IterType)` | Required Methods                           |
|:------------------------------------------ |:------------------------------------------ |
| `HasLength()`                              | `length(iter)`                             |
| `HasShape()`                               | `length(iter)`  and `size(iter, [dim...])` |
| `IsInfinite()`                             | (*none*)                                   |
| `SizeUnknown()`                            | (*none*)                                   |

| Value returned by `iteratoreltype(IterType)` | Required Methods   |
|:-------------------------------------------- |:------------------ |
| `HasEltype()`                                | `eltype(IterType)` |
| `EltypeUnknown()`                            | (*none*)           |

-->
```
| 必須メソッド               |                        | 概説                                                                     |
|:------------------------------ |:---------------------- |:------------------------------------------------------------------------------------- |
| `start(iter)`                  |                        | 繰り返しの初期状態を返します。                                                   |
| `next(iter, state)`            |                        | 現在のアイテムと次の状態を返します。                                           |
| `done(iter, state)`            |                        | 残りのアイテムがあるかどうか検査します。                                                |
| **重要な必須ではないメソッド** | **デフォルトのメソッド** | **概説**                                                                 |
| `iteratorsize(IterType)`       | `HasLength()`          | `HasLength()`, `HasShape()`, `IsInfinite()`,  `SizeUnknown()` の中で適切なもの一つ|
| `iteratoreltype(IterType)`     | `HasEltype()`          | `EltypeUnknown()`と`HasEltype()`のどちらか適切のもの                              |
| `eltype(IterType)`             | `Any`                  | `next()`が返すアイテムの型                                               |
| `length(iter)`                 | (*undefined*)          | アイテムの数(既知の場合)                                                         |
| `size(iter, [dim...])`         | (*undefined*)          | 各次元のアイテムの数(既知の場合)                                           |

| `iteratorsize(IterType)`の戻り値 | 必要なメソッド                           |
|:------------------------------------------ |:------------------------------------------ |
| `HasLength()`                              | `length(iter)`                             |
| `HasShape()`                               | `length(iter)`と`size(iter, [dim...])` |
| `IsInfinite()`                             | (*none*)                                   |
| `SizeUnknown()`                            | (*none*)                                   |

|  `iteratoreltype(IterType)`の戻り値  |必要なメソッド  |
|:-------------------------------------------- |:------------------ |
| `HasEltype()`                                | `eltype(IterType)` |
| `EltypeUnknown()`                            | (*none*)           |


```@raw html
<!--
Sequential iteration is implemented by the methods [`start()`](@ref), [`done()`](@ref), and [`next()`](@ref). Instead
of mutating objects as they are iterated over, Julia provides these three methods to keep track
of the iteration state externally from the object. The `start(iter)` method returns the initial
state for the iterable object `iter`. That state gets passed along to `done(iter, state)`, which
tests if there are any elements remaining, and `next(iter, state)`, which returns a tuple containing
the current element and an updated `state`. The `state` object can be anything, and is generally
considered to be an implementation detail private to the iterable object.
-->
```
順次実行される繰返しは、[`start()`](@ref), [`done()`](@ref), [`next()`] (@ref) のメソッドをつかって実装されています。
Juliaでは、繰返し処理の状態の追跡は、この3つのメソッドを使用してオブジェクトの外部から行い、処理の行われるオブジェクトには変更を加えません。
この`start(iter)`メソッドは、イテラブルオブジェクト`iter`の初期状態を返し、
その状態は`done(iter, state)`、`next(iter, state)`と順に渡されます。
`done(iter, state)`は残りの要素があるかどうかを検査し、`next(iter, state)`は現在の要素と更新された`state`オブジェクトを含むタプルを返します。
`state`オブジェクトは何でも構いませんが、通常はイテラブルオブジェクト内でプライベートな実装の詳細を示すものでしょう。



```@raw html
<!--
Any object defines these three methods is iterable and can be used in the [many functions that rely upon iteration](@ref lib-collections-iteration).
It can also be used directly in a `for` loop since the syntax:
-->
```
これらの3つのメソッドを定義するオブジェクトはすべてイテラブルであり、[繰返しを使う多くの関数]（@ ref lib-collections-iteration）で使用できます。
また以下のような構文で`for`ループ内で直接使用することもできます。


```julia
for i in iter   # or  "for i = iter"
    # body
end
```


```@raw html
<!--
is translated into:
-->
```
はこのように変換されます。

```julia
state = start(iter)
while !done(iter, state)
    (i, state) = next(iter, state)
    # body
end
```


```@raw html
<!--
A simple example is an iterable sequence of square numbers with a defined length:
-->
```
簡単な例は、イテラブルな長さの決まった平方数の数列です。


```jldoctest squaretype
julia> struct Squares
           count::Int
       end

julia> Base.start(::Squares) = 1

julia> Base.next(S::Squares, state) = (state*state, state+1)

julia> Base.done(S::Squares, state) = state > S.count

julia> Base.eltype(::Type{Squares}) = Int # Note that this is defined for the type

julia> Base.length(S::Squares) = S.count
```


```@raw html
<!--
With only [`start`](@ref), [`next`](@ref), and [`done`](@ref) definitions, the `Squares` type is already pretty powerful.
We can iterate over all the elements:
-->
```
[`start`](@ref), [`next`](@ref),  [`done`](@ref) の定義だけでも、`Squares`型はすでにかなり強力です。
すべての要素に対する繰り返し処理を実行できます。


```jldoctest squaretype
julia> for i in Squares(7)
           println(i)
       end
1
4
9
16
25
36
49
```


```@raw html
<!--
We can use many of the builtin methods that work with iterables, like [`in()`](@ref), [`mean()`](@ref) and [`std()`](@ref):
-->
```
[`in()`](@ref), [`mean()`](@ref), [`std()`](@ref) のような多くの標準装備のメソッドがイテラブルオブジェクトで動作します。


```jldoctest squaretype
julia> 25 in Squares(10)
true

julia> mean(Squares(100))
3383.5

julia> std(Squares(100))
3024.355854282583
```


```@raw html
<!--
There are a few more methods we can extend to give Julia more information about this iterable
collection.  We know that the elements in a `Squares` sequence will always be `Int`. By extending
the [`eltype()`](@ref) method, we can give that information to Julia and help it make more specialized
code in the more complicated methods. We also know the number of elements in our sequence, so
we can extend [`length()`](@ref), too.
-->
```
対象とするイテラブルコレクションに関する詳しい情報を与えるために、拡張して使うメソッドがJuliaにはいくつかあります。
`Squares`の数列の要素は常に`Int`であることがわかります。
 [`eltype()`](@ref)メソッドを拡張して、この情報をJuliaに渡して、もっと複雑なメソッドでも、もっと型に特化したコードを作成する手助けをすることができます。
シーケンスの要素数もわかっているので[`length()`](@ref)も拡張することもできます。



```@raw html
<!--
Now, when we ask Julia to [`collect()`](@ref) all the elements into an array it can preallocate a `Vector{Int}`
of the right size instead of blindly [`push!`](@ref)ing each element into a `Vector{Any}`:
-->
```
ここまでくれば、Juliaで、 すべての要素を[`collect()`](@ref) を使って配列化する際に、
`Vector{Int}`のように 正しいサイズに事前に割り当てることができます。[`push!`](@ref)を使って盲目的に各要素を`Vector{Any}`におしこまなくてもよいのです。


```jldoctest squaretype
julia> collect(Squares(10))' # transposed to save space
1×10 RowVector{Int64,Array{Int64,1}}:
 1  4  9  16  25  36  49  64  81  100
```


```@raw html
<!--
While we can rely upon generic implementations, we can also extend specific methods where we know
there is a simpler algorithm. For example, there's a formula to compute the sum of squares, so
we can override the generic iterative version with a more performant solution:
-->
```
汎化的な実装のまま使うこともできますが、もっと単純なアルゴリズムがあると分かっている場合は、特化したメソッドに拡張することもできます。
たとえば、平方和を算出する公式があれば、汎化的な繰返しをもっと効率的な解法で上書きすることができます。


```jldoctest squaretype
julia> Base.sum(S::Squares) = (n = S.count; return n*(n+1)*(2n+1)÷6)

julia> sum(Squares(1803))
1955361914
```


```@raw html
<!--
This is a very common pattern throughout the Julia standard library: a small set of required methods
define an informal interface that enable many fancier behaviors. In some cases, types will want
to additionally specialize those extra behaviors when they know a more efficient algorithm can
be used in their specific case.
-->
```
これは、Julia標準ライブラリ全体に非常によくあるパターンです。
少数の必須メソッドによって、仮実装のインターフェイスが定義されていますが、多くの便利な動作が利用可能です。
もっと効率的なアルゴリズムを使用できる場合には、さらに型に特化させてその動作を行うことができます。

[](## Indexing)
## インデックス


```@raw html
<!--
| Methods to implement | Brief description                |
|:-------------------- |:-------------------------------- |
| `getindex(X, i)`     | `X[i]`, indexed element access   |
| `setindex!(X, v, i)` | `X[i] = v`, indexed assignment   |
| `endof(X)`           | The last index, used in `X[end]` |
-->
```

| 実装すべきメソッド | 概説                |
|:-------------------- |:-------------------------------- |
| `getindex(X, i)`     | `X[i]`, インデックスによる要素の参照   |
| `setindex!(X, v, i)` | `X[i] = v`,  インデックスによる代入   |
| `endof(X)`           | インデックスの最後尾,  `X[end]`で使われる |


```@raw html
<!--
For the `Squares` iterable above, we can easily compute the `i`th element of the sequence by squaring
it.  We can expose this as an indexing expression `S[i]`. To opt into this behavior, `Squares`
simply needs to define [`getindex()`](@ref):
-->
```
上記の`Squares`イテラブルでは、数列の`i`番目は、2乗すれば簡単に算出できます。
`S[i]`というインデックス式で、これを公開することができです。
`Squares` に[`getindex()`](@ref) を定義すればいいだけです。


```jldoctest squaretype
julia> function Base.getindex(S::Squares, i::Int)
           1 <= i <= S.count || throw(BoundsError(S, i))
           return i*i
       end

julia> Squares(100)[23]
529
```


```@raw html
<!--
Additionally, to support the syntax `S[end]`, we must define [`endof()`](@ref) to specify the last valid
index:
-->
```
さらに、`S[end]`構文を使えるようにするには、[`endof()`](@ref)を定義して有効な最後尾のインデックスを指定する必要があります。


```jldoctest squaretype
julia> Base.endof(S::Squares) = length(S)

julia> Squares(23)[end]
529
```


```@raw html
<!--
Note, though, that the above *only* defines [`getindex()`](@ref) with one integer index. Indexing with
anything other than an `Int` will throw a [`MethodError`](@ref) saying that there was no matching method.
In order to support indexing with ranges or vectors of `Int`s, separate methods must be written:
-->
```
ただし、上記は [`getindex()`](@ref) を1つの整数インデックスで**のみ**定義していることに注意してください。
`Int`以外のものを使ってインデックスを使うと [`MethodError`](@ref) を投げて、適合するメソッドが存在しないというメッセージが表示されるでしょう。
範囲や`Int`のベクトルに対してインデックスをつかう場合は、別のメソッドを記述する必要があります。


```jldoctest squaretype
julia> Base.getindex(S::Squares, i::Number) = S[convert(Int, i)]

julia> Base.getindex(S::Squares, I) = [S[i] for i in I]

julia> Squares(10)[[3,4.,5]]
3-element Array{Int64,1}:
  9
 16
 25
```


```@raw html
<!--
While this is starting to support more of the [indexing operations supported by some of the builtin types](@ref man-array-indexing),
there's still quite a number of behaviors missing. This `Squares` sequence is starting to look
more and more like a vector as we've added behaviors to it. Instead of defining all these behaviors
ourselves, we can officially define it as a subtype of an [`AbstractArray`](@ref).
-->
```
[一部の標準装備の型で可能なインデックス操作]（@ ref man-array-indexing）をがけっこう使えるようになりましたが、依然として動作しないものが多くあります。
この`Squares`シーケンスに、もっと動作を加えると、ますますベクトルのように見えます。
これらの動作はすべてを自前で定義しなくても、正式な [`AbstractArray`](@ref)のサブタイプとして定義することができます。

[](## [Abstract Arrays](@id man-interface-array))
## [抽象配列](@id man-interface-array)


```@raw html
<!--
| Methods to implement                            |                                          | Brief description                                                                     |
|:----------------------------------------------- |:---------------------------------------- |:------------------------------------------------------------------------------------- |
| `size(A)`                                       |                                          | Returns a tuple containing the dimensions of `A`                                      |
| `getindex(A, i::Int)`                           |                                      ｓ    | (if `IndexLinear`) Linear scalar indexing                                              |
| `getindex(A, I::Vararg{Int, N})`                |                                          | (if `IndexCartesian`, where `N = ndims(A)`) N-dimensional scalar indexing                 |
| `setindex!(A, v, i::Int)`                       |                                          | (if `IndexLinear`) Scalar indexed assignment                                           |
| `setindex!(A, v, I::Vararg{Int, N})`            |                                          | (if `IndexCartesian`, where `N = ndims(A)`) N-dimensional scalar indexed assignment       |
| **Optional methods**                            | **Default definition**                   | **Brief description**                                                                 |
| `IndexStyle(::Type)`                            | `IndexCartesian()`                       | Returns either `IndexLinear()` or `IndexCartesian()`. See the description below.      |
| `getindex(A, I...)`                             | defined in terms of scalar `getindex()`  | [Multidimensional and nonscalar indexing](@ref man-array-indexing)                    |
| `setindex!(A, I...)`                            | defined in terms of scalar `setindex!()` | [Multidimensional and nonscalar indexed assignment](@ref man-array-indexing)          |
| `start()`/`next()`/`done()`                     | defined in terms of scalar `getindex()`  | Iteration                                                                             |
| `length(A)`                                     | `prod(size(A))`                          | Number of elements                                                                    |
| `similar(A)`                                    | `similar(A, eltype(A), size(A))`         | Return a mutable array with the same shape and element type                           |
| `similar(A, ::Type{S})`                         | `similar(A, S, size(A))`                 | Return a mutable array with the same shape and the specified element type             |
| `similar(A, dims::NTuple{Int})`                 | `similar(A, eltype(A), dims)`            | Return a mutable array with the same element type and size *dims*                     |
| `similar(A, ::Type{S}, dims::NTuple{Int})`      | `Array{S}(dims)`                         | Return a mutable array with the specified element type and size                       |
| **Non-traditional indices**                     | **Default definition**                   | **Brief description**                                                                 |
| `indices(A)`                                    | `map(OneTo, size(A))`                    | Return the `AbstractUnitRange` of valid indices                                       |
| `Base.similar(A, ::Type{S}, inds::NTuple{Ind})` | `similar(A, S, Base.to_shape(inds))`     | Return a mutable array with the specified indices `inds` (see below)                  |
| `Base.similar(T::Union{Type,Function}, inds)`   | `T(Base.to_shape(inds))`                 | Return an array similar to `T` with the specified indices `inds` (see below)          |
-->
```
| 実装すべきメソッド                            |                                          | 概説                                                                     |
|:----------------------------------------------- |:---------------------------------------- |:------------------------------------------------------------------------------------- |
| `size(A)`                                       |                                          | `A`の次元を含むタプルを返す                                      |
| `getindex(A, i::Int)`                           |                                          | ( `IndexLinear`)線形スカラインデックスによる参照                                              |
| `getindex(A, I::Vararg{Int, N})`                |                                          | ( `IndexCartesian`,`N = ndims(A)`) N次元のスカラインデックスにによる参照                |
| `setindex!(A, v, i::Int)`                       |                                          | ( `IndexLinear`) 線形スカラインデックスによる代入                                           |
| `setindex!(A, v, I::Vararg{Int, N})`            |                                          | ( `IndexCartesian`, `N = ndims(A)`) N次元のスカラインデックスによる代入       |
| **省略可能なメソッド**                            | **デフォルトの定義**                   | **概説**                                                                 |
| `IndexStyle(::Type)`                            | `IndexCartesian()`                       |  `IndexLinear()` と `IndexCartesian()`のどちらかを返す。下記参照      |
| `getindex(A, I...)`                             | スカラーの  `getindex()`による定義  | [多次元で非スカラーのインデックスによる参照](@ref man-array-indexing)                    |
| `setindex!(A, I...)`                            | スカラーの `setindex!()`による定義 | [多次元で非スカラーのインデックスによる代入](@ref man-array-indexing)          |
| `start()`/`next()`/`done()`                     | スカラーの `getindex()`による定義  | 繰返し                                                                             |
| `length(A)`                                     | `prod(size(A))`                          | 要素の数                                                                    |
| `similar(A)`                                    | `similar(A, eltype(A), size(A))`         | 同形・同要素型の可変配列を返す                          |
| `similar(A, ::Type{S})`                         | `similar(A, S, size(A))`                 | 同形・指定要素型の可変配列を返す|
| `similar(A, dims::NTuple{Int})`                 | `similar(A, eltype(A), dims)`            | 同要素型でサイズ**dims**の可変配列を返す |
| `similar(A, ::Type{S}, dims::NTuple{Int})`      | `Array{S}(dims)`                         | 指定形・指定要素型の可変配列を返す|
| **通常とは異なるインデックス**                     | **デフォルトの定義**                   | **概説**                                                                 |
| `indices(A)`                                    | `map(OneTo, size(A))`                    | 妥当なインデックスの`AbstractUnitRange`を返す                                       |
| `Base.similar(A, ::Type{S}, inds::NTuple{Ind})` | `similar(A, S, Base.to_shape(inds))`     | `inds`で指定したインデックスの可変配列を返す (下記参照)                  |
| `Base.similar(T::Union{Type,Function}, inds)`   | `T(Base.to_shape(inds))`                 | `inds`で指定したインデックスの`T`と同様な可変配列を返す (下記参照)          |


```@raw html
<!--
If a type is defined as a subtype of `AbstractArray`, it inherits a very large set of rich behaviors
including iteration and multidimensional indexing built on top of single-element access.  See
the [arrays manual page](@ref man-multi-dim-arrays) and [standard library section](@ref lib-arrays) for more supported methods.
-->
```
`AbstractArray`のサブタイプとして定義された型は、多様な動作を数多く継承していて、反復処理や、1要素アクセスをもとに構築された多次元インデックスなどが利用できます。
その他の利用可能なメソッドについては、[多次元配列のマニュアルページ]（@ ref man-multi-dim-arrays）と[標準ライブラリの配列のセクション]（@ ref lib-arrays）を参照してください。



```@raw html
<!--
A key part in defining an `AbstractArray` subtype is [`IndexStyle`](@ref). Since indexing is
such an important part of an array and often occurs in hot loops, it's important to make both
indexing and indexed assignment as efficient as possible.  Array data structures are typically
defined in one of two ways: either it most efficiently accesses its elements using just one index
(linear indexing) or it intrinsically accesses the elements with indices specified for every dimension.
 These two modalities are identified by Julia as `IndexLinear()` and `IndexCartesian()`.
 Converting a linear index to multiple indexing subscripts is typically very expensive, so this
provides a traits-based mechanism to enable efficient generic code for all array types.
-->
```
`AbstractArray`のサブタイプの定義で重要な部分は[`IndexStyle`](@ref) です。
インデックスは配列の重要な部分であり、頻繁にループで使わわれるため、インデックスによる参照と代入をできる限り効率的に行うことは重要です。
配列のデータ構造は、通常、２つの手法のいずれかが定義に採用されます。
一方は、インデックス（線形インデクシング）をただ一つ使用して要素にアクセスする最も効率のよい手法で、もう一方は、本質的にはすべての次元に対してインデックスを指定して要素にアクセスする手法です。
これらの2つのモードは、Juliaでは`IndexLinear()`と`IndexCartesian()`によって同定されます。
線形インデックスを多重インデックスの添字に変換するのは、通常非常にコストがかかるので、すべての配列の型に対して効率的で汎化的なコードを可能にするトレイトを使ったメカニズムが備わっています。



```@raw html
<!--
This distinction determines which scalar indexing methods the type must define. `IndexLinear()`
arrays are simple: just define `getindex(A::ArrayType, i::Int)`.  When the array is subsequently
indexed with a multidimensional set of indices, the fallback `getindex(A::AbstractArray, I...)()`
efficiently converts the indices into one linear index and then calls the above method. `IndexCartesian()`
arrays, on the other hand, require methods to be defined for each supported dimensionality with
`ndims(A)``Int` indices.  For example, the builtin `SparseMatrixCSC` type only supports two dimensions,
so it just defines `getindex(A::SparseMatrixCSC, i::Int, j::Int)()`.  The same holds for `setindex!()`.
-->
```
この`IndexStyle`の違いによって、どのスカラーインデックスのメソッドを型に対して定義しなければならないかが決定します。
`IndexLinear()`の配列は単純で、`getindex(A::ArrayType, i::Int)`を定義するだけです。
配列が多次元で複数のインデックスによってインデックス付けされている場合、フォールバックの`getindex(A::AbstractArray, I...)()` はインデックスを線形インデックスひとつに効率的に変換し、上記のメソッドを呼び出します。
一方、`IndexCartesian()` の配列は、`ndims(A)`、`Int`の指定によって利用可能となる次元すべてに対して、メソッドを定義する必要があります。
たとえば、標準装備の`SparseMatrixCSC`型は2次元しか利用可能ではないため、`getindex(A::SparseMatrixCSC, i::Int, j::Int)()`だけを定義しています。`setindex!()`に関しても同様です。



```@raw html
<!--

Returning to the sequence of squares from above, we could instead define it as a subtype of an
`AbstractArray{Int, 1}`:
-->
```
上記の二乗の数列に戻ると、別の手法として`AbstractArray{Int, 1}`のサブタイプを定義することもできます。


```jldoctest squarevectype
julia> struct SquaresVector <: AbstractArray{Int, 1}`
           count::Int
       end

julia> Base.size(S::SquaresVector) = (S.count,)

julia> Base.IndexStyle(::Type{<:SquaresVector}) = IndexLinear()

julia> Base.getindex(S::SquaresVector, i::Int) = i*i
```


```@raw html
<!--
Note that it's very important to specify the two parameters of the `AbstractArray`; the first
defines the [`eltype()`](@ref), and the second defines the [`ndims()`](@ref). That supertype and those three
methods are all it takes for `SquaresVector` to be an iterable, indexable, and completely functional
array:
-->
```
`AbstractArray`の2つのパラメータの指定は、非常に重要であることに注意してください。
1番目は[`eltype()`](@ref) を定義し、2番目は[`ndims()`](@ref)を定義します。
このスーパータイプと3つのメソッドのすべてによって、`SquaresVector`はループとインデックスによるアクセスが可能になり、完全に機能する配列となります。


```jldoctest squarevectype
julia> s = SquaresVector(7)
7-element SquaresVector:
  1
  4
  9
 16
 25
 36
 49

julia> s[s .> 20]
3-element Array{Int64,1}:
 25
 36
 49

julia> s \ [1 2; 3 4; 5 6; 7 8; 9 10; 11 12; 13 14]
1×2 Array{Float64,2}:
 0.305389  0.335329

julia> s ⋅ s # dot(s, s)
4676
```


```@raw html
<!--
As a more complicated example, let's define our own toy N-dimensional sparse-like array type built
on top of [`Dict`](@ref):
-->
```
より複雑な例として、N次元で疎な配列型のおもちゃのようなものを[`Dict`](@ref)を使って定義しましょう。


```jldoctest squarevectype
julia> struct SparseArray{T,N} <: AbstractArray{T,N}
           data::Dict{NTuple{N,Int}, T}
           dims::NTuple{N,Int}
       end

julia> SparseArray{T}(::Type{T}, dims::Int...) = SparseArray(T, dims);

julia> SparseArray{T,N}(::Type{T}, dims::NTuple{N,Int}) = SparseArray{T,N}(Dict{NTuple{N,Int}, T}(), dims);

julia> Base.size(A::SparseArray) = A.dims

julia> Base.similar(A::SparseArray, ::Type{T}, dims::Dims) where {T} = SparseArray(T, dims)

julia> Base.getindex(A::SparseArray{T,N}, I::Vararg{Int,N}) where {T,N} = get(A.data, I, zero(T))

julia> Base.setindex!(A::SparseArray{T,N}, v, I::Vararg{Int,N}) where {T,N} = (A.data[I] = v)
```


```@raw html
<!--
Notice that this is an `IndexCartesian` array, so we must manually define [`getindex()`](@ref) and [`setindex!()`](@ref)
at the dimensionality of the array. Unlike the `SquaresVector`, we are able to define [`setindex!()`](@ref),
and so we can mutate the array:
-->
```
これは`IndexCartesian`の配列のため、 [`getindex()`](@ref) と[`setindex!()`](@ref) を次元ごとに手動で定義する必要がある点に注意してください。
`SquaresVector`配列とは違って、[`setindex!()`](@ref)を定義できるので、配列に変更を加えることができます：


```jldoctest squarevectype
julia> A = SparseArray(Float64, 3, 3)
3×3 SparseArray{Float64,2}:
 0.0  0.0  0.0
 0.0  0.0  0.0
 0.0  0.0  0.0

julia> fill!(A, 2)
3×3 SparseArray{Float64,2}:
 2.0  2.0  2.0
 2.0  2.0  2.0
 2.0  2.0  2.0

julia> A[:] = 1:length(A); A
3×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
 3.0  6.0  9.0
```


```@raw html
<!--
The result of indexing an `AbstractArray` can itself be an array (for instance when indexing by
a `Range`). The `AbstractArray` fallback methods use [`similar()`](@ref) to allocate an `Array` of the
appropriate size and element type, which is filled in using the basic indexing method described
above. However, when implementing an array wrapper you often want the result to be wrapped as
well:
-->
```
`AbstractArray`をインデックスによって読み取った値は、それ自体が配列になることもあります（たとえば、`Range`を使ってインデックス付けした場合）。
`AbstractArray`のフォールバックしたメソッドは[`similar()`](@ref) を利用して、適切なサイズと基本型の`配列`をメモリに割り当て、上述した基本的なインデックスのメソッドを使ってを値を埋めていきます。
しかし、配列のラッパーが実装されているときには、当然、結果をラップしたくなることもよくあるでしょう。


```jldoctest squarevectype
julia> A[1:2,:]
2×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
```


```@raw html
<!--
In this example it is accomplished by defining `Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`
to create the appropriate wrapped array. (Note that while `similar` supports 1- and 2-argument
forms, in most case you only need to specialize the 3-argument form.) For this to work it's important
that `SparseArray` is mutable (supports `setindex!`). Defining `similar()`, `getindex()` and
`setindex!()` for `SparseArray` also makes it possible to [`copy()`](@ref) the array:
-->
```
この例では`Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`を定義して、適切にラップされた配列を作成しています。
（`similar`は1引数や2引数の場合も動作しますが、ほとんどの場合、3引数に特化した場合だけが必要になる点に注意してください。）
これが動作するには`SparseArray`が更新可能（`setindex!`を利用可能）であることが重要です。
`similar()`、`getindex()`、`setindex!()`を`SparseArray`に定義すると、配列を [`copy()`](@ref)することができるようになります。


```jldoctest squarevectype
julia> copy(A)
3×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
 3.0  6.0  9.0
```


```@raw html
<!--
In addition to all the iterable and indexable methods from above, these types can also interact
with each other and use most of the methods defined in the standard library for `AbstractArrays`:
-->
```
上記のすべての反復可能なメソッドとインデックス可能なメソッドのほかにも、これらの型は相互に利用することができ、標準ライブラリで定義されている`AbstractArrays`のほとんどのメソッドを使用できます。


```jldoctest squarevectype
julia> A[SquaresVector(3)]
3-element SparseArray{Float64,1}:
 1.0
 4.0
 9.0

julia> dot(A[:,1],A[:,2])
32.0
```


```@raw html
<!--
If you are defining an array type that allows non-traditional indexing (indices that start at
something other than 1), you should specialize `indices`. You should also specialize [`similar`](@ref)
so that the `dims` argument (ordinarily a `Dims` size-tuple) can accept `AbstractUnitRange` objects,
perhaps range-types `Ind` of your own design. For more information, see [Arrays with custom indices](@ref).
-->
```
通常ではない（1以外から始まる)インデックスを使うには、`indices`を特化する必要があります。
また引数の`dims`（通常は`Dims`のサイズのタプル）が`AbstractUnitRange`オブジェクト、おそらく独自に設計したの範囲型の`Ind`を受けとれるようにするには、[`similar`](@ref)を特化する必要があります。
詳細については、[Arrays with custom indices](@ref)を参照してください。