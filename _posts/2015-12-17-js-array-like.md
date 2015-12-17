---
layout: post
title: Manipulating jQuery and other array-like objects
---
*Pop, push, slice, splice, shift, and unshift anyone?*

### Overview

JavaScript has a number of enumerable objects that look, smell, 
and feel like arrays but unfortunately do not have `Array` in
their prototype chain.  These objects usually lack the "true"
array methods (like `pop`, `push`, `shift`, `slice`, `splice', 
and `unshift`) and properties (like `length`).  These object 
can be frustrating to manipulate because we *want* to use Array
methods and properties.  Three enumerable objects come immediately to mind:

1. jQuery collections.
2. The `document.styleSheets` enumerable list.
3. The `arguments` enumerable list

There are at least two options to manipulate these data like real
arrays: either create a new data structure that **is** a *real array*,
or manipulate the data in-place.  Both approaches use
`Array.prototype` methods and `call` or `apply`.

### Option 1: Convert the list into an array

This one is probably better for `document.styleSheets` and `arguments`, 
because direct manipulation is either dangerous or simply does not work.
Creating the new array is usually quite simple:

    var arg_list = [], stylesheet_list = [];

    Array.prototype.push.apply( arg_list, arguments );
    Array.prototype.push.apply( stylesheet_list, document.styleSheets );

Both `arg\_list` and `stylesheet\_list` are *real arrays* that 
can be maniplated as such.  Don't forget that there are many
other handy array methods like `each` or `map` or `reduce`.

### Option 2: Use `Array.protype` to manipulate data in-place.

I have found this useful to manipulate jQuery collections.
Here's an example:

    // Module declarations
    var
      listPop     = Array.prototype.pop,
      listPush    = Array.prototype.push,
      listShift   = Array.prototype.shift,
      listSlice   = Array.prototype.slice,
      listSplice  = Array.prototype.splice,
      listUnshift = Array.prototype.unshift,

      rotateByThree, $h1
      ;

    rotateByThree = function ( list ) {
      // The following is the same as list.unshift( list.pop() )
      // but works on many more array-like entities
      //
      listUnshift.call( list, protoPop.call( list ));
      listUnshift.call( list, protoPop.call( list ));
      listUnshift.call( list, protoPop.call( list ));
    };

    $h1 = $( 'h1' );
    rotateByThree( $h1 );

The function, `rotateByThree` not only works on regular arrays, but also
on array-like objects such as a jQuery collection.  We use 
`Array.prototype` to do more things, like join two jQuery collections:

    var
      listPush = Array.prototype.push,
      $h_list, $h2_list
      ;

    $h_list = $( 'h1' ); $h2_list = $( 'h2' );

    // The following is the same as 
    //   Array.prototype.push.apply( $h1_list, $h2_list );
    //
    listPush.apply( $h_list, $h2_list );

Do be aware that manipulating data in-place on array-like 
objects can be dangerous.  The jQuery object methods, for example,
might get corrupted if they rely on cached property values such
as list length. Tread lightly and test thoroughly, Padawan.

Stay tuned for a blog post on my favorite technique for keeping JavaScript
object light and fast.

Cheers, Mike
