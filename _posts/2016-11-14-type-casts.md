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
**typecasting**. We show you how to typecast using simple functions, and
then we show how it integrates with other best practices such as naming
variables by type, consistent API documentation, and testing.

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
value. These are usually easy to identify because they have a defining a
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

Let's look at a few examples. If you're playing along at home, you might
want to cover the right-hand side of the table below to see if you can
guess what the returned value will be. 

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
market (V8, Jakarta, Nitro, and IonMonkey) and [at least another dozen minor 
players][6]. We wouldn't be surprised to find that the behaviors show here 
(using V8) vary from engine to engine and generation to generation.

Other languages have less complex behaviors because they usually have
stricter type checking, stricter coercion rules, and fewer polymorphic
operators, and fewer vendors. **Perl**, for example, uses the dot (`.`) 
operator to *join* strings and the plus (`+`) operator to add values.
Perl also uses *sigals* (prefixes) like `$`, `@`, and `%` to denote variable
types and a special syntax to identify 'wild-card' references.

### 3.1.3 No static checking
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

Perhaps the greates advantage of static (compile-time) type checking is
that it can potential performance bottlenecks: every type check that can be
resolved *once* at compile time removes a type check that would need to be
invoked during *every* call of a function or method. This can remove a large
number of calls during application run-time.

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
in a cascade of errors. The originating flaw can be hard to spot especially 
hard if variable aren't named by their intended type, like so:

```js
  var total = watches / in_use;
```

If we simply name our variable by intended type the  mismatches become
obvious:

```js
  var total_str = watch_list / use_bool;
```
Yes, the intended variable type **is that important**.
We've had to maintain plenty of third-party modules which use an
*if-it-sounds-good-use-it* convention with no hint of variable type or
purpose in the name, and we can't count the time we've wasted trying
to deduce the *intended* variable type for many symbols by combing through 
multiple functions and files. There are far better ways to spend development 
time than on this needless cognitive overhead.

## 3.3. Type errors are often serious
As we have shown, type errors can result in severe application failures and
security holes. Imagine some NodeJS code that doesn't properly type-check
its JSON API. One could implement a Denial Of Service (DOS) attack and shut 
down an entire web farm by simply sending strings instead of numbers in API 
requests. This stuff happens.

# 4. How do we get type safety?
Getting type safety in native JavaScript isn't particularly
hard. First recognize that the values we need to worry about are inputs
to public methods and external data. When we guarantee input types using 
typecasting and track the intended types for our variables we can nearly
eliminate JavaScript type errors.

## 4.1 Typecasting
Typecasting, for the purposes of this article, is the process of converting
a value into the desired data type using a very strict set of rules. It
is **guaranteed** to return the correct type **or** a failure failure value.

### 4.1.1 Use typecasting
Let's rewrite our problem function from above using typecasting:

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
This is much better. The function is now impervious to type errors.

### 4.1.2 Get typecast methods
We can get typecast methods from the [hi_score][1] project.
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

### 4.1.2 More typecasting
The `xhi` utility library provides the following typecast methods:

```
  castBool, castFn, castInt, castJQ,
  castList, castMap, castNum, castObj, castStr
```

All these methods take either one or two arguments. Only numbers, strings,
and integers are converted between types and only when the conversion is
unambiguous.

The first argument is always the value to cast; the second argument is the
value to use if the `cast` fails. If a second argument is omitted,
`undefined` is returned instead. Let's update our example again to use an
argument map and a default value for the `counts` variable:

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
use a typecasting library like [Flow][3] or a framework like [TypeScript][4]
which in theory can perform static type analysis. However, it is not clear
that this provides a performance benefit worth the overhead. We will compare
native typecasting with these solutions in an upcoming article.

### 4.1.3 The Zen of typecasting
The `cast` methods have four benefits over native JavaScript coercion:
they are explicit, predictable, strict, and self-documenting.
Let's compare the JavaScript coercion examples with the `cast` methods:

#### 4.1.3.1 JavaScript type coercion examples

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

#### 4.1.4.1 hi_score Cast method examples

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

Blank cells are conditions where the default value will be returned.

Typecasting makes writing type safe functions in JavaScript a breeze.
And they also do a great job of self-documenting the code.
The `castFn` call to define the `run` variable, for example, clearly
illustrates we expect a function despite the fact that `run` is an
awful function name.

## 4.2 Best practices that work well with typecasting
There are a few best practices that work with typcasting to create
a virtuous cycle that accelerates product development:

1. Name variables to indicate type
2. Write consistent API definition
3. Test the APIs

Let's look at each of these and update our sample code as we go along.
These are the steps we take to bring the code to the next level.

## 4.3 Name variables to indicate type
Our first step to update our example code is to fix the awful variable names.
While they might make sense to one brain, they are inconsistent and
misleading to another. We've purposely used plural variable names to
illustrate how bad a practice they can be. Let's fix this using our
[JS Code Standard ][a] where we name variables by type. There's also a
handy [reference cheat sheet][b] if we want to just focus on
the rules and not the reasons why the rules exist.

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
formatting (tabs, alignment, K&R indenting) and replaced the dangerous
`while` loop with a `for` loop. This code will now pass JSLint.
Thanks to the naming convention we can tell that tell that `fn` should be
a function and and `idx` should be an integer *regardless if any other code
is visible*.

The [full code standard][a] discuss *why* a simple naming convention can
vastly reduce the need for comments. We think it's an interesting read if
you're itching for reason.

## 4.4. Write consistent API definitions
Now that we have consistent naming that easily identifies type and
formatting that makes the code easy to read, creating in-line API
documentation is simple and recommended. Using the guide from the
code standard we get the following:

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

Remember where we suggested you shouldn't copy our first code example
because it was "awful?" Now the code is impervious to type errors, readable,
testable, maintainable, and well documented. Please copy it if you want!

## 4.5 Test the Code
We can use tools like `Istanbul` and `nodeunit` along with the in-line API
documentation to test our our shiny new type-safe `repeatFn`
function. Check out the test suite for `hi_score` to see how this is done:

```bash
  npm install hi_score;
  cd npm_modules/hi_score;
  npm install;
  npm test;
  npm run cover;
  google-chrome coverage/lcov-info/index.html
  vi test/xhi_level_0.js # or whatever editor you prefer
```

# 5. What about frameworks and libraries?
An excellent question is how does this native **typecasting** technique
compare with [Flow][3] and [TypeScript][4]? We intend to publish a sequel
that will answer this question. In **theory** both these transpiled
languages **should** be able to provide static type checking and eliminate
many dynamic type check calls. However, in **practice** the results may
be surprising since the real-world overhead of `cast` methods can be
actually quite low, and these languages introduce their own overhead.

One thing to remember is don't go typcasting crazy. Use it where it makes
sense, when processing external data and inputs of public
methods. Private methods often don't benefit from typecasting since the
the callers and data types will already be known.

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
