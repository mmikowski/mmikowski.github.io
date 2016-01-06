---
layout: post
title: Wrestling jQuery collections and other array-like objects
---
*Pop, push, slice, splice, shift, and unshift anyone?*

### Overview

JavaScript has a number of enumerable objects that look, smell,
and feel like arrays but unfortunately do not have `Array` in
their prototype chain.  These objects can be frustrating
because we usually want to manipulate them like "true"
arrays, yet they do not have the full range of array methods (like
`pop`, `push`, `shift`, `slice`, `splice`, and `unshift`) or
properties.  Three enumerable objects come
immediately to mind:

1. jQuery collections
2. `document.styleSheets`
3. `arguments`

There are at least two options to manipulate these objects like real
arrays: either create a new data structure that **is** a real array,
or manipulate the data in-place.  Both approaches use
`Array.prototype`.

### Option 1: Convert the object into an array

This is the safest option, especially for 'special' objects
like `document.styleSheets` and `arguments`.
Direct manipulation of either of these can have far-reaching
and unexpected effects.  It is better to make a *real* array from 
them, and then if we need to alter the original object, we can
do so later. Creating the new array is usually quite simple:

    var arg_list = [], stylesheet_list = [];

    Array.prototype.push.apply( arg_list, arguments );
    Array.prototype.push.apply( stylesheet_list, document.styleSheets );

Both `arg_list` and `stylesheet_list` are real arrays that
can be maniplated using using `each`, `map`, `reduce`, or any
other method supported by the Array prototype.

### Option 2: Use `Array.prototype` to manipulate data in-place.

I have found this useful to manipulate jQuery collections.
Here's an example:

    var
      aryProtoPop     = Array.prototype.pop,
      aryProtoUnshift = Array.prototype.unshift,

      rotateList, $h1List
      ;

    // BEGIN utility /rotateList/
    // Moves the last element of the /list/ to the first element.
    // Will do so /count/ times.  If /count/ is not provided,
    // will rotate once.
    //
    rotateList = function ( arg_list, arg_count ) {
      var count = arg_count === undefined ? 1 : arg_count;

      for ( i = 0; i < count; i++ ) {
        aryProtoUnshift.call( arg_list, aryProtoPop.call( arg_list ));
      }
    };

    $h1List = $( 'h1' );
    rotateList( $h1List, 3 );

The function, `rotateList` not only works on regular arrays, but also
on array-like objects such as a jQuery collection.  We can use
`Array.prototype` to do more things, like join two jQuery collections:

    var
      aryProtoPush = Array.prototype.push,

      addToList, $h1List, $h2List
      ;

    addToList = function ( tgt_list, src_list ) {
      aryProtoPush.apply( tgt_list, src_list );
    };

    $h1List = $( 'h1' );
    $h2List = $( 'h2' );

    addToList( $h1List, $h2List );

    // $h1List now has $h2List added to it.

Remember that manipulating data in-place on array-like
objects can be dangerous.  jQuery methods, for example,
might get corrupted if they rely on cached property values such
as list length. Tread lightly and test thoroughly, Padawan.

Stay tuned for a blog post on my favorite technique for making
fast, light, and clean JavaScript objects.

Cheers, Mike
