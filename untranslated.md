# variables

Some Unicode characters are considered to be equivalent in identifiers.
Different ways of entering Unicode combining characters (e.g., accents)
are treated as equivalent (specifically, Julia identifiers are NFC-normalized).
The Unicode characters `ɛ` (U+025B: Latin small letter open e)
and `µ` (U+00B5: micro sign) are treated as equivalent to the corresponding
Greek letters, because the former are easily accessible via some input methods.

For more information about stylistic conventions, see the [Style Guide](@ref).


# Mathematical Operations and Elementary Functions
## [Vectorized "dot" operators](@id man-dot-operators)

For *every* binary operation like `^`, there is a corresponding
"dot" operation `.^` that is *automatically* defined
to perform `^` element-by-element on arrays. For example,
`[1,2,3] ^ 3` is not defined, since there is no standard
mathematical meaning to "cubing" an array, but `[1,2,3] .^ 3`
is defined as computing the elementwise
(or "vectorized") result `[1^3, 2^3, 3^3]`.  Similarly for unary
operators like `!` or `√`, there is a corresponding `.√` that
applies the operator elementwise.

```jldoctest
julia> [1,2,3] .^ 3
3-element Array{Int64,1}:
  1
  8
 27
```

More specifically, `a .^ b` is parsed as the ["dot" call](@ref man-vectorized)
`(^).(a,b)`, which performs a [broadcast](@ref Broadcasting) operation:
it can combine arrays and scalars, arrays of the same size (performing
the operation elementwise), and even arrays of different shapes (e.g.
combining row and column vectors to produce a matrix). Moreover, like
all vectorized "dot calls," these "dot operators" are
*fusing*. For example, if you compute `2 .* A.^2 .+ sin.(A)` (or
equivalently `@. 2A^2 + sin(A)`, using the [`@.`](@ref @__dot__) macro) for
an array `A`, it performs a *single* loop over `A`, computing `2a^2 + sin(a)`
for each element of `A`. In particular, nested dot calls like `f.(g.(x))`
are fused, and "adjacent" binary operators like `x .+ 3 .* x.^2` are
equivalent to nested dot calls `(+).(x, (*).(3, (^).(x, 2)))`.

Furthermore, "dotted" updating operators like `a .+= b` (or `@. a += b`) are parsed
as `a .= a .+ b`, where `.=` is a fused *in-place* assignment operation
(see the [dot syntax documentation](@ref man-vectorized)).

Note the dot syntax is also applicable to user-defined operators.
For example, if you define `⊗(A,B) = kron(A,B)` to give a convenient
infix syntax `A ⊗ B` for Kronecker products ([`kron`](@ref)), then
`[A,B] .⊗ [C,D]` will compute `[A⊗C, B⊗D]` with no additional coding.

### Elementary Functions
Moreover, these functions (like any Julia function) can be applied in "vectorized" fashion to
arrays and other collections with the [dot syntax](@ref man-vectorized) `f.(A)`,
e.g. `sin.(A)` will compute the sine of each element of an array `A`.

### Operator Precedence
For a complete list of *every* Julia operator's precedence, see the top of this file:
[`src/julia-parser.scm`](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm)

You can also find the numerical precedence for any given operator via the built-in function `Base.operator_precedence`, where higher numbers take precedence:

