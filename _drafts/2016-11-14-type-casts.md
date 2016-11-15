---
layout: post
title: Type safety with native JavaScript

![Come on, you know you want type safety][0]
---
*Why everybody wants type safety and how to get it*

# Overview

Many tools like IDEs, frameworks, libraries, and linters try to provide
some level of type safety to JavaScript. This article defines types
safety, illustrates why we want it, and shows how we can get
it using native JavaScript.

# What is type safety?

Type safety is the extent a programming language discourages or prevents
**type errors**. Type errors happen when a developer uses the wrong variable
type in an expression or when invoking a function.

Let's see how easy it is to create a **type error** in Javascript using the 
semantic style ("if it sounds right, name it") all the cool kids are using
these days:

```js
  function doStuff ( counts, run ) {
    while ( counts < 0 ) {
      run( counts );
      counts += 1;
    }
  }
  
  function reports ( info ) { console.log( info ); } 
  doStuff( '-3', reports );
```

Well that didn't take long.  First, let's identify the initial **type error**:
the first argument we provided to the `repeatFn` invocation. This value should
be an integer, but we instead provide a string.  Then all hell breaks loose.

Thanks this initial type error, the `repeatFn` invocation starts a
loop that will rather quickly consume all available resources within its 
execution environment. Eventually it will cause the execution environment to 
fail, resulting in a forced-kill of a of a NodeJS process, or a 
browser tab, or a browser, or event the entire OS.

If we watch the progression of the value `idx` in the `while` loop we see the
following series: `'-3', '-31', '-311', '-3111', ...`.  The test condition
`idx < 0` *does* coerce the `idx` string into a number, but that value is
always than 0.  That's because the expression `idx += 1` always *appends* the
string `'1'` to the `idx` string.

# Why do we want type safety?

We want type safety with JavaScript because **type errors** can be quite
troublesome:

1. JS type errors are easy to create
2. They can be challenging to identify and debug
3. They can result in severe application failures and security holes. 

## JS type errors are easy to create

JavaScript is missing almost all type controls because it was not originally 
intended to run large-scale applications. This makes creating type errors
exceedingly easy.

### No variable declarations
JavaScript has no means to declare a variable a type. A variable may
contain any type and that may be changed at any time during execution. 
You may have seen JavaScript projects where a single symbol like 'watches'
is used as an object, a map, a list, a boolean, a string, and integer, and a 
number.

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
rules, `sigals` to indicate type (like $, @, and % in Perl and PHP), and fewer 
polymorphic operators.  For example, Perl and PHP use the `.` operator to 
join strings.

## No static checking
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

The big advantage to static checking is it removes a potential performance 
bottlenecks: every type check that can be resolved *once* at compile time 
removes a type check that would need to be invoked during *every* call of a 
function or method.

## Roll-your-own dynamic checking 
Static type checking does not work in all situations, especially when
dealing with data from unknown or untrusted sources. In those cases,
the application must resort to *dynamic type checking* at run-time.
JavaScript again has limited native tools to assist with this.  Indeed, even 
the `typeof` keyword provides disappointing results.

# How do we get Type safety?

Getting a near-complete type safety in native JavaScript isn't particularly
hard. We found three techniques do the trick:

1. Name our variables for type
2. Use `cast` methods to ensure type
3. Write consistent API definition

This approach not only provides a great deal of type safety in JavaScript but
they also reinforce other positive project aspects. Typecasting and 
variable-type-names provides always-accurate documentation and eliminate the 
need for comments, *and* they make consistent API documentation very easy to 
accomplish, which in turn makes writing and validating code tests better.

Let's look technique in order.

## Typecasting

Typecasting, for the purposes of this article, is the process of converting
a value into the desired data type using a very strict set of rules. It 
is **guaranteed** to return the correct type **or** a failure failure value.

We recognize that the most likely cause of type errors occurs from function 
inputs and returned values.  By simply typecasting function arguments, we can
eliminate a large class of type errors.

Let's rewrite our problem function from above using typecasting:

```js
  /*global xhi */
  var
    castFn  = xhi._util_._castFn_,
    castInt = xhi._util_._castInt_
    ;
  
  function repeatFn ( arg_idx, arg_fn ) {
    var
      fn  = castFn(  arg_fn  ),
      idx = castInt( arg_idx );
    if ( idx === undefined && fn === undefined ) { return; }

    while ( idx < 0 ) {
      fn( idx );
      idx += 1;
    }
  }

  repeatFn( '-3', function ( i ) { console.log( i ); } );
```

After this update, the function is nearly impervious to type errors.

You can get typecast methods from the [hi_score][1] project.  Installation is
simple: `npm install hi_score`.  If you edit the example application you can 
use all the `cast` methods from `xhi.util.js`.

```bash
  npm install hi_score;
  cd npm_modules;
  npm install; 
  npm test;
  npm run cover;
  google-chrome ./index.html
```

All the `cast` methods take either one or two arguments.
The first argument is always the value to cast; the second argument is the 
value to use if the `cast` fails.  Here are some examples:





# Did we miss anything?

Drop us a note below if you have a particular area of interest or strategy
that you think we've missed.

Cheers, Mike

[0]:/images/2016-11-14-typecast-02.jpg
[1]:https://github.com/mmikowski/hi_score
[2]:https://www.npmjs.com/package/hi_score
[3]:https://flowtype.org/

