[](# [Modules](@id modules))
# [モジュール](@id modules)


```@raw html
<!--
Modules in Julia are separate variable workspaces, i.e. they introduce a new global scope. They
are delimited syntactically, inside `module Name ... end`. Modules allow you to create top-level
definitions (aka global variables) without worrying about name conflicts when your code is used
together with somebody else's. Within a module, you can control which names from other modules
are visible (via importing), and specify which of your names are intended to be public (via exporting).

The following example demonstrates the major features of modules. It is not meant to be run, but
is shown for illustrative purposes:
-->
```
Juliaのモジュールは独立した変数のワークスペースで、新しいグローバルスコープが導入されます。
モジュールは`module Name ... end`のなかに構文的に区切られます。
モジュールを使うと、(グローバル変数として知られる)最上位の定義を行うことできて、自分のコードを他の誰かのコードと一緒に使っても、名前の競合を心配しなくてすみます。
モジュール内では、他のモジュールのどの名前を（インポートして）参照するかを制御したり、
自分のモジュールのどの名前を（エクスポートして）公開するかを指定することができます。

次の例は、モジュールの主な機能を示しています。
これは実行用ではなく、説明用途のものです。

```julia
module MyModule
using Lib

using BigLib: thing1, thing2

import Base.show

importall OtherLib

export MyType, foo

struct MyType
    x
end

bar(x) = 2x
foo(a::MyType) = bar(a.x) + 1

show(io::IO, a::MyType) = print(io, "MyType $(a.x)")
end
```


```@raw html
<!--
Note that the style is not to indent the body of the module, since that would typically lead to
whole files being indented.

This module defines a type `MyType`, and two functions. Function `foo` and type `MyType` are exported,
and so will be available for importing into other modules.  Function `bar` is private to `MyModule`.
-->
```
表記スタイル上の注意点は、モジュールの本体は字下げすべきではないことでしょう。
これは字下げをすると、通常ファイル全体を字下げすることになってしまいがちだからです。

このモジュールでは、`MyType`という型と2つの関数が定義されています。
関数`foo`と型`MyType`はエクスポートされているので、他のモジュールにインポートして利用することができます。
`bar`は`MyModule`内でプライベートな関数です。



```@raw html
<!--
The statement `using Lib` means that a module called `Lib` will be available for resolving names
as needed. When a global variable is encountered that has no definition in the current module,
the system will search for it among variables exported by `Lib` and import it if it is found there.
This means that all uses of that global within the current module will resolve to the definition
of that variable in `Lib`.
-->
```

`using Lib`と宣言すると、必要に応じて`Lib`というモジュールを名前の解決に利用できます。
グローバル変数が現在のモジュールで定義されていない場合、`Lib`でエクスポートされている変数の中から検索し、見つかった場合はインポートします。
これは、グローバル変数を現在のモジュール内で使った場合はすべて、`Lib`内の同名の変数の定義によって解決されることを意味します。


```@raw html
<!--
The statement `using BigLib: thing1, thing2` is a syntactic shortcut for `using BigLib.thing1, BigLib.thing2`.

The `import` keyword supports all the same syntax as `using`, but only operates on a single name
at a time. It does not add modules to be searched the way `using` does. `import` also differs
from `using` in that functions must be imported using `import` to be extended with new methods.

In `MyModule` above we wanted to add a method to the standard `show` function, so we had to write
`import Base.show`. Functions whose names are only visible via `using` cannot be extended.
-->
```
`using BigLib: thing1, thing2`という文は`using BigLib.thing1, BigLib.thing2`の簡易構文です。

`import`キーワードは`using`と同じ構文で利用可能ですが、一度に一つの名前にしか使えません。
`using`のようにモジュール全体を探索対象に加えることはできません。
インポートした関数を新しいメソッドで拡張したい時は、必ず`import`を使う必要がある点も、`using`と`import`の相違点です。

上記の`MyModule`で、標準的な`show`関数にメソッドを追加したい場合は`import Base.show`と書かなければなりません。
`using`を通じてしか名前を参照できない関数は、拡張することはできません。



```@raw html
<!--
The keyword `importall` explicitly imports all names exported by the specified module, as if
`import` were individually used on all of them.

Once a variable is made visible via `using` or `import`, a module may not create its own variable
with the same name. Imported variables are read-only; assigning to a global variable always affects
a variable owned by the current module, or else raises an error.
-->
```
キーワード`importall`を明示的に使うと、指定したモジュールでエクスポートされているすべての名前をインポートするので、
`import`を使って個々にすべてをインポートするのと同じ効果があります。

`using`や`import`を通じて、変数がいったん参照可能になると、同じ名前の変数をそのモジュールでは作成できません。
インポートされた変数は読み取り専用です。
グローバル変数への代入は、常に現在のモジュールに所属する変数に対して行われるので、そうでなければエラーが発生します。

[](## Summary of module usage)
## モジュールの用法のまとめ


```@raw html
<!--
To load a module, two main keywords can be used: `using` and `import`. To understand their differences,
consider the following example:
-->
```
モジュールのロードには、主に二つのキーワード`using`と`import`を使用することができます。
この違いを理解するために、次の例を考えましょう。

```julia
module MyModule

export x, y

x() = "x"
y() = "y"
p() = "p"

end
```


```@raw html
<!--
In this module we export the `x` and `y` functions (with the keyword `export`), and also have
the non-exported function `p`. There are several different ways to load the Module and its inner
functions into the current workspace:
-->
```
このモジュールには、（キーワード`export`を使って）エクスポートしている関数`x`,`y`と、エクスポートしていない関数`p`があります。
モジュールをロードして、その内部の関数を、現在のワークスペースに導入するには、いくつかの方法があります。

```@raw html
<!--
| Import Command                  | What is brought into scope                                                      | Available for method extension              |
|:------------------------------- |:------------------------------------------------------------------------------- |:------------------------------------------- |
| `using MyModule`                | All `export`ed names (`x` and `y`), `MyModule.x`, `MyModule.y` and `MyModule.p` | `MyModule.x`, `MyModule.y` and `MyModule.p` |
| `using MyModule.x, MyModule.p`  | `x` and `p`                                                                     |                                             |
| `using MyModule: x, p`          | `x` and `p`                                                                     |                                             |
| `import MyModule`               | `MyModule.x`, `MyModule.y` and `MyModule.p`                                     | `MyModule.x`, `MyModule.y` and `MyModule.p` |
| `import MyModule.x, MyModule.p` | `x` and `p`                                                                     | `x` and `p`                                 |
| `import MyModule: x, p`         | `x` and `p`                                                                     | `x` and `p`                                 |
| `importall MyModule`            | All `export`ed names (`x` and `y`)                                              | `x` and `y`                                 |
-->
```
| インポートするコマンド                 | スコープに導入されるもの                                                     | メソッドの拡張に利用可能なもの              |
|:------------------------------- |:------------------------------------------------------------------------------- |:------------------------------------------- |
| `using MyModule`                | `エクスポート`されたすべての名前 (`x`と`y`), `MyModule.x`, `MyModule.y`,`MyModule.p` | `MyModule.x`, `MyModule.y`,`MyModule.p` |
| `using MyModule.x, MyModule.p`  | `x`と`p`                                                                     |                                             |
| `using MyModule: x, p`          | `x`と`p`                                                                     |                                             |
| `import MyModule`               | `MyModule.x`, `MyModule.y`,`MyModule.p`                                     | `MyModule.x`, `MyModule.y`,`MyModule.p` |
| `import MyModule.x, MyModule.p` | `x`と`p`                                                                     | `x`と`p`                                |
| `import MyModule: x, p`         | `x`と`p`                                                                     | `x`と`p`                                 |
| `importall MyModule`            | `エクスポート`されたすべての名前 (`x`と`y`)                                              | `x`と`p`                                |

[](### Modules and files)
### モジュールとファイル


```@raw html
<!--
Files and file names are mostly unrelated to modules; modules are associated only with module
expressions. One can have multiple files per module, and multiple modules per file:
-->
```
ファイルやファイル名は、モジュールとはほとんど無関係です。
モジュールと関連するのは、モジュール式だけです。
1つのモジュールが複数のファイルにまたがったり、1つのファイルに複数のモジュールを入れたりすることができます。

```julia
module Foo

include("file1.jl")
include("file2.jl")

end
```


```@raw html
<!--
Including the same code in different modules provides mixin-like behavior. One could use this
to run the same code with different base definitions, for example testing code by running it with
"safe" versions of some operators:
-->
```
異なるモジュールに同じコードを組み込むと、mixinのような動作を実現できます。
これを利用して、同じコードでも基本の定義を変えて実行することができます。
たとえば、演算子を「安全な」バージョンに変えてコードを検査する場合などがあります。



```julia
module Normal
include("mycode.jl")
end

module Testing
include("safe_operators.jl")
include("mycode.jl")
end
```

[](### Standard modules)
### 標準モジュール


```@raw html
<!--
There are three important standard modules: Main, Core, and Base.

Main is the top-level module, and Julia starts with Main set as the current module.  Variables
defined at the prompt go in Main, and `whos()` lists variables in Main.

Core contains all identifiers considered "built in" to the language, i.e. part of the core language
and not libraries. Every module implicitly specifies `using Core`, since you can't do anything
without those definitions.

Base is the standard library (the contents of base/). All modules implicitly contain `using Base`,
since this is needed in the vast majority of cases.
-->
```
3つの重要な標準モジュールがあります。
Mainモジュール、Coreモジュール、Baseモジュールです。

Mainは最上位のモジュールであり、JuliaはMainを現在のモジュールに設定することから開始します。
プロンプトで定義された変数はMainに格納され、Mainの変数は`whos()`を使って列挙できます。

Coreには、言語に"組み込まれている"と考えてよいすべての識別子、つまりライブラリではなく言語の核の一部が含まれています。
これらの定義がないと何もできないので、すべてのモジュールには暗黙的な`using Core`の宣言が含まれます。

Baseは標準ライブラリ（base/の中身）です。
これは大抵の場合に必要となるので、すべてのモジュールには暗黙的な`using Base`の宣言が含まれます。
 
[](### Default top-level definitions and bare modules)
### デフォルトの最上位の定義とベアモジュール


```@raw html
<!--
In addition to `using Base`, modules also automatically contain a definition of the `eval` function,
which evaluates expressions within the context of that module.

If these default definitions are not wanted, modules can be defined using the keyword `baremodule`
instead (note: `Core` is still imported, as per above). In terms of `baremodule`, a standard
`module` looks like this:
-->
```
モジュールは、`using Base`のほかに、そのモジュールのコンテキストに沿って式を評価する `eval` 関数の定義を自動的に取り込みます。

これらのデフォルトの定義が不要の場合は、キーワード `baremodule` を使用してモジュールを定義することができます （上記のように Core はインポートされます）。
 `baremodule`を使うと、標準的な`モジュール`の動作は次のように書けます。

```
baremodule Mod

using Base

eval(x) = Core.eval(Mod, x)
eval(m,x) = Core.eval(m, x)

...

end
```

[](### Relative and absolute module paths)
### 相対モジュールパスと絶対モジュールパス

```@raw html
<!--
Given the statement `using Foo`, the system looks for `Foo` within `Main`. If the module does
not exist, the system attempts to `require("Foo")`, which typically results in loading code from
an installed package.

However, some modules contain submodules, which means you sometimes need to access a module that
is not directly available in `Main`. There are two ways to do this. The first is to use an absolute
path, for example `using Base.Sort`. The second is to use a relative path, which makes it easier
to import submodules of the current module or any of its enclosing modules:
-->
```
`using Foo`文が書かれている場合、システムは `Main` 内の `Foo` を検索します。 モジュールが存在しない場合、システムは require("Foo") を試行し、通常はインストールされたパッケージからコードがロードされます。

しかし、モジュールの中にはサブモジュールが含まれているものがあり、`Main`から直接利用できないモジュールにアクセスする必要となります。
 これを行うには2つの方法があります。
 1つ目は `using Base.Sort` などの絶対パスを使用する方法です。
 2つ目は相対パスを使用し、サブモジュールを含む現在のモジュールなどからのインポートを容易にする方法です。
```
module Parent

module Utils
...
end

using .Utils

...
end
```


```@raw html
<!--
Here module `Parent` contains a submodule `Utils`, and code in `Parent` wants the contents of
`Utils` to be visible. This is done by starting the `using` path with a period. Adding more leading
periods moves up additional levels in the module hierarchy. For example `using ..Utils` would
look for `Utils` in `Parent`'s enclosing module rather than in `Parent` itself.

Note that relative-import qualifiers are only valid in `using` and `import` statements.
-->
```
ここではモジュール `Parent` にサブモジュール `Utils` が含まれるので、`Utils` の内容は `Parent` のコードから参照できる必要があります。
 これは、`using`の パスをピリオドから始めれば可能です。
先頭のピリオドを追加すると、モジュール階層の追加レベルが上がります。
例えば `using ..Utils` は、 `Parent` 自体ではなく `Parent` が囲むモジュール内で `Utils` を検索します。

相対インポート修飾子は、`using` および `import` 文でのみ有効であることに注意してください。


[](### Module file paths)
### モジュールファイルパス


```@raw html
<!--
The global variable [`LOAD_PATH`](@ref) contains the directories Julia searches for modules when calling
`require`. It can be extended using [`push!`](@ref):
-->
```
グローバル変数[`LOAD_PATH`](@ref)には、Juliaが`require`を呼び出す時にモジュールを検索するディレクトリが含まれています 。 これは[`push!`](@ref)を使って拡張することができます。

```julia
push!(LOAD_PATH, "/Path/To/My/Module/")
```


```@raw html
<!--
Putting this statement in the file `~/.juliarc.jl` will extend [`LOAD_PATH`](@ref) on every Julia startup.
Alternatively, the module load path can be extended by defining the environment variable `JULIA_LOAD_PATH`.
-->
```
この文を`~/.juliarc.jl`ファイルに書き込むと、Juliaの起動時ごとに[`LOAD_PATH`](@ref)が拡張されます。
あるいは、環境変数の`JULIA_LOAD_PATH`を定義してモジュールのロードパスを拡張することもできます。

[](### Namespace miscellanea)
### 名前空間に関する雑記


```@raw html
<!--
If a name is qualified (e.g. `Base.sin`), then it can be accessed even if it is not exported.
This is often useful when debugging. It can also have methods added to it by using the qualified
name as the function name. However, due to syntactic ambiguities that arise, if you wish to add
methods to a function in a different module whose name contains only symbols, such as an operator,

`Base.+` for example, you must use `Base.:+` to refer to it. If the operator is more than one
character in length you must surround it in brackets, such as: `Base.:(==)`.
-->
```
名前が修飾子をつけると（例えば`Base.sin`）、名前がエクスポートされていなくてもアクセスできます。
これは、デバッグ時に便利です。
また、修飾子のついた関数に対してメソッドを追加することもできます。
しかし、構文上のあいまいさを避けるため、別のモジュールにある、演算子のような記号だけからなる名前の関数（例えば`Base.+`）にメソッドを追加する場合 `Base.:+`のように参照する必要があります。
演算子が２文字以上の場合は、`Base.:(==)`のよう括弧で囲む必要があります。


```@raw html
<!--
Macro names are written with `@` in import and export statements, e.g. `import Mod.@mac`. Macros
in other modules can be invoked as `Mod.@mac` or `@Mod.mac`.

The syntax `M.x = y` does not work to assign a global in another module; global assignment is
always module-local.

A variable can be "reserved" for the current module without assigning to it by declaring it as
`global x` at the top level. This can be used to prevent name conflicts for globals initialized
after load time.
-->
```
インポートやエクスポートの文中では、マクロ名は`@`をつけて`import Mod.@mac`のように書きます。
他のモジュールのマクロは、`Mod.@mac`や`@Mod.mac`のようにして呼び出すことができます。

`M.x = y`という構文で、別のモジュールのグローバルに代入することはできません。
グローバル変数の代入は常にローカルなモジュールで行われます。

変数は、`global x`のように最上位で宣言することによって、代入をせずに、現在のモジュールに対して "予約"することができます。
これは、ロード後に初期化されるグローバルの名前の競合を防ぐために利用できます。

[](### Module initialization and precompilation)
### モジュールの初期化とプレコンパイル


```@raw html
<!--
Large modules can take several seconds to load because executing all of the statements in a module
often involves compiling a large amount of code. Julia provides the ability to create precompiled
versions of modules to reduce this time.

To create an incremental precompiled module file, add `__precompile__()` at the top of your module
file (before the `module` starts). This will cause it to be automatically compiled the first time
it is imported. Alternatively, you can manually call `Base.compilecache(modulename)`. The resulting
cache files will be stored in `Base.LOAD_CACHE_PATH[1]`. Subsequently, the module is automatically
recompiled upon `import` whenever any of its dependencies change; dependencies are modules it
imports, the Julia build, files it includes, or explicit dependencies declared by `include_dependency(path)`
in the module file(s).
-->
```
大きなモジュールはロードに数秒かかることがあり、これはモジュール内のすべてのステートメントを実行する際に、大量のコードをコンパイルすることが多いためです。
Juliaには、モジュールをプリコンパイルして、この時間を短縮する機能があります。

逐次的にモジュールをプリコンパイルするには、モジュールファイルの先頭に（module開始前に）`__precompile__()`を追加します。
こうすると、はじめてインポートされるときに自動的にコンパイルされます。
また、手動で`Base.compilecache(modulename)`を呼び出すこともできます。
実行結果のキャッシュファイルは`Base.LOAD_CACHE_PATH[1]`に保存されます。
その後、依存関係のいずれかが変更されると、モジュールはインポート時に自動的に再コンパイルされます。
依存関係とは、インポートするモジュール、Juliaのビルド、含まれるファイル、または`include_dependency(path)`と モジュールファイルで宣言された明示的な依存関係のことです。

```@raw html
<!--
For file dependencies, a change is determined by examining whether the modification time (mtime)
of each file loaded by `include` or added explicitly by `include_dependency` is unchanged, or equal
to the modification time truncated to the nearest second (to accommodate systems that can't copy
mtime with sub-second accuracy). It also takes into account whether the path to the file chosen
by the search logic in `require` matches the path that had created the precompile file.

It also takes into account the set of dependencies already loaded into the current process and
won't recompile those modules, even if their files change or disappear, in order to avoid creating
incompatibilities between the running system and the precompile cache. If you want to have changes
to the source reflected in the running system, you should call `reload("Module")` on the module
you changed, and any module that depended on it in which you want to see the change reflected.
-->
```
ファイル依存関係の場合、変更は、`include`によってロードされたファイル、または`include_dependency`によって明示的に追加された各ファイルの変更時刻（mtime）が変更されていないか、または最も近い秒に切り捨てられた変更時刻に等しいかどうかを調べることによって判断されます（mtimeを秒未満の精度でコピーできないシステムに対応するため）。
また、`require`中の検索ロジックによって選択されたファイルへのパスがプリコンパイル・ファイルを作成したパスと一致するかどうかも考慮されます。

また、現在のプロセスにすでにロードされている依存関係を考慮に入れ、ファイルが変更や削除されても、実行中のシステムとプリコンパイル・キャッシュとの間の非互換性を避けるために、それらのモジュールを再コンパイルしません。
実行中のシステムにソースに加えた変更を反映させたい場合は、変更したモジュールやそれに依存しているモジュールを、変更を反映したい場所で、`reload("Module")`のように呼び出す必要があります。

```@raw html
<!--
Precompiling a module also recursively precompiles any modules that are imported therein. If you
know that it is *not* safe to precompile your module (for the reasons described below), you should
put `__precompile__(false)` in the module file to cause `Base.compilecache` to throw an error
(and thereby prevent the module from being imported by any other precompiled module).

`__precompile__()` should *not* be used in a module unless all of its dependencies are also using
`__precompile__()`. Failure to do so can result in a runtime error when loading the module.
-->
```
また、モジュールをプリコンパイルすると、そこにインポートされたモジュールも再帰的にプリコンパイルされます。
モジュールをプリコンパイルするのが**安全でない**ことが(下記のような理由で)分かっている場合、そのモジュールに`__precompile__(false)`と書いて、`Base.compilecache`がエラーを投げて、（他のプリコンパイル済みモジュールがインポートするのを防ぐ）必要があります。

`__precompile__()`はすべての依存先でも`__precompile__()`を使用していない限り、モジュール内で使用すべきではありません。
そうしないと、モジュールをロードするときにランタイムエラーが発生する可能性があります。


```@raw html
<!--
In order to make your module work with precompilation, however, you may need to change your module
to explicitly separate any initialization steps that must occur at *runtime* from steps that can
occur at *compile time*.  For this purpose, Julia allows you to define an `__init__()` function
in your module that executes any initialization steps that must occur at runtime. This function
will not be called during compilation (`--output-*` or `__precompile__()`). You may, of course,
call it manually if necessary, but the default is to assume this function deals with computing
state for the local machine, which does not need to be – or even should not be – captured
in the compiled image. It will be called after the module is loaded into a process, including
if it is being loaded into an incremental compile (`--output-incremental=yes`), but not if it
is being loaded into a full-compilation process.
-->
```
ただし、モジュールをプリコンパイルして動作するようにするには、**実行時**の初期化処理と、**コンパイル時**の処理をモジュール内で明確に分離する必要がでてくるかもしれません。
この用途に、Juliaではモジュール内で`__init__()`関数を定義して、実行時に必ず発生する初期化処理を記述することができます。
この関数は、コンパイル時には呼び出されません（`--output-*`や`__precompile__()`）。
もちろん、必要に応じて手動で呼び出すこともできますが、デフォルトでは、この関数はローカルマシンの計算状態を扱うことが想定されているので、コンパイル後のイメージに捕捉される必要はありませんし、されるべきでもありません。
`__init__()`関数はモジュールがプロセスにロードされた後で呼び出され、これはインクリメンタルコンパイル（`--output-incremental=yes`）でロードされる場合も含まれますが、フルコンパイルプロセスにロードされている場合は含まれません。


```@raw html
<!--
In particular, if you define a `function __init__()` in a module, then Julia will call `__init__()`
immediately *after* the module is loaded (e.g., by `import`, `using`, or `require`) at runtime
for the *first* time (i.e., `__init__` is only called once, and only after all statements in the
module have been executed). Because it is called after the module is fully imported, any submodules
or other imported modules have their `__init__` functions called *before* the `__init__` of the
enclosing module.
-->
```

特にモジュール内に、`function __init__()`が定義されている場合は、`__init__()`はモジュールがロードされた直後に、（例えば`import`、`using`、`require`によって）初めての実行時に（すなわち、`__init__`は一度だけ、モジュール内のすべての文の後にモジュールが実行されてから）Juliaから呼び出されます。
`__init__` はモジュールが完全にインポートされた後に呼び出されるため、サブモジュールまたはその他のインポートされたモジュールにある`__init__`は、それを囲むモジュールの`__init__`の前に呼び出されます。



```@raw html
<!--
Two typical uses of `__init__` are calling runtime initialization functions of external C libraries
and initializing global constants that involve pointers returned by external libraries.  For example,
suppose that we are calling a C library `libfoo` that requires us to call a `foo_init()` initialization
function at runtime. Suppose that we also want to define a global constant `foo_data_ptr` that
holds the return value of a `void *foo_data()` function defined by `libfoo` -- this constant must
be initialized at runtime (not at compile time) because the pointer address will change from run
to run.  You could accomplish this by defining the following `__init__` function in your module:
-->
```
`__init__`の2つの典型的な用途は、外部Cライブラリの実行時の初期化関数を呼び出すことと、外部ライブラリによって返されるポインタをもつグローバル定数を初期化することです。
たとえば、実行時に初期化関数`foo_init()`を呼び出す必要のあるCライブラリ`libfoo`を呼び出すとします。
また`libfoo`で定義された`void *foo_data()`関数の戻り値を保持するグローバル定数`foo_data_ptr`も定義したいとします。
ポインタのアドレスが実行ごとに変わるため、この定数は実行時（コンパイル時ではなく）に初期化する必要があります。__init__モジュール内で次のような`__init__`関数を定義することでこれを達成できます：

```julia
const foo_data_ptr = Ref{Ptr{Void}}(0)
function __init__()
    ccall((:foo_init, :libfoo), Void, ())
    foo_data_ptr[] = ccall((:foo_data, :libfoo), Ptr{Void}, ())
end
```


```@raw html
<!--
Notice that it is perfectly possible to define a global inside a function like `__init__`; this
is one of the advantages of using a dynamic language. But by making it a constant at global scope,
we can ensure that the type is known to the compiler and allow it to generate better optimized
code. Obviously, any other globals in your module that depends on `foo_data_ptr` would also have
to be initialized in `__init__`.

Constants involving most Julia objects that are not produced by `ccall` do not need to be placed
in `__init__`: their definitions can be precompiled and loaded from the cached module image. This
includes complicated heap-allocated objects like arrays. However, any routine that returns a raw
pointer value must be called at runtime for precompilation to work (Ptr objects will turn into
null pointers unless they are hidden inside an isbits object). This includes the return values
of the Julia functions `cfunction` and `pointer`.
-->
```
グローバル変数を`__init__`のような関数の内部に定義することは本当に可能であることに注意してください。
これは動的言語を使用する利点の1つです。
しかし、これをグローバルスコープにおける定数とすることで、型がコンパイラに認識され、より最適化されたコードを生成できるようにすることができます。
明らかに、`foo_data_ptr`に依存するモジュール内のその他のグローバルも`__init__`で初期化する必要があります。

`ccall`によって生成されたわけではないほとんどのJuliaオブジェクトなどの定数は、`__init__`に配置する必要はありません。
その定義は、プリコンパイルして、キャッシュされたモジュールイメージからロードできます。
これには、配列のようなヒープに割り当てられた複雑なオブジェクトが含まれます。
しかし、生のポインタ値を返すルーチンは、実行時にプリコンパイルを実行するために呼び出さなければなりません（Ptrオブジェクトはisbitsオブジェクト内に隠されていない限り、NULLポインタになります）。
これには、Juliaの関数`cfunction`や`pointer`の戻り値が含まれます。



```@raw html
<!--
Dictionary and set types, or in general anything that depends on the output of a `hash(key)` method,
are a trickier case.  In the common case where the keys are numbers, strings, symbols, ranges,
`Expr`, or compositions of these types (via arrays, tuples, sets, pairs, etc.) they are safe to
precompile.  However, for a few other key types, such as `Function` or `DataType` and generic
user-defined types where you haven't defined a `hash` method, the fallback `hash` method depends
on the memory address of the object (via its `object_id`) and hence may change from run to run.
If you have one of these key types, or if you aren't sure, to be safe you can initialize this
dictionary from within your `__init__` function. Alternatively, you can use the `ObjectIdDict`
dictionary type, which is specially handled by precompilation so that it is safe to initialize
at compile-time.
-->
```
辞書や集合の型、または一般に`hash(key)`メソッドの出力に依存するものはすべて、トリッキーなケースです。
キーが数字、文字列、記号、範囲、`Expr`、またこれらの型の複合（配列、タプル、セット、ペアなど）である一般的なケースでは、プリコンパイルするのが安全です。
ただし、いくつかの他のキーの型、例えば`Function`や`DataType`や`hash`メソッドを定義していない汎化的なユーザー定義型などのの場合、副次的な`hash`メソッドはオブジェクトのメモリアドレスに依存します（`object_id`を介して）ので、実行ごとに変更される可能性があります。
これらのキーの型のいずれかの場合や、わからない場合は、安全のために`__init__`関数内からこの辞書を初期化することができます。
また、`ObjectIdDict`という辞書型をかわりに利用することもできます。
これは、コンパイル時に安全に初期化できるように、プリコンパイルで特別に処理される辞書型です。



```@raw html
<!--
When using precompilation, it is important to keep a clear sense of the distinction between the
compilation phase and the execution phase. In this mode, it will often be much more clearly apparent
that Julia is a compiler which allows execution of arbitrary Julia code, not a standalone interpreter
that also generates compiled code.

-->
```

プリコンパイルを使用する場合は、コンパイル・フェーズと実行フェーズの区別を明確にすることが重要です。
このモードでは、Juliaがコンパイルされたコードを生成するスタンドアロンインタプリタではなく、任意のJuliaコードを実行できるコンパイラであることがはっきりしています。

```@raw html
<!--

Other known potential failure scenarios include:

1. Global counters (for example, for attempting to uniquely identify objects) Consider the following
   code snippet:
-->
```
その他の既知の潜在的な障害シナリオには、

    1.グローバルカウンタ（たとえば、オブジェクトを一意に識別しようとする場合）次のコードスニペットを考えてみましょう。


   ```julia
   mutable struct UniquedById
       myid::Int
       let counter = 0
           UniquedById() = new(counter += 1)
       end
   end
   ```


```@raw html
<!--
   while the intent of this code was to give every instance a unique id, the counter value is recorded
   at the end of compilation. All subsequent usages of this incrementally compiled module will start
   from that same counter value.

   Note that `object_id` (which works by hashing the memory pointer) has similar issues (see notes
   on `Dict` usage below).

   One alternative is to store both [`current_module()`](@ref) and the current `counter` value, however,
   it may be better to redesign the code to not depend on this global state.
-->
```
このコードの目的はすべてのインスタンスに一意のIDを与えることでしたが、カウンタ値はコンパイルの最後に記録されます。この逐次的にコンパイルした後にモジュールを使うとすべて、同じカウンタ値から開始されます。

`object_id`（メモリポインタをハッシュすることによって動作する）にも同様の問題があるのでちゅういしてください（下記の`Dict`の用法を参照)。

代替方法の1つに [`current_module()`](@ref)と現在の`counter`値の両方を保存することがありますが、このグローバル状態に依存しないようにコードを再設計するほうがよい場合があります。



```@raw html
<!--
2. Associative collections (such as `Dict` and `Set`) need to be re-hashed in `__init__`. (In the
   future, a mechanism may be provided to register an initializer function.)
3. Depending on compile-time side-effects persisting through load-time. Example include: modifying
   arrays or other variables in other Julia modules; maintaining handles to open files or devices;
   storing pointers to other system resources (including memory);
4. Creating accidental "copies" of global state from another module, by referencing it directly instead
   of via its lookup path. For example, (in global scope):
-->
```
2.連想コレクション(`Dict`や`Set`など）は`__init__`内で再ハッシュ化する必要があります。（将来的に、初期化関数を登録するためのメカニズムが提供されるかもしれません。）

3.コンパイル時の副作用の依存はロード時まで持続します。
例をあげると、他のJuliaモジュールの配列や他の変数を変更する、 
ファイルやデバイスを開くためのハンドルを保持する、
他のシステムのリソース（メモリを含む）へのポインタを格納するなどです。

4.ルックアップパス経由ではなく直接参照することにより、別のモジュールからグローバル状態の偶発的な「コピー」を作成する。たとえば、（グローバルスコープ内で）

   ```julia
   #mystdout = Base.STDOUT #= will not work correctly, since this will copy Base.STDOUT into this module =#
   # instead use accessor functions:
   getstdout() = Base.STDOUT #= best option =#
   # or move the assignment into the runtime:
   __init__() = global mystdout = Base.STDOUT #= also works =#
   ```


```@raw html
<!--
Several additional restrictions are placed on the operations that can be done while precompiling
code to help the user avoid other wrong-behavior situations:

1. Calling [`eval`](@ref) to cause a side-effect in another module. This will also cause a warning to be
   emitted when the incremental precompile flag is set.
2. `global const` statements from local scope after `__init__()` has been started (see issue #12010
   for plans to add an error for this)
3. Replacing a module (or calling [`workspace()`](@ref)) is a runtime error while doing an incremental precompile.
-->
```
コードをプリコンパイルしている間に実行できる操作は、ユーザーが他の誤った動作状況を回避するために、いくつかのさらなる制限があります。

    1. [`eval`](@ref)を呼び出して他のモジュールに副作用を引き起こすこと。
     インクリメンタル・プリコンパイル・フラグが設定されている場合に警告が出されます。
    2.`__init__()`の開始後にローカルスコープから`global const`文を書くこと（これにエラーを追加する計画については、問題＃12010を参照してください）
    3.モジュールを置き換える(または[`workspace()`](@ref) を呼び出す)こと。
    逐次プリコンパイル中はランタイムエラーが起こります。

```@raw html
<!--
A few other points to be aware of:

1. No code reload / cache invalidation is performed after changes are made to the source files themselves,
   (including by [`Pkg.update`](@ref)), and no cleanup is done after [`Pkg.rm`](@ref)
2. The memory sharing behavior of a reshaped array is disregarded by precompilation (each view gets
   its own copy)
3. Expecting the filesystem to be unchanged between compile-time and runtime e.g. [`@__FILE__`](@ref)/`source_path()`
   to find resources at runtime, or the BinDeps `@checked_lib` macro. Sometimes this is unavoidable.
   However, when possible, it can be good practice to copy resources into the module at compile-time
   so they won't need to be found at runtime.
4. `WeakRef` objects and finalizers are not currently handled properly by the serializer (this will
   be fixed in an upcoming release).
5. It is usually best to avoid capturing references to instances of internal metadata objects such
   as `Method`, `MethodInstance`, `MethodTable`, `TypeMapLevel`, `TypeMapEntry` and fields of those objects,
   as this can confuse the serializer and may not lead to the outcome you desire. It is not necessarily
   an error to do this, but you simply need to be prepared that the system will try to copy some
   of these and to create a single unique instance of others.
-->
```
注意すべき他のいくつかの点は次のとおりです。

    1.ソースファイル自体が変更された後（[`Pkg.update`](@ref)によるものも含む）は、コードのリロードやキャッシュの無効化は実行されず、
    [`Pkg.rm`](@ref)の後にはクリーンアップは行われません。
    2.再構成された配列のメモリ共有動作は、プリコンパイルでは無視されます（各ビューは独自のコピーを取得します）
    3.ファイルシステムがコンパイル時と実行時の間で変更されないことを前提としているもの注意する（例：[`@__FILE__`](@ref)/`source_path()`や`BinDeps @checked_lib`マクロ。)
    これは避けられないこともありますが、可能であれば、コンパイル時にリソースをモジュールにコピーして実行時にリソースを見つける必要をなくすのはよい作法です。
    4.`WeakRef` オブジェクトとファイナライザは現在、シリアライザによって正しく処理されていません（これは、今後のリリースで修正される予定です）。
    5.次のような内部のメタデータ・オブジェクトのインスタンスへの参照を取得するのは避けるのが最善です。
    例えば`Method`、`MethodInstance`、`MethodTable`、`TypeMapLevel`、`TypeMapEntry`やこれらのオブジェクトのフィールドです。
    これはシリアライザが混同する可能性がありますし、あなたが望む出力にならない可能性があります。
    これを行うことは必ずしも間違いではありませんが、システムがこれらのいくつかをコピーし、他の一意のインスタンスを作成するように準備すればいいだけです。



```@raw html
<!--
It is sometimes helpful during module development to turn off incremental precompilation. The
command line flag `--compilecache={yes|no}` enables you to toggle module precompilation on and
off. When Julia is started with `--compilecache=no` the serialized modules in the compile cache
are ignored when loading modules and module dependencies. `Base.compilecache()` can still be called
manually and it will respect `__precompile__()` directives for the module. The state of this command
line flag is passed to [`Pkg.build()`](@ref) to disable automatic precompilation triggering when installing,
updating, and explicitly building packages.
-->
```
逐次プリコンパイルをオフにすることは、モジュール開発中に役立つことがあります。
コマンドラインフラグの`--compilecache={yes|no}`を使用すると、モジュールのプリコンパイルのオンとオフを切り替えることができます。
`--compilecache=no`でJuliaを起動すると、モジュールやモジュールの依存関係をロードするときに、コンパイル時のキャッシュで直列化されているモジュールが無視されます。
`Base.compilecache()`依然として手動で呼び出すことができ、モジュールの`__precompile__()`ディレクティブを尊重します。
このコマンドラインフラグの状態は[`Pkg.build()`](@ref)に渡され、パッケージをインストール、更新、および明示的にビルドするときに、自動プリコンパイルのトリガーが無効になります。