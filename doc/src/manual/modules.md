[](# [Modules](@id modules))

# [モジュール](@id modules)

```@raw html
<!--
Modules in Julia are separate variable workspaces, i.e. they introduce a new global scope. They
are delimited syntactically, inside `module Name ... end`. Modules allow you to create top-level
definitions (aka global variables) without worrying about name conflicts when your code is used
together with somebody else's. Within a module, you can control which names from other modules
are visible (via importing), and specify which of your names are intended to be public (via exporting).
-->
```

Juliaにおけるモジュールは別々の変数ワークスペースです。つまり、新しいグローバルスコープを導入します。モジュールは構文的には`module Name ... end`の内側として区切られます。モジュールは、自分のコードが誰かのコードと一緒に使われるときに、名前の競合を心配することなく、トップレベルの定義（別名グローバル変数）を作成することを可能にします。モジュール内では、他のモジュールのどの名前を（インポートによって）参照できるか制御したり、名前を公開することを（エクスポートを介して）指定することができます。

```@raw html
<!--
The following example demonstrates the major features of modules. It is not meant to be run, but
is shown for illustrative purposes:
-->
```

次の例はモジュールの主な機能を説明しています。実行できるわけではありませんが、理解しやすくするために載せています。

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
-->
```

モジュールの中身はインデントしていないことに注意してください。これは、通常ファイル全体がインデントされるためです。

```@raw html
<!--
This module defines a type `MyType`, and two functions. Function `foo` and type `MyType` are exported,
and so will be available for importing into other modules.  Function `bar` is private to `MyModule`.
-->
```

このモジュールは型`MyType`と２つの関数を定義しています。関数`foo`と型`MyType`はエクスポートされているため、他のモジュールの中にインポートすることができます。関数`bar`は`MyModule`専用です。

```@raw html
<!--
The statement `using Lib` means that a module called `Lib` will be available for resolving names
as needed. When a global variable is encountered that has no definition in the current module,
the system will search for it among variables exported by `Lib` and import it if it is found there.
This means that all uses of that global within the current module will resolve to the definition
of that variable in `Lib`.
-->
```

文`using Lib`が意味するのは、必要に応じて名前を解決するために`Lib`と呼ばれるモジュールを利用できるということです。現在のモジュールに定義されていないグローバル変数が見つかった場合、システムは`Lib`によってエクスポートされた変数の中から変数を検索し、そこに見つかった場合はインポートします。 つまり、現在のモジュール内のそのグローバルのすべての使用は、`Lib`内のその変数の定義に解決されます。

```@raw html
<!--
The statement `using BigLib: thing1, thing2` is a syntactic shortcut for `using BigLib.thing1, BigLib.thing2`.
-->
```

文`using BigLib: thing1, thing2`は`using BigLib.thing1, BigLib.thing2`の構文上の省略形です。

```@raw html
<!--
The `import` keyword supports all the same syntax as `using`, but only operates on a single name
at a time. It does not add modules to be searched the way `using` does. `import` also differs
from `using` in that functions must be imported using `import` to be extended with new methods.
-->
```

`import`キーワードは、`using`と同じ構文をサポートしますが、一度に１つの名前でしか動作しません。`using`とは違って、`import`は検索されるモジュールには追加しません。関数を新しいメソッドで拡張するために`import`を使ってインポートする必要がある点でも、 `import`は`using`とは異なります。

```@raw html
<!--
In `MyModule` above we wanted to add a method to the standard `show` function, so we had to write
`import Base.show`. Functions whose names are only visible via `using` cannot be extended.
-->
```

上記の`MyModule`では、標準の`show`関数にメソッドを追加したいので、`import Base.show`と書く必要がありました。`using`を使わないと名前を参照できない関数は、拡張できませんでした。

```@raw html
<!--
The keyword `importall` explicitly imports all names exported by the specified module, as if
`import` were individually used on all of them.
->
```

キーワード`importall`は指定したモジュールでエクスポートされた全ての名前を、それぞれ`import`が使われているかのように明示的にインポートします。

```@raw html
<!--
Once a variable is made visible via `using` or `import`, a module may not create its own variable
with the same name. Imported variables are read-only; assigning to a global variable always affects
a variable owned by the current module, or else raises an error.
-->
```

変数を`using`または`import`を通して表示すると、モジュールはその中に同じ名前の変数を作成しないことがあります。 インポートされた変数は読み取り専用です。 グローバル変数に割り当てることは、常に現在のモジュールが所有する変数に影響します。そうでない場合は、エラーが発生します。

[](## Summary of module usage)

## モジュールの使い方の概要

```@raw html
<!--
To load a module, two main keywords can be used: `using` and `import`. To understand their differences,
consider the following example:
-->
```

モジュールを読み込むには、主に２つのキーワードが使われます。`using`と`import`です。２つの違いを理解するために、次の例を考えましょう。

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

このモジュールでは、関数`x`と関数`y`は（キーワード`export`を使って）エクスポートされていて、関数`p`はエクスポートされていません。モジュールとその内部の関数を現在の作業領域に読み込むにはいくつかの方法があります。

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

| 読み込みコマンド                        | スコープにもってこられるもの                           | メソッド拡張で利用可能なもの                           |
| ------------------------------- | ---------------------------------------- | ---------------------------------------- |
| `using MyModule`                | `export`された全ての名前（`x`と`y`）と`MyModule.x`, `MyModule.p` | `MyModule.x`, `MyModule.y`, `MyModule.p` |
| `using MyModule.x, MyModule.p`  | `x`と`p`                                  |                                          |
| `using MyModule: x, p`          | `x`と`p`                                  |                                          |
| `import MyModule`               | `MyModule.x`, `MyModule.y`, `MyModule.p` | `MyModule.x`, `MyModule.y`, `MyModule.p` |
| `import MyModule.x, MyModule.y` | `x`と`p`                                  | `x`と`p`                                  |
| `import MyModule: x, p`         | `x`と`p`                                  | `x`と`p`                                  |
| `importall MyModule`            | `export`された全ての名前（`x`と`y`）                | `x`と`y`                                  |

[](### Modules and files)

### モジュールとファイル

```@raw html
<!--
Files and file names are mostly unrelated to modules; modules are associated only with module
expressions. One can have multiple files per module, and multiple modules per file:
-->
```

ファイルとファイル名はモジュールとはほとんど関係がありません。モジュールはモジュールの表現とだけ関連付けられます。１つのモジュールが複数のファイルに分かれていても良いし、１つのファイルに複数のモジュールが含まれていてもよいです。

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

同じコードを別のモジュールに含めると、mixinのような動作をさせることができます。これを使って異なるベース定義を持つ同じコードを走らせることができます。例えば、ある演算子の「安全な」バージョンでコードをテストすることができます。

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
-->
```

３つの重要な標準モジュールがあります。MainとCoreとBaseです。

```@raw html
<!--
Main is the top-level module, and Julia starts with Main set as the current module.  Variables
defined at the prompt go in Main, and `whos()` lists variables in Main.
-->
```

Mainはトップレベルのモジュールで、JuliaはMainを現在のモジュールとしてセットした状態で起動します。プロンプトで定義された変数はMainに登録され、`whos()`はMainの中にある変数をリストアップします。

```@raw html
<!--
Core contains all identifiers considered "built in" to the language, i.e. part of the core language
and not libraries. Every module implicitly specifies `using Core`, since you can't do anything
without those definitions.
-->
```

Coreは言語に「組み込まれている」とみなされる全ての識別子を持っています。つまり、コア言語の一部でありライブラリではありません。全てのモジュールは暗黙的に`using Core`が指定されています。そうでなければCoreの定義がないため、何もできなくなってしまうからです。

```@raw html
<!--
Base is the standard library (the contents of base/). All modules implicitly contain `using Base`,
since this is needed in the vast majority of cases.
-->
```

Baseは標準ライブラリ（base/の内容）です。全てのモジュールで大抵Baseを必要とするため、全てのモジュールは暗黙的に`using Base`を含むようになっています。

[](### Default top-level definitions and bare modules)

### デフォルトトップレベル定義と裸モジュール

```@raw html
<!--
In addition to `using Base`, modules also automatically contain a definition of the `eval` function,
which evaluates expressions within the context of that module.
-->
```

`using Base`に加えて、モジュールは自動的に`eval`関数の定義を含むようにもなっています。`eval`関数はそのモジュールの中身の表現を評価します。

```@raw html
<!--
If these default definitions are not wanted, modules can be defined using the keyword `baremodule`
instead (note: `Core` is still imported, as per above). In terms of `baremodule`, a standard
`module` looks like this:
-->
```

もし、これらのデフォルト定義が必要ない場合は、代わりにキーワード`baremodule`を使うことによってモジュールを定義することができます（注意：上記のように、この場合でも`Core`は読み込まれます）。`baremodule`では標準の`module`はこのように見えます。

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
-->
```

`using Foo`という文が与えられると、システムはトップレベルモジュールの内部テーブルを参照して`Foo`という名前のテーブルを探します。 モジュールが存在しない場合、システムは`require(:Foo)`を試みます。これは通常、インストールされたパッケージからコードをロードします。

```@raw html
<!--
However, some modules contain submodules, which means you sometimes need to access a module that
is not directly available in `Main`. There are two ways to do this. The first is to use an absolute
path, for example `using Base.Sort`. The second is to use a relative path, which makes it easier
to import submodules of the current module or any of its enclosing modules:
-->
```

ただし、一部のモジュールにはサブモジュールが含まれているため、トップレベルではないモジュールにアクセスする必要があることがあります。 これを行うには２つの方法があります。 １つ目は、絶対パスを使用することです（例えば`using Base.Sort`など）。２つ目は相対パスを使用することです。相対パスを使用することにより、現在のモジュールのサブモジュールやサブモジュールを含んでいる任意のモジュールを簡単にインポートできます。

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
-->
```

ここで、モジュール`Parent`はサブモジュール`Utils`を含み、`Parent`中のコードは`Utils`の中身を参照したいとします。これは`using`のパスをピリオド付きで始めることで成されます。もう１つピリオドを加えると、モジュール階層のもう１段階上のレベルに移動します。例えば、`using ..Utils`とすると、`Parent`自体ではなく、`Parent`を囲むモジュールで`Utils`を探します。

```@raw html
<!--
Note that relative-import qualifiers are only valid in `using` and `import` statements.
-->
```

相対インポート修飾子は、`using`文と`import`文でのみ有効であることに注意してください。

[](### Module file paths)

### モジュールファイルパス

```@raw html
<!--
The global variable [`LOAD_PATH`](@ref) contains the directories Julia searches for modules when calling
`require`. It can be extended using [`push!`](@ref):
-->
```

グローバル変数[`LOAD_PATH`](@ref)には、`require`を呼び出したときにJuliaがモジュールを検索するディレクトリが含まれています。この変数は[`push!`](@ref)を使って拡張できます。

```julia
push!(LOAD_PATH, "/Path/To/My/Module/")
```

```@raw html
<!--
Putting this statement in the file `~/.juliarc.jl` will extend [`LOAD_PATH`](@ref) on every Julia startup.
Alternatively, the module load path can be extended by defining the environment variable `JULIA_LOAD_PATH`.
-->
```

この文を`~/.juliarc.jl`ファイルに入れると、Juliaの起動時に毎回[`LOAD_PATH`](@ref)が拡張されます。 この他に、環境変数`JULIA_LOAD_PATH`を定義してモジュールの読み込みパスを拡張することもできます。

[](### Namespace miscellanea)

### 名前空間雑録

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

名前が修飾されている場合（例えば`Base.sin`など）、エクスポートされていなくてもアクセスできます。
これは、デバッグ時に便利です。 また、修飾名を関数名として使用してメソッドを追加することもできます。
しかし、構文上の曖昧さが原因で、名前にシンボルのみが含まれている
別のモジュール（たとえば、`Base.+`など）の関数にメソッドを追加する場合は、
`Base.:+`を使用して参照する必要があります。
演算子が２文字以上からなる場合、`Base.:(==)`のように括弧で囲む必要があります。

```@raw html
<!--
Macro names are written with `@` in import and export statements, e.g. `import Mod.@mac`. Macros
in other modules can be invoked as `Mod.@mac` or `@Mod.mac`.
-->
```

マクロ名は、インポート文およびエクスポート文中では`import Mod.@mac`のように`@`を付けて書かれます。
他のモジュールのマクロは、`Mod.@mac`または`@Mod.mac`として呼び出すことができます。

```@raw html
<!--
The syntax `M.x = y` does not work to assign a global in another module; global assignment is
always module-local.
-->
```

構文`M.x = y`は、別のモジュールでグローバルを割り当てるのには機能しません。
グローバル割り当ては常にモジュールローカルです。

```@raw html
<!--
A variable can be "reserved" for the current module without assigning to it by declaring it as
`global x` at the top level. This can be used to prevent name conflicts for globals initialized
after load time.
-->
```

変数名はトップレベルで`global x`と宣言して割り当てずに、現在のモジュールで「予約」することができます。
これにより、読み込み時間に初期化されたグローバル変数の名前の競合を防止することができます。

### Module initialization and precompilation

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

Precompiling a module also recursively precompiles any modules that are imported therein. If you
know that it is *not* safe to precompile your module (for the reasons described below), you should
put `__precompile__(false)` in the module file to cause `Base.compilecache` to throw an error
(and thereby prevent the module from being imported by any other precompiled module).

`__precompile__()` should *not* be used in a module unless all of its dependencies are also using
`__precompile__()`. Failure to do so can result in a runtime error when loading the module.

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

In particular, if you define a `function __init__()` in a module, then Julia will call `__init__()`
immediately *after* the module is loaded (e.g., by `import`, `using`, or `require`) at runtime
for the *first* time (i.e., `__init__` is only called once, and only after all statements in the
module have been executed). Because it is called after the module is fully imported, any submodules
or other imported modules have their `__init__` functions called *before* the `__init__` of the
enclosing module.

Two typical uses of `__init__` are calling runtime initialization functions of external C libraries
and initializing global constants that involve pointers returned by external libraries.  For example,
suppose that we are calling a C library `libfoo` that requires us to call a `foo_init()` initialization
function at runtime. Suppose that we also want to define a global constant `foo_data_ptr` that
holds the return value of a `void *foo_data()` function defined by `libfoo` -- this constant must
be initialized at runtime (not at compile time) because the pointer address will change from run
to run.  You could accomplish this by defining the following `__init__` function in your module:

```julia
const foo_data_ptr = Ref{Ptr{Void}}(0)
function __init__()
    ccall((:foo_init, :libfoo), Void, ())
    foo_data_ptr[] = ccall((:foo_data, :libfoo), Ptr{Void}, ())
end
```

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

When using precompilation, it is important to keep a clear sense of the distinction between the
compilation phase and the execution phase. In this mode, it will often be much more clearly apparent
that Julia is a compiler which allows execution of arbitrary Julia code, not a standalone interpreter
that also generates compiled code.

Other known potential failure scenarios include:

1. Global counters (for example, for attempting to uniquely identify objects) Consider the following
   code snippet:

   ```julia
   mutable struct UniquedById
       myid::Int
       let counter = 0
           UniquedById() = new(counter += 1)
       end
   end
   ```

   while the intent of this code was to give every instance a unique id, the counter value is recorded
   at the end of compilation. All subsequent usages of this incrementally compiled module will start
   from that same counter value.

   Note that `object_id` (which works by hashing the memory pointer) has similar issues (see notes
   on `Dict` usage below).

   One alternative is to store both [`current_module()`](@ref) and the current `counter` value, however,
   it may be better to redesign the code to not depend on this global state.
2. Associative collections (such as `Dict` and `Set`) need to be re-hashed in `__init__`. (In the
   future, a mechanism may be provided to register an initializer function.)
3. Depending on compile-time side-effects persisting through load-time. Example include: modifying
   arrays or other variables in other Julia modules; maintaining handles to open files or devices;
   storing pointers to other system resources (including memory);
4. Creating accidental "copies" of global state from another module, by referencing it directly instead
   of via its lookup path. For example, (in global scope):

   ```julia
   #mystdout = Base.STDOUT #= will not work correctly, since this will copy Base.STDOUT into this module =#
   # instead use accessor functions:
   getstdout() = Base.STDOUT #= best option =#
   # or move the assignment into the runtime:
   __init__() = global mystdout = Base.STDOUT #= also works =#
   ```

Several additional restrictions are placed on the operations that can be done while precompiling
code to help the user avoid other wrong-behavior situations:

1. Calling [`eval`](@ref) to cause a side-effect in another module. This will also cause a warning to be
   emitted when the incremental precompile flag is set.
2. `global const` statements from local scope after `__init__()` has been started (see issue #12010
   for plans to add an error for this)
3. Replacing a module (or calling [`workspace()`](@ref)) is a runtime error while doing an incremental precompile.

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

It is sometimes helpful during module development to turn off incremental precompilation. The
command line flag `--compilecache={yes|no}` enables you to toggle module precompilation on and
off. When Julia is started with `--compilecache=no` the serialized modules in the compile cache
are ignored when loading modules and module dependencies. `Base.compilecache()` can still be called
manually and it will respect `__precompile__()` directives for the module. The state of this command
line flag is passed to [`Pkg.build()`](@ref) to disable automatic precompilation triggering when installing,
updating, and explicitly building packages.
