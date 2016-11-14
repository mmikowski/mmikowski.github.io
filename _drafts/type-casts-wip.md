--
layout: post
title: TypeCasting in JavaScript, Part I

![Typecast using native JavaScript, will it save your soul?][0]
---




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

## Recent changes
We bumped the version to 1.1 because there was a significant API addition:
support for arbitrary-depth nested conditional expressions. However, the
API remains almost completely backward compatible with version 1.0.
Media queries are now easily created and automatically optimized.
We added numerous regression tests to check this capability, and
currently have 106 assertions, which you can check like so:


## JavaScript default conversions

| Value        | Number     | String            | Boolean |
| ""           | 0          | ""                | false   |
| "0"          | 0          | "0"               | true    |
| "1"          | 1          | "1"               | true    |
| "20"         | 20         | "20"              | true    |
| "twenty"     | NaN        | "twenty"          | true    |
| -Infinity    | -Infinity  | "-Infinity"       | true    |
| 0            | 0          | "0"               | false   |
| 1            | 1          | "1"               | true    |
| Infinity     | Infinity   | "Infinity"        | true    |
| NaN          | NaN        | "NaN"             | false   |
| [ ]          | 0          | ""                | true    |
| ["ten"]      | NaN        | "ten"             | true    |
| ["ten","t"]  | NaN        | "ten,t"           | true    |
| [10,20]      | NaN        | "10,20"           | true    |
| [20]         | 20         | "20"              | true    |
| false        | 0          | "false"           | false   |
| function(){} | NaN        | "function(){}"    | true    |
| null         | 0          | "null"            | false   |
| true         | 1          | "true"            | true    |
| undefined    | NaN        | "undefined"       | false   |
| { }          | NaN        | "[object Object]" | true    |

Source: [W3schools JS conversion chart][2]

## Cast function conversions

The `xhi` utility functions for casting values are as follows:
castBool, castFn, castInt, castJQ, castList, castMap, castNum, castObj, and
castStr.  The conversion is nowhere near as loose as typical JavaScript
coersion.  Only numbers, strings, and integers can be coerced, and only when
the conversion is unambigous.  Let's see how these cast functions handle the
same values as the W3Schools chart:


| Value        | Bool | Fn | Int | JQ | List | Map | Num | Obj | Str      |
| ""           | -    | -  | -   | -  | -    | -   | -   | -   | ""       |
| "0"          | -    | -  | 0   | -  | -    | -   | 0   | -   | "0"      |
| "1"          | -    | -  | 1   | -  | -    | -   | 1   | -   | "1"      |
| "20"         | -    | -  | 20  | -  | -    | -   | 20  | -   | "20"     |
| "ten"        | -    | -  | -   | -  | -    | -   | -   | -   | "ten" |
| -Infinity | - | - | -Infinity  | -  | - | - | -Infinity | - | "-Infinity" |
| 0            | -    | -  | 0   | -  | -    | -   | 0   | -   | "0"      |
| 1            | -    | -  | 1   | -  | -    | -   | 1   | -   | "1"      |
| Infinity     | -    | -  | -   | -  | - | - | Infinity | - | "Infinity" |
| NaN          | -    | -  | -   | -  | -    | -   | -   | -   | -        |
| [ ]          | -    | -  | -   | -  | []   | -   | -   | -   | -        |
| ["ten"]      | -    | -  | -   | -  | ["ten"] | - | -  | -   | -        |
| ["ten","t"]  | -    | -  | -   | -  | ["ten","t"] | -  | -   | -        |
| [10]         | -    | -  | -   | -  | [10]    | - | -  | -   | -        |
| [10,20]      | -    | -  | -   | -  | [10,20] | - | -  | -   | -        |
| false        | false| -  | -   | -  | -    | -   | -   | -   | -        |
| function(){} | -    | function(){} | - | - | - | - | - | -   | -        |
| null         | -    | -  | -   | -  | -    | -   | -   | -   | -        |
| true         | true | -  | -   | -  | -    | -   | -   | -   | -        |
| undefined    | -    | -  | -   | -  | -    | -   | -   | -   | -        |
| {}           | -    | -  | -   | -  | -    | {}  | -   | -   | -        |

[0]:/images/2016-11-14-typecast-02.jpg
[1]:https://github.com/mmikowski/hi_score
[2]:https://www.npmjs.com/package/hi_score
[3]:http://www.w3schools.com/js/js_type_conversion.asp
