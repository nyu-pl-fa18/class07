# Class 7

## Types and Type Systems

The compiler/interpreter of a programming language defines a mapping
of the values that a program computes onto the representation of these
values in the underlying hardware (e.g. how floating point numbers are
mapped to sequences of bits). A *type* describes a set of values that
share the same mapping to the low-level representation and the same
operations that perform computations on those values.

More generally, one can take different view points of what types are:

* *Denotational* view:
  * a type is a set *T* of values (e.g. the Scala type `Int` denotes
    the set of all integer values)
  * a value has type *T* if it belongs to the set
  * an expression has type *T* if it evaluates to a value in *T*

* *Constructive* view:
  * a type is either a built-in primitive type (`int`, `real`, `bool`,
    `char`, etc.) or
  * a composite type constructed from simpler types using a
    *type-constructor* (record, array, set, etc.)
  
* *Abstraction-based* view:
  * Type is an *interface* consisting of a set of operations.

We will mostly stick to the denotational view.

Types serve several purposes in programming languages:

* *Detecting Errors*: an important purpose of types is for detecting
  *run-time errors* related to the usage of operations on values they
  are not meant to be used on. Example:
  
  ```scheme
  (define (f x) (+ 1 x))
  (f "hello")
  ```
  
  This program fails with an error when (+ 1 "hello") is executed
  because `+` expects two numbers as argument and not a number and a
  string:
  
  ```
  +: contract violation
  expected: number?
  given: "hello"
  argument position: 1st
  other arguments...:
  ```
  
  If the Scheme interpreter were to continue executing `+`
  without producing an error, then this would likely lead to
  unintended behavior because the low-level representation of numbers
  and strings are incompatible.

* *Documentation*: Type annotations are useful when reading
  programs. For instance, type declarations in function headers
  document constraints on the inputs that the functions assumes,
  respectively constraints on the return value that it guarantees:
  
  ```scala
  def f(x: Int): Int = x + 1
  ```

* *Abstraction*: Types are often at the heart of module and class
  systems that help to structure large programs into smaller
  components. In particular, types help to abstract away
  implementation details of these components. In turn, this helps to
  enforce a disciplined programming methodology that ensures that
  changes to the internal implementation details of one module do not
  affect other modules in the program.

The *type system* of a programming language is a mechanism for
defining types in the language and associating them with language
constructs (expressions, functions, classes, modules, etc.) as well
as a set of rules for answering questions such as

* *Type compatibility*: where can a value of a given type be used?

* *Type checking*: how do you verify that a value observed at run-time
  is compatible with a given type, respectively, how do you verify
  that an expression will only evaluate to values compatible with a
  given type?

* *Type inference*: how do you determine the type of an expression from
  the types of its parts?

* *Type equivalence*: when do two types represent the same set of
  values?
  
### Type Checking 

*Type checking* is the process of ensuring that a program obeys the
type system's type compatibility rules. A violation of the rules is
called a *type error* or *type clash*. A program that is free of type
errors is considered to be *well-typed*.

Languages differ greatly in the way they implement type
checking. These approaches can be loosely categorized into

* strong vs. weak type systems
* static vs. dynamic type systems

A *strongly typed* language does not allow expressions to be used in a
way inconsistent with their types (no loopholes). A strongly typed
language thus guarantees that all type errors are detected (either at
compile-time or at run-time). Examples of strongly typed languages
include Java, Scala, OCaml, Python, Lisp, and Scheme.

On the other hand, a *weakly typed* language allows many ways to
bypass the type system (e.g., by using pointer arithmetic or unchecked
casts). In such languages, type errors can go undetected, causing
e.g. data corruption and other unpredictable behavior when the program
is executed. C and C++ are poster children for weakly typed
languages. Their motto is: "Trust the programmer".

The other high-level classification is to distinguish between static
and dynamic type system. In a static type system, the compiler ensures
that the type rules are obeyed at compile-time whereas in dynamic type
systems, type checking is performed at run-time while the program
executes.

Advantages of static typing over dynamic typing:

* more efficient: code runs faster because no run-time type checks are
  needed; compiler has more opportunities for optimization because it
  has more information about what the program does.

* better error checking: type errors are detected earlier; compiler
  can guarantee that no type errors will occur (if language is also
  strongly typed).

* better documentation: easier to understand and maintain code.

Advantages of dynamic typing over static typing:

* less annotation overhead: programmer needs to write less code to
  achieve the same task because no type declarations and annotations
  are needed. This can be mitigated by automated type inference in
  static type systems.

* more flexibility: a static type checker may reject a program that is
  correct but does not follow the specific static typing discipline of
  the language. This can be mitigated by supporting more expressive
  types, which, however, comes at the cost of increasing the
  annotation overhead and the mental gymnastics that the programmer
  has to perform to get the code through the static type checker.
  
  
It is often considered easier to write code in dynamically typed
languages. However, as the size and complexity of a software system
increases, the advantages of dynamic typing turn into disadvantages
because type errors become harder to detect and debug. Hence,
statically typed languages are often preferred for large and complex
projects. Also, for performance critical code, dynamic typing is often
too costly.

The distinction between weak/strong and static/dynamic is not always
clear cut. For instance, Java is mostly statically typed but certain
type checks are performed at run-time due to loopholes in the static
type checker. Some so-called *gradually typed* languages even allow
the programmer to choose between static and dynamic type checking for
different parts of a single program. Examples of such languages
include Dart and TypeScript.

Similarly, the distinction between weak and strong type systems is not
always completely clear and some people use slightly different
definitions of what strong vs. weak typing means. For instance, the
notion of type compatibility in JavaScript is so permissive that the
language is often considered to be weakly typed even though, according
to the definition given above, JavaScript is strongly typed.

### Type Inference

Type inference concerns the problem of statically inferring the type
of an expression from the types of its parts. All static type systems
perform some amount of type inference. For instance, consider the
`Scala` expression

```scala
1 + 2
```

Here, the static type checker can infer that the result of value must be
an `Int` value because the two operands are `Int` values, and hence
`+` is interpreted as integer addition, which produces a value of type
`Int`.

The situation is more complicated if variables are involved:

```scala
x + y
```

The operator `+` is overloaded and can mean different things depending
on the types of its operands. For instance, if `x` is a string, `+` is
interpreted as string concatenation. If `x` is a `Double` it is
interpreted as floating point addition, etc. This is one of the
reasons why Scala requires type annotations for the parameters of
functions, as this is needed for overloading resolution.

In languages without operator overloading, the types of expressions
like `x + y` can still be inferred automatically even if the types of
the variables `x` and `y` are not explicitly specified. For instance,
if `+` is not overloaded and always means integer addition, then the
expression can only be well-typed if both `x` and `y` are integer
values. One can thus infer the types of variables from how they
are used in the code. Enabling this kind of more general type
inference requires a carefully designed type system. For instance, the
ML family of languages supports this feature.

### Type equivalence

Type equivalence concerns the question when two types are considered
to be equal (i.e. represent the same set of values). One distinguishes
two basic ways of how to define type equivalence: *nominal typing* and
*structural typing*:

* Nominal typing: two types are the same only if they have the same name
 (each type definition introduces a new type)
 
 * *strict*: aliases (i.e. declaring a type equal to another type) are distinct
 * *loose*: aliases are equivalent

* Structural typing: two types are equivalent if they have the same
  structure. That is, they are structurally identical when viewed as
  terms constructed from primitive types and type constructors.

Most languages use a mixture of nominal and structural typing. For
instance, in Scala, object types use nominal typing but generic types
use structural typing.

### Type compatibility

Type compatibility concerns the questions when a particular value (or
expression) of type `t` can be used in a context that expects a
different type `s`. From a denotational view of types this corresponds
to the question whether the values described by `t` are a subset of
the values described by `s`. This question is closely related to the
notion of subtyping in object-oriented languages.

However, more generally, type compatibility may also concern cases
where the representations of the values of the two types `t` and `s`
differ, but there is a natural conversion from `t` to `s`. For
instance, many languages allow integer values to be used in floating
point operations. E.g. consider the following `Scala` expression

```scala
1.3 + 1
```

Here, the `+` operator will be interpreted as an operation on `Double`
values because at least one of the operands is a double value. Hence,
both operands are expected to be of type `Double`. However, the second
operand is `1`, which is of type `Int`. The representations of `Int`
and `Double` values differ. Nevertheless, the expression is well-typed
according to Scala's typing rules: the type `Int` is compatible with
the type `Double`.

To account for the different representation of `Int` and `Double`
values, the compiler will generate code that performs an implicit
conversion of the `Int` value `1` to the `Double` value `1.0` (in this
case, the conversion can be done statically at compile-time but this
is not possible in general if e.g. `1` is replaced by an `Int`
variable). This kind of implicit conversion code is referred to as a
*type coercion*.

Determining if and when type coercions should be applied is a major
design aspect that distinguishes the type systems of different
programming languages.

## Introduction to OCaml 

We will study these concepts related to types and type systems in more
depth in the context of the OCaml language and later when we revisit
Scala as an example of an object-oriented language.

OCaml belongs to the ML family of languages. ML stands for *Meta
Language*, which was originally developed by Robin Milner in the early
1970s for writing theorem provers (i.e., programs that can
automatically find and check the proofs of mathematical theorems). ML
took many inspirations from Lisp but introduced several new language
features intended to make programming less error prone. In particular,
ML features a powerful and very well designed static type system. 

Specifically, the type systems of ML-style languages provide strong
static type checking while eliminating most of the overhead that is
normally associated with static type systems. In particular, for the
most part, the programmer does not need to provide any explicit type
annotations. Instead, the type checker of the compiler can
automatically infer these types by analyzing the syntactic structure
of the program. To this day, the languages in the ML family occupy a
unique sweet spot in the design space of type systems by providing
the benefits of static typing while retaining some of the most
important benefits of dynamically typed languages.

More generally, languages that belong to the ML family share the
following features:

* clean syntax (few parenthesis)
* functions are first-class values
* declarations use static scoping and deep binding semantics
* function calls use call-by-value semantics (call-by-name can be
  simulated using closures)
* automated memory management via garbage collection
* strong static typing
* powerful type system
  * parametric polymorphism (similar to generics)
  * structural type equivalence
  * user-definable algebraic data types and pattern matching
  * all of this with automated static type inference!
* advanced module system with higher-order modules (*functors*)
* exceptions
* imperative features: mutable arrays, references, and record fields

Popular implementations and dialects of ML include

* Standard ML of New Jersey (SML/NJ)
* Poly/ML
* MLton
* OCaml
* F\# (Microsoft; derived from OCaml)
* Reason (Facebook; derived from and implemented on top of OCaml)

The design of Haskell was also strongly influenced by ML.

We will focus on OCaml, which is a general purpose programming
language that was initially developed in the late 1990s by a team of
French computer scientists. In addition to the features shared by all
languages in the ML family, OCaml also provides:

* a compiler that produces efficient native machine code for many
  architectures
* a class system that enables object-oriented programming

We will ignore OCaml's class system and imperative features and focus
on the functional core of the language.

### Installation, Build Tools, and IDEs

#### Linux and MacOS

Most Linux distributions as well as homebrew on Mac OS come with
precompiled packages for OCaml. There is also a Windows
installer. However, I suggest to install OCaml
using [opam](https://opam.ocaml.org/), which is a package manager for
OCaml that makes it easy to install many other useful tools for
developing OCaml programs. 

On MacOS do:

```bash
brew install ocaml
brew install opam
```

On Ubuntu do:

```bash
sudo apt install opam
```


Please read the [installation instructions of
opam](https://opam.ocaml.org/doc/Install.html) if you have another
operating system.

Once you have opam installed, you can install and set up the most
recent version of the OCaml language and compiler by executing the
following commands in a terminal:

```bash
opam init
```

If you see a warning related to a missing `m4` dependency, then
install `m4` before you proceed using

```bash
brew install m4
```

respectively

```bash
sudo apt install m4
```

Then you can create a 'switch' for the most recent OCaml release by executing

```bash
opam switch create 4.07.0 # may not be needed
eval `opam config env`
```

The installation will take a while since opam will download the
sources of the OCaml compiler and compile it from scratch. Follow
the instructions provided by the output of these commands to set up your
environment variables. Once, the installation has completed, you can
execute `ocaml`, which starts an OCaml REPL session:

```ocaml
        OCaml version 4.07.0

#
```

If you want to evaluate an OCaml expression in the REPL, you need to
terminate it by a double semicolon `;;` and then press `Enter`:

```ocaml
# 3 + 1 ;;
- : int = 4

# let x = 3 + 1 ;;
val x : int = 4

# #quit ;;
```

These double semicolons are only needed in the REPL but not in source
code files that are processed by the compiler.

In addition to the OCaml compiler and runtime, you also want to
install the OCaml library manager
[ocamlfind](http://projects.camlcity.org/projects/findlib.html), the
OCaml build tool [ocamlbuild](https://github.com/ocaml/ocamlbuild/),
and the OCaml unit testing framework
[OUnit](http://ounit.forge.ocamlcore.org/). You can do this via opam:

```bash
opam install -y ocamlfind
opam install -y ocamlbuild
opam install -y ounit
```

These tools provide similar functionality as `sbt` and `scalatest`
do for Scala.

Several IDEs have plugins for OCaml. I suggest to use
[Merlin](https://github.com/ocaml/merlin) which provides IDE support
for OCaml in common editors like Emacs and Vim. Merlin can also be
integrated into other editors and IDEs via third-party plugins,
including Atom, Sublime, and Visual Studio Code. You can install it
via opam by executing:

```bash
opam install -y merlin
```

See the installation instructions on [Merlin's project
website](https://github.com/ocaml/merlin) for further details on how
to configure various editors.

If you want a modern IDE, [Visual Studio
Code](https://code.visualstudio.com/) works well with OCaml in my
experience. Follow the installation instructions for your operating
system/distribution. For supporting OCaml, you should install the
[OCaml and Reason
IDE](https://marketplace.visualstudio.com/items?itemName=freebroccolo.reasonml)
extension. You can do this from within Visual Studio Code by selecting

```
File -> Preferences -> Extensions
```

Then search for the `vscode-reasonml` extension and install it. The
extension builds on top of Merlin which you can install via opam (see
above).

#### Windows 10

If you are using Windows 10, I suggest to proceed as follows (this is
the setup I have been using in class). Install Ubuntu via the Windows
Subsystem for Linux. Use e.g. the instructions in the following two
tutorials to do this:

* [Install WSL - Windows Subsystem for Linux](https://solarianprogrammer.com/2017/04/15/install-wsl-windows-subsystem-for-linux/)

* [Using the Windows Subsystem for Linux with Xfce 4](https://solarianprogrammer.com/2017/04/16/windows-susbsystem-for-linux-xfce-4/)

Then follow the installation instructions for Ubuntu above and install
everything within your Ubuntu subsystem. In particular, install Visual
Studio Code within Ubuntu rather than as a Window app.

### Syntax

OCaml expressions are constructed from constant literals (numbers,
booleans, etc.) and inbuilt operators (arithmetic and logical
operators, etc.) using lambda abstraction and function
application. Inbuilt binary operators use infix notation and follow
standard rules for operator precedence and associativity. Function
application has higher precedence than any other infix operator and is
left associative, just like in the lambda
calculus. See
[here](https://caml.inria.fr/pub/docs/manual-ocaml/expr.html) for more
details about OCaml expression syntax. In the following, we discuss
the most important syntactic forms. More detailed OCaml tutorials can
be found [here](https://ocaml.org/learn/tutorials/). We also refer to
the [OCaml Manual](http://caml.inria.fr/pub/docs/manual-ocaml/) for a
comprehensive coverage of all features of the language and its
accompanying tools and standard library.

### Basics

OCaml programs are structured into modules, which can be nested. Every
source code file automatically defines a module. We will discuss the
module system later and for now assume that we only work with a single
module.

Top-level definitions in a module are introduces using `let x = init`
which binds `x` to the value obtained from evaluating the expression
`init`. The scope of the let-binding is the remainder of the module
that follows the definition (with nested let-bindings of `x` within
that scope yielding holes in the scope as usual). The scope of the
binding for `x` introduced by `let` excludes the definition expression
`init`.

Example:

```ocaml
let x = 3
```

Lambda abstraction for defining anonymous function values takes the
form `fun x -> body`. Example:

```ocaml
let plusOne = fun x -> x + 1
```

A `let`-bound name that is defined by a lambda abstraction as in the
previous example can be abbreviated by

```ocaml
let plusOne x = x + 1
```

This notation generalizes to curried functions. That is

```ocaml
let plus = fun x -> fun y -> x + y
```

can be abbreviated by

```ocaml
let plus x y = x + y
```

Note that all functions in OCaml take a single parameter. Multiple
parameter functions can be implemented by passing a tuple as a single
parameter:

```ocaml
let plus (x, y) = x + y
```

Recursive definitions are introduced using `let rec x = init`. Here
the scope of the binding includes the expression `init`.

```ocaml
let rec fac x = 
  if x = 0 
  then 1 
  else x * fac (x + 1)
```

If multiple definitions have mutually recursive dependencies, they can be
defined with `let rec ... and ...`:

```ocaml
let rec f x = g x
and g x = f x
```

Local variables are introduced using ```let x = init in body```:

```ocaml
let plus x y = x + y in 
plus 1 2
```

Local mutual recursive definitions use `let rec ... and ... in body`
syntax similar to top-level let-bindings.

(Structural) equality between two values can be tested with `=` and
disequality with `<>`. 

```ocaml
# 1 = 1 ;;
- : bool = true

# 1 <> 1 ;;
- : bool = false

# 1 <> 2 ;;
- : bool = true
```

### Primitive Types

The most important primitive types of OCaml are

Type | Examples | Description
---|---|---
`int` | 1, 2, 42, ... | 31-bit signed int on 32-bit processors, or 63-bit signed int on 64-bit processors
`int32` | Int32.zero, ... | 32-bit signed integer
`int64` | Int64.zero, ... | 64-bit signed integer
`float` | 1.0 3.4, ... | IEEE double-precision floating point, equivalent to C's `double`
`bool` | true, false | booleans
`char` | 'a', 'b', 'x' | 8-bit character
`string` | "Hello" | strings
`unit` | () | the empty tuple (like Scala's `Unit` type)

For each type, we also include some examples of constant literals that
have that type.

### Compound Types

More complex compound types are obtained from simpler types via *type
constructors*. Each type constructor is accompanied by one or more
*value constructors* that construct values of the corresponding
compound type from values of its composite types. We discuss the most
important type and value constructors in the following.

#### Function types

Function types are constructed using the *arrow* type constructor
`->`. The type `t1 -> t2` represents functions that take a value of
type `t1` and return a value of type `t2`. We have already seen the
accompanying value constructor for functions, which is lambda
abstraction `fun x -> body`. For example, the expression

```ocaml
`fun x -> x + 1`
```

has type `int -> int`. The arrow operator is right-associative. In
particular, the type `int -> int -> int` should be interpreted as `int
-> (int -> int)`, which is the type of a curried function. Example

```ocaml
# let plus x y = x + y ;;
val plus : int -> int -> int
```

#### Tuples and Lists

OCaml has inbuilt types for tuples and lists. Tuple types are
constructed using the *product* operator `*`. For example, the type
`t1 * t2` denotes pairs whose first component takes values of type
`t1` and whose second component takes values of type `t2`. The type
`t1 * t2` can thus be viewed as the Cartesian product of the sets of
values denoted by types `t1` and `t2`. Similarly, `t1 * t2 * t3`
denotes triples of values taken from `t1`, `t2`, and `t3`, etc. Note
that the types `t1 * t2 * t3`, `(t1 * t2) * t3`, and `t1 * (t2 * t3)`
are not equivalent.

Tuple values are constructed using the notation `(e1, ..., en)` where
`e1` to `en` are again expressions. Examples:

```ocaml
# (1, 2) ;;
- : int * int = (1, 2)

# (true, "hello) ;;
- : bool * string = (true, "hello)

# (1, true, 3)) ;;
- : int * bool * int) = (1, true, 3)

# (1, (true, 3)) ;;
- : int * (bool * int) = (1, (true, 3))

# let plus (x, y) = x + y ;;
val plus: int * int -> int
```

The type constructor `*` has higher precedence than `->`. So the type
`int * int -> int` denotes a function that takes a pair of integers
and returns again an integer (rather than a pair of an integer and a
function from integers to integers).

The components of a pair `(1, 2)` can be extracted using the
predefined functions `fst` and `snd` (these are similar to `car` and
`cdr` in Scheme):

```ocaml
# let p = (1, 2) ;;
val p : int * int = (1, 2)

# fst p ;;
- : int = 1

# snd p ;;
- : int = 2
```

For tuples of higher arity, use pattern matching to extract components
(see below). Note that the enclosing parenthesis around tuples can
often be omitted:

```ocaml
# let p = 1, 2 ;;
val p : int * int = (1, 2)
```

However, this feature of the syntax should be used carefully (see
below).

The type `t list` denotes a list whose elements belong to type `t`. As
in Scheme, lists are immutable. However, unlike in Scheme where lists
are *heterogeneous* (i.e., values of different types can be stored in
the same list), OCaml lists are *homogeneous* (all values stored in a
list must belong to the same type).

Similar to Scheme, list values are constructed from the empty list,
denoted `[]`, using the *cons* constructor, denoted `::`. The cons
constructor `::` is an infix operator and right-associative. Examples

```ocaml
# 1 :: 2 :: 3 :: []
- : int list = [1; 2; 3]

# "Hello" :: "World" :: []"
- : string list = ["Hello"; "World"]

# (1 :: []) :: (2 :: []) :: []
- : (int list) list = [[1]; [2]]
```

As the output of the pretty printer suggest, the notation `[e1;
...; en]` is syntactic sugar for the expression `e1 :: ... :: en :: []`.

**Caution**: The list elements are separated by semicolons and not
commas in the `[...]` syntax (which differs from other ML
dialects). In particular, be careful not to get confused when tuple
constructors with optional parenthesis appear inside list constructors:

```ocaml
# [1, 2, 3] ;;
- : (int * int * int) list = [(1, 2, 3)]
```

vs.

```ocaml
# [1; 2; 3] ;;
- : int list = [1; 2; 3]
```

The
[`List`](https://caml.inria.fr/pub/docs/manual-ocaml/libref/List.html)
module of the OCaml standard library provides common higher-order
functions on lists such as `map`, `fold_left`, `fold_right`, etc. It
also provides functions `hd` and `tl` that can be used to decompose an
non-empty list into its components. However, [pattern matching](#algebraic-datatypes-and-pattern-matching) is
usually the preferred way of decomposing lists.

Equality on lists and tuples is defined structurally:

```ocaml
# let l1 = [1; 2; 3] ;;
val l1 : int list = [1; 2; 3]

# let l2 = [2; 3] ;;
val l2 : int list = [2; 3]

# let l3 = 1 :: l2 ;;
val l3 : int list = [1; 2; 3]

# l1 = l3 ;;
-: bool = true
```

#### Parametric Polymorphism

One key feature of the type systems of languages in the ML family is
that they support *polymorphic types*. Polymorphic types are similar
to generics in languages like Java and Scala. However, they are
slightly different and we will discuss their relationship to generics
later.

More generally, *polymorphism* allows a single piece of code to work
with values of multiple types. The kind of polymorphism that languages
in the ML family support is referred to as *parametric polymorphism*
(or *let-polymorphism*). This can be viewed as allowing functions to
implicitly depend on type parameters. 

As an example, consider the identity function in OCaml:

```ocaml
let id x = x
```

Clearly, `id` does not depend on the specific type of the value passed
to `x` because the function simply returns `x` without doing anything
with it. In fact, OCaml allows `id` to be called with values of
different types:

```ocaml
# id 3 ;;
- : int = 3

# id "banana" ;;
- : string = "banana"
```

The type that the compiler infers for `id` is `'a -> 'a`. A type name
that is preceded with a quote symbol, like `'a`, is a *type
variable*. One typically reads type variables as if they were greek
letters, i.e., `'a` is pronounced "alpha", `'b` is pronounced "beta",
etc.

The type variables occurring in an OCaml type are implicitly
*universally quantified*. That is, the type `'a -> 'a` should be read
as describing functions that for every `'a` can take in a value of
type `'a` and return again a value of type `'a`. The function `id` is
implicitly parameterized by the type `'a` of its parameter `x`. 

The type tells us that for every call to `id`, the return value is
guaranteed to have the same type as the argument value that we pass
into `id`. Because type variables are implicitly quantified, they can
be alpha-renamed without changing the meaning of the type. For
instance, the types `'a -> 'a` and `'b -> 'b` are equivalent. On the
other hand, the types `'a -> 'b -> 'a` and `'a -> 'a -> 'a` are not
equivalent.  However, `'a -> 'b -> 'a` is compatible with the type `'a
-> 'a -> 'a`, but not the other way around - more on that later.

Parametric polymorphism allows us to write generic code while
retaining the benefits of static type checking and inference. 

For example, consider the following code snippet that uses the
function `id`:

```ocaml 
id 3 + 1 
``` 

The type checker can now infer that the addition operation will safely
execute at run-time. The reason is that it knows that `id` is called
with an `int` value (namely `3`) and the type of `id` then tells it
that the result value of `id` will again be of type `int`. Thus, the
addition operation will be applied to two `int` values and is
safe. Note that in order to do this reasoning, the type checker no
longer needs to inspect how `id` is exactly implemented. It only needs
to know its type, which it infers from the definition of `id` once and
for all.

For example, the following polymorphic function allows us to flip the
order of the arguments of an arbitrary curried function:

```ocaml
# let flip f x y = f y x ;;
val flip : ('a -> 'b -> 'c) -> 'b -> 'a -> 'c
```

That is `flip` takes a function of type `'a -> 'b -> 'c` and returns a
function of type `b -> 'a -> 'c`.

We will discuss below how the compiler infers this type from the
definition of `flip`.

#### Records

A *record* is a collection of named values called *fields* (think of
them as unordered tuples, whose components have names). Records are
similar to `struct` types in C and C++ and closely related to object
types in object-oriented languages.

Record types take the form `{f1: t1; ...; fn: tn}` where the `fi` are
field names and the `ti` are the types of the fields. The value
constructor of records takes a similar form: `{f1 =
e1; ...; fn = en}`. This expression creates a record value with fields
`f1` to `fn` initialized with the values that `e1` to `en` evaluate
to. Note that record types must be declared explicitly with a type
definition before they can be used. Example:

```ocaml
# type person = 
  { first_name: string;
    last_name: string;
    age: int
  } ;;

# let jane = 
    { first_name = "Jane"; 
      last_name = "Doe"
    } ;;
val jane : person = {first_name = "Jane"; last_name = "Doe"}
```

We can access a field of `f` of a record `x` by using the notation
`x.f`:

```ocaml
# jane.last_name ;;
- : string = "Doe"
```

Record fields are immutable by default (they can be made mutable by
prepending the field name in the record type definition with the
keyword `mutable`. When working with immutable records, instead of
"modifying" the value of a record is done by creating a copy of the
record and changing the value of the field to be modified to its new
value. The old record value thus remains unaffected. OCaml provides a
convenient syntax for copying record fields:

```ocaml
# let john = { jane with first_name = "John } ;;
val john : person = {first_name = "John"; last_name = "Doe"}

# john.first_name ;;
- : string = "John"

# jane.first_name ;;
- : string = "Jane"
```

