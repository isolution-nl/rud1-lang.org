# Good, better, best

> As we all know, *better* is the enemy of *good*. We want to be good, and better is in the eye
> of the beholder. (The author.)

rud1 has a personality, and is opinionated. This file lists where its choices depart from what
most languages do, from what a newcomer meets first to the specialised, with the reasons and
the price, so that the reader can judge what "better'' is. Each section gives the rule, an example
that was run, the reason as the spec or the project's history gives it (none where none is
written), and the status: **built** (the prototype does it today), **decided** (designed,
waits for a build) or **proposed** (not confirmed by the author).

Examples run as `rud1 'code'` or `rud1 file.rud`, a shell function around the executable,
which is named `rud1_run`; files end in `.rud`. `\\` starts a comment, so
`[rud1] code \\> result` is a program and what it printed. Positions in messages are
`line:column`. Contrasts were run with Perl 5.38.2, Python 3.12.3 and gp 2.15.4, and again in generation 54
with Perl 5.44.0, Python 3.15.0 and gp 2.19.0: no result differed. In generation 63 the others were run:
Apple clang 21 (C++20), rustc 1.99.1, OpenJDK 27.0.1, GHC 9.14.1, Go 1.27.3, Julia 1.13.3, Rakudo
2026.09 (Raku), Ruby 2.6.10, Lua 5.5.1, Zig 0.17.0, Swift 6.4, Elixir 1.20.4, Scala 3.10.0 (scala-cli 1.18.0) and, for WASI, wasmtime 50.0.0.
A language not run is not named.

# At a glance

One row a section: what rud1 chose, where others choose alike, and where they don't. "The rest"
means C++, Rust, Java, Perl, Python and gp.

| § | rud1 | Alike | Unlike | Status |
|---|---|---|---|---|
| 1 | the last value is shown unless the text ends in `;` | gp | the rest | built |
| 2 | `a= 1`, leading commas, the constant first; `rud1_fmt` | gofmt, rustfmt | a free layout | built |
| 3 | immutable by default, `mut my(…)` | Rust, Haskell | C++, Java, Perl, Python, gp | built |
| 4 | undeclared names, call counts and argument names are compile errors | C++, Rust, Java; Perl's `strict` for names | Python, gp | built |
| 5 | whole numbers of any size, exact rationals; no float that rounds | gp, Haskell, Raku; Python for whole numbers; Julia for rationals | C++, Rust, Java, Perl, Julia (`Int` wraps) | built |
| 6 | `3 - 5` of `uint`s is `underflow`, not a wrap | Rust in a debug build (a panic) | C++ (wraps), Java and Python (no unsigned type) | built |
| 7 | Euclidean `//` and `%` | gp, Rust's `rem_euclid` | C++, Java, Rust (truncate), Python, Raku, Lua (floor), Perl (both) | built |
| 8 | `0177` is an error, octal `\o{177}`, hex `\x{7F}` | Python (refuses `0177`) | C++, Java, Perl (127), gp, Rust (177) | built |
| 9 | a strict `bool`: no arithmetic, the only condition | Rust, Java | C++, Perl, Python, gp | built |
| 10 | `==` across families an `error`, `===` identity | gp's `===`; Rust (a type error) | Python, Perl, Java | built |
| 11 | comparisons chain in one direction | Python; Perl, Raku, Julia (both directions) | C++, Rust, Java, Haskell, gp | built |
| 12 | errors are states of a value; `guard`, no exceptions | Rust's `Result`, Go | C++, Java, Perl, Python, gp | built |
| 13 | from 0; a literal `-1` from the end, a computed one `underflow` | Python, Perl (literal) | gp (from 1), Python and Perl (computed) | built |
| 14 | ranges hold both ends, a step after `;`, splice | gp, Perl, Raku | Python, Rust (half-open) | built |
| 15 | lazy lists, endless ones | Haskell, Raku | gp, Perl, Python lists | built |
| 16 | `_+_` is `+` as a value, sealed | Haskell `(+)`, Raku | the rest | built |
| 17 | placeholders `_1`, `_2` | Scala `_`, Elixir `&1` | Python, Perl, gp | built |
| 18 | a rest anywhere, spreads, every parameter by name | Ruby, Python | C++, Rust, Java, gp | built |
| 19 | `defer`, which gets the block's result | Go, Swift, Zig | C++, Rust (RAII), Java, Python (`finally`) | built |
| 20 | long names are the names; short ones by `alias` | — | Perl, gp | built |
| 21 | a `str` is characters (extended grapheme clusters): `#s`, `s[i]` and `s[a..b]` by character; `bytes` apart | Swift 6.4 (`s.count`; no `s[i]` by number) | Python (code points), C++, Perl, gp (bytes), Java (UTF-16), Rust (no `s[i]`, slices by byte) | built |
| 22 | full Unicode case mapping, Final_Sigma, no locale | Python, Rust | Perl, Java (locale), C++ | built |
| 23 | `\x{89}`, braces required | Perl's `\x{…}` | C++, Java, Python | built |
| 24 | reading changes nothing: CR, LF, BOM kept | Perl on Unix | Python | built |
| 25 | a template `t{…}` is a checked literal | — | Perl's `pack` and regexes | built |
| 26 | `unpack` never tries again | PEG parsers | Perl's regexes | built |
| 27 | no silent limits | — | everyone | decided |
| 28 | every message has an id; `--explain` | `rustc --explain` | the rest | built |
| 29 | types settled when compiled, `auto` specialized per call | C++ templates, Julia | Python, Perl, gp | decided |
| 30 | sub-types: a refinement, a nominal type, an extension | Haskell's `newtype` | Java (inheritance) | decided; refinements built |
| 31 | floats of infinite precision; a type picks its overflow | Rust's `wrapping_*` | C++, Rust, Java, Perl, Python | decided |
| 32 | a type's number of values; array or map by the index type | Lua (one table) | the rest | decided |
| 33 | `apx`: an approximation as a state | — | the rest | decided |
| 34 | `pending`: `async` as dataflow | — | async/await | decided |
| 35 | capabilities as bindings | WASI | the rest | decided |
| 36 | modules accepted by a proof of the importer's spec | — | the rest | decided |
| 37 | regions and taint, inferred when compiled | Perl's `-T` (input only) | the rest | decided |
| 38 | `sym` parameters, quoting; no `eval`, no macros | Zig's `comptime` | Perl, Python (`eval`), Rust (macros) | decided |
| 39 | pure parallelism, a fixed reduction order, no locks | gp's `par*` | C++, Java, Python | built but `parfor` |
| 40 | modules are values; no reach into `__MAIN__` | Perl | Python | decided |
| 41 | the executable is safe (dry-run) by default | — | everyone | decided |
| 42 | the reference implementation is rud1's own | — | the rest | decided |

## 1. The result is shown, unless the text ends in `;`

The value of the last expression of a program is shown after its output. A final `;`
silences it. Display only: the value, and the way the program ends, stay the same.

```text
[rud1] 1 + 1  \\> 2
[rud1] 1 + 1;  \\> (no output)
```

Reason: gp's rule, taken over "a file never shows" and "never shown outside the REPL": one
rule for a file, a one-liner and the REPL, "the most predictable too" (the author).
Status: built.

## 2. Layout: no space before `=`, leading commas

A style, not a rule of the language: white space carries no meaning. The reference style
writes an assignment operator with no space before it and one after (`a= 1`, `x+= 1`,
`v[i]= e`); other operators keep their spaces. A long `my(…)` or call has one item per
line, the comma in front. Example code puts the constant first in `==` and `!=`, so that a
slip of `=` for `==` is a compile error.

```text
[rud1]
my
( width= 3
, height= 4
);
width * height  \\> 12
```

```text
[rud1] my(n= 2); if(1 = n, "one", "other")  \\> 1:16 error invalid assignment target
```

Reason: the missing space sets assignment apart, "as `:=` does elsewhere". Status: built;
the formatter `rud1_fmt` writes the normalized form in this style (generation 62), as gofmt and
rustfmt do for theirs.

## 3. Immutable by default

A name declared with `my(…)` can't be assigned again; `mut my(…)` makes it changeable.
Values are immutable unless their type says otherwise: if `w` is a (dynamic or fixed length)
array, then by default `w[0]= 9` builds a new list and rebinds `w`; in-place change comes
with the `{mut}` type attribute (decided, with the type pass). A function
captures the values of the names it uses, not the names.

```text
[rud1] my(a= 1); a= 2  \\> 1:11 error assignment to immutable 'a'
[rud1] mut my(a= 1); a= 2; a  \\> 2
[rud1] my(v= [1, 2, 3]); mut my(w= v); w[0]= 9; [v, w]  \\> [[1,2,3],[9,2,3]]
[rud1] mut my(k= 1); my(f(x)= x + k); k= 10; f(1)  \\> 2
```

Status: built.

## 4. Static checks before the program runs

A program is checked as a whole before its first step. An undeclared name, a name declared
twice in one `my(…)`, and a call of a known function with a wrong count or a wrong argument
name are compile errors: nothing has run.

```text
[rud1] say("start"); say(total)  \\> 1:19 error undeclared 'total'
[rud1] my(a= 1, a= 2); a  \\> 1:1 error duplicate declaration 'a'
[rud1] my(g(a, b)= a - b); g(1, c: 3)  \\> 1:21 error call of 'g': no parameter 'c'
```

`python3.12 -c 'print("start"); print(total)'` prints `start`, then
`NameError: name 'total' is not defined`.

A callee is known when it is an immutable name bound to a function. Through a `mut` name or
a parameter the same rule is checked when the call runs, and a break of it is an `error`.

Reason (spec, principles): "whatever can feasibly be solved (or proved) at compile time is,
even at the cost of extra compile rounds". Status: these checks are built. So are the checks on
declared types (generation 45): `my(uint a= -1)` is a compile error.

## 5. Whole numbers and fractions are exact, of any size

`uint` (0 and up) and `sint` have arbitrary precision, and `/` on exact numbers gives an
exact rational. Nothing overflows in silence or becomes a float by itself.

```text
[rud1] 2^64 + 1  \\> 18446744073709551617
[rud1] 1/3 + 1/6  \\> 1/2
[rud1] [7 / 2, 2^-3, 1/2 + 1, 1.5 * 2]  \\> [7/2,1/8,3/2,3]
```

`perl -e 'print 2**64 + 1'` gives `1.84467440737096e+19`; `python3.12 -c 'print(1/3 + 1/6)'`
gives `0.5`; `echo '1/3 + 1/6' | gp -q` gives `1/2`. In Julia `1//3 + 1//6` is `1//2`,
but `typemax(Int) + 1` is `-9223372036854775808`; Haskell's `2^70` and Raku's `2**70`
are exact.

A float has a dot with a digit after it (`1.5`), and the prototype keeps it exact, as a
rational: `0.1 + 0.2` is `0.300000000000000`, exactly $`3/10`$; `1.0 / 3` shows as the
continued fraction `0.[3]`; `2.0^100` is the integer it equals, as is any float result that is
whole (`0.1 * 3 * 10` is `3`). A binary64 is asked for by name, `f64`, and refuses a value it does not hold exactly
(`my(f64 x= 0.1)` is an error).

Reason: gp's numbers; the author's first principle (generation 29): "Types are by default
of infinite precision." Status: `uint`, `sint`, rationals and the exact float are built. Decided, not built:
native floats under names like `float8`, a native `i64` for whoever asks for it.

## 6. A `uint` does not go below zero silently; prefix `+` makes a `sint`

A literal without a sign is a `uint`, and the difference of two `uint`s is a `uint`: it does
not silently become negative. A prefix `+` gives a `sint`, and the subtraction then happens
in `sint`. The rule protects an index: a position that went below 0 is not read as "from the
end", while a literal negative position is.

```text
[rud1] +2 - 3  \\> -1
[rud1] my(a= 3, b= 5); abs(+a - b)  \\> 2
[rud1] my(v= [10, 20, 30], i= 0); [v[-1], guard(v[i - 1], "none")]  \\> [30,"none"]
```

`python3.12 -c 'print([10, 20, 30][0 - 1])'` prints `30`. Rust's `3 - 5` of two `u32`s
panics in a debug build ("attempt to subtract with overflow'') and gives `4294967294` in a
release build, as C++ does.

Reason (history, generation 11): the author reasons from full types, `nat 3 - nat 5` "will
of course error in nat-context but not in int-context"; a `uint - uint` of type `sint` with a
checked narrowing was rejected as "too lax in several places". Status: the state `underflow` (generation 38) and the
prefix `+` are built, and so is a `sint` context from a declared type
(`my(sint d= a - b)`, a `sint` parameter or return type), which widens the operands first (generation 45).

## 7. Division and remainder are Euclidean

`//` is the whole quotient, `%` the remainder; the remainder is never negative, and
`a == b*(a // b) + a % b` holds. `/` is the exact division.

```text
[rud1] [7 // -2, 7 % -2, -7 // 2, -7 % 2]  \\> [-3,1,-4,1]
```

For the first two: `python3.12 -c 'print(7 // -2, 7 % -2)'` gives `-4 -1`;
`perl -e 'print int(7 / -2), " ", 7 % -2'` gives `-3 -1`; `echo '[7 \ -2, 7 % -2]' | gp -q`
gives `[-3, 1]`. Rust and Java give `-3 1` (truncation); Rust's `div_euclid` and
`rem_euclid` are Euclidean. Raku's `7 div -2` and Lua's `7 // -2` give `-4`.

Reason: as gp's `\` and `%`, with the invariant above. Status: built.

## 8. Number literals: `0177` is an error

Decided in generation 24, built in generation 40: a `0` followed by a digit is an error,
and one `_` between two digits of a number is ignored
(`1_000`; `1__2` and `1_` are errors). The same holds for `uint.read(s)` and
`sint.read(s)` and for a template's `$uint` and `$sint`. Built in generation 66: a number
in a base is spelled as the escape of a byte, `\x{7F}`, `\o{177}`, `\b{111_1111}`, a whole number
of any size; `0_177`, octal up to then, is an error that names `\o{177}`.

```text
[rud1] 0177  \\> 1:1 error malformed number '0177': a 0 before a digit (an octal number is \o{177})
[rud1] [\o{177}, \x{DEAD_BEEF}, \b{1001}, 1_000_000, uint.read("1_000")]  \\> [127,3735928559,9,1000000,1000]
[rud1] 0x1F  \\> 1:1 error malformed number '0x1F': a number in a base is \x{1F}
```

Reason: the readings elsewhere
disagree. `perl -e 'print 0177, " ", "0177" + 0'` gives `127 177`;
`python3.12 -c 'print(0177)'` is a `SyntaxError` ("leading zeros in decimal integer literals
are not permitted"); `echo '0177' | gp -q` gives `177`; Java gives `127`, Rust `177`, Raku `177` with a
warning. The braces make every number in a base findable by a search (the author: "Most coders love
grep-ability."). Status: built.

## 9. A strict `bool`

`true` and `false` are values of a type of their own, shown as `1` and `0`. A `bool` is no
number: arithmetic on it is an `error`, `uint(b)` is 0 or 1, and a count of the elements that
pass a test is `#[x | x <- L, x > 3]`. Against a number it is 0 or 1 under `==`, `!=` and
`===` (`true == 1`, A45.4), but not ordered (`true < 2` is an `error`), and it is no index (`v[true]` is an `error`). A condition takes a `bool` and nothing else: gp's
`if(n%2, …)` is `if(0 != n % 2, …)`, `while(1, …)` is `while(true, …)`; `0`, `""` and `[]`
are not false (decision 521).

```text
[rud1] [uint(true) + uint(true), #[x | x <- [1, 5, 7], x > 3]]  \\> [2,2]
[rud1] true < 2  \\> we exited <error("true < 2: a bool and a uint are not compared ('===' tells whether two values are the same)")> at 1:1
[rud1] [10, 20][uint(true)]  \\> 20
```

`python3.12 -c 'print(True == 1, [10, 20][True])'` prints `True 20`; Rust and Java refuse
`true + true` when compiled.

Reason (the author, generation 29): "Built-ins should be strict where feasible"; a user's
type may allow more. Status: built.

## 10. `==` compares values, `===` asks whether they are the same

`==` promotes numbers as arithmetic does and ignores the names of a list's fields. `===`
is identity: numbers by value whatever their kind, names included. Values of
different families are an `error` under `==` and plainly not identical under `===`.

```text
[rud1] [1 == 1.0, 1 === 1.0, 0.5 == 1/2, 4/2 === 2]  \\> [1,1,1,1]
[rud1] [[x: 1] == [1], [x: 1] === [1]]  \\> [1,0]
[rud1] "1" == 1  \\> we exited <error("\"1\" == 1: a str and a uint are not compared ('===' tells whether two values are the same)")> at 1:1
[rud1] "1" === 1  \\> 0
```

Reason: `===` is gp's identity; `==` promotes because otherwise `0.1 - 1/10 == 0` and
`0.1 == 1/10` would disagree. On the `error` (generation 29): "let's not allow `==` between
differently typed values, unless they are really of the exact same family". Status: built.
A float and a rational of one value are the same number, so `0.1 === 1/10` is 1 (the prototype
keeps `0.1` exact, section 5).

## 11. Comparisons chain, in one direction

`a < b <= c` means `a < b` and `b <= c`, with `b` evaluated once. A chain mixes `<`, `<=`,
`==` or `>`, `>=`, `==`, never both directions; `!=`, `!==` and `<=>` do not chain.

```text
[rud1] [1 < 2 < 3, 3 > 2 > 1, 1 <= 1 < 2]  \\> [1,1,1]
[rud1] 1 < 3 > 2  \\> 1:7 error comparison chain mixes '<' and '>'
[rud1] 1 < 2 != 3  \\> 1:7 error '!=' doesn't chain
```

`echo '3 > 2 > 1' | gp -q` gives `0` (the result of `3 > 2` against 1);
`python3.12 -c 'print(1 < 3 > 2)'` prints `True`; Perl chains since 5.32, also
in both directions: `perl -e 'print 1 < 3 > 2'` prints `1`. C++ compares a `bool`
with the third operand, and clang 21 refuses `3 > 2 > 1` by default (`-Wparentheses`);
rustc refuses a chain ("comparison operators cannot be chained''), javac a `boolean` against a
number, GHC `1 < 2 < 3` ("cannot mix''); Raku and Julia chain, as Perl, in both directions.

Status: built.

## 12. Errors are states of a value, not exceptions

A result is a value, or a state with an origin. `undef` is soft: it is bound, passed and
combined, and the code around it goes on. `error` is hard: it ends each sequence, loop and
call around it, up to the nearest `guard(x, h)`, which gives `h` in its place. Each call
that an `error` ends is added to its stack. There is no `throw`, no `try`, no `return`. A
state is no value: it has no literal and no `==`; `is_defined(x)` asks.

```text
[rud1]
my
( half(n)= if(0 == n % 2, n // 2, error("odd"))
, twice_half(n)= 2 * half(n)
);
say(guard(twice_half(7), -1));
my(r= max([]));
say(is_defined(r));
twice_half(7);
say("not reached")
```

Output:

```text
-1
0
we exited <error("odd")> at 2:35
  in (half at 3:22, twice_half at 8:1)
```

A program whose last value is not defined ends with such a message and exit status 1.

Reason (history, generation 11): the author, "evaluation should stop at `1/0`", with a
full disk as the case (`write(f, d); say("saved")` must not say "saved"). "It is exception
behaviour done with values: no new mechanism, `guard` is the catch, a defer the cleanup."
Status: built; the stopping is proved in Lean per evaluation step. Not built: `state(x)`,
`origin(x)`, a handler that is a function receiving the failure. A run-time error of an
operator says why since generation 31 (`1/0` is `error(1̈ / 0: division by zero)̈`); `2 - 3`
in section 6 is the state `underflow`, which carries no message.

## 13. Positions count from 0, a literal negative one from the end

Lists are indexed from 0; a literal `-N` means `#__ - N`, where `__` is the list being
indexed, so `v[-1]` is the last element; a computed position below 0 is the state `underflow`, not the
end of the list as in Python; several indices gather. A position
out of range is an `error`, reading as well as storing.

```text
[rud1] my(v= [10, 20, 30, 40]); [v[0], v[-1], v[0, -1]]  \\> [10,40,[10,40]]
[rud1] [10, 20, 30][3]  \\> we exited <error("[10, 20, 30][3]: index 3 is outside 0 .. 2")> at 1:1
```

It departs from gp: `echo '[10, 20, 30][1]' | gp -q` gives `10`. Status: built. A 1-based
list type for translated gp code, `vector`, is proposed.

## 14. Ranges hold both ends, take a step after `;`, and splice

`[a..b]` runs from `a` to `b`, both included; backwards it is empty. A step follows a `;`
and its sign is the direction: `[a..b;s]`. A range among the other elements of a list is
spliced in, so `[1, 3..9]` is 1, then 3 to 9, and not the odd numbers. The same forms work
as an index; a stepped range stands alone in its brackets.

```text
[rud1] [[1..4], [4..1], [1, 3..5, 9]]  \\> [[1,2,3,4],[],[1,3,4,5,9]]
[rud1] [[1..9;2], [10..1;-3]]  \\> [[1,3,5,7,9],[10,7,4,1]]
[rud1] my(v= [10, 20, 30, 40, 50]); [v[1..3], v[4..0;-2]]  \\> [[20,30,40],[50,30,10]]
```

`python3.12 -c 'print(list(range(1, 4)))'` prints `[1, 2, 3]`.

Reason: the step mimics gp's `forstep`; the older spelling with a second element
(`[7, 5..0]`) went because it "is just ugly, is much harder to explain" (the author,
generation 26). The splice is the rule an index had already (`v[1, 3..5]`). Status: built
(generation 27). Open: a right-open range, omitted bounds.

## 15. Lists and comprehensions are lazy

A range and a comprehension `[e | x <- L, filter, …]` give their elements when asked, so a
list may be endless (`oo` as a bound).

```text
[rud1] [x^2 | x <- [1..oo], 0 == x % 3][0..3]  \\> [9,36,81,144]
```

gp's comprehensions are strict. Status: built: an element is computed once, at the first ask,
then kept; `my(L= [mark(x) | x <- [1..3]]); [L[2], L[2], L[2]]` under `rud1_run -p.marks.fd=2`
writes one mark line.

## 16. An operator is a function with a name

A binary operator has the name `_op_`, one token without spaces, and the name is its
function as a value. The names are sealed: a script can't rebind `_+_`.

```text
[rud1] [fold(_+_, [1..10]), _*_(6, 7), scan(_+_, [3, 1, 2])]  \\> [55,42,[3,4,6]]
[rud1] my(_+_= 5); 1  \\> 1:4 error '_+_': names starting with '_' belong to the language
```

Reason: operators are calls of internal names; the names `_+_` are those of gp's library, libpari (gp's own syntax has no such form).
The spec's example of what sealing prevents: `_+_` rebound to a concatenation. Status: the
binary names as values are built; the prefix names (`-_`, `#_`) and operators declared by
a module are not.

## 17. Placeholders make a function of an argument

`_1`, `_2`, … inside an argument turn that argument into a function of as many parameters
as the highest number. The function is the whole argument the placeholder stands in, and a
call around a placeholder takes it as its own argument: `h(_1) + 1` is `h(x -> x) + 1`.
The second line shows both readings; here they happen to agree, and the compiler still warns
on `h(_1) + 1`, since a bare placeholder argument of a call that is itself an operand or an
argument is seldom meant so. Scala's `List(1, 2, 3).map(_ * 10)` gives
`List(10, 20, 30)`, Elixir's `Enum.map([1,2,3], &(&1 * 10))` gives `[10, 20, 30]`.

```text
[rud1] [sum([1..10], _1^2), fold(_1 + 2 * _2, [1, 2, 3])]  \\> [385,11]
[rud1] my(h(f)= f(1)); [h(_1 + 1), h(_1) + 1]  \\> [2,2]
1:29 warning: '_1' alone makes the argument of 'h' the function x -> x: for a function of the whole, write 'x -> …' around the call [W03d207]
```

Where it matters, the arrow form names the variable: `d -> h(d) + 1`. The handler of
`guard` is the exception: `_1` there is the failed result itself (`guard(v[i], str(_1))`).

Reason (history, generation 12): rud1 is to be as short as gp, whose iterator forms
(`sum(i=1, 10, i^2)`) win otherwise; on the extent: "no rule without a delimiter is right
in every nesting, this one is short". Status: built. A policy switch on placeholders is
proposed.

## 18. Rest parameters, spreads, named arguments

One parameter, anywhere in the list, may collect the arguments the others leave: `v[...]`.
An argument `L[...]` spreads a list into a call. Every parameter can be given by name
(`z: 9`); the names bind first, then the unnamed arguments fill what is left.

```text
[rud1]
my(f(a, v[...], z= 0)= [a, v, z]);
[f(1), f(1, 2, 3), f(1, 2, z: 9), f([4, 5][...])]  \\> [[1,[],0],[1,[2,3],0],[1,[2],9],[4,[5],0]]
```

The binding is resolved when the program is compiled, wherever the callee is known (section
4). A rest takes unnamed arguments only, so a mistyped name is reported, never absorbed;
there is no options bag. Status: built. Decided, not built: binding by type (`sort(cmp, L)`
accepted with a warning that names the order).

## 19. `defer`

`defer(s)` runs `s` when the enclosing block ends, however it ends, also by an `error`. A
block is a `{…}` or a loop; a `defer` in a loop body runs at the end of each round. Several
run newest first.

```text
[rud1]
{ defer(say("closed")); say("work"); 5 }
```

Output:

```text
work
closed
5
```

A deferred function is called with the block's result, however the block ended; at an
`exit` it gets `undef`.

```text
[rud1] my(log(r)= say("result ", r)); { defer(log); 5 }  \\> result 5, then 5
[rud1] { defer((r) -> say("got ", is_defined(r))); exit(3) }  \\> got 0
```

Reason (spec): a block is entered one way, at its top; "`defer` exists because there are
many ways out". Status: built. `amend(f)`, a defer that replaces the block's result, is
proposed.

## 20. Long names are the names; short ones come from a module

The names of the language are full words, taken from the standards where they have them
(the Unicode Standard's toUppercase is `str.to_uppercase`). A function that makes a text
has `str.` in front; `to_` marks a new value, `is_` a question.

```text
[rud1] [str.to_uppercase("abc"), is_defined(max([]))]  \\> ["ABC",0]
[rud1] str.uc("abc")  \\> 1:1 error no function 'str.uc'
[rud1] alias {isdef: is_defined}; isdef(max([]))  \\> 0
```

Reason (the author, generation 18): "The long names are the main names, the ones that the
compiler will put in a normalized transformation of a script"; "`str.to_uppercase` does
send the message more, that it doesn't change in-place"; "shortnames leave the `core`, so
an `alias.perl` module, or such, can bring perlish shortnames." Status: the names are
built. Modules are not: today a script gives its own short names with `alias {…}`, as in
the last line, a dotted one too (`alias {uc: to_uppercase}; str.uc("abc")` is `"ABC"`).

## 21. A `str` is characters; `bytes` is another type; `s[i]` is a character

A `str` is always well-formed UTF-8, and its length counts characters as Unicode defines
them, the extended grapheme clusters (UAX \#29): `é` written as `e` and a combining accent, a flag,
an emoji with its skin tone each count one. `bytes` is raw, with
its own literal `b"…"`. `s[i]` is a character, a `str`, and a slice `s[a..b]` takes characters and is a `str`,
to read and to assign; the code points are `str.to_codepoints(s)`; `b[i]` of a `bytes` value is one byte, a number, so a `str` and a
`bytes` value act as lists of their elements, `max` and `sort` included (`sum` of the bytes).
Text is joined with `~`; `+` is never defined for it, and no text turns into a number by
itself (`uint.read("42")`, `str(42)`; `uint("42")` is the code points `[52,50]`). A `str` is never normalized unasked.

```text
[rud1] [#"hé", #b"h\x{c3}\x{a9}"]  \\> [2,3]
[rud1] [str.to_codepoints("hé"), str.to_characters("hé")]  \\> [[\x{68},\x{e9}],["h","é"]]
[rud1] ["héllo"[1], "héllo"[1..2], b"abc"[1]]  \\> ["é","él",\x{62}]

[rud1] "abc" + "def"  \\> we exited <error("\"abc\" + \"def\": no '+' for a str and a str")> at 1:1
```

`perl -e 'print length("hé")'` and `echo '#"hé"' | gp -q` give `3`;
`python3.12 -c 'print(len("hé"))'` gives `2`. Rust's `"hé".len()` is `3`, its
`.chars().count()` `2`, `s[0]` is a compile error, and a slice counts bytes: `&s[0..3]` of
`"héllo"` is `hé`, `&s[1..2]` panics inside the `é`; Python's `"héllo"[1:3]` is `él`; Java's `length()` counts UTF-16
units, 2 for one code point above FFFF.

Reason: for `s[i]` the spec gives the rule only; for `+` it names the trap of one operator
that adds and joins. Status: built: `str` and its `str.` family; of `bytes` the literal,
`#`, the index, `~`, the comparisons and `str(b)`, the checked way to a `str`. `str.to_graphemes` and `bytes(s)` (`bytes("é")` is `b"\x{c3}\x{a9}"`) are built
as well. Not built: the compile error for a `str` joined with `bytes` (an `error` when run, today).

## 22. Case mapping is the Unicode Standard's full one

`str.to_uppercase`, `str.to_lowercase` and `str.to_casefold` use the full mappings of the
Standard (data of Unicode 18.0.0): a result can be longer than its input, and lowercasing
follows Final_Sigma. No language or locale setting changes a result.

```text
[rud1] [str.to_uppercase("straße"), str.to_lowercase("ΟΔΟΣ")]  \\> ["STRASSE","οδος"]
```

`perl -CS -Mutf8 -e 'print lc("ΟΔΟΣ")'` gives `οδοσ`;
`python3.12 -c 'print("ΟΔΟΣ".lower())'` gives `οδος`. Rust's `to_lowercase` gives
`σας` for `ΣΑΣ`, and Java's too with `Locale.ROOT`; Java's result depends on the locale
(`"I"` lowercases to `ı` in Turkish).

Reason (history, generation 18): a simple uppercase leaves `straße` as `STRAßE`, "so a
lookup of an uppercased key misses"; without Final_Sigma "every Greek word ending in sigma
is misspelled". The price: lowercasing no longer works piece by piece. On locale: "stay
away from `locale` for now, just don't make it impossible". Status: built (generation 19).
Not built: `str.to_titlecase` of a whole text (an `error` that says so), the rules of a
language; `str.is_casefolded` is built since generation 67 (YN).

## 23. A byte escape has braces

A byte is `\x{89}` in hex, `\o{211}` in octal or `\b{10001001}` in binary, and values are
shown the same way. `\x89` without braces is a compile error. `\0` to `\7` are one digit
each, and a digit right after one is a compile error too (`"\012"`).

```text
[rud1] b"\x{89}PNG" ~ b"\o{15}\b{00001010}"  \\> b"\x{89}PNG\x{0d}\x{0a}"
[rud1] b"\x89PNG"  \\> 1:1 error \x needs braces: \x{...}
[rud1] [b"\x{0}", b"\0", b"\o{0}", b"\b{0}"]  \\> [b"\x{00}",b"\x{00}",b"\x{00}",b"\x{00}"]
```

`perl -e 'print "\012" eq "\n" ? "LF" : "other"'` prints `LF`. Java has no `\x` ("illegal escape character''); Rust refuses
`"\x89"` in a string ("out of range hex escape'') and writes `\u{89}` with braces.

Reason: the author, generation 23: "Let's make braces required for now"; they balance,
which a `t{…}` literal needs (section 25). `"\012"` would be three bytes here and is one
byte elsewhere, so it is refused. Status: built.

## 24. Reading changes nothing

A read gives what came: no line end taken off, no CR dropped, no signature removed, nothing
substituted. `read_line()` gives the text up to and including the next LF, `undef` at the
end of input, and an `error` for a piece that is not well-formed UTF-8.

```text
[rud1]
my(a= read_line(), b= read_line(), c= read_line()); [a, b, is_defined(c)]
```

Run as `printf 'quit\r\nlast' | rud1 file.rud`. Output:

```text
["quit\x{0d}\x{0a}","last",0]
```

With the bytes `quit` CR LF in a file `f.txt`,
`python3.12 -c 'print(repr(open("f.txt").readline()))'` prints `'quit\n'`.

Reason (the author, generation 20): "Never change any data, that is for the user to
decide." It departs, knowingly, from the Unicode Standard's recommendation to treat CR, LF,
CRLF and NEL alike on input. Status: the principle is decided; `read_line()` on standard
input is built; the reader in sends cut by time, the other verbs and files are not.

## 25. A template is a checked literal

`unpack` reads bytes by a template: Perl's `pack` codes, extended with sets, repeats,
alternatives and named patterns. A template is a literal `t{…}`, a value of its own type
and no `str`. It is parsed when the program is compiled, so a wrong template is a compile
error. The check goes beyond syntax: two variants of a `|` that can read the same text are
refused, with the shortest such text in the message; `||` ("or else") lets the first win.

```text
[rud1] unpack(t{magic:a4 w:n}, b"\x{89}PNG\x{00}\x{10}")  \\> [magic:b"\x{89}PNG",w:16]
[rud1] unpack(t{n a[=k]}, b"A")  \\> 1:8 error template 1:5: the count =k is not named before its use
```

```text
[rud1]
unpack(t{%[0-9A-Fa-f]+ | %[0-9]+}, b"15")
```

Output:

```text
1:8 error template 1:1: variants 1 and 2 can read the same text, "1": make one of them narrower, or write '||' where the first is to win
```

Reason (history): a template in a cooked `"…"` was unreadable (`a["\n"]` became
`"a[\"\\n\"]"`), hence the raw literal; the check on variants has to hold for every input,
as a search over short texts "would leave a silent hole". Status: built: the literal, the
parser and its checks (generations 23 to 28), `unpack(t, b)` on a `bytes` value (generation
29). Built since: `unpack(t)` on standard input (generation 40), `pack` (generation 41). How strict the check on variants is, is
proposed.

## 26. `unpack` never tries again

The reader goes through the data once. A `|` takes the variant that reads furthest, not the
first listed. A repeat takes what it can and gives nothing back. The template has to cover
the data: bytes left over are an `error`, unless it ends in `x*`, which takes them.

```text
[rud1] unpack(t{"foo" | "foot"}, b"foot")  \\> [b"foot"]
[rud1] unpack(t{C x*}, b"AB")  \\> [65,b"B"]
```

```text
[rud1]
unpack(t{%[a-z]* "z"}, b"abz")
```

Output:

```text
we exited <error("unpack(t{%[a-z]* \"z\"}, b\"abz\"): template 1:9: the data ends inside the literal \"z\": 1 wanted at byte 3, 0 left")> at 1:1
```

In Perl: `perl -e '"foot" =~ /foo|foot/; print $&'` gives `foo`;
`perl -e 'print unpack("C", "AB")'` gives `65`, the `B` ignored;
`perl -e 'print "abz" =~ /^[a-z]*z$/ ? "match" : "no match"'` gives `match`.

Reason (history, generation 25): a model of a reader that tries variants showed "the cost
doubling per nesting level"; a reader that never tries again is a function of the input,
which makes the check of section 25 possible. The price: a grammar written for a parser
that tries again may need rewording. Status: built (generation 29); parts of the rules are
proposed.

## 27. No silent limits

Decided in generation 29, as a first principle. The author: "It is one of the reason why
Lean 4 is the primary host-language: to catch such things. Will lead to interesting
discussions: no silent limits." Its first rule: a text is a finished value, so a cast or a
format whose text can be endless (a rational in decimal: `1/9`) is refused when compiled
unless the call gives a limit; by the type, not by the value.

Status: decided; the refusal when compiled is not built. The prototype still has silent
cases; an audit of generation 30 lists twelve. Two of them, a value dropped without a word,
are an `error` since generation 40 (decided in generation 34):

```text
[rud1] mut my(a, b); [a, b]= [1, 2, 3]; [a, b]  \\> we exited <error("destructuring [a, b]: more than 2 values for 2 names")> at 1:15
[rud1] mut my(v= [0, 0, 0]); v[0, 1]= [5, 6, 7]; v  \\> we exited <error("scatter assignment: more than 2 values for 2 positions")> at 1:23
```

Others on that list: a type word that is not used (section 4), the binary float that section
5 replaced (exact since A21.6), and an old `[1, 3..9]` that changed its meaning with section
14. What counts as a silent limit (a value dropped, an argument ignored, a meaning changed,
a digit cut) is not written down yet.

## 28. Every message has an id; `--explain` tells the ways out

A message with more than one way out ends with an id, `E0` and five hex digits of the SHA-256
of a seed and its dotted name (a warning: `W0`, by a seed of its own), so a changed text keeps its id.
`rud1 --explain ID` prints the explanation, written beside the code that raises the message;
a policy names a warning by its id or its dotted name.

```text
[rud1] t{[t{=n:C}, t{=n:C}]}  \\> 1:1 error a template of parts: both parts name n [E038b37]
```

`rud1_run --explain E038b37` starts with `E038b37 template.parts.same_name` and lists the ways
out. Rust has the same idea (`rustc --explain E0308`), its ids from a counter.
Reason (spec, principles): ids come from their origin, "predictable, good for parallelization,
lock-free'' (the author). Status: built.

## 29. Types are settled when compiled

Every type is known before the program runs. A parameter without a type word is `auto`, an
interface: the compiler makes the function's code for the argument types of each call, as C++
does for a template and Julia for a method. A value's type is looked at when the program runs
only where the source can't tell it: an element of a mixed list read by a computed index, the
result of `unpack`, and `str`, `say` or `==` on those. Python, Perl and gp settle types
as they run. Reason (spec, principles): whatever can be solved at compile time is. Status:
decided; the checks of declared types are built (section 4).

## 30. Sub-types: nothing is fitted without a word

A refinement (`type Pct = uint {0..100}`), a nominal type (`type Id = new sint`, no cast
either way) and an extension (the parent's value and more: a `template` is its text and its
tree). A sub-type stands for its parent only where its declaration says so; nothing is inherited
by default, and the slot's type picks the operation. Haskell's `newtype` and Rust's
newtypes are the second; Java's subclasses fit their parent
always. Reason (the author): "Built-ins should be strict where feasible''; a user can make a lax
variant. Status: decided; refinements built (`Pct(150)` is an `error`, a slot `Pct p` checks each
value), while the prototype still lets a `Pct` pass where a `uint` is wanted.

## 31. Floats of infinite precision; a type picks its overflow

The default float has no fixed precision, as `uint` has no fixed size; a native `f64` or
`i64` is there for whoever asks for it. A type declares what overflow gives: `sat`, `wrap`
or `oo`, and the state is always `overflow`. C++, Java, Perl and Python compute in binary64,
Rust and C++ in fixed widths unless asked; Rust names the wrap per operation
(`wrapping_sub`). Reason (the author, generation 29): "Types are by default of infinite
precision.'' Status: decided; the prototype's float is exact, a rational
(section 5).

## 32. A type has a number of values

`#bool()` is 2. A collection, a "multi'', has an index type: indexed by its order it is an
array, by its value a map; an array only where the type's values can be counted from a first
one. The declaration picks, else the compiler infers it from what the code does. Lua has one
table for both. Status: decided.

## 33. An approximation is a state: `apx`

A value that was rounded (a float that rounds, a result of a foreign library) carries the state
`apx`, shown with it (`0.333… <apx>`), and passes it to every value made from it. A
binding module removes it by declared rules, for libpari by the type of a result or by a
predicate over the call (gp's `ispseudoprime(x, {flag})`: a 0 is exact, a 1 stays `apx`).
C++, Java, Perl, Python and gp say nothing. Status: decided (generation 53, U4).

## 34. `async` is dataflow: `pending`

`async(e)` gives the state `pending`; an operation on a `pending` value gives
`pending`, and the program waits only at `await`, at a read or a write, and at a condition.
async/await asks for an `await` at
each use. Status: decided.

## 35. Capabilities are bindings

The top level holds `stdout`, `fs`, `net` and the other root capabilities as ordinary
names. A module is evaluated without them and sees only what it is handed, so a module given none
is provably pure. A WASI module sees only the directories its host grants: a C program built by
`zig cc -target wasm32-wasi` that opens `data/a.txt` gets "No such file or directory'' under
`wasmtime run`, and reads the file under `wasmtime run --dir=.`. In C++, Rust, Java, Perl,
Python and gp any code may open a file. Status: decided.

## 36. A module is accepted by a proof

`my(s= use("sort", Sorting))` accepts the module only if its stored proof checks against the
specification `Sorting`, chosen by the importer (the result sorted and a permutation). The
checker is the Lean kernel, against rud1's semantics in Lean. None of the languages named here
does it at an import. Reason: "no unit
tests needed''; the price, a proof covers only what the specification says. Status: decided.

## 37. Regions and taint

A region, `SOx_( … )_SOx`, runs only while its conditions hold. A value made in it is
tainted, also through control flow (`if(t > 100, 1, 0)`), and goes only to a sink that
accepts the taint; the compiler infers it where it can, else the value carries it as a state. The
names (`SOx`, `PI`) are the coder's. Perl's `-T` taints input only and lets a regex
clean it; Reason (spec, §10): what a
run-time design for Perl (Hounding) catches late, rud1 is to refuse when compiled. Status: decided.

## 38. Code as data without eval

A `sym` parameter gets the expression with the caller's names; `'(a + b*2)` is an
`expr`. There is no `eval`, of a text or of an `expr`, and no macro language: the
compiler folds every call whose inputs are known, also one that returns an `expr`. Zig's `comptime` folds; Perl and Python have `eval`, Rust
has macros. Reason: static analysis covers the code fully. Status: decided; `lazy` and
`sym` parameters are built.

## 39. Parallel without locks

gp's names, `parapply`, `parsum`, `parfor`, take pure arguments only, and a reduction
runs in a fixed tree order, so a sum of floats gives the same result on any number of threads.
Shared data is immutable and a `{mut}` buffer can't cross threads: no locks in user code.
Rust keeps a mutable value on one thread too; C++, Java and Python leave the locks and the order
to the coder. Status: built but `parfor`: `parapply`, `parselect`, `parsum` and `parvector`
(`parsum([1, 2, 3])` is 6); `parfor`, `async` and `await` are not built.

## 40. A module is a value

`use` runs when the program is compiled and gives the module, a list of named fields; a
module that ends undefined or `false` is refused, Perl's rule without its truthiness. A module
can't reach `__MAIN__`; there is no `our`, no `local`, no `CORE::GLOBAL`.
`CORE.say` is the language's `say`, as Perl's `CORE::say`. Python's modules are objects
that any code can change. Status: decided; the startup module is built, `__CORE__` is proposed and its spelling open
(`__CORE__.sum([1, 2])` is the compile error `undeclared '__CORE__'`).

## 41. Safe by default

The executable does only safe, dry-run things unless a flag allows more. No language above has
such a default. Reason (decision 58): the coder decides when a program may act. Status: decided.

## 42. rud1 is its own reference

The reference implementation is written in rud1. A build asks its host which parts it has
ported, prefers those for speed, and checks each port against the rud1 one on generated data.
C++, Rust and Java have a standard, or a compiler, as the reference.
Status: decided; today's reference is the Lean prototype.
