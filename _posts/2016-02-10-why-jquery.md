---
layout: post
title: Why jQuery?
---
*jQuery: the most useful library we love to hate*

JS Stratification across the nation
===================================
Recently the JavaScript community appears to have striated into three
distinct groups:

1. **The Vanilla JS gang** believes all JS code should use only native
   constructs. jQuery is avoided because this group thinks it is too slow.
   This group tends to attract experienced developers.

2. **The library liberation front** believe much of their utility code should
   come from popular, well-tested libraries. However, if a desired capability
   isn't available from existing libraries, a member of **the front** will 
   often *collaborate* with an existing library team to add it.  More
   radical members of **the front** have been known to publish entire
   libraries themselves.  Members of **the front** will sometimes avoid library
   calls and write a faster custom solution as needed. 
   This group tends to attract experience developers and, 
   especially since jQuery's arrival, a fair number of neophytes.

3. **The framework country club** believe JS or GUI development is too scary
   to approach unarmed. Native JS takes a back-seat to an
  ["it's-our-way-or-the-highway" domain specific language
  ](2016_2016-01-30-no-framework) which guides the developer into 
  what the framework creator thinks is best practice. One only has to 
  consider the Closure framework to see how far off the mark
  the framework creator can get.  This group tends to attract 
  less-experienced developers and lots of neophytes.

I'm a card-carrying member of **The library liberation front**. 
Let me explain why.

Vanilla vs. jQuery
==================
I have have had the pleasure of writing multiple commercial code bases both
with and without jQuery and can give you the inside scoop. The first Vanilla
JS SPA I wrote was the AMD comparison shopping site which went live in 2007.
Although jQuery 1.0 had been released in August 2006, it was still far from
a popular or even stable library. And I didn't even know it existed until
after I finished the project. The site was replaced by a much slower
but much-more-SEO-friendly site in 2010. Today, the SEO issue is minimal,
but it was big limitation with SPAs back at that time.

My second commercial Vanilla JS product was the Qualaroo universal client,
released world-wide in 2014. The last I knew it was servicing 100m users per day.
I got to write *everything* in that codebase. Event queues and promises?
*Wrote 'em*. DOM search mechanisms? *Wrote those too.* Object extend and
numerous other utilities? *Yep.* And if I had to do it all over again, I
would do the exact same thing, because *the desired user
experience* determined how the code [was developed and
deployed](https://www.youtube.com/watch?v=aoH0J6lL2w0).

I am currently working on a Vanilla JS library (PowerCSS), *and* 
using jQuery for another project. Why? Because one benefits from jQuery,
and one doesn't. It's as simple as that.

We should *avoid* jQuery when it is very important that the code be as
light-weight and performant as possible, as with the Qualaroo universal client.
In that deployment, a *single file smaller than the compressed jQuery library*
provided the entire application, data, images, and CSS. We got to write or
refactor *everything* from the ground up, which provided highly optimized
code at the expense of time. And we also write regression tests for
*every* level of the application.

We should *use* jQuery when we value development time, a common API, and
a stable DSL, and a vast, stable ecosystem (plugins). jQuery routines 
will often be more correct and almost certainly much better tested than any 
methods we "hand-roll". Have you ever written the equivalent to 
`jQuery.ready()`? I have, and trust me, it's a lot easier just typing `$()`,
especially if you want to support anything before IE 10.

JQuery routines are highly generic and flexible, and they *can* be much 
slower than Vanilla JS. But there are three reasons why this is less an 
issue than one might think:

1. Often times, performance is 'good enough'. While we may feel proud that
   using vanilla JS we can toggle a CSS attribute faster, that doesn't mean
   the performance delta is worth worrying about. Also, when you compare jQuery
   to Vanilla JS,
   [make sure you compare the same thing
   ](https://jsperf.com/vanilla-js-v-jquery-hide/11).
2. Since jQuery is a library, not a framework, we can easily write our own
   plugins or routines to provide performance optimized for our situation
   *when it is important*. jQuery routines are general, and therefore
   have to consider many issues that are not present in our situation. For
   example, when you `show` an element in jQuery, it checks things like
   `z-index` and `opacity` of an element. Perhaps that those aren't condition
   our optimized routines have to worry about.
3. Optimizing DOM manipulation is often solving the wrong problem. For example,
   I once wrote a photo management application that would show 3,000
   thumbnails on screen in multiple scrolling areas. I tried to use jQuery UI
   `draggable` to drag and drop between these windows, and *it took 45 seconds
   to process*. A very similar issue arose when highlight a selected element:
   a hover effect took *15 seconds* to show. But the problem wasn't with jQuery 
   selector or manipulation time. If those times had dropped to zero, the
   performance problems wouldn't have budged. Both problems were because
   the `draggable` algorithm that forced a very costly reflows that we
   couldn't accept. Also, instead of attaching a click handler to 3,000
   elements, we attached one to the body and delegated *everything*.

Overall, jQuery is a wonderfully useful library for certain projects, 
and completely unnecessary in others. We don't ever *need* it, but when
we *want* a toolkit with excellent quality, stability, familiarity,
and extensability, jQuery is probably the best way to accelerate our development.
Hopefully **The Vanilla JS gang** can understand my point.

jQuery vs. the Frameworks
=========================
I wrote about this in depth a few weeks ago.  Let's just say
I suggest framework users
[spend some quality time outside of the clubhouse](no-frameworks).


A side note about Language Snobs
================================
I find that people who deride jQuery usually aren't so upset with the library,
but instead with the quality of developers that jQuery tends to attract.
Because jQuery greatly lowers barriers that have traditionally prevented
people from developing web applications, their experience and quality as a whole
is much lower than those who had to write our own DOM manipulations,
client-side databases, event queues, etc. This is very similar to how PHP
developers usually are less adept than C developers.

However, there are great developers using PHP, jQuery *and* C. This
language snobbery isn't unfounded but it is quite pronounced. I recently
interviewed with a start-up CTO who dismissed everyone who wasn't using
Python as a 'bad developer'. He was openly hostile to any solution to
virtually anything non-Python. Unfortunately, until someone writes a web 
browser with embedded Python and a Database with native Python data
structures nobody's going to write a web app in Python end-to-end.

I chose to adopt JavaScript because Node.JS was the natural successor to 
mod\_perl for writing modular HP/HA web application servers.
But if I had gone with Scala instead (which was a very near thing), 
I probably wouldn't have to deal with titters from some Python-snob
that doesn't know about all my other language experience and considers 
all JavaScript "disposable."

So can we all grow up just a little and not be language snobs? If a developer
prefers an SPA framework, chances *are* higher that he is a neophyte.
But he could be a fantastic developer that simply started at a company that
required code written in a framework. Remember, we ultimately don't develop
*code*, we develop *products* that people use, sometimes millions of times
a day. We should use the best language and tools for the job, and let the
language snobs titter themselves to irrelevance.

Cheers, Mike
