---
layout: post
title: TypeCasting in JavaScript, Part I

![Typecast using native JavaScript, will it save your soul?][0]
---

*Part 1 of 3: Type errors, casting, safety, and options*

# Overview
We've seen an explosion of frameworks and transpilers for JavaScript
which are designed to protect developers from the language's weaknesses.
Numerous frameworks such as TypeScript and Closure have tried to improve
the type safety of over Native JavaScript.

This series of two articles intends to discuss and compare the framework
solutions compared to a lighter weight approach using native JavaScript and
code standards.

In **Part 1** (this article), we define the type errors, typecasting, and
type safety.  We illustrate run-time type checking techniques, and explore
strategies to improving JavaScript type safety.

In **Part 2**, we look at the TypeScript solution to address type safety.

In **Part 3**, we detail how to address type safety in native
JavaScript and compare the results with TypesScript.

# Type errors
Type errors happen when a developer uses the wrong variable type in an
expression.  Consider the JavaScript expressions below and the returned
values:

```js
  var x;
  // expression       // |     returned value |
  x = 3;              // |                 3  |
  x = 3 + [];         // |                '3' |
  x = 3 + [ 21 ];     // |              '321' |
  x = 3 + [ 21, 2 ];  // |            '321,2' |
  x = 3 + {};         // | '3[object Object]' |
  x = '3' - 2;        // |                 1  |
  x = '3' + 2;        // |               '32' |
````

These results are often surprising. For example, subtracting a number from
a string results in a number, yet adding a number to a string results in a
string.  When these results surprise the developer with results different
than they expect, we call this a **type error**.

Type errors occur more frequently in JavaScript then one might expect
judging from the example above.  The most frequent source of type errors
occurs with function arguments.  Since arguments can take any data type,
sending the *wrong* type to a function can be catastrophic.  Consider the
the following example:

```js
  function doSomething ( idx, fn ) {
    while ( idx < 0 ) {
      fn( idx );
      idx += 1;
    }
  }

  doSomething( '-3', function ( i ) { console.log( i ); } );
```

Yup, that loop is **never** going to end as the value of `idx`
follows this sequence:

```
  '-3', '-31', '-311', '-3111', ...
```

The test condition, `( k < 0 )`, converts `k` to a number, but it is
always less than 0.  There are two lessons here. First, we should
avoid the `while` similar constructs like `setInterval` as they are prone
to inadvertent endless loop conditions.  Second, type errors are
common in JavaScript and they can be hard to debug.

# Typecasting

Typecasting, for the purposes of this article, is the process of converting
one data type to another using a very strict set of rules.  It is a
run-time method to **always** ensure the type of a variable.  We contrast
this with *type coercion* where the JavaScript notices a type mismatch and
resolves it using its own set of rules.

Let's rewrite our problem function from above using typecasting:

```js
  function doSomething ( arg_idx, arg_fn ) {
    var idx = castInt( arg_idx ), fn  = castFn(  arg_fn  );
    if ( idx === undefined && fn === undefined ) { return; }

    while ( idx < 0 ) {
      fn( idx );
      idx += 1;
    }
  }

  doSomething( '-3', function ( i ) { console.log( i ); } );
```

In this example, our casting methods, `castInt` and `castFn` guarantee
either a valid value of the requested type *or* `undefined` if one cannot
be determined. Now the function is all but impervious to type errors.

## Type safety

Type safety is the extent a programming language discourages or prevents
type errors. The **Java** language enforces type safety by resolving most
variable types at compile time.  This is called *static type checking*.
If JavaScript had a similar mechanism we would see numerous compile
errors and would need to fix them before we could run the application.

```js
02: compile_error: type_mismatch | x = 3;             |
03: compile_error: type_mismatch | x = 3 + [];        |
04: compile_error: type_mismatch | x = 3 + [ 21 ];    |
05: compile_error: type_mismatch | x = 3 + [ 21, 2 ]; |
06: compile_error: type_mismatch | x = 3 + {};        |
07: compile_error: type_mismatch | x = '3' - 2;       |
08: compile_error: type_mismatch | x = '3' + 2;       |
```

Static type checking does not work in all situations, especially when
dealing with data from unknown or untrusted sources. In those cases,
Java resorts to *dynamic type checking* at run-time.

# Strategies to improve JS type safety
We have considered least five methods to improve JS type safety.
They are ordered from least practical to most.

## 1. Change the JS JIT compiler
This strategy is almost certainly impractical in the near-term.
JavaScript's compile stage occurs on the device owned by the end user and
varies between engines such as Google's V8, Apple's Nitro, and Firefox's
IonMonkey.  Even if these were all adjusted to meet the a static-type-check
standard, there would be billions of legacy JIT compilers in the field for
years to come.

## 2. Provide a Linter
A lint tool like JSLint with improved static type checking would be
appealing.  JSLint does limited static type checking already.
Linting would need to be paired with embedded hints like comments or
variable names to intelligently trace intended types. The closest we've
seen to this is [flow][1], although this is still a transpiler.

## 3. Instrument development code
One might instrument code to provide static type checking during
development similar to test coverage tools like Istanbul.  It may
work well with our fifth strategy. However, we reserve this for
further study and not pursue it in this series.

## 4. Transpile
Transpile from a language designed for static type checking to Javascript.
This approach has been taken by Google teams with the TypeScript and
Closure frameworks as well as their own Dart language.  We explore
the benefits and pitfalls of this strategy in Part 2 of this series.

## 5. Type name and typecast
We've employed this technique for many years.  We name variable to indicate
type, name functions to indicate return type, and typecast on input to
functions.  We look at this strategy in **Part 3** of this series, and show
you the tools we use.  We also compare the results to transpiled
language such as TypeScript and Flow.

# Did we miss anything?

Drop us a note below if you have a particular area of interest or strategy
that you think we've missed.

Cheers, Mike

[0]:/images/2016-11-14-typecast-02.jpg
[1]:https://flowtype.org/

