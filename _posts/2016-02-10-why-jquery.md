---
layout: post
title: Why jQuery?
---
*jQuery: the most useful library we love to hate*

JS Stratification across the nation
===================================
Recently the JavaScript community appears to have striated into three
distinct groups:

1. **The Vanilla JS Gang** believes all JS code should use only native
   constructs. jQuery is avoided because this group thinks it is too slow.
   This group tends to attract experienced developers.

2. **The Library Liberation Front** believe much of their utility code should
   come from popular, well-tested libraries. However, if a desired capability
   isn't available from existing libraries, a member of **The Front** will 
   often *collaborate* with an existing library team to add it.  More
   radical members of **The Front** have been known to publish entire
   libraries themselves.  Members of **The Front** will sometimes avoid library
   calls and write a faster custom solution as needed. 
   This group tends to attract experience developers and, 
   especially since jQuery's arrival, a fair number of neophytes.

3. **The Framework Country Club** believe web client development is 
   too scary to approach unarmed. Native JS takes a back-seat to an
   ["it's-our-way-or-the-highway" domain specific language
   ](2016_2016-01-30-no-framework) which guides the developer into 
   *what the framework creator thinks* is best practice. One only has to 
   consider the Closure framework to see how far off the mark
   the framework creator can get. This group tends to attract 
   less-experienced developers and lots of neophytes.

I'm a card-carrying member of **The Library Liberation Front**,
and this post explains why.

Vanilla vs. jQuery
==================
I have had the pleasure of writing multiple commercial code bases both
with and without jQuery and can give you the inside scoop. The first Vanilla
JS SPA I wrote was the AMD comparison shopping site which went live in 2007.
Although jQuery 1.0 had been released in August 2006, it was still far from
a popular or even stable library. And I didn't even know it existed until
after I finished the project. The site was replaced by a much slower
but much-more-SEO-friendly site in 2010. Today, the SEO issue is minimal,
but it was a big limitation with SPAs back at that time.

My second commercial Vanilla JS product was the Qualaroo universal client,
released world-wide in 2014. The last I knew it was servicing 100m users per day.
I got to write *everything*. Event queues and promises?
*Wrote 'em*. DOM search mechanisms? *Wrote those too.* Object extend and
numerous other utilities? *Yep.* And if I had to do it all over again, I
would do the exact same thing, because *the desired user experience* 
determined how the code [was developed and deployed
](https://www.youtube.com/watch?v=aoH0J6lL2w0).

I am currently working on a Vanilla JS library (PowerCSS), *and* 
using jQuery for another project. Why? Because one benefits from jQuery,
and one doesn't. It's as simple as that.

When should we AVOID jQuery?
============================
We should *avoid* jQuery when it is very important that the code be as
light-weight and performant as possible, as with the Qualaroo universal client.
In that deployment, a *single file smaller than the compressed jQuery library*
provided the entire application, data, images, and CSS. We got to write or
refactor *everything* from the ground up, which provided highly optimized
code at the expense of time. And we also got to write regression tests for
*every* level of the application.

When should we USE jQuery?
==========================
We should *use* jQuery when we value development time, a stable and well-known
DSL, an extensible architecture, and a vast, stable ecosystem (plugins).
jQuery routines will often be more correct and almost certainly much better 
tested than any methods we "hand-roll". Have you ever written the equivalent to 
`jQuery.ready()`? I have, and trust me, it's a lot easier just typing `$()`,
especially if you want to support anything before IE 10.

JQuery routines are highly generic and flexible, and they *can* be much 
slower than Vanilla JS. But there are three reasons why this is less an 
issue than one might think:

1. Performance is 'good enough'. While we may feel proud that
   using Vanilla JS we can toggle a CSS attribute faster, that doesn't mean
   the performance delta is worth worrying about. Also, when you compare jQuery
   to Vanilla JS,
   [make sure you compare the same thing
   ](https://jsperf.com/vanilla-js-v-jquery-hide/11).

2. Since jQuery is a library, not a framework, we can easily write our own
   plugins or routines to provide performance optimized for our situation
   *when it is important*. jQuery routines are general, and therefore
   have to consider many issues that are not present in our situation. For
   example, when you `show` an element in jQuery, it checks things like
   `z-index` and `opacity` of an element. Perhaps those aren't conditions
   our optimized routines have to worry about.

3. DOM access speed often isn't the problem. For example,
   I once wrote a photo management application that would show 3,000
   thumbnails on screen in multiple scrolling areas. I tried to use jQuery UI
   `draggable` to drag and drop between the scroll areas, and *it took 45 seconds
   to process*. A very similar issue arose when highlight a selected element:
   a hover effect took *15 seconds* to show. But the problem wasn't with jQuery 
   selector or manipulation time. If those times had dropped to zero, the
   performance problems would have barely budged. Both problems were because
   the `draggable` algorithms forced very costly reflows that we
   could not accept. Also, instead of adding a click handler each of 3,000
   elements, we attached one to the body and delegated *everything*.
   This smarter approach got those times down to 1s and .1s respectively.

Overall, jQuery is a wonderfully useful library for certain projects, 
and completely unnecessary in others. We don't ever *need* it, but when
we *want* a toolkit with excellent quality, stability, familiarity,
and extensibility, jQuery is probably the best way to accelerate our development.
Hopefully **The Vanilla JS Gang** can understand my point.

jQuery vs. the Frameworks
=========================
I wrote about this in depth a few weeks ago.  I suggest 
**The framework country club** members [spend some quality time outside of
the clubhouse](no-frameworks). Sometimes it's very useful to be practiced
in driving and parking your own car, metaphorically speaking.

Stop the language snobbery!
===========================
I find that people who deride jQuery usually aren't so upset with the library,
but instead with the quality of developers that jQuery tends to attract.
Because jQuery greatly lowers barriers that have traditionally prevented
people from developing web applications, their experience and quality as a whole
is much lower than those of us who had to write our own DOM manipulations,
client-side databases, event queues, etc. This is very similar to how PHP
developers usually are less adept than C developers.

There *are*, however, great developers using PHP. And jQuery. *And* C.
This language snobbery isn't unfounded - If a JS developer prefers an SPA 
framework, there is a good chance that he is a JS neophyte. But he could 
*alternately* be a fantastic developer that simply worked at a company
where all JS code had to be written to a specific framework.

I recently spoke with a start-up co-founder who dismissed everyone who wasn't using
Python as a 'bad developer'. Unfortunately for him, there is no popular web browser
with an embedded Python compiler that Grandma can use on her iPhone.  Python's 
integration to the browser DOM appears, shall we say, weak at best.
And I am not aware of any HP/HA database clusters with native Python data 
structure support or command-line interface. Apparently his dream of writing
web applications in Python end-to-end is still at least a few weeks off.

I chose to adopt JavaScript because Node.JS was the natural successor to 
mod\_perl for writing modular HP/HA web application servers.
But if I had gone with Scala instead (which was a very near thing), 
I probably wouldn't have to deal with titters from some Python-snob
that doesn't know about all my other language experience and considers 
all JavaScript "disposable."

So can we all grow up just a little and not be language snobs?
Remember, we ultimately don't develop *code*, we develop *products*
that people *use*, sometimes millions of times a day. We should use the
best language for the job and let the snobs titter away into irrelevance.

Cheers, Mike
