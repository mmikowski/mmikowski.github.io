---
layout: post
title: Type safety with native JavaScript
---
*Why everybody wants type safety and how to get it*

![Typecast example](/images/2016-11-14-typecast-02.jpg)

# Overview
Many tools like IDEs, frameworks, libraries, and linters try to provide
some level of type safety to JavaScript. This article defines types
safety, illustrates why we want it, and shows how we can get it using native
JavaScript.

Most **type errors** in JavaScript can be resolved using **typecasting**.
We show you how to typecast using a set of utilities. Typecasting works very
well with other best practices such as naming variables for type, consistent
API documentation, and testing.

# What is type safety?
Type safety is the extent a programming language discourages or prevents
**type errors**. Type errors happen when a developer uses the wrong value
type in an expression or when invoking a function.

Let's see how easy it is to create a **type error** in Javascript using the
semantic style all the cool kids are using these days. **This is awful
code**, so please don't copy it. We'll fix it up as we go along.

```js
    function doStuff(counts, run)
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

    doStuff('-3', reports)
```

Well that didn't take long. First, let's identify the initial **type error**:
the first argument we provided to the `repeats` function invocation. This value
should be an integer, but we instead provide a string. Then all hell breaks
loose.

Thanks this initial type error, the `doStuff` invocation starts a
loop that will rather quickly consume all available resources within its
execution environment. Eventually this result in a force-kill of a of a
NodeJS process, or a browser tab, or a browser, or the host OS.

If we watch the progression of the value of `counts` in the `while` loop we
see the following series: `'-3', '-31', '-311', '-3111', ...`. The test
condition `counts < 0` *does* coerce the `counts` string into a number, but
that value is always less than 0. That's because the expression `counts+=1`
always *appends* the string `'1'` to the `counts` string.

# Why do we want type safety?

We want type safety with JavaScript because **type errors** can be quite
troublesome:

1. They are easy to create
2. They are challenging to resolve
3. They are often serious

Let's look at the each of these issues in turn:

## 1. JS type errors are easy to create
JavaScript is missing almost all type controls because it was not originally
intended to run large-scale applications. This makes creating type errors
exceedingly easy.

### No variable declarations
JavaScript has no means to declare a variable a type. A variable may
contain any type and that may be changed at any time during execution.
There is are no *sigils* to identify type, and a variable name is
unconstrained. We may have a JavaScript projects where a single symbol like
'watches' is used as an object, a map, a list, a boolean flag, a string, an
integer, and a floating point number.

### Coercion and polymorphism
JavaScript expressions often intermingle **type coercion** and
**polymorphic operators** which results in a very large numbers of conditions
that provide inconsistent results.

**Type coercion** occurs when the language "automatically" converts one type
to another. **Polymorphic operators** are symbols like `+` which in
JavaScript either concatenates a string *or* adds two numbers *or* converts a
string to a number. When an expression includes type coercion and polymorphic
operators results get surprising and confusing fast:

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

Other languages have less complex behaviors because have stricter type coercion
rules, *sigals* to indicate type (like `$`, `@` , and `%` in Perl and PHP), and
fewer polymorphic operators. For example, Perl and PHP use the `.` operator to
join strings. Trying to `+` two strings always returns a number.

### No static checking
Many languages provide some level of static type checking at compile time.
**Java**, for example, resolves most variable types at compile time.
If JavaScript had a similar mechanism we wouldn't be able to run our
application until we resolved these compile errors:

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

The big advantage to static checking is it removes potential performance
bottlenecks: every type check that can be resolved *once* at compile time
removes a type check that would need to be invoked during *every* call of a
function or method. This can remove millions or billions of overhead of
type-checking calls per hour in an application that is used intensely.

### Roll-your-own dynamic checking
Static type checking does not work in all situations, especially when
dealing with data from unknown or untrusted sources. In those cases,
the application must resort to *dynamic type checking* at run-time.
JavaScript again has limited native tools to assist with this compared to
other languages. Even the `typeof` keyword provides disappointing result,
failing to distinguish between an object and an array.

## 2. Type errors are challenging to resolve
Type errors can be hard to identify and debug. Any one routine that fails to
check for type can send bad results on in the call stack and result in a
cascade of errors.

## 3. Type errors are often serious
As we have shown, type errors can result severe application failures and
security holes. Imagine where we might crash a web server farm by sending
strings instead of number to a JSON API. This stuff happens.

# How do we get type safety?

Getting type safety in native JavaScript isn't particularly
hard. First recognize that the most likely cause of type errors occurs from
function inputs and returned values. If we guarantee input types 
using **typecasting** and track the intended types for our variables we
can nearly eliminate JavaScript type errors.

## Typecasting

Typecasting, for the purposes of this article, is the process of converting
a value into the desired data type using a very strict set of rules. It
is **guaranteed** to return the correct type **or** a failure failure value.

### Use typecasting
Let's rewrite our problem function from above using typecasting:

```js
    function doStuff(arg_counts, arg_run)
    {
        var counts = castInt(arg_counts)
        var run = castFn(arg_run)

        while (counts < 0)
        {
            run( counts )
            counts+=1
        }
    }

    function reports(info)
    {
        console.log(info)
    }

    doStuff('-3', reports)
```
After this update, the function is nearly impervious to type errors.

### Get typecast methods
We can get typecast methods from the [hi\_score][1] project.
Installation is simple: `npm install hi_score`. If you edit the
example application you can use all the `cast` methods from `xhi.util.js`.

```bash
  npm install hi_score;
  cd npm_modules/hi_score;
  npm install;
  npm test;
  npm run cover;
  google-chrome coverage/lcov-report/index.html;
  google-chrome ./index.html;
```

You don't have to use the whole library; you can just crib the methods from
`xhi.util.js` if you want. Go ahead, you won't hurt anyone's feelings.

### More typecasting
The `xhi` utility functions for casting values are as follows:

```
  castBool, castFn, castInt, castJQ,
  castList, castMap, castNum, castObj, castStr
```

All the `cast` methods take either one or two arguments. Only numbers, strings,
and integers can be converted and only when the conversion is unambiguous.

The first argument is always the value to cast; the second argument is the
value to use if the `cast` fails. If a second argument is omitted,
`undefined` is returned instead. Let's update our example again to use an
argument map and a default values.

```js
    function doStuff(arg_map) {
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
    doStuff({counts:'-3',run:reports});
```

To be clear, this is dynamic type checking. There is no native static
checking in JavaScript in any real sense (one could make arguments about the
JIT compiler, but at best such checking is *very* incomplete).

### The Zen of typecasting
The `cast` methods have four benefits over native JavaScript coercion:
they are explicit, predictable, strict, and self-documenting.
Let's compare the JavaScript coercion examples with the `cast` methods:

#### JavaScript type coercion examples
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
| [10]         | t     |     | 10  | [10] | { 0 : 10 } | | '10' |
| [10,20]      | t     |     | NaN | [10,20] | { 0: 10, 1:20 }  | '10,20' |
| false        | FALSE |     | 0   |      |     | 'false'  |
| function(){} | t     | function(){} | NaN | [] | {} | 'function(){}' |
| null         | FALSE |     | 0   |      |     | 'null'      |
| true         | t     |     | 1   |      |     | 'true'      |
| undefined    | FALSE |     | NaN |      |     | 'undefined' |
| {}           | t     |     | NaN | []   | {}  | '0'         |
| -Infinity    | t     | -Infinity  | |   |     | '-Infinity' |
| Infinity     | t     | Infinity   | |   |     | 'Infinity'  |

Blank cells are conditions where an exception is thrown.

#### Cast method conversion examples
| Value        | Bool  | Fn   | Num  | Ary   | Obj  | Str     |
| :---         | :---  | :--- | :--- | :---  | ---  | ---     |
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

Blank cells are conditions where the default value will be returned.

Typecasting makes writing **type safe** functions in JavaScript a breeze.
And don't underestimate the value of self-documenting either.
For example the `castFn` call to define the `run` variable clearly illustrates
we expect a function despite the fact that `run` is an awful name for it.

As illustrated by the 'awful' name for the `run` function, there are a few
practices that, when combined with typecasting, can result in a virtuous
cycle that accelerates product development:

1. Name variable to indicate type
2. Write consistent API definition
3. Test the APIs

Let's look at each of these and update our sample code as we go along.
These are the steps we take to bring the code to the next level of Zen.

## Name variable to indicate type
Our first step to update our example code is to fix the awful variable names.
While they might make sense to one brain, they are inconsistent and
misleading to another. Let's apply this [full code standard][a] which names
variables by type. There's also a handy [reference cheat sheet][b].


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

We also fix up other details suggested by the guide such as formatting (tabs,
spaces, alignment, K&R indenting) and replaced the `while` loop with a `for`
loop. This code will now pass JSLint. If this utility were 100 lines long,
as long as we know the naming convention, we can easily spot that `fn` is a
function and idx is an integer.

The [full code standard][a] argues that a simple naming convention can can
vastly reduce the need for comments. We think it's an interesting read if you
enjoy philosophical discussions.

## Write consistent API definition
Now that we have consistent naming that easily identifies type, creating in-line
API documentation is easy and recommended:

```js
  // BEGIN utility method /repeatFn/
  // Summary   : repeatFn({ _int_ : <integer>, _fn_ : <function> )
  // Purpose   : Repeats a call to a function as long as the index (idx) is < 0
  // Example   : repeatFn({
  //               _int_ : -3,
  //               _fn_ : function (idx ) { console.log( idx )
  //             });
  // Arguments  : (named)
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

Remember where we suggested you shouldn't copy our first code example because
it was "awful?" Now the code is impervious to type errors, readable, maintainable,
and well documented. Go ahead and copy it if you want :)

## Test the APIs
We can use tools like `Istanbul` and `nodeunit` we can now easily test to the API
we just documented for our shiny, new, and nearly bullet-proof `repeatFn`
function. Check out the test suite for `hi_score` to see how its done:

```bash
  npm install hi_score;
  cd npm_modules/hi_score;
  npm install;
  npm test;
  npm run cover;
  google-chrome coverage/lcov-info/index.html
  vi test/xhi_level_0.js
```

# What about frameworks and libraries?
This is an excellent question: how do native **typecasting** techniques
compare with [Flow][3] and [TypeScript][4]? I intend to publish a subsequent
article that answers this question. In **theory** both these
transpiled languages **should** be able to provide static type checking and
eliminate many dynamic type checking calls. However, in **practice** however
the results may be surprising since the real-world overhead of `cast` methods
can be actually quite low.

We hope you found this useful! Please share your thoughts and experiences
in the comments.


Cheers, Mike

[1]:https://github.com/mmikowski/hi_score
[2]:https://www.npmjs.com/package/hi_score
[3]:https://flowtype.org/
[4]:https://www.typescriptlang.org/index.html
[a]:https://github.com/mmikowski/spa/blob/master/js-code-std-2016.pdf
[b]:https://github.com/mmikowski/spa/raw/master/js-cheat-sheet-2016.pdf
