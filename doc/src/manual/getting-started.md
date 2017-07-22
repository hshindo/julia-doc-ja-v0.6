[](# Getting Started)
# Juliaをはじめる

```@raw html
<!--
Julia installation is straightforward, whether using precompiled binaries or compiling from source.
Download and install Julia by following the instructions at [https://julialang.org/downloads/](https://julialang.org/downloads/).
-->
```

Juliaのインストール作業は、インストール済みのバイナリを使用する、
またはソースからコンパイルする、のどちらの手順を実施しても複雑ではありません。
以下リンク先の手順に従ってJuliaをダウンロードしてインストールしてください。
[<http://julialang.org/downloads/>](http://julialang.org/downloads/).

```@raw html
<!--
The easiest way to learn and experiment with Julia is by starting an interactive session (also
known as a read-eval-print loop or "repl") by double-clicking the Julia executable or running
`julia` from the command line:
-->
```
Juliaの使用法を学んだりJuliaを試すには、Juliaの実行ファイルをダブルクリックするか、
以下のようにコマンドラインから `julia`
を起動して対話型セッション（read-eval-print-loopまたは「repl」）を
開始することが一番の近道です。:

```
$ julia
               _
   _       _ _(_)_     |  A fresh approach to technical computing
  (_)     | (_) (_)    |  Documentation: https://docs.julialang.org
   _ _   _| |_  __ _   |  Type "?help" for help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 0.5.0-dev+2440 (2016-02-01 02:22 UTC)
 _/ |\__'_|_|_|\__'_|  |  Commit 2bb94d6 (11 days old master)
|__/                   |  x86_64-apple-darwin13.1.0

julia> 1 + 2
3

julia> ans
3
```
```@raw html
<!--
To exit the interactive session, type `^D` -- the control key together with the `d` key or type
`quit()`. When run in interactive mode, `julia` displays a banner and prompts the user for input.
Once the user has entered a complete expression, such as `1 + 2`, and hits enter, the interactive
session evaluates the expression and shows its value. If an expression is entered into an interactive
session with a trailing semicolon, its value is not shown. The variable `ans` is bound to the
value of the last evaluated expression whether it is shown or not. The `ans` variable is only
bound in interactive sessions, not when Julia code is run in other ways.
-->
```
対話型セッションは、 `^D`
（コントロールキーと「D」を同時押し）と入力するか、 `quit()`
と入力することで 終了することができます。 `julia`
を対話モードで実行した場合、バナーおよびユーザに入力を求めるプロンプトが表示されます。
ユーザが  `1 + 2`
などの完全な式を入力しエンターキーを押すと、対話型セッションは入力された式を判別し、
その結果の値を出力します。もし式の末尾にセミコロンを付けて入力した場合は、結果の値は表示されません。
`ans` 変数は、
最後に判別された式の値が表示されているか否かに紐付きます。 `ans`
変数は、対話型セッションだけでなく、
他の方法でJuliaが実行された場合にも紐付けがされます。

```@raw html
<!--
To evaluate expressions written in a source file `file.jl`, write `include("file.jl")`.
-->
```
ソースファイル `file.jl` に記載された式を読み込ませるためには、
`include("file.jl")` と入力してください。

```@raw html
<!--
To run code in a file non-interactively, you can give it as the first argument to the `julia`
command:
-->
```
ファイルに記載されたコードを非対話的に実行する場合は、以下のようにファイル名を
`julia` コマンドの 第一引数として与えることができます。:

```
$ julia script.jl arg1 arg2...
```
```@raw html
<!--
As the example implies, the following command-line arguments to `julia` are taken as command-line
arguments to the program `script.jl`, passed in the global constant `ARGS`. The name of the script
itself is passed in as the global `PROGRAM_FILE`. Note that `ARGS` is also set when script code
is given using the `-e` option on the command line (see the `julia` help output below) but `PROGRAM_FILE`
will be empty. For example, to just print the arguments given to a script, you could do this:
-->
```
記載例のように、 `julia` の後に続くコマンドラインの引数は、 `script.jl`
内のプログラムの引数として解釈され、 グローバル定数 `ARGS`
に渡されます。スクリプト自体の名前は、 `PROGRAM_FILE` に渡されます。
`ARGS` も `-e`
オプションを使用したコマンドラインにてセットすることができますが、
`PROGRAM_FILE` は 空白となりますので注意してください（以下 `julia`
のヘルプを参照）。
例として、スクリプトの引数を出力したい場合、以下のコマンドで実行することができます。:

```
$ julia -e 'println(PROGRAM_FILE); for x in ARGS; println(x); end' foo bar

foo
bar
```
```@raw html
<!--
Or you could put that code into a script and run it:
-->
```
もしくは以下のようにコードをスクリプトに組み込んで実行することもできます。:

```
$ echo 'println(PROGRAM_FILE); for x in ARGS; println(x); end' > script.jl
$ julia script.jl foo bar
script.jl
foo
bar
```
```@raw html
<!--
The `--` delimiter can be used to separate command-line args to the scriptfile from args to Julia:
-->
```
区切り文字 `--`
は、コマンドライン内のスクリプトファイルに対する引数と、Juliaに対する引き数を区別させるために使用できます。:

```
$ julia --color=yes -O -- foo.jl arg1 arg2..
```

```@raw html
<!--
Julia can be started in parallel mode with either the `-p` or the `--machinefile` options. `-p n`
will launch an additional `n` worker processes, while `--machinefile file` will launch a worker
for each line in file `file`. The machines defined in `file` must be accessible via a passwordless
`ssh` login, with Julia installed at the same location as the current host. Each machine definition
takes the form `[count*][user@]host[:port] [bind_addr[:port]]` . `user` defaults to current user,
`port` to the standard ssh port. `count` is the number of workers to spawn on the node, and defaults
to 1. The optional `bind-to bind_addr[:port]` specifies the ip-address and port that other workers
should use to connect to this worker.
-->
```
Juliaは `-p` または `--machinefile`
オプションにより並列モードとして起動することができます。 `-p n` は `n`
個の 追加のワーカープロセスを起動し、 `--machinefile file` は `file`
に記載されたそれぞれ一行に1つのワーカーを 起動します。 `file`
に定義されたマシンはパスワード不要の `ssh` 接続が可能である必要があり、
またJuliaがインストールされた使用しているホストと同じ場所である必要があります。
それぞれのマシンの定義の方法は、
`[count*][user@]host[:port] [bind_addr[:port]]` となります。 `user` の
デフォルトはカレントユーザ、 `port` は標準sshポートとなります。 `count`
はノードに起動するワーカーの数であり、 デフォルトは1です。オプショナルの
`bind-to bind_addr[:port]`
は他のワーカーが使用してるワーカーに接続する際に
必要なIPアドレスとポートを指定します。

```@raw html
<!--
If you have code that you want executed whenever Julia is run, you can put it in `~/.juliarc.jl`:
-->
```
Juliaが実行されるたびに実行したいコードがある場合は、 `~/.juliarc.jl`
にそのコードを記載することが可能です。:

```
$ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.juliarc.jl
$ julia
Greetings! 你好! 안녕하세요?

...
```
```@raw html
<!--
There are various ways to run Julia code and provide options, similar to those available for the
`perl` and `ruby` programs:
-->
```
`perl` や `ruby`
と同様に、Juliaコードの実行やオプションを与えるための様々な方法があります。:

```
julia [switches] -- [programfile] [args...]
 -v, --version             Display version information
 -h, --help                Print this message

 -J, --sysimage <file>     Start up with the given system image file
 --precompiled={yes|no}    Use precompiled code from system image if available
 --compilecache={yes|no}   Enable/disable incremental precompilation of modules
 -H, --home <dir>          Set location of `julia` executable
 --startup-file={yes|no}   Load ~/.juliarc.jl
 --handle-signals={yes|no} Enable or disable Julia's default signal handlers

 -e, --eval <expr>         Evaluate <expr>
 -E, --print <expr>        Evaluate and show <expr>
 -L, --load <file>         Load <file> immediately on all processors

 -p, --procs {N|auto}      Integer value N launches N additional local worker processes
                           "auto" launches as many workers as the number of local cores
 --machinefile <file>      Run processes on hosts listed in <file>

 -i                        Interactive mode; REPL runs and isinteractive() is true
 -q, --quiet               Quiet startup (no banner)
 --color={yes|no}          Enable or disable color text
 --history-file={yes|no}   Load or save history

 --compile={yes|no|all|min}Enable or disable JIT compiler, or request exhaustive compilation
 -C, --cpu-target <target> Limit usage of cpu features up to <target>
 -O, --optimize={0,1,2,3}  Set the optimization level (default is 2 if unspecified or 3 if specified as -O)
 -g, -g <level>            Enable / Set the level of debug info generation (default is 1 if unspecified or 2 if specified as -g)
 --inline={yes|no}         Control whether inlining is permitted (overrides functions declared as @inline)
 --check-bounds={yes|no}   Emit bounds checks always or never (ignoring declarations)
 --math-mode={ieee,fast}   Disallow or enable unsafe floating point optimizations (overrides @fastmath declaration)

 --depwarn={yes|no|error}  Enable or disable syntax and method deprecation warnings ("error" turns warnings into errors)

 --output-o name           Generate an object file (including system image data)
 --output-ji name          Generate a system image data file (.ji)
 --output-bc name          Generate LLVM bitcode (.bc)
 --output-incremental=no   Generate an incremental output file (rather than complete)

 --code-coverage={none|user|all}, --code-coverage
                           Count executions of source lines (omitting setting is equivalent to "user")
 --track-allocation={none|user|all}, --track-allocation
                           Count bytes allocated by each source line
```
[](## Resources)
## 関連資料
```@raw html
<!--
In addition to this manual, there are various other resources that may help new users get started
with Julia:
-->
```
当マニュアルに加えて、Juliaを開始するにあたり参考となる様々な資料があります。

  * [Julia and IJulia cheatsheet](http://math.mit.edu/~stevenj/Julia-cheatsheet.pdf)
  * [Learn Julia in a few minutes](https://learnxinyminutes.com/docs/julia/)
  * [Learn Julia the Hard Way](https://github.com/chrisvoncsefalvay/learn-julia-the-hard-way)
  * [Julia by Example](http://samuelcolvin.github.io/JuliaByExample/)
  * [Hands-on Julia](https://github.com/dpsanders/hands_on_julia)
  * [Tutorial for Homer Reid's numerical analysis class](http://homerreid.dyndns.org/teaching/18.330/JuliaProgramming.shtml)
  * [An introductory presentation](https://raw.githubusercontent.com/ViralBShah/julia-presentations/master/Fifth-Elephant-2013/Fifth-Elephant-2013.pdf)
  * [Videos from the Julia tutorial at MIT](https://julialang.org/blog/2013/03/julia-tutorial-MIT)
  * [YouTube videos from the JuliaCons](https://www.youtube.com/user/JuliaLanguage/playlists)
