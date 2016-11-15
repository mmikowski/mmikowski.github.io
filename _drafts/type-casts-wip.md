--
layout: post
title: TypeCasting in JavaScript, Part I

![Typecast using native JavaScript, will it save your soul?][0]
---



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


[Hi_Score][1] is a library system that I've been constructing for a little
while now that embodies many of the hard-won lessons I've accumulated from
years of commercial SPA development.  It's not a framework, and its not
complete, but I'm using it currently as the foundation of all my projects.
[Installation][2] is simple: `npm install hi_score]`. The
The
README
gives you the rest o
*JSDoc vs. TypeScript vs. `hi_score` cast functions*

Do we need to cede control of our application to complex frameworks like
TypeScript or Closure to ensure type safety with JavaScript?  Well, I
haven't. Instead I've solved most variable type challenges by using two
simple rule that that accelerate and enhance collaboration:

1. Name your variables for type.
2. Use `cast` methods to enforce type for function arguments and other
external data.

I will try to make this

### 1. Name your variables for type
It's easy to name variables for type by adding a 2-4 character suffix.
See the [reference cheat sheet][b] or read [full code standard][a].  This
embeds your intentions *in the code* which is much more durable than using
comments to explain variables.

### 2. Use `cast` methods to ensure type
The `cast` methods are simple, self-documenting functions found in the base
utilities library (`xhi.util.js`). They convert a provided argument into the
requested type using standard JS rules. If this is not possible, they return
the alternate value which by default is `undefined`. They are typically used
at the top of functions where arguments are processed.

```js
  var
    __util    = xhi._util_,
    __castInt = __util._castInt_,
    __castMap = __util._castMap_
    ;

  function getTimeList( arg_map ) {
    var
      map      = __castMap( arg_map,    {} ),
      end_ms   = __castInt( map._end_ms_   ),
      start_ms = __castInt( map._start_ms_ )
      ;

    // Return empy array if time range is not provided
    if ( ! ( end_ms && start_ms ) ) { return []; }

    // ... continue logic here
  }
```

Compare this with solutions that use `JSDoc` like TypeScript or
Closure. Both introduce a transpile step to make the comments userful and add
an additional layer of complexity to provide similar benefits.

## Overview

## JavaScript default conversions

| Value        | Bool  | Fn  | Num | Ary  | Obj | Str        |
| ---          | ---   | --- | --- | ---  | --- | ---        |
| ''           | FALSE |     | 0   |      |     | ''         |
| '0'          | t     |     | 0   |      |     | '0'        |
| '1'          | t     |     | 1   |      |     | '1'        |
| '20'         | t     |     | 20  |      |     | '20'       |
| 'ten'        | t     |     | NaN |      |     | 'ten'      |
| 0            | FALSE |     | 0   |      |     | '0'        |
| 1            | t     |     | 1   |      |     | '1'        |
| NaN          | FALSE |     | NaN |      |     | 'NaN'      |
| []           | t     |     | 0   | []   | {}  | ''         |
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


Source: [W3schools JS conversion chart][2]

## Cast function conversions

The `xhi` utility functions for casting values are as follows:
castBool, castFn, castInt, castJQ, castList, castMap, castNum, castObj, and
castStr.  The conversion is nowhere near as loose as typical JavaScript
coercion.  Only numbers, strings, and integers can be coerced, and only when
the conversion is unambiguous.  Let's see how these cast functions handle the
same values compared to the W3Schools chart.

| Value        | Bool  | Fn  | Num | Ary  | Obj  | Str     |
| ---          | ---   | --- | --- | ---  |      | ---     |
| ''           |       |     |     |      |      | ''      |
| '0'          |       |     | 0   |      |      | '0'     |
| '1'          |       |     | 1   |      |      | '1'     |
| '20'         |       |     | 20  |      |      | '20'    |
| 'ten'        |       |     |     |      |      | 'ten'   |
| 0            |       |     | 0   |      |      | '0'     |
| 1            |       |     | 1   |      |      | '1'     |
| NaN          |       |     |     |      |      |         |
| []           |       |     |     | []   |      |         |
| ['ten']      |       |     |     | ['ten']     | |       |
| ['ten','t']  |       |     |     | ['ten','t'] | |       |
| [10]         |       |     |     | [10]        | |       |
| [10,20]      |       |     |     | [10,20]     | |       |
| false        | FALSE |     |     |      |      |         |
| function(){} |       | function(){} | | |      |         |
| null         |       |     |     |      |      |         |
| true         | true  |     |     |      |      |         |
| undefined    |       |     |     |      |      |         |
| {}           |       |     |     |      | {}   |         |
| -Infinity    |       |     | -Infinity  | |    |         |
| Infinity     |       |     |  Infinity  | |    |         |

[0]:/images/2016-11-14-typecast-02.jpg
[1]:https://github.com/mmikowski/hi_score
[2]:https://www.npmjs.com/package/hi_score
[3]:http://www.w3schools.com/js/js_type_conversion.asp

