> Goals:
> * introduce the REPL
> * show standard types

The simplest possible expression in Scala is a literal value:

```scala
1

"foobar"

true

(1, false, "foobar")

()
```

---

> Goals:
> * introduce `var` and `val`

It's often useful to give names to values:

```scala
var i: Int = 0
val j: Int = 1
```

The difference between `val` and `var` is mutability:
* `val` is a value and therefore immutable
* `var` is a variable whose value can change

```scala
// Compiles
i = 1

// Doesn't compile
j = 2
```

---

> Goals:
> * introduce type inference

Note that the syntax can be simplified thanks to type inference:

```scala
var i = 0
val j = 1
```

> We don't yet know about `public` / `private`, so it's a bit early to talk about best practices.
> But we can certainly say that type ascriptions are useful for the reader, when the type is not obvious.

---

> Goals:
> * make sure people don't mistake type inference for dynamic typing. People do.

It's important to realise that even though `i` and `j` don't have explicit type ascriptions, they still have a type, checked at compile time.

```scala
var i = 0

// Doesn't compile
i = "foo"
```

---

> Goals:
> * make attendants think about what they just learned
> * introduce the notion of strict evaluation
> * this is actually hard to explain properly, be ready to step in if attendants start getting confused

MCQ: what is the value of `j` after:

```scala
var i = 0
val j = i

i = 2
```

* `0`
* `2`

---

Answer: `0`

In Scala, evaluation is strict by default - when `j` takes the value `i`, `i` is evaluated.

> Take the opportunity to talk about expressions & evaluation. In Scala, everything (that is not an import or a declaration) is an expression, and reduces to a value.
> Evaluating an expression means turning it into a value (simplification, but valid in Scala (I think?))

---

> Goals:
> * dispell any remaining misconception people might have on how `var` works

MCQ: what is the value of `j` after:

```scala
var i = 0
var j = i

i = 2
```

* `0`
* `2`

---

Answer: still `0`, the fact that `j` is mutable doesn't change evaluation rules.

---

> Goals:
> * introduce lazy evaluation
> * introduce just a bit more syntax (`{ ... }` for blocks, sneak in `println`)

_Strict by default_, though. It's possible to make a value _lazy_, which means it won't be evaluated until it's actually needed:

```scala
lazy val test = {
  println("evaluated")
  1
}

test

// This second one is necessary to show that test is only evaluated once
test
```

---

> Goals:
> * make people think about lazy evaluation

MCQ: What is the value of `j` after:

```scala
var i = 0
lazy val j = i

i = 2
```

* `0`
* `2`

---

Answer: `2`, since `j` will not be evaluated until needed.

---

> Goals:
> * introduce a few common `Int` operators

You can combine values using common operators. Arithmetic ones, for `Int`s:

MCQ: what does the following yield:
```scala
1 + 2 * 3 - 6 / 2
```

* `1`
* `1.5`
* `4`

---

Answer: `4`, because of operator precedence.

> The point is to suss out people who don't know about operator precedence. They exist.

Precedence (and associativity) rules can be found at https://www.scala-lang.org/files/archive/spec/2.11/06-expressions.html#infix-operations .

---

> Goals:
> * well, one must show string concatenation. It's expected


```scala
"foo" + "bar"
```

---

> Goals:
> * show a few boolean operators, they'll come in handy later

For `Boolean`s:

```scala
true || false
!true
false == true
false != true
```

> If you're in a playful mood, ask if anybody can guess how to do a XOR between two booleans. A lot of people don't realise that's `!=`.
> And yes, `^` is also a valid answer, but where's the fun in that.

---

> Goals:
> * show if / then / else

Some languages have a ternary boolean operator. In Scala, that's `if / then / else`:

```scala
if(true) println("was true")
else println("was false")
```

---

> Goals:
> * get people thinking about expressions again

Exercise: can you rewrite that so that `println` is only used once?

---

Solution:

```scala
println(if(true) "was true" else "was false")
```

> If you're feeling fancy, you could also use the opportunity to introduce quasiquotes: `println(s"was $b")`

---

> Goals:
> * introduce method syntax
> * prove that Scala has too much syntax?

In Scala, operators are just unary methods with syntactic sugar.

> Now might be a good time to make sure everybody knows what a method is.

MCQ: what does the following yield?

```scala
1.+(2).*(3).-(6)./(2)
```

---

* `1`
* `1.5`
* `4`

---

Answer: `1`, because:
* methods don't have priority, they're evaluated left to right
* without explicit type ascriptions, integer literals have type `Int`, and division for `Int`s yields `Int`s.

> That second point is important, as it might be used to prove that type ascriptions are useful:
> `1 / (2: Float)` yields 0.5F, sometimes the type infered by the compiler is not the one you want.
> You can also use it to introduce more literal syntax, `2F`, `2D`... although that's not terribly important.

---

> Goals:
> * show how to define a simple method

Here's how you define a method:

```
def add1(i: Int): Int = i + 1
```

> This bit is slightly shaky - if anyone asks how to use `add1` as an infix operator, you're kind of screwed.
> The best I've got is _we'll need to introduce classes before I can show you that_.

---

> Goals:
> * show that type inference doesn't just work for values

This can be simplified thanks to type inference:

```scala
def add1(i: Int) = i + 1
```

---

> Goals:
> * we've been talking about unary methods so far. Show that other arities are supported

Methods can of course take multiple parameters:

```scala
def multiplyAndAdd1(i: Int, j: Int) = (i * j) + 1
```

> This is a bit heavy handed, but some people, having read a few blog posts, might assume that methods are curried by default.


---

> Goals:
> * introduce functions as values

Methods should not be confused with functions, which also exist in Scala:

```scala
val add1: Int ⇒ Int = i ⇒ i + 1
```

Note that `add1` here is a value. Functions are values!


> TODO: THIS IS A BIT SHAKY. HOW CAN I SAY FUNCTIONS ARE DIFFERENT FROM VALUES, BUT NOT SHOW IT?

> It's possible to point out that method and values are fundamentaly different, but we don't have the tools to prove it yet
> * functions are monomorphic, but we don't know that methods aren't
> * functions don't take implicit parameters, but we don't know what that is
> * methods aren't values, but it's hard to demonstrate because of eta-expansion

---

> Goals:
> * get people to work with functions as values. At this point, mostly realise that they can stick a `A ⇒ B` wherever a value type is expected

Since functions are values, we can pass them as parameters to methods.

Exercise: write a `applyAndAdd1` method that takes 2 arguments, one `Int ⇒ Int` and one `Int`, such that:

```scala
applyAndAdd1(i ⇒ i * 2, 3)
// 7
```

> One *could* introduce the `_ * 2` notation here. I think it does more harm than good this early on.

---

Solution:

```scala
def applyAndAdd1(f: Int ⇒ Int, i: Int) = f(i) + 1
```

---

> Goals:
> * same as previous section - *everywhere* a value is expected. An `A ⇒ B` *is* a value

Functions can also be returned by methods.

Exercise: write a function `addN` that takes an `Int` and returns a function that takes and `Int` and return the sum of the 2

```scala
addN(10)(1)
// 11
```

---
Solution:

```
def addN(n: Int): Int ⇒ Int = i ⇒ i + n
```

> Hopefully someone will come up with `def addN(n: Int)(i: Int)`. If not, ask if anybody tried that.
> It's not a good answer for the question (`addN` does not match the requested type signature) but the example has been crafted to suggest that.

---

> Goals:
> * introduce multiple parameter lists

It's possible to declare methods with multiple parameter lists:

```scala
def addN(n: Int)(i: Int): Int = n + i
```

In Scala, this is called currying.

> But this is not strictly accurate, since currying is turning one n-ary function into n unary functions.

---

> Goals:
> Partial application

This, however, doesn't work exactly as you'd expect:

```scala
addN(10) // fails to compile
```

It needs to be _partially applied_, which has special syntax for methods:

```scala
val add10 = addN(10) _
```

Partial application is a way of not providing a method with all its parameters: turning it into a function that ask for the missing parameter(s) before yielding the desired result.

---

> Goals:
> * Partial application, but for functions

Exercice: can you think of a way to do something similar for functions?
Think about what it would mean - a function that takes an `Int` and return a function that takes an `Int` and returns an `Int`.

---

Solution:

```scala
val addN: Int ⇒ Int ⇒ Int = n ⇒ i ⇒ n + i
```

> Point out that partial application is much more direct for functions

---

> Goals:
> * learn about default parameters

Another way of not providing a method with all its parameters is default parameters:

```scala
def foo(i: Int = 1) = i * 2

foo()
// 2

foo(2)
// 4
```

---

> Goals:
> * show why we need named parameters

This can be confusing when you have multiple parameters.

MCQ: what does the following yield?

```scala
def foo(i: Int = 1, j: Int = 2) = i + j

foo(10)
```

* `12`
* `11`

---

Answer: who knows? I had to run it, I wasn't sure. But it's `12`.

---

> Goals:
> * introduce named parameters

To remove the ambiguity, we can use named parameters.

MCQ: what does the following yield?

```
foo(i = 10)
```

* `12`
* `11`

---

Answer: 12, because now it's clear that `i` is 10 and `j` is its default value.

---

> Goals:
> * wrap up named parameters

Note that while named parameters are more often used in conjunction with default values, they don't *have* to:

```scala
def divide(i: Int, j: Int) = i / j

divide(j = 3, i = 33)
// 11
```

---

> Goals:
> * motivate by-name parameters
> * motivate parametric polymorphism

We've talked a lot about methods. Let's define a useful one.

Exercise: implement `if / then /else` as a method, such that:

```scala
ifElse(true)(1)(2)
// 1

ifElse(false)(1)(2)
// 2
```

---

Solution:
```scala
def ifElse(cond: Boolean)(a: Int)(b: Int): Int =
  if(cond) a
  else     b
```

This implementation is the best we know how to do, but it's flawed. There are two major flaws, can anyone spot them?

> `a` and `b` are both evaluted
> this is monomorphic

---

> Goals:
> * introduce by-name parameters

Let's first fix the unnecessary evaluation problem.

> With a responsive audience, you can ask if anyone can think of a way to do that. Someone's likely to bring up `lazy`.

Scala supports by-name parameters, which is similar to lazy evaluation for parameters, but not exactly the same thing:

```scala
def add1(i: ⇒ Int): Int = {
  println("before evaluation")
  val ret = i + 1
  println("after evaluation")
  ret
}

add1 {
  println("evaluated")
  1
}
```

---

> Goals:
> * explain why by-name is not strictly equivalent to `lazy`

Careful though, by-name parameters are not lazy - they're re-evaluated whenever needed.

MCQ: what does the following print?

```scala
def time2(a: ⇒ Int) = a + a

time2 {
  println("evaluated")
  1
}
```

* `evaluated`, once
* `evaluated`, twice

---

Answer: `evaluated`, twice. `a` is needed twice and thus evaluated twice.

---

> Goals:
> * use by-name parameters

Exercise: knowing what you now know about by-name parameters, rewrite `ifElse`

---

Solution:

```scala
def ifElse(cond: Boolean)(a: ⇒ Int)(b: ⇒ Int): Int =
  if(cond) a
  else     b
```

---

> Goals:
> * introduce parametric polymorphism
> * introduce `???`

Let's now fix the fact that we only work with `Int`s.

> With a responsive audience, ask if anyone has an idea. People know about parametric polymorphism.

Scala supports parametric polymorphism:

```scala
def foo[A](a: A) = ???
```

---

> Goals:
> * start people thinking on how constraints are *good*

Open question: what are the possible implementations of `foo`?

---

Answer: with our current knowledge, 1: `def foo[A](a: A): A = a`, or `identity`

> Other (pathological) implementations:
> * throwing
> * runtime reflexivity
> * some other demented patterns

---

> Goals:
> * use parametric polymorphism

Exercise: re-implement `ifElse` to be polymorphic

---

Solution:

```scala
def ifElse[A](cond: Boolean)(a: ⇒ A)(b: ⇒ A): A =
  if(cond) a
  else     b
```

---

> Goals:
> * further practice with the various things we've seen
> * introduce recursion if anybody's not familiar with it

You've re-implemented `if / then / else`.

Exercise: can you re-implement a `while` loop?

```scala
var i = 0

myWhile(i < 10) {
  println(i)
  i += 1
}
```

---

Solution:

```scala
def myWhile(cond: ⇒ Boolean)(f: ⇒ Unit): Unit =
  if(cond) {
    f
    myWhile(cond)(f)
  }
```

---

> Goals:
> * introduce `while`

There is a native `while` loop in Scala:

```scala
var i = 0

while(i < 10) {
  println(i)
  i += 1
}
```

---

> Goals:
> * introduce `do ... while`

Similarly, Scala supports `do .. while`:

```scala
var i = 0

do {
  println(i)
  i += 1
} while(i < 10)
```

---

> Goals:
> * introduce for loops

Scala also has for loops:

```scala
for(i ← 0 until 10) println(i)
```

> Don't forget to name things. Explain a generator.

> Point out that `until` is not a magical keyword but a method on `Int`.

---

> Goals:
> * multiple generators

You can have multiple generators:

```scala
for {
  i ← 1 to 5
  j ← 1 to 2
} println(i * j)
```

> Using `to` rather than `until` is done on purpose, to slowly start building up a vocabulary of combinators.

---

> Goals:
> * guards

It's also possible to filter on specific values:

```scala
for {
  i ← 1 to 5 if i % 2 == 0
  j ← 1 to 2 if j % 2 != 0
} println(i * j)
```

Question: does this have a return value? If so, what? if not, why not?

---

Answer: `()`, because everything is an expression and must evaluate to something.

---

> Goals:
> * for-comprehensions

It's possible to evaluate to something else than `()`:

```scala
for {
  i ← 1 to 5 if i % 2 == 0
  j ← 1 to 2 if j % 2 != 0
} yield i * j
```

This is called a for-comprehension, and is much, much more common than for loops.
We'll have more to say on this in a later session.


---

Exercises:
* https://www.scala-exercises.org/scala_tutorial/terms_and_types
* https://www.scala-exercises.org/scala_tutorial/definitions_and_evaluation
* https://www.scala-exercises.org/scala_tutorial/lexical_scopes
* https://www.scala-exercises.org/scala_tutorial/higher_order_functions

More advanced, if you want to go further:
* the rest of https://www.scala-exercises.org/scala_tutorial
* https://www.scala-exercises.org/scala_tutorial
