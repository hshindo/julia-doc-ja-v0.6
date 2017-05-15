# [Introduction](@id man-introduction)


[](Scientific computing has traditionally required the highest performance, yet domain experts have)
[](largely moved to slower dynamic languages for daily work. We believe there are many good reasons)
[](to prefer dynamic languages for these applications, and we do not expect their use to diminish.)
[](Fortunately, modern language design and compiler techniques make it possible to mostly eliminate)
[](the performance trade-off and provide a single environment productive enough for prototyping and)
[](efficient enough for deploying performance-intensive applications. The Julia programming language)
[](fills this role: it is a flexible dynamic language, appropriate for scientific and numerical computing,)
[](with performance comparable to traditional statically-typed languages.)


科学分野におけるプログラミングはこれまで高いパフォーマンスが
要求されてきましたが、専門家たちは日々の仕事のために遅い動的
プログラミング言語へと移っていきました。動的プログラミング言語が
好まれる理由は多く、今後動的プログラミング言語の需要が
減ることはないでしょう。幸いにも、昨今の言語の設計と
コンパイラ性能は、パフォーマンスのトレードオフを解消し、
また単一環境での試験的開発に十分な生産性と、
パフォーマンスに特化したアプリケーションの開発に十分な効率性を
提供しています。Juliaのプログラミング言語は、フレキシブルな
動的プログラミング言語であり、科学分野や計算処理におけるプ
ログラミングに適し、これまでの静的プログラミング言語に
匹敵するパフォーマンスを提供します。


[](Because Julia's compiler is different from the interpreters used for languages like Python or)
[](R, you may find that Julia's performance is unintuitive at first. If you find that something is)
[](slow, we highly recommend reading through the [Performance Tips](@ref man-performance-tips) section before trying anything)
[](else. Once you understand how Julia works, it's easy to write code that's nearly as fast as C.)


JuliaのコンパイラはPythonやRなどに使用されている解釈プログラムとは
異なるため、はじめは直感的な操作ができないと感じるかもしれません。
もし操作性が悪いと感じた場合は、使用前に[Performance Tips](@ref man-performance-tips)を
お読みになることをお勧めします。Juliaの動きを理解された後には、
C言語を使用するのと同様のスピードで簡単にコードを入力することが
できるでしょう。

<!---
Julia features optional typing, multiple dispatch, and good performance, achieved using type inference
and [just-in-time (JIT) compilation](https://en.wikipedia.org/wiki/Just-in-time_compilation),
implemented using [LLVM](https://en.wikipedia.org/wiki/Low_Level_Virtual_Machine). It is multi-paradigm,
combining features of imperative, functional, and object-oriented programming. Julia provides
ease and expressiveness for high-level numerical computing, in the same way as languages such
as R, MATLAB, and Python, but also supports general programming. To achieve this, Julia builds
upon the lineage of mathematical programming languages, but also borrows much from popular dynamic
languages, including [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)), [Perl](https://en.wikipedia.org/wiki/Perl_(programming_language)),
[Python](https://en.wikipedia.org/wiki/Python_(programming_language)), [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)),
and [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)).
-->

Juliaの特徴として、データの型予測の使用、および[LLVM](https://en.wikipedia.org/wiki/Low_Level_Virtual_Machine)による
[JITコンパイル](https://en.wikipedia.org/wiki/Just-in-time_compilation)
実装による、オプションの入力、マルチディスパッチ、および高い性能を
実現したことが挙げられます。Juliaは、必須となる機能や実用性が高い機能、
およびオブジェクト指向のプログラミングの特徴を組み合わせた
マルチパラダイムです。Juliaは、R、MATLABおよびPythonなどの言語と同様に、
高いレベルの計算処理における手軽さと見やすさを提供し、また一般的な
プログラミングにも対応が可能です。この特性を実現するため、Juliaは数学的な
プログラミング言語の系譜上に構築され、また[Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language))や[Perl](https://en.wikipedia.org/wiki/Perl_(programming_language))、[Python](https://en.wikipedia.org/wiki/Python_(programming_language))、[Lua](https://en.wikipedia.org/wiki/Lua_(programming_language))や[Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language))など
多くの人々に支持された動的プログラミング言語も参考にしています。

<!---
The most significant departures of Julia from typical dynamic languages are:

  * The core language imposes very little; the standard library is written in Julia itself, including
    primitive operations like integer arithmetic
  * A rich language of types for constructing and describing objects, that can also optionally be
    used to make type declarations
  * The ability to define function behavior across many combinations of argument types via [multiple dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch)
  * Automatic generation of efficient, specialized code for different argument types
  * Good performance, approaching that of statically-compiled languages like C
-->

Juliaが実現した、典型的な動的プログラミング言語からの最も
重要な発展は以下の通りです。

  * 標準ライブラリは、整数演算などの基本操作を含め、Julia自身により
    作成されており、コア言語は非常に少なくなっています。
  * オブジェクトの構築または記述する型の種類が豊富にあります。
    オブジェクトはオプションとして型の宣言を行うために
    使用することもできます。
  * [マルチディスパッチ](https://en.wikipedia.org/wiki/Multiple_dispatch)
    により、引数の型の多くの組み合わせに対して関数の動作を
    定義することが可能です。
  * 異なる引数の型の効率的な特殊コードの自動生成が可能です。
  * C言語のような静的にコンパイルされた言語を使用しているかのような
    高いパフォーマンスを発揮します。

<!---
Although one sometimes speaks of dynamic languages as being "typeless", they are definitely not:
every object, whether primitive or user-defined, has a type. The lack of type declarations in
most dynamic languages, however, means that one cannot instruct the compiler about the types of
values, and often cannot explicitly talk about types at all. In static languages, on the other
hand, while one can -- and usually must -- annotate types for the compiler, types exist only at
compile time and cannot be manipulated or expressed at run time. In Julia, types are themselves
run-time objects, and can also be used to convey information to the compiler.
-->

動的プログラミング言語は「型が不要」と言われることがありますが、
これは正しくはありません。たとえ単純なオブジェクトであったとしても、
またはユーザ定義によるものでも、すべてのオブジェクトには型があります。
多くの動的プログラミングにおいて型宣言をしないということは、
値の型をコンパイラに指示できない、もしくは型についてなにも
知らないことを意味します。一方静的プログラミング言語では、
コンパイラのために型の注釈をつけるとが可能であり、通常は必須です。
静的プログラミング言語では型はコンパイル時のみに必要であり、
実行時に変更したり記載することはできません。Juliaでは、型自体が
実行時のオブエクトであり、また型はコンパイラに情報を伝えるめに
使用することも可能です。

<!---
While the casual programmer need not explicitly use types or multiple dispatch, they are the core
unifying features of Julia: functions are defined on different combinations of argument types,
and applied by dispatching to the most specific matching definition. This model is a good fit
for mathematical programming, where it is unnatural for the first argument to "own" an operation
as in traditional object-oriented dispatch. Operators are just functions with special notation
-- to extend addition to new user-defined data types, you define new methods for the `+` function.
Existing code then seamlessly applies to the new data types.
-->

一般的なプログラマは型やマルチティスパッチを使用する必要が
ないかもしれませんが、関数が、異なる引数のタイプの
組み合わせにより定義されてることや、最も的確な定義のマッチング対して
ディスパッチすることによって実行される点は、Juliaの特徴を統合する核です。
この仕組みは、従来のオブジェクト指向のディスパッチのように
最初の引数が処理を「占有」するといったことが一般的ではない、
数学的プログラミングに適しています。Juliaにおける操作者は特殊な
記号を伴う関数であると言えます。例えば、新しいユーザ定義のデータ型に
追加拡張するために、新しいメソッドを`+`関数に定義することができます。
既存コードはシームレスに新しいデータ型に適用されます。

<!---
Partly because of run-time type inference (augmented by optional type annotations), and partly
because of a strong focus on performance from the inception of the project, Julia's computational
efficiency exceeds that of other dynamic languages, and even rivals that of statically-compiled
languages. For large scale numerical problems, speed always has been, continues to be, and probably
always will be crucial: the amount of data being processed has easily kept pace with Moore's Law
over the past decades.
-->

実行時型予測（オプションの型注釈により増大）およびこの
プロジェクト当初からのパフォーマンス重視の姿勢により、
Juliaの計算効率は他の動的プログラミング言語や、さらには
静的プログラム言語に対しても上回っていると言えます。
過去数十年においてデータの処理量がムーアの法則のペースを
維持しながら増加していることからわかるように、
大規模な計算処理にとって処理速度はこれまで常に、
そしてこれからも非常に重要です。

<!---
Julia aims to create an unprecedented combination of ease-of-use, power, and efficiency in a single
language. In addition to the above, some advantages of Julia over comparable systems include:

  * Free and open source ([MIT licensed](https://github.com/JuliaLang/julia/blob/master/LICENSE.md))
  * User-defined types are as fast and compact as built-ins
  * No need to vectorize code for performance; devectorized code is fast
  * Designed for parallelism and distributed computation
  * Lightweight "green" threading ([coroutines](https://en.wikipedia.org/wiki/Coroutine))
  * Unobtrusive yet powerful type system
  * Elegant and extensible conversions and promotions for numeric and other types
  * Efficient support for [Unicode](https://en.wikipedia.org/wiki/Unicode), including but not limited
    to [UTF-8](https://en.wikipedia.org/wiki/UTF-8)
  * Call C functions directly (no wrappers or special APIs needed)
  * Powerful shell-like capabilities for managing other processes
  * Lisp-like macros and other metaprogramming facilities
-->

Juliaは、単一言語での使いやすさ、処理能力、そして
処理効率の前例の無い連携を創造することを目的としています。
上記に加えて、以下は他のシステムには無いJuliaの利点です。

  * フリーオープンソース（[MITライセンス](https://github.com/JuliaLang/julia/blob/master/LICENSE.md)）
  * ユーザ定義の型は既存の型と同じくらい早く、コンパクト
  * パフォーマンス向上のためにコードをベクトル化する必要がない
    非ベクトル化コード処理は早い
  * 並列処理と分散計算ができるよう設計
  * 軽く「エコ」なスレッド（[コルーチン](https://en.wikipedia.org/wiki/Coroutine)）
  * 控えめでありながら処理能力が高いシステム
  * 簡潔かつ拡張可能な数値および他のデータ型のための変換と推進
  * 効率的な[UTF-8](https://en.wikipedia.org/wiki/UTF-8)
    を含む[Unicode](https://en.wikipedia.org/wiki/Unicode)への対応
  * C関数を直接呼び出すことが可能（ラッパーや特別なAPIは不要）
  * 他のプロセスを管理するための処理能力が高いシェルに似た機能
