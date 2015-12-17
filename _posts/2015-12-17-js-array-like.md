---
layout: post
title: Using Array.prototype for jQuery and argument manipulation
---
*Push, pop, shift, unshift?  Sure!*

### Overview

One of the more frustrating aspects of JavaScript are enumerable
objects that look, smell, and feel like an array, but really are not.
These objects frequently lack array methods (like `pop`,`shift`,`push`,
`unshift`) and properties (like `length`).  Three great examples come
to mind:

1. jQuery collections.
2. The `document.styleSheets` enumerable list.
3. The `arguments` enumerable list

There are two options, discussed below.

### Option 1: Convert the list into an array

This one is probably better for `document.styleSheets` and `arguments`, 
because array manipulatuion of these in-place either is dangerous or
simply does not work.  Conversion is simple:

    arg_list = [];
    Array.prototype.push.apply( arg_list, arguments );

And our work is done: `arg_list` is a *real array* and can be manipulated 
as such.

### Option 2: Use call to manipulate in-place.

This is especially handy for jQuery collections.

    // Module declarations
    var
      listPop     = Array.prototype.pop,
      listPush    = Array.prototype.push,
      listShift   = Array.prototype.shift,
      listSlice   = Array.prototype.slice,
      listSplice  = Array.prototype.splice,
      listUnshift = Array.prototype.unshift,

      rotateByThree
      ;

    // Used to rotate a list
    rotateByThree = function ( list ) {
      // The following is the same as list.unshift( list.pop() )
      // but works on many more array-like entities
      //
      listUnshift.call( list, protoPop.call( list ));
      listUnshift.call( list, protoPop.call( list ));
      listUnshift.call( list, protoPop.call( list ));
    };

The function, `rotateByThree` not only works on regular arrays, but also
on array-like objects such as a jQuery collection.  We use 
`Array.prototype` to do more things, like join two jQuery collections:

    $h_list = $( 'h1' ); $h2_list = $( 'h2' );

    // Array.prototype.push.apply( $h1_list, $h2_list );
    listPush.apply( $h_list, $h2_list );

Do be aware that if you use these manipulations in-place on array-like 
objects, you *are* forging into uncharted territory.  The jQuery object
methods, for example, might get corrupted if it caches the list length
or other properties. Test thoroughly, Padawon.

Stay tuned for a blog post on my favorite technique for keeping JavaScript
object light and fast.

Cheers, Mike
