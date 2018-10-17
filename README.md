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
called a *type error* or *type clash*.

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

* better error checking: type errors are detected earlier, compiler
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
increases, these advantages turn into disadvantages because type
errors become harder to detect and debug. Hence, statically typed
languages are often preferred for large and complex projects.

The distinction between weak/strong and static/dynamic is not always
clear cut. For instance, Java is mostly statically typed but certain
type checks are performed at run-time due to loopholes in the static
type checkers. Some so-called *gradually typed* languages even allow
the programmer to choose between static and dynamic type checking for
different components of the same program. Examples include Dart and
TypeScript.

Similarly, the distinction between weak and strong type systems is not
always completely clear and some people use slightly different
definitions of what strong vs. weak typing means. For instance, the
notion of type compatibility in JavaScript is so permissive that the
language is often considered to be weakly typed even though, according
to the definition given above, JavaScript is strongly typed.

### Type Inference



### Type equivalence

### Type compatibility

We will study these concepts in more depth in the context of the OCaml
language and later when we revisit Scala as an example of an
object-oriented language.

## Introduction to OCaml 

OCaml belongs to the ML family of languages. ML stands for *Meta
Language*, which was originally developed by Robin Milner in the early
1970s for writing theorem provers, i.e., programs that can
automatically find and check the proofs of mathematical theorems. ML
took many inspirations from Lisp but introduced several new language
features intended to make programming less error prone, in particular
a powerful and very well designed static type system.

Languages that belong to the ML family share the following features:

* clean syntax (few parenthesis)
* functions are first-class values
* declarations use static scoping and deep binding semantics
* function calls use call-by-value semantics (call-by-name can be
  simulated using closures)
* automated memory management via garbage collection
* strong and static typing
* powerful type system
  * parametric polymorphism (somewhat like generics)
  * structural equivalence of types
  * all with automated static type inference!
* advanced module system with higher-order modules (*functors*)
* exceptions
* miscellaneous features:
  * user-definable algebraic data types and pattern matching
  * imperative features: mutable arrays, references, and record fields

Popular implementations and dialects of ML include

* Standard ML of New Jersey (SML/NJ)
* Poly/ML
* MLton
* OCaml
* F\# (Microsoft; derived from OCaml)
* Reason (Facebook; derived from and implemented in OCaml)

The design of the Haskell language was also strongly influenced by ML.

We will focus on the OCaml language. OCaml is a general purpose
programming language that was initially developed in the late 1990s by
a team of French computer scientists. In addition to the features
shared by all languages in the ML family, OCaml also provides:

* a compiler that produces efficient native machine code for many
  architectures
  
* a class system that enables object-oriented programming

We will mostly ignore OCaml's class system and imperative features and
focus on the functional core of the language.

### Installation, Build Tools, and IDEs

Most Linux distributions as well as homebrew on Mac OS come with
precompiled packages for OCaml. There is also a Windows
installer. However, I suggest to install OCaml
using [opam](https://opam.ocaml.org/), which is a package manager for
OCaml and provides many other useful tools for developing OCaml
programs. For the installation instructions
see [here](https://opam.ocaml.org/doc/Install.html).

Once you have opam installed, you can install and set up the most
recent version of OCaml by executing the following commands in a
terminal:

```bash
opam switch 4.07.0
eval $(opam config env)
```

The installation will take a while since opam will download the
sources of the OCaml compiler and compile them from scratch. Once, the
installation has completed, you can execute `ocaml`, which should
start an OCaml REPL session.

If you want to evaluate an OCaml expression in the REPL, you need to
terminate it by a double semicolon `;;` and then press `Enter`:

```ocaml
        OCaml version 4.07.0

# 3 + 1;;
- : int = 4

# let x = 3 + 1;;
val x : int = 4

# #quit;;
```

Double semicolons are not needed in OCaml source code files
that are processed by the compiler.

In addition to the OCaml compiler and runtime, you also want to
install the OCaml library
manager
[ocamlfind](http://projects.camlcity.org/projects/findlib.html) and
build tool [ocamlbuild](https://github.com/ocaml/ocamlbuild/) which
you can do via opam:

```bash
opam install -y ocamlfind
opam install -y ocamlbuild
```

These tools provide similar functionality as `sbt` does for Scala.

Several IDEs have plugins for OCaml. I suggest
using [Merlin](https://github.com/ocaml/merlin) which can be used
directly with common editors like Emacs and Vim, and can also be
integrated into other editors and IDEs via third-party plugins,
including Atom, Sublime, and Visual Studio Code. For installation
instructions via opam and further details, please see
Merlin's [project website](https://github.com/ocaml/merlin).

### Syntax

OCaml expressions are constructed from inbuilt constants (numbers,
booleans, etc.) and inbuilt operators (arithmetic and logical
operators, etc.) using lambda abstraction and function
application. Inbuilt binary operators use infix notation and follow
standard rules for operator precedence and associativity. Function
application has higher precedence than any other infix operator and is
left associative, just like in the lambda
calculus. See
[here](https://caml.inria.fr/pub/docs/manual-ocaml/expr.html) for more
details about OCaml expression syntax. In the following, we discuss
the most important constructs. More in-depth OCaml tutorials can be
found [here](https://ocaml.org/learn/tutorials/).

### Basics

OCaml programs are structured into modules, which can be nested. Every
source code file automatically defines a module. We will discuss the
module system later and for now assume that we only work with a single
module.

Top-level definitions in a module are introduces using `let x = init`
which binds `x` to the value obtained from evaluating the expression
`init`. The scope of the let-binding is the remainder of the module
that follows the definition (with nested let-bindings of `x` yielding
holes in the scope as usual). The scope of the binding for `x`
introduced by `let` excludes the definition expression `init`.

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

Recursive definitions are introduces using `let rec x = init`. Here
the scope of the binding includes the expression `init`.

```ocaml
let rec fac x = 
  if x = 0 
  then 1 
  else x * fac (x + 1)
```

If multiple definitions have recursive dependencies, they can be
combined with `and`:

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
inequality with `<>`. 

```ocaml
# 1 = 1 ;;
- : bool = true

# 1 <> 1 ;;
- : bool = false

# 1 <> 2 ;;
- : bool = true
```

### Primitive Types

The primitive types of OCaml are

Type | Examples | Description
---|---|---
`int` | 1, 2, 42, ... | 31-bit signed int on 32-bit processors, or 63-bit signed int on 64-bit processors
`int32` | Int32.zero, ... | 32-bit signed integer
`int64` | Int64.zero, ... | 64-bit signed integer
`float` | 1.0 3.4, ... | IEEE double-precision floating point, equivalent to C's `double`
`bool` | true, false | booleans
`char` | 'a', 'b', 'x' | 8-bit character
`string` | "Hello" | strings
`unit` | () | the empty tuple (like Scala's `Unit`)

For each type, we also include some examples of constant literals that
have that type.

### Compound Types

More complex compound types are obtained from simpler types via *type
constructors*. Each type constructor is partnered with one or more
*value constructor* that constructs a value of the corresponding
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
# let plus x y = x + y;;
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

The components of a pair `(1, 2)` can be extracted using predefined
functions `fst` and `snd` (similar to `car` and `cdr` in Scheme):

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

However, this feature should be used carefully (see below).

The type `t list` denotes a list whose elements belong to type `t`. As
in Scheme, lists in OCaml are immutable. However, unlike in Scheme
where lists are *heterogeneous* (i.e., elements of different types can
be stored in the same list), OCaml lists are *homogeneous* (all
elements of a list must belong to the same type `t`).

Similar to Scheme, list values are constructed from the empty list,
denoted `[]`, using the *cons* constructor, denoted `::`. Note that
the operator `::` is right-associative. Examples

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
non-empty list into its components. However, pattern matching is
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

#### Algebraic Datatypes and Pattern Matching

Note that both tuples and lists are just special cases of algebraic
data types. In particular, we can use list and tuple constructors in
patterns for pattern matching like any other constructor of an
algebraic data type:

Example: reversing a list

```ocaml
let reverse xs = 
  let rec reverse_helper rev_xs = function
  | hd :: tl -> reverse_helper (hd :: rev_xs) tl
  | [] -> rev_xs
  in reverse_helper [] xs
  
# reverse [1; 2; 3];;
- : int list = [3; 2; 1]
```

Having tuples and lists as types that are built into the language is
just a matter of convenience. One could equally treat them as
user-defined types that are implemented in a library. Here, is a
user-defined version of the type `'a list`:

```ocaml
type 'a mylist = 
  | Nil
  | Cons of 'a * 'a mylist
```

```ocaml
# let reverse xs = 
  let rec reverse_helper rev_xs = function
  | Cons (hd, tl) -> reverse_helper (Cons (hd, rev_xs)) tl
  | Nil -> rev_xs
  in reverse_helper Nil xs 
;;
val reverse: 'a mylist -> 'a mylist

# reverse (Cons (1, Cons (2, Cons (3, Nil))));;
- : int mylist = Cons (3, Cons (2, Cons (1, Nil)))
```

#### Records

A *record* is a collection of named values called *fields*. 



### Type Equivalence and Compatibility

### Type Inference



