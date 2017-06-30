---
layout: post
title: Type safety with native JavaScript
---
*Why everybody wants type safety and how to get it.*

![Typecast example](/images/2016-11-14-typecast-02.png)

# 1. Overview
Many developer tools like IDEs, frameworks, libraries, and linters try
to provide some level of **type safety** to JavaScript. This article
explains what type safety is, why we want it, and how we can get it
using native JavaScript.

The good news is most **type errors** in JavaScript can be resolved using
**typecasting**. In this article we demonstrate how to typecast using
simple functions, and then we show how typecasting works with other best
practices to improve productivity and quality.

# 2. What is type safety?
Type safety is the extent a programming language discourages or prevents
**type errors**. A type error occurs when an unintended or incompatible type
value is provided to a function or expression, usually as an argument
or context. Dividing a number by an array is a perfect example.
Usually this doesn't make sense, yet JavaScript support this very operation,
often with surprising and unwanted results.

Let's see how easy it is to create a type error in Javascript using the
semantic style all the cool kids are using these days. **This is awful
code**, so please don't copy it. We'll improve it as we go along.

```js
  function repeats(counts, run)
  {
      while(counts < 0)
      {
          run(counts)
          counts+=1
      }
  }

  function reports(info)
  {
      console.log(info)
  }

  repeats('-3', reports)
```

That didn't take long! The first type error occurs when we provide the
'-3' argument to the `repeats` invocation. This value should be an
integer, but we instead provide a string. Then all hell breaks loose.

When `repeats` is invoked it creates an 'endless' loop condition that
will rather quickly consume all available resources within its execution
environment. This results in a force-kill of a NodeJS process, or a browser
tab, or the entire browser process, or the host OS.

If we watch the progression of the value of `counts` in the `while` loop we
see the following series: `'-3', '-31', '-311', '-3111', ...`. The test
condition `counts < 0` *does* convert the `counts` string into a number,
but by then it is too late: the value is always less than 0 and it only gets
more negative with each iteration. That's because the expression `counts+=1`
always converts the number `1` into a string `'1'` and *appends* it to the
`counts` variable.

# 3. Why do we want type safety?
We want type safety with JavaScript because type errors can be quite
troublesome:

1. They are easy to create
2. They are challenging to resolve
3. They are often serious

Let's look at each of these issues in turn.

## 3.1. JS type errors are easy to create
JavaScript is missing almost all type controls because it was not originally
intended to run large-scale applications. This makes creating type errors
exceedingly easy.

### 3.1.1 No variable declarations
JavaScript has no means to formally declare a variable type. A variable may
contain any type which may be changed at any time during execution.
There are no *sigils* to identify type, and a variable name is
unconstrained. We probably all have seen a JavaScript application
or two where a single symbol like `watches` is used as an object, a map,
a list, a boolean flag, a string, an integer, and a floating point number
all in different contexts. Yet in practice most variables used in JavaScript
are not *intended* to change type *within* a context because doing so is
needlessly confusing.

Other languages provide a 'wild card' variables to reference any type of
value. These are usually easy to identify because they have a
unique syntax. Developers know they these 'wild cards' need to be
handled with care. In JavaScript, all variables are 'wild cards.'

### 3.1.2 Coercion and polymorphism
JavaScript expressions often intermingle **type coercion** and
**polymorphic operators** which result in a large number of behaviors that
provide surprising and undesirable results.

Type coercion occurs when the language "automatically" converts one type
to another. Polymorphic operators are symbols like the plus sign (`+`)
which in JavaScript either concatenates strings *or* adds two numbers
*or* converts a string to a number.

Let's look at a few examples. Those playing along at home might
want to cover the right-hand side of the table below to see if you can
guess what the returned value will be. How'd you do?

```js
  var x, y;
  // expression   // |     returned value | Coercion       | Op
  x = 3;          // |                 3  | -              |
  x = 3 + 1;      // |                 4  | -              | +num
  x = 3 + '1';    // |               '31' | 3      => '3'  | +str
  x = 3 + [];     // |                '3' | []     => ''   | +str
  x = 3 + [ 21 ]; // |              '321' | [ 21 ] => '21' | +str
  x = 3 + {};     // | '3[object Object]' | {}     => str  | +str
  x = '3' - 2;    // |                 1  | '3'    =>  3   | -num
  x = '3' + 2;    // |               '32' | 2      => '2'  | +str
  x = + '3';      // |                 3  | '3'    =>  3   | cast_num
  x = 0 + '3';    // |               '03' | 0      => '0'  | +str
  x = y + 3;      // |                NaN | -              | NaN
  x = y + '3';    // |    'undefined3'    | undef  => str  | +str
```

Remember that there at least 4 major JavaScript engines on the
market (V8, IonMonkey, Nitro, Chakra) and [at least another dozen minor
players][5]. We wouldn't be surprised to find that the behaviors show here
(using V8) vary from engine to engine and generation to generation.

Other languages have less complex behaviors because they usually have
stricter type checking, stricter coercion rules, fewer polymorphic
operators, and fewer vendors. **Perl**, for example, uses the dot (`.`)
operator to *join* strings and the plus (`+`) operator to add values.
Perl also uses *sigals* (prefixes) like `$`, `@`, and `%` to denote variable
types and a special syntax to identify 'wild-card' references.

### 3.1.3 No static checking
Many languages provide some level of static type checking.
**Java**, for example, resolves most variable types during compilation.
If JavaScript had a similar mechanism we wouldn't be able to run our
application until we resolved the compile errors.  In this imaginary world,
our JavaScript compile output might look like this:

```
00: ok                           | x = 3;          |
01: ok                           | x = 3 + 1;      |
02: compile_error: type_mismatch | x = 3 + '1';    |
03: compile_error: type_mismatch | x = 3 + [];     |
04: compile_error: type_mismatch | x = 3 + [ 21 ]; |
06: compile_error: type_mismatch | x = 3 + {};     |
07: compile_error: type_mismatch | x = '3' - 2;    |
08: compile_error: type_mismatch | x = '3' + 2;    |
09: compile_error: type_mismatch | x = + '3';      |
10: compile_error: type_mismatch | x = 0 + '3';    |
11: compile_error: type_mismatch | x = y + 3;      |
12: compile_error: type_mismatch | x = y + '3';    |
```

Perhaps the greatest advantage of static (compile-time) type checking is
that it can improve performance: every type check that can be
resolved *once* during a compile removes a type check that would
need to be invoked on *every* call of a function or method. This can
remove a large number of calls when the application is run and thus
improves performance.

### 3.1.4 Roll-your-own dynamic checking
Static type checking does not work in all situations, especially when
dealing with data from unknown or untrusted sources. In those cases
the application must resort to *dynamic type checking* at run-time.
With native JavaScript, run-time checks are pretty much our only option,
yet the language only has limited tools to help here. Even the `typeof`
built-in function fails to distinguish between an object and an array.

## 3.2. Type errors are challenging to resolve
Type errors can be hard to identify and debug. When one routine fails to
check for type an incorrect result can propagate up the call stack resulting
in a cascade of errors. The originating flaw can be hard to spot
if variable aren't named to indicate their intended type, like so:

```js
  // Cool-kid, no-consistency convention:
  var total = watches / in_use;
```

However, if we name our variable by intended type the mismatches
become obvious:

```js
  // Real-developer naming convention:
  var total_str = watch_list / use_bool;
```

Yes, the intended variable type **is that important**. We've had to maintain
plenty of third-party modules which used the *cool-kid, no-consistency*
convention where variable names provide no hint of type or purpose, or worse,
are patently misleading. For example, what genius decided to call a boolean
flag `item_array`? We'd rather name our variables sensibly and use the time
saved to focus on new challenges.

## 3.3. Type errors are often serious
As we have shown, type errors can result in severe application failures and
security holes. Imagine some NodeJS code that doesn't properly type-check
its JSON API. One could implement a Denial Of Service (DOS) attack and shut
down an entire web farm by simply sending strings instead of numbers in API
requests. This stuff happens.

# 4. How do we get type safety?
Getting type safety in native JavaScript isn't particularly
hard. First recognize that the values we need to worry about are inputs
to public methods like arguments, external data, and context. We can use
typecasting to guarantee these value types.

## 4.1 Typecasting
Typecasting, for the purposes of this article, is the process of converting
a value into the desired data type using a very strict set of rules.
Our typecasting functions either return **the requested value type** or 
**a failure value** which is `undefined` by default. Consider the examples below:

```js
  // return_data = castInt( <value-to-cast> [, <failure-value>] );

  return_data = castInt( 0      ); // 0
  return_data = castInt( '0'    ); // 0
  return_data = castInt( 'a'    ); // undefined
  return_data = castInt( []     ); // undefined
  return_data = castInt( 'a', 0 ); // 0
  return_data = castInt( [],  0 ); // 0
```

`castInt`:


### 4.1.1 Use typecasting
Let's adjust our example function to use `castInt` and `castFn`
to ensure the provided arguments are the correct type.  If they 
are not, the function will return without any further processing:

```js
  function repeats(arg_counts, arg_run)
  {
      var counts = castInt(arg_counts)
      var run = castFn(arg_run)
      if (!(counts && run)){return}

      while (counts < 0)
      {
          run(counts)
          counts+=1
      }
  }

  function reports(info)
  {
      console.log(info)
  }

  repeats('-3', reports)
```

The function is now impervious to most type errors.

### 4.1.2 Get typecast methods
We can get typecast methods from the [hi\_score][1] project which is
easy to install (type `npm install hi_score` into a terminal). If you edit the
example application you can use all the `cast` methods from `xhi.util.js`.

```bash
  npm install hi_score
  cd npm_modules/hi_score
  npm install
  npm run setup
  npm test
  google-chrome ./index.html
  # Open the JavaScript console to access xhi._util_ functions.
```

You don't have to use the whole library; you can just crib the methods from
`xhi.util.js` if you want. Go ahead, you won't hurt anyone's feelings.

### 4.1.2 More typecasting
The `xhi` utility library provides the following typecast methods:

```
  castBool, castFn,  castInt, castJQ,
  castList, castMap, castNum, castObj, castStr
```

All these methods take either one or two arguments. Only numbers, strings,
and integers are converted between types and only when the conversion is
unambiguous.

The first argument is always the value to cast; the second argument is the
value to use if the `cast` fails. If a second argument is omitted,
`undefined` is returned instead. Let's update our example again to use an
argument map and a failure value for the `counts` variable:

```js
  function repeats(arg_map) {
      var map = castMap(arg_map, {})
      var counts = castInt(map.counts, 0)
      var run = castFn(map.run)

      if (run)
      {
          while (counts < 0)
          {
              run(counts)
              counts+=1
          }
      }
  }

  function reports (info)
  {
      console.log(info)
  }
  repeats({counts:'-3',run:reports});
```

This is run-time (dynamic) type checking. There is no native static
checking in JavaScript in any real sense (one could make arguments about the
JIT compiler, but at best such checking is *very* incomplete). One could
use a type-safety tool like [Flow][3] or a framework like [TypeScript][4]
which in theory can perform static type analysis. However, it is not clear
that this provides a performance benefit worth the compile overhead.
We will compare native typecasting with these solutions in an upcoming
article.

### 4.1.3 The Zen of typecasting
The `cast` methods have three benefits over native JavaScript coercion:
they are predictable, explicit, and self-documenting.
Let's compare pedictability first:

#### 4.1.3.1 JavaScript type coercion examples
JavaScript tries hard to coerce types, and the results
are often undesirable. Blank cells are conditions where a
type error exception is usually thrown.

| Value        | Bool  | Fn   | Num  | Ary   | Obj  | Str        |
| :---         | :---  | :--- | :--- | :---  | :--- | :---       |
| ''           | FALSE |      | 0    |       |      | ''         |
| '0'          | t     |      | 0    |       |      | '0'        |
| '1'          | t     |      | 1    |       |      | '1'        |
| '20'         | t     |      | 20   |       |      | '20'       |
| 'ten'        | t     |      | NaN  |       |      | 'ten'      |
| 0            | FALSE |      | 0    |       |      | '0'        |
| 1            | t     |      | 1    |       |      | '1'        |
| NaN          | FALSE |      | NaN  |       |      | 'NaN'      |
| []           | t     |      | 0    | []    | {}   | ''         |
| ['ten']      | t | | NaN | ['ten']  | { 0: 'ten' } | 'ten' |
| ['ten','t']  | t | | NaN | ['ten','t'] | { 0: 'ten', 1: 't'}  | 'ten,t' |
| [10]         | t     |     | 10  | [10] | { 0 : 10 } | '10' |
| [10,20]      | t     |     | NaN | [10,20] | { 0: 10, 1:20 }  | '10,20' |
| false        | FALSE |     | 0   |      |     | 'false'  |
| function(){} | t     | function(){} | NaN | [] | {} | 'function(){}' |
| null         | FALSE |     | 0   |      |     | 'null'      |
| true         | t     |     | 1   |      |     | 'true'      |
| undefined    | FALSE |     | NaN |      |     | 'undefined' |
| {}           | t     |     | NaN | []   | {}  | '0'         |
| -Infinity    | t     | -Infinity  | |   |     | '-Infinity' |
| Infinity     | t     | Infinity   | |   |     | 'Infinity'  |


#### 4.1.4.1 hi\_score cast method examples
The `cast` methods are very strict, and only convert the most
unambigous values. Blank cells are conditions where the failure
value (`undefined` by default) will be returned.
No exceptions are thrown by these methods.

| Value        | Bool  | Fn   | Num  | Ary   | Obj  | Str     |
| :---         | :---  | :--- | :--- | :---  | :--- | :---    |
| ''           |       |      |      |       |      | ''      |
| '0'          |       |      | 0    |       |      | '0'     |
| '1'          |       |      | 1    |       |      | '1'     |
| '20'         |       |      | 20   |       |      | '20'    |
| 'ten'        |       |      |      |       |      | 'ten'   |
| 0            |       |      | 0    |       |      | '0'     |
| 1            |       |      | 1    |       |      | '1'     |
| NaN          |       |      |      |       |      |         |
| []           |       |      |      | []    |      |         |
| ['ten']      |       |      |      | ['ten]      | |        |
| ['ten','t']  |       |      |      | ['ten','t'] | |        |
| [10]         |       |      |      | [10]        | |        |
| [10,20]      |       |      |      | [10,20]     | |        |
| false        | FALSE |      |      |       |      |         |
| function(){} |       | function(){} | |    |      |         |
| null         |       |      |      |       |      |         |
| true         | true  |      |      |       |      |         |
| undefined    |       |      |      |       |      |         |
| {}           |       |      |      |       | {}   |         |
| -Infinity    |       |      | -Infinity  | |      |         |
| Infinity     |       |      |  Infinity  | |      |         |


The `cast` methods are explicit and visible calls unlike JavaScript's
native type coercion, and they are named with the intent to illustrate
exactly what is being accomplished without the need for comments.
We hope that it is obvious, for example, that the `castFn` call
is intended validate a function.

## 4.2 Best practices with typecasting
Typecasting works best when we adopt a few additional good habits:

1. Name variables to indicate type
2. Write consistent API definition
3. Test the APIs

Let's look at each of these and update our sample code as we go along.

## 4.3 Name variables to indicate type
Let's improve our code again by changing the variable names.
While they might make sense to one brain, they are inconsistent and
misleading to another. We've purposely used plural variable names to
illustrate how bad a practice *that* can be.  We use our handy
[JS Code standard cheat sheet][b] to light the way:

```js
  function repeatFn ( arg_map ) {
    var
      map = castMap( arg_map,  {} ),
      int = castInt( map._int_, 0 ),
      fn  = castFn(  map._fn_     ),
      idx
      ;

    if ( ! fn ) { return; }

    for ( idx = int; idx < 0; idx++ ) {
      fn( idx );
    }
  }

  function printToConsole ( idx ) { console.log( idx ); }

  repeatFn({ _int_ : '-3', _fn_ : printToConsole });
```

We employed additional best practices suggested by the guide such as
formatting (spacing, alignment, K&R indenting) and replaced the dangerous
`while` loop with a `for` loop. This code will now pass JSLint.
Thanks to the naming convention we can tell that tell that `fn` should be
a function and and `idx` should be an integer *regardless if any other code
is visible*. Think about how much time can be saved by this alone.

The [full JS Code standard][a] discusses *why* a simple naming convention can
vastly reduce the need for comments. We think it's an interesting and 
compelling read if you're into that kind of thing.

## 4.4. Write consistent API definitions
Now that we have consistent named-by-type variables and better formatting,
we can easily read the code to create in-line API documentation.
Using the guide from the code standard we get the following:

```js
  // BEGIN utility method /repeatFn/
  // Summary   : repeatFn({ _int_ : <integer>, _fn_ : <function> )
  // Purpose   : Repeatedly call a function 'fn' as long as the
  //             counter 'int' is < 0.  After each call, 'int' is
  //             incremented by 1.  If the initial value of 'int'
  //             is not < 0 the function 'fn' is not called.
  // Example   : repeatFn({
  //               _int_ : -3,
  //               _fn_ : function (idx ) { console.log( idx ) }
  //             });
  // Arguments  : ( named )
  //   _fn_     : The function to execute. The current value of the
  //              index (idx) is provided as its sole argument.
  //   _int_    : The initial value of idx. Idx is incremented after
  //              _fn_ is executed. Thus a value of '-1' will result in a
  //              single execution of _fn_( idx );
  // Returns    : undefined
  // Throws     : none
  //
  function repeatFn ( arg_map ) {
    var
      map = castMap( arg_map,  {} ),
      int = castInt( map._int_, 0 ),
      fn  = castFn(  map._fn_     ),
      idx
      ;

    if ( ! fn ) { return; }
    for ( idx = int; idx < 0; idx++ ) {
      fn( idx );
    }
  }
  // END utility method /repeatFn/

  function printToConsole ( idx ) { console.log( idx ); }
  repeatFn({ _int_ : '-3', _fn_ : printToConsole });
```

Remember where we begged you not to copy our first code example?
Now the code is impervious to most type errors, readable,
testable, maintainable, and well documented. If you insist on
copying some code, we recommend this version!

## 4.5 Test the Code
We can use tools like `Istanbul` and `nodeunit` along with the in-line API
documentation to test our our shiny new type-safe `repeatFn`
function. Check out the test suite for `hi_score` to see how this is done:

```bash
  npm install hi_score
  cd npm_modules/hi_score
  npm install
  npm run setup
  npm test
  npm run coverage
  google-chrome index.html
  google-chrome coverage/lcov-info/index.html
  vi test/xhi_level_0.js # Or preferred editor
```

# 5. What about frameworks and libraries?
An excellent question is how does this native **typecasting** technique
compare with [Flow][3] and [TypeScript][4]? We intend to publish a sequel
that will answer this question. In **theory** both these transpiled
languages **should** be able to provide static type checking and eliminate
many dynamic type check calls. However, in **practice** the results may
be surprising since the real-world overhead of `cast` methods can be
actually quite low, and these languages introduce their own overhead.

One thing to remember is don't go typcasting crazy. Use it when processing
external data and inputs of public methods. Private methods often don't
benefit from typecasting since the the callers and data types are already
known, at least if we name our variables to indicate type.

We hope you found this useful! Please share your thoughts and experiences
in the comments below.

Cheers, Mike

[1]:https://github.com/mmikowski/hi_score
[2]:https://www.npmjs.com/package/hi_score
[3]:https://flowtype.org/
[4]:https://www.typescriptlang.org/index.html
[5]:https://en.wikipedia.org/wiki/List_of_ECMAScript_engines
[a]:https://github.com/mmikowski/spa/blob/master/js-code-std-2016.pdf
[b]:https://github.com/mmikowski/spa/raw/master/js-cheat-sheet-2016.pdf
