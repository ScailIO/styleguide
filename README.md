Scail Scala Style Guide
=======================

Introduction
------------

The Scail Scala Style Guide contains recommendations for formatting conventions,
coding style, and best practices to be used in all Scail projects.
It is based on vast experience gathered across numerous Scala projects.

### Organization

The guide is organized as follows:

0. The first section, "[Code Formatting](#code-formatting)", covers only lexical
(layout) code conventions, i.e., guidelines that do not alter the semantic
meaning of a program:
    1. The section itself highlights the main points of the official
    [Scala Style Guide].
    Reading the official Scala guide itself is required.
    1. The subsection
    "[Additions and Deviations from the Official Style Guide](#additions-and-deviations-from-the-official-style-guide)"
    contains the points where we differ from the official guide plus a few items
    not covered by it.
    To ease on-boarding and favor consistency, we try to deviate as little as
    possible from the official guide.
0. The second section, "[Best Practices](#best-practices)" covers rules that may
change the semantic meaning of the code:
    1. "[Additional recommendations](#additional-recommendations)" are, for the
    most part, required rules that must be followed unless there is a very good
    reason not to.
    Failure to obey these conventions may **introduce errors**, degrade
    performance, or create maintenance headaches.
    1. "[Tips & Tricks](#tips--tricks)" are mostly friendly reminders that may
    not apply in all situations.
    Always keep them in mind and use your best judgment.
    1. "[Additional Remarks](#additional-remarks)" contains general hints that
    are always helpful to remember.

All Scail projects make extensive use of
[Static Analysis Tools](#static-analysis-tools) to enforce as many
best practices as is currently allowed.
Use the provided [EditorConfig](http://editorconfig.org) profile to
[configure your editor](resources/.editorconfig).
Also see [Recommended IntelliJ Settings](#recommended-intellij-settings).

Code Formatting
---------------

Please read the [Scala Style Guide] carefully. The main points to consider are:

0. Use **two-space** indentation. No tabs.

0. Omit unnecessary blocks to reduce excessive nesting:

    ```scala
    if (condition) {
      bad()
    }

    if (condition) good()
    ```

0. Avoid wrapping lines.
Split long lines into multiple expressions,
assigning intermediate results to `val`s.

0. Use lower camel case for `valName`, `varName`, `methodName`,
`functionObject`, `packageObject`, and `annotationName`.

0. Use upper camel case for `ConstantName`, `EnumerationValue`, `ClassName`,
`TraitName`, `ObjectName`, `TypeParameter`.

0. No `UPPERCASE_UNDERSCORE`, not even for constants or type parameters.

0. No `get`/`set` prefixes for accessors and mutators.

0. Always use empty parentheses when, and only when, declaring or calling
methods with side effects.
That includes using factory methods like `Seq.empty` instead of `Seq()`.

0. Unless an established convention already exists (e.g, mathematical symbols),
avoid symbolic method names (*"operators"*).

0. Use type inference where possible.
But put clarity first and favor explicitness when an inferred type may not be
obvious.

    ```scala
    val good = 1 // Int, obviously

    val bad = config.get("key") // What does it return?
    val better: Option[String] = config.get("key")
    ```

0. Opening curly braces (`{`) must be on the same line as the declaration.

0. Constructors should be declared all on one line.
If not possible, put *each* constructor argument on its own line.

0. Class extensions follow the same rule:
either all on one line or each on its own line.

0. Favor short, single-expression, single-line method bodies.

0. No procedure syntax.

    ```scala
    def bad() { ??? }
    def worse { ??? }

    def good(): Unit = { ??? }
    ```

0. Postfix operator notation is unsafe and shall not be used.
Consider it deprecated.
Never import `scala.language.postfixOps`.

    ```scala
    val bad = seq mkString

    val good = seq.mkString
    ```

0. Always use infix notation for methods with symbolic names or higher-order
functions (`map`, `foreach`, etc.).

    ```scala
    val bad = seq.map(_ * 2)

    val good = seq map (_ * 2)
    ```

    When the syntax does not allow it, stick with traditional method invocation
    syntax:

    ```scala
    val bad = (seq map f).toSet // Starts to read like LISP

    val good = seq.map(f).toSet
    ```

### Additions and Deviations from the Official Style Guide

0. Text file format: UTF-8, no BOM, Unix line ending (LF, '\n'), newline at EOF.

0. 100 characters maximum line length.

0. No trailing whitespace at the end of lines,
they cause problems when diffing between files or between versions.
Configure your text editor to do this automatically for you.

0. One blank line between method, class, and object definitions.

0. Use blank lines between statements and declarations to create logical
groupings.

0. No double blank lines, anywhere.

0. No double (four-space) indent, including class constructors,
method declarations, and multi-line statements (wrapped long lines).

    ```scala
    val bad = fourSpace &&
        doubleIndent

    val good = multiLineExpression +
      twoSpace + singleIndent

    class Platypus(
      name: String
    , age: Int
    ) extends Beaver
      with Duck
    ```

0. Acronyms follow camel case rules: `Http`, `Json`, `Xml`, `Db`, `Io`.

0. In general, obey English rules and mathematical conventions for punctuation:
    1. Single spaces around `=>`, `=`, `<-`, `{`, `-`, `+`, `}`, `)`, `*`, etc.
    1. A single space after and no space before `:`, `,`, `)`, `;`, etc.
    1. A single space before `(`, except for method invocation or declaration.
    1. No space after `(` and `[`.
    1. No space before `[` and `]`.
    1. No spaces between consecutive braces, brackets, or parentheses.

0. Use [comma-first](http://ajaxian.com/archives/is-there-something-to-the-crazy-comma-first-style)
style.

0. Do not align vertically: it demands constant realignment,
making diffs longer and code reviews more difficult.

0. Methods must always have explicit return types.
Explicitly declaring the return type allows the compiler to verify correctness.

0. Modifiers should be declared in the following order: `override`, `abstract`,
`private` or `protected`, `final`, `sealed`, `implicit`, `lazy`.

0. Put imports at the top of the file.
Imports should be grouped from most to least specific:
    1. The project's own classes
    1. Frameworks and libraries: `com._`, `net._`, `org._`, `play._`, etc.
    1. `scala._`
    1. `java._` and `javax._`

    (Mnemonic: your project is built on top of frameworks and libraries,
    which are built on top of Scala, which itself is built on top of Java.)

    Inside each group, packages and classes must be sorted alphabetically.
    Separate groups with blank lines:

    ```scala
    import myapp.util.StringUtils

    import org.joda.time.DateTime

    import play.api.Configuration

    import scala.concurrent.Future

    import java.net.URI
    ```

0. Do not use relative imports.
Full imports are easier to search, and never ambiguous:

    ```scala
    package foo.bar

    // Bad
    import baz.Qux

    // Good
    import foo.bar.baz.Qux
    ```

0. One import per line, as in Java.

0. Do not wildcard-import entire packages: `import bad._`

0. The bigger the scope, the more descriptive the name.
Only for very small, local scopes may single-letter mnemonics be used.

0. Use `_` for simple, single line functions:

    ```scala
    val bad = seq filter (number => number % 2 == 0)

    val good = seq filter (_ % 2 == 0)
    ```

0. Omit the `_` for functions that take a single argument:

    ```scala
    bad foreach println(_)

    good foreach println
    ```

0. Use infix notation for single argument methods on monadic types
(`contains`, `getOrElse`, etc.)

0. For single-line functions and for-comprehensions, use parentheses.
For multi-line ones, use brackets:

    ```scala
    for (i <- 1 to 3) println(i)

    seq map (_ * 2)

    seq map { a =>
      if (a < 0) -a
      else a
    }
    ```

0. Whenever possible, simplify pattern matching expressions by omitting the
`match` keyword and using partial functions:

    ```scala
    bad map { x =>
      x match {
        case 1 => "one"
        case _ => "not one"
      }
    }

    good map {
      case 1 => "one"
      case _ => "not one"
    }
    ```

0. When passing functions, do not use inner block syntax:

    ```scala
    (bad => {
      ???
    })

    { good =>
      ???
    }
    ```

0. For documentation comments, use Javadoc left-hand margin convention instead
of Scaladoc style:

    ```scala
    /** Bad
      * convention
      */

    /**
     * Good
     */
    ```

0. **Optimize for readability.**
Readability trumps consistency (but not by much).
Consistency trumps everything else.

Best Practices
--------------

### Additional recommendations

0. Do not abuse "suppress warning" annotations.

    Although in rare, exceptional situations there may be legitimate uses,
    mindlessly suppressing warnings defeats the purpose of static analysis.
    Take a warning as an opportunity to rethink your design and refactor
    as needed for improvements.
    Only after careful consideration contemplate the need to suppress it.

0. For unit tests, use
[DiagrammedAssertions](http://doc.scalatest.org/3.0.0/index.html#org.scalatest.DiagrammedAssertions).
Do not use, import, or mix in matchers.

0. Avoid [pattern matching Options](http://blog.originate.com/blog/2014/06/15/idiomatic-scala-your-options-do-not-match).
Use the [Scala Option Cheat Sheet](https://gist.github.com/marconilanna/66c65c548ed879d875364c0f0cd8778a)
as reference.

0. Never use `return`:
[http://tpolecat.github.io/2014/05/09/return.html](http://tpolecat.github.io/2014/05/09/return.html)

0. Parentheses in Scala are not *optional*.
Be aware of extraneous parentheses:

    ```scala
    val bad = Set.empty() // expands to Set.empty.apply(), evaluates to false

    val evil = Seq(1,2,3).toSet() // same as above
    ```

0. Use the most generic collection type possible, typically one of:
`Iterable[T]`, `Seq[T]`, `Set[T]`, or `Map[T]`.
(Program to an interface, not an implementation.)

0. Use `Seq[T]`, not `List[T]`
([http://stackoverflow.com/a/10866807/410286](http://stackoverflow.com/a/10866807/410286))
except where you specifically need to force one implementation over another.
The most common exception is that Play form mappers require `List[T]`,
so you have to use it there.
`Seq` is the interface while `List` the implementation,
analogous to `Map` and `HashMap` in Java.

0. When possible, use `scala.collection.breakOut` to avoid producing and
converting intermediate collections with `.toMap`, `.toSeq`, `.toSet`, etc.

    ```scala
    val cities = Seq("Toronto", "New York", "San Francisco")

    // Produces an intermediate Seq[(String, Int)], converts to Map[String, Int]
    val bad = cities.map(s => (s, s.length)).toMap

    // No intermediate values or conversions involved
    val good: Map[String, Int] = cities.map(s => (s, s.length))(breakOut)
    ```

    Please note that the type annotation is required or `breakOut` will not be
    able to infer and use the proper builder.
    To know when `breakOut` can be used, check in the Scaladoc if the
    higher-order function (`map` in the example above) takes an implicit
    `CanBuildFrom` parameter.
    Notice that `breakOut` is being passed in lieu of the implicit parameter.

0. Do not overuse tuples, decompose them or, better, use case classes:

    ```scala
    val paul = Some(("Paul", 42))

    // Bad
    paul map (p => s"Name: ${p._1}, Age: ${p._2}")

    // Good
    paul map { case (name, age) =>
      s"Name: $name, Age: $age"
    }

    // Better
    case class Person(name: String, age: Int)

    val paul = Some(Person("Paul", 42))

    paul map (p => s"Name: ${p.name}, Age: ${p.age}")
    ```

0. Do not overdo method chaining, use `val`s to name intermediate steps:

    ```scala
    val bad = xs.filter(...).map(...).exists(...)

    val filteredXs = xs filter (...)
    val ys = filteredXs map xToY
    val answer = ys exists (...)
    ```

0. Avoid defining new implicit conversions (`implicit def`).
Instead, use the safer and more efficient implicit value classes
([extension methods](http://docs.scala-lang.org/overviews/core/value-classes.html#extension-methods)):

    ```scala
    class Bad(n: Int) {
      def stars = "*" * n
    }
    implicit def bad(n: Int) = new Bad(n)

    implicit class Good(val n: Int) extends AnyVal {
      def stars = "*" * n
    }
    ```

0. Whenever possible, avoid matching on `case _`.
Try to be specific and thorough, favor exhaustive matches.
Do not let unforeseen conditions silently fall through.

0. Never use `null`, use `Option` instead.
If dealing with legacy APIs, wrap possible `null`s:

    ```scala
    Option(javaClass.returnsNull())

    javaClass.takesNull(option.orNull)
    ```

0. Avoid `Some.apply`, use `Option.apply` instead.
`Option.apply` protects against `null`, while `Some.apply` is perfectly happy to
return `Some(null)`, eventually raising an unexpected null pointer exception.
Also note that `Some.apply` is type-inferred as `Some[T]`, not `Option[T]`.

    ```scala
    val bad = Some(System.getenv("a")) // NPE waiting to happen

    val good = Option(System.getenv("a")) // Would return None
    ```

    By the principle of least astonishment, use `Option.apply` even if you
    "know" your reference can never be null.
    By being consistent, we avoid wondering whether a `Some.apply` was
    overlooked by the developer or not:

    ```scala
    val bad = Some(math.random())

    val good = Option(math.random()) // And never worry about it again
    ```

0. Do not abuse `Option`.
Some types already provide a good default to represent "nothing".
For instance, before declaring an `Option[Seq[T]]`, ask yourself whether there
is any semantic difference between `Some(Nil)` and `None`.
If not (and usually, there isn't), use `Seq[T]` and return an empty list.

0. Never extend a case class.
Extending a case class with another case class is forbidden by the compiler.
Extending a case class with a regular class, while permitted,
produces nasty results:

    ```scala
    case class A(a: Int)

    // error: case class B has case ancestor A,
    // but case-to-case inheritance is prohibited.
    case class B(a: Int, val b: String) extends A(a)

    class C(a: Int, c: Int) extends A(a)

    val a = A(1)
    val c = new C(1, 2)
    assert(a == c)
    assert(a.hashCode == c.hashCode)
    assert(c.toString == "A(1)") // Wat

    val d = new C(1, 3)
    assert(c.hashCode == d.hashCode)

    val e = c.copy(4) // note there is no C#copy(Int, Int) method
    assert(!e.isInstanceOf[C]) // e is a proper instance of A
    ```

0. Do not use
[`JavaConversions`](http://www.scala-lang.org/api/current/scala/collection/JavaConversions$.html).
Instead use
[`JavaConverters`](http://www.scala-lang.org/api/current/scala/collection/JavaConverters$.html)
and its `asScala` and `asJava` methods.

    `JavaConversions` may happen "automagically" at unexpected times, usually
    masquerading type errors.
    `JavaConverters` gives you explicit control of when conversions happen
    (only as dangerous as you want).
    You can then transparently use Java collections as if they were Scala
    collections, usually for performance or interoperability reasons:

    ```scala
    import scala.collection.JavaConverters.mapAsScalaMapConverter
    import scala.collection.mutable

    val map: mutable.Map[String, String] = new java.util.HashMap[String, String].asScala
    map += "foo" -> "bar"
    assert(map("foo") == "bar")
    ```

    ```scala
    // Counter-example
    val m = Map(1 -> "one")
    m.contains("") // Fails to compile: type mismatch

    import collection.JavaConversions._
    m.contains("") // false
    ```

0. Instead of throwing exceptions, use
[a more suitable type](https://tersesystems.com/blog/2012/12/27/error-handling-in-scala):
`Option`, `Either`, or `Try`.

    Exceptions are not functional.
    Functions that throw exceptions are not total functions,
    they do not return a value for all possible inputs.

0. Do not `try` to `catch` exceptions, `Try` to catch exceptions.
`Try` does for exceptions what `Option` does for `null`.

0. Remember that `Future` already encapsulates a `Try`.
Instead of `Future[Try[T]]`, use `Future.failed`, `Future.fromTry`,
and `Future.successful`.

0. No matter what, never use a "catch-all" exception handler.
Some features in Scala are implemented relying on exceptions.
Use `NonFatal` instead:

    ```scala
    import scala.util.control.NonFatal

    try {
      1 / 0
    } catch {
      case e: Exception => // Bad
      case _: Throwable => // Worse
      case _ =>            // Worst, scalac will warn and "recommend" the expression above
      case NonFatal(e) =>  // The only acceptable way to catch all exceptions
    }
    ```

    Please note that `NonFatal` is not needed when pattern matching `Future`
    or `Try` since they already filter for it.

0. Make judicious use of the various assertions (contracts) offered by Scala.
See [scala.Predef](http://www.scala-lang.org/api/current/scala/Predef$.html)
for the complete reference.
    1. Assertions (`assert(b != 0)`) are used to document and check
    design-by-contract invariants in code.
    They can be disabled at runtime with the `-Xdisable-assertions`
    command line option.
    1. `require` is used to check pre-conditions, blaming the caller of a
    method for violating them.
    Unlike other assertions, `require` throws `IllegalArgumentException`
    instead of `AssertionError` and can never be disabled at runtime.

        Make ample and liberal use of `require`, specially in constructors.
        Do not allow invalid state to ever be created:

        ```scala
        case class Person(name: String, age: Int) {
          require(name.trim.nonEmpty, "name cannot be empty")
          require(age >= 0, "age cannot be negative")
          require(age <= 130,
            "oldest unambiguously documented person to ever live died at age 122")
        }
        ```

        Please note that even though `require` throws an exception,
        this is not a violation of the previous recommendations.
        Constructors are not methods, they cannot return `Try[Person]`.

    1. `ensuring` is used on a method return value to check post-conditions:

        ```scala
        def square(a: Int) = {a * a} ensuring (_ > 0)
        ```

0. Know and make good use of the standard Scala collections library:
    1. Never test for `c.length == 0` (may be O(n)), use `c.isEmpty` instead.
    1. Instead of `c.filter(_ > 0).headOption`, use `c find(_ > 0)`.
    1. Instead of `c.find(_ > 0).isDefined`, use `c exists (_ > 0)`.
    1. Instead of `c exists (_ == 0)`, use `c contains 0`.

    This is not only a matter of style: for some collections,
    the recommendations above can be algorithmically more efficient than
    the naive approach.

0. Do not import `scala.collection.mutable._` or any single mutable
collection directly.
Instead, import the `mutable` package itself and use it explicitly as a
namespace prefix to denote mutability,
which also avoids name conflicts if using both mutable and
immutable structures in the same scope:

    ```scala
    import scala.collection.mutable.Set
    val bad = Set(1, 2, 3) // Too subtle and risky for the inattentive reader

    import scala.collection.mutable
    val good = mutable.Set(1, 2, 3)
    ```

0. Prefer a mutable `val` over an immutable `var`:

    ```scala
    import scala.collection.mutable

    var bad = Set(1, 2, 3)
    bad += 4

    val good = mutable.Set(1, 2, 3)
    good += 4

    assert(good.sameElements(bad))
    ```

0. No "stringly" typed code.
Use `Enumeration` or `sealed` types with `case` objects.
`Enumeration` and `sealed` types have similar purpose and usage,
but they do not fully overlap.
`Enumeration`, for instance, does not check for exhaustive matching while
`sealed` types do not, well, enumerate.

    ```scala
    object Season extends Enumeration {
      type Season = Value
      val Spring, Summer, Autumn, Winter = Value
    }

    sealed trait Gender
    case object Male extends Gender
    case object Female extends Gender
    ```

0. When creating new `Enumeration`s, always define a type alias (as above).
Never use `MyEnum.Value` to refer to the type of enumeration values:

    ```scala
    def bad(a: Season.Value) = ???

    import Season.Season
    def good(a: Season) = ???
    ```

0. If a function takes multiple arguments of the same type,
use named parameters to ensure values are not passed in the wrong order:

    ```scala
    def geo(latitude: Double, longitude: Double) = ???

    val bad = geo(a, b)

    val good = geo(latitude = a, longitude = b)
    ```

0. Always use named parameters with booleans,
even when they are the only parameter:

    ```scala
    // Bad
    Utils.delete(true)

    // Good
    Utils.delete(recursively = true)
    ```

0. Avoid declaring functions with boolean arguments ("magic booleans").
Do not model any two arbitrary states as boolean:

    ```scala
    // Bad
    case class Person(male: Boolean)
    case class Car(isManual: Boolean, isElectric: Boolean, isTwoDoor: Boolean)

    // Good
    case class Person(gender: Gender)

    // Bad
    def findPeople(filterMales: Boolean): Seq[People]

    // Good
    def findMales: Seq[People]
    def findFemales: Seq[People]
    ```

0. Do not define abstract `val`s in traits or abstract classes.
Abstract `val`s are a source of headaches and unexpected behavior in Scala:

    ```scala
    trait Bad {
      val bad: Int
      val worse = bad + bad
    }

    object ImBad extends Bad {
      val bad = 1
    }

    assert(ImBad.worse == 2)
    assert(ImBad.worse == 0)
    ```

    Always define abstract `def`s, they are more general and safer:

    ```scala
    trait Good {
      def good: Int
      val better = good + good
    }

    object ImGood extends Good {
      def good = 1
    }

    assert(ImGood.better == 2)
    ```

    Note: beware that overriding the abstract `def` with a concrete `val`
    also suffers from the problem above.

0. Avoid `lazy val`.
`lazy val` is *not* free, or even cheap.
Use it only when you absolutely need laziness semantics for correctness,
never for "optimization".
The initialization of a `lazy val` is expensive due to monitor acquisition cost,
while every access is expensive due to `volatile`.
Worse, `lazy val`
[may deadlock](http://axel22.github.io/2013/06/10/on-lazy-vals.html)
even when there are no circular dependencies between them.

    > `lazy val`s are compiled into a double-checked locking instance with a
    > dedicated `volatile` guard.
    > Notably, this compilation template yields a source of significant
    > performance woes:
    > we need to pass through the `volatile` read on the guard boolean for
    > every read of the `lazy val`.
    > This creates write barriers, cache invalidation and general mayhem with
    > a lot of the optimizations that HotSpot tries to perform.
    > All for a value that may or may not even care about asynchronous access,
    > and even more importantly, is guaranteed to be true only once and is
    > false indefinitely thereafter (baring hilarious tricks with reflection).
    > Contrary to what is commonly believed, this guard isn't simply optimized
    > away by HotSpot (evidence for this comes from benchmarks on warm code
    > and assembly dumps).
    >
    > Daniel Spiewak

0. With default parameters, secondary constructors are a lot less frequently
needed in Scala than in Java
But they can still be quite useful, just avoid pathological cases:

    ```scala
    class Bad(a: Int, b: Int) {
      def this(a: Int) = this(a, 0)
    }

    class Good(a: Int, b: Int = 0)
    ```

0. If you need to wrap a value that can be immediately computed into a `Future`,
use `Future.successful`, which returns an already completed `Future`.
Calling `Future.apply` incurs all the overhead of starting an asynchronous
computation, no matter if the computation is a simple, immediate result:

    ```scala
    val bad = Future(0)

    val good = Future.successful(str.trim)
    ```

0. When combining `Future`s in for-comprehensions,
do not use the following idiom:

    ```scala
    for {
      a <- futureA
      b <- futureB
      c <- futureC
    } yield a + b + c
    ```

    Unless `futureB` depends on `a` and `futureC` depends on `b`,
    that will unnecessarily chain (serialize) the `Future`s,
    starting a future only after the previous one has finished,
    which most likely defeats the purpose.
    To properly start `Future`s in parallel and combine their results,
    use the following idiom:

    ```scala
    val fa = futureA
    val fb = futureB
    val fc = futureC

    for {
      a <- fa
      b <- fb
      c <- fc
    } yield a + b + c
    ```

0. Avoid `isInstanceOf` or `asInstanceOf`.
Safe casting is actually one of the best use cases for pattern matching.
It is more flexible, guarantees that a cast will only happen if it can succeed,
and allows, for instance, the use of different branches to carry out multiple
conditional casts at the same time for various types, perform conversions,
or fallback to a default value instead of throwing an exception:

    ```scala
    val bad = if (a.isInstanceOf[Int]) x else y

    val good = a match {
      case _: Int => x
      case _ => y
    }

    val bad = a.asInstanceOf[Int]

    val good = a match {
      case i: Int => i
      case d: Double => d.toInt
      case _ => 0 // Or just throw new ClassCastException
    }
    ```

0. Do not use structural types, never import `scala.language.reflectiveCalls`.
Structural types are implemented with reflection at runtime,
and are inherently less performant than nominal types.

Tips & Tricks
-------------

0. Avoid naked base types, such as `String`, `Int`, or `Date`.
Those unwrapped types have no semantic meaning and provide little type safety
(e.g., mixing `firstName` and `lastName`).
Instead, make plentiful use of value classes to enforce strong, semantic typing:

    ```scala
    // No extra object allocation at runtime
    case class Email(email: String) extends AnyVal

    // Never gets mixed with other strings
    val email = Email("joe@example.com")
    ```

0. Combine value classes with implicit classes to define extension methods:

    ```scala
    implicit class IntOps(val n: Int) extends AnyVal {
      def stars = "*" * n
    }

    // Equivalent to a static method call, no conversion actually takes place
    5.stars
    ```

0. Whenever possible, use `private[this]` and `final val` instead of `private`
and `val` as they enable the Scala compiler and the JVM to perform additional
optimizations, namely, direct field access instead of accessor method, and
inlined constant instead of field access.
(If `final val` surprised you, remember that it is not redundant:
in Scala `final` means "cannot be overridden",
while in Java it means both that as well as "cannot be reassigned".)

0. Leverage parallel collections, use `.par` judiciously.

0. `import System.{currentTimeMillis => now}` or
`import System.{nanoTime => now}` are very useful to have around.

0. Use the `@tailrec` annotation to ensure the compiler can recognize a
recursive method is tail-recursive.

0. You can use the `@scala.beans.BeanProperty` and `@BooleanBeanProperty`
annotations to automatically generate JavaBeans style getter and setter methods.

0. You can use the following syntax to pass a sequence as a parameter to a
variable length argument list (varargs) method:

    ```scala
    def foo(args: Int*) = ???

    val seq = Seq(1, 2, 3)

    foo(seq: _*)
    ```

    (Mnemonic: it looks like an emoticon)

0. There are some really neat tricks we can do with pattern matching:

    ```scala
    val tuple = ("foo", 1, false)
    val (x, y, z) = tuple
    assert((x, y, z) == (("foo", 1, false)))

    case class Foo(x: String, y: Int)
    val foo = Foo("foo", 1)
    val Foo(a, b) = foo
    assert((a, b) == (("foo", 1)))

    val seq = Seq(1, 2, 3, 4, 5, 6)
    val x :: xs = seq
    assert((x, xs) == ((1, Seq(2, 3, 4, 5, 6))))

    // Same as above
    val Seq(y, ys@_*) = seq
    assert((y, ys) == ((1, Seq(2, 3, 4, 5, 6))))

    // Skipping elements
    val _ :: a :: b :: _ :: zs = seq
    assert((a, b, zs) == ((2, 3, Seq(5, 6))))

    // Works with other collections, too
    val vector = Vector(1, 2, 3, 4, 5, 6)
    val Vector(_, a, b, _, ws@_*) = vector
    assert((a, b, ws) == ((2, 3, Vector(5, 6))))

    val Array(key, value) = "key:value".split(':')

    // Regular expressions
    val regex = """(.)(.)(.)""".r
    val regex(a, b, c) = "xyz" // Matches and extracts regex against "xyz"
    assert((a, b, c) == (("x", "y", "z")))
    ```

    Please note that the extra parentheses are needed due to the
    `-Yno-adapted-args` compiler option.

0. Java `enum`s are very powerful and flexible.
Enumeration types in Java can include methods and fields, and enum constants
are able to specialize their behavior.
For more information, refer to the awesome "Effective Java, 2nd Edition" by
Joshua Bloch, in particular Chapter 6, "Enums and Annotations", Item 30:
"Use enums instead of int constants".

    Unfortunately, Scala enums are not at feature parity with their Java
    counterparts.
    They do not, by default, offer the same flexibility and power:

    1. They do not interoperate with Java.
    1. They have the same type after erasure (see below).
    1. Pattern matching is not exhaustively checked.
    1. Play JSON and Scala Pickling do not support enumerations.

    [Do not expect](http://groups.google.com/forum/#!msg/scala-internals/8RWkccSRBxQ/U4y0XpRJfdQJ)
    the situation
    [to change](http://www.reddit.com/r/scala/comments/3aqlhu/is_there_a_voting_mechanism_for_new_scala_features/csgy8i0).

    ```scala
    object A extends Enumeration { val Abc = Value }
    object X extends Enumeration { val Xyz = Value }

    object DoesNotCompile {
      def f(v: A.Value) = "I'm A"
      def f(v: X.Value) = "I'm X"
    }
    ```

    The following idioms bring a little more power to Scala enums:

    ```scala
    // Custom properties
    object Suit extends Enumeration {
      import scala.language.implicitConversions

      type Suit = SuitVal

      implicit def toVal(v: Value) = v.asInstanceOf[SuitVal]

      case class SuitVal private[Suit] (symbol: Char) extends Val

      val Spades   = SuitVal('♠')
      val Hearts   = SuitVal('♥')
      val Diamonds = SuitVal('♦')
      val Clubs    = SuitVal('♣')
    }

    // Behaviour specialization and exhaustiveness checking
    object Lang extends Enumeration {
      import scala.language.implicitConversions

      type Lang = LangVal

      implicit def toVal(v: Value) = v.asInstanceOf[LangVal]

      sealed abstract class LangVal extends Val {
        def greet(name: String): String
      }

      case object English extends LangVal {
        def greet(name: String) = s"Welcome, $name."
      }

      case object French extends LangVal {
        def greet(name: String) = s"Bienvenue, $name."
      }
    }
    ```

0. Instead of running sbt tasks directly from the command line
(`$ sbt compile`, for instance), it is better to open an sbt prompt
(just type `$ sbt`) and never leave it.
Running all your sbt tasks (`clean`, `update`, `compile`, `test`, etc.) inside
the sbt prompt is a lot faster since you only have to start sbt, load the JVM,
and wait for it to warm up (if ever) once.
If your `build.sbt` file changes, just run the `reload` task and you are
good to go again.

    Having an sbt instance running `~test` in the background is one of
    the best ways to develop in Scala.
    You can run some sbt tasks and be left inside the prompt by using
    the `shell` task:
    `$ sbt clean update compile test:compile shell`.

0. The following shorthand trick works in the Scala REPL:

    ```scala
    scala> "The quick brown fox jumps over the lazy dog!"
    res0: String = The quick brown fox jumps over the lazy dog!

    scala> .toLowerCase
    res1: String = the quick brown fox jumps over the lazy dog!

    scala> .distinct
    res2: String = the quickbrownfxjmpsvlazydg!

    scala> .filter(_.isLetter)
    res3: String = thequickbrownfxjmpsvlazydg

    scala> .sorted
    res4: String = abcdefghijklmnopqrstuvwxyz

    scala> .size
    res5: Int = 26
    ```

0. Learn you a few Scala tricks for great good:
http://github.com/marconilanna/talks.
Recommended talks:

    1. Powerful and Elegant Scala One-Liners
    1. What's New since Programming in Scala
    1. Idiomatic Scala: Your Options Do Not Match
    1. F-Bounded Polymorphism

Additional Remarks
------------------

0. Program in Scala. You are not writing Java, Haskell, or Python.

0. Leverage type safety.
Let the compiler and the type system do the grunt work for you.
Type early, type often.

    > "When in doubt, create a type"
    >
    > Martin Fowler

0. Favor immutability, avoid mutability whenever possible.
Mutability encapsulated in small scopes internal to functions is acceptable.

0. Obey the principle of least astonishment.

0. Always favor readability.
Be clear.

0. Brevity enhances clarity.

0. Favor generic code but not at the expensive of clarity.

0. Always be aware of the trade offs you make.

0. "Premature optimization is the root of all evil" is not an excuse
to do stupid things on purpose!

0. Do not leave unreachable ("dead") code behind.

0. Do not comment out code, that is what version control is for.

0. Take advantage of simple language features that afford great power
but avoid the esoteric ones, especially in the type system.

0. Learn and use the most advanced features of your favorite text editor.
Make sure to configure it to perform as many formatting functions for
you as possible, so that you do not have to think about it:
remove whitespace at end of lines, add a newline at end of file, etc.
If your editor does not support even those basic features,
find yourself a better one. :-)

0. Functions should communicate purpose and intent.

    Kent Beck tells the history of a piece of code, a single line method in a
    word processor, that astonished him the first time he saw it.
    Something like `def highlight(x: X) = underline(x)`.
    Why write a straightforward alias for the `underline` function,
    he asked himself?
    Then he realized the power of this simple abstraction.

    It just so happened that highlights, at that particular point in time,
    were implemented with underlinings, but that does not mean that highlights
    and underlines are the same thing.
    They do not serve the same purpose.
    Highlights are semantic, underlines are presentational.

    It could be that in the future it is decided a highlight should have,
    for instance, a yellow background.
    It would be a trivial change using the method above, taking only
    but a few seconds to implement.
    Had they instead used `underline(x)` interchangeably everywhere
    across the code, one could spend hours looking at each usage site,
    trying to infer whether the intention of that particular `underline`
    call was highlight or to actually underline.

    That is one of the reasons why simple methods like
    `def isEmpty = this.length == 0` are extremely valuable.
    No matter how short the equivalent code they capture may be,
    abstractions that better express intent and purpose are invaluable.

0. A word about _thin_ models.
In object-oriented design, a object is an implementation of an
[abstract data type (ADT)](http://en.wikipedia.org/wiki/Abstract_data_type).
Objects must define both a set of values _and_ the operations on them.
Objects are not Pascal records or C structs glorified with getters and setters.
Classes are not just namespaces for methods.

    > "Domain Model: An object model of the domain that incorporates both
    > **behavior and data**.
    > [...] There is hardly any behavior on [thin] objects, making them
    > little more than bags of getters and setters.
    > The fundamental horror of this anti-pattern is that it is so
    > contrary to the basic idea of object-oriented design;
    > which is to combine data and process together.
    > The anemic domain model is really just a procedural style design."
    >
    > Martin Fowler,
    > [Anemic Domain Model](http://www.martinfowler.com/bliki/AnemicDomainModel.html)

0. Always remember Tip 4:

    > "[Do not Live with Broken Windows](http://www.artima.com/intv/fixit.html).
    > Fix each as soon as it is discovered."
    >
    > Andrew Hunt and David Thomas, The Pragmatic Programmer

    [Orthogonality and the DRY Principle](http://www.artima.com/intv/dry.html)
    is also an interesting read.
    [Full interview](http://www.artima.com/intv/plain.html).

Appendix
--------

### Recommended IntelliJ Settings

Go to File, Import Settings..., and select the file
[settings.jar](resources/settings.jar).

* Editor:
    * General:
        * Other:
            * Strip trailing spaces on Save: **All**
            * Ensure line feed at file end on Save
    * Code Style:
        * Scheme: **Scail**
            * Line Separator (for new files): **Unix and OS X (\n)**
            * Right margin (columns): **100**
            * Enable EditorConfig support
        * Scala:
            * Tabs and Indents:
                * Use tab character: **no**
                * Smart tabs: **no**
                * Tab size: **2**
                * Indent: **2**
                * Continuation indent: **4**
            * Wrapping and Braces:
                * Method declaration parameters:
                    * Align when multiline: **no**
            * ScalaDoc:
                * Use scaladoc indent for leading asterisk: **no**
            * Imports:
                * Class count to use import with '\_': **99**
                * Sort imports (for optimize imports): **scalastyle consistent**
                * Collect imports with the same prefix into one import: **no**
        * Other File Types:
            * Use tab character: **no**
            * Tab size: **2**
            * Indent: **2**
* Plugins:
    *  Install JetBrains plugin...
        * **Scala**
    * Browse repositories...
        * **Lines Sorter**
        * **String Manipulation**

[Scala Style Guide]: http://docs.scala-lang.org/style

[Scalastyle]: http://www.scalastyle.org
[WartRemover]: http://github.com/wartremover/wartremover
[Scapegoat]: http://github.com/sksamuel/scapegoat
[Linter]: http://github.com/HairyFotr/linter
[cpd4sbt]: https://github.com/sbt/sbt-cpd

[Scalafmt]: http://scalameta.org/scalafmt
[Scalariform]: http://github.com/scala-ide/scalariform

### Static Analysis Tools

0. [Scalastyle]
0. [WartRemover]
0. [Scapegoat]
0. [Linter]
0. [cpd4sbt]
0. [Scalafmt]
0. [Scalariform]
