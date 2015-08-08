---
layout: post
title: The X-to-JavaScript Kraken
---
![The X-to-Javascript Kraken](/images/2015-06-15-the_kraken.jpg)

I recently read a post that lamented the retirement of the [GWT](https://en.wikipedia.org/wiki/Google_Web_Toolkit) JavaScript compiler.  A nostalgic mood washed over me and I reminisced about those [hot summer nights and my radio](https://www.youtube.com/watch?v=lPUb4Wsi9kI). GWT was a good toolkit for its time, but that time has past.  In fact, if you are considering using an **X-to-Javascript Compiler** to convert another high-level language into JavaScript, you might want to think again.  Because all their promises, these tools usually require a major investment that rarely gets paid back.  This post explores why.

Let's get started by looking at what GWT was and how it's spirit lives on in X-to-JavaScript compilers.

### Hello GWT
We might remember GWT as [the compiler toolkit](https://en.wikipedia.org/wiki/Google_Web_Toolkit) Google created so that Java developers didn't have to learn how JavaScript or a browser really works.  The niggling elephant in the room was that JavaScript and Java are completely different languages that handle fundamental operations in completely different ways.  That didn't stop Google, however, and at the time it was a good thing.

When Google released GWT around 2005, it made a good bit of sense: IE6 was the dominant browser, cross-browser compatibility was horrible, and client execution speed ranged from "good" to "I can't believe Microsoft out-sourced it's JScript engine again."  Back then, JavaScript and it's evil twin, JScript, were truly dangerous, confusing, and painfully inconsistent.  The browser was a frightening application execution environment and the DOM was a mess. Can you say "extendo-property-memory-leak?"  I knew you could.

But time moves on, and the development environment is vastly different then it was a decade ago. JavaScript has become universally fast and consistent. Chrome has replaced IE as the world's most popular browser by almost all accounts.  The extreme wilderness that GWT conquered a decade ago is now gone, replaced by a civilized, mostly uniform, fast, and an *almost* friendly execution environment.  And so Google let GWT slip into that good night.

Yet the soul of GWT lives on in X-to-JavaScript compilers. Some examples include Closure, TypeScript, Cappuccino, CoffeeScript and our favorite its-not-JavaScript-but-will-be-once-we-run-this-compiler of the week.  Let's explore their costs and benefits.

### The big red flag
X-to-JavaScript compilers are alluring, especially if we don't know JavaScript and want to leverage our existing language competence.  And that's a valid use of a compiler if we have a little bit of disposable code we need to create for short-time use.

But X-to-JavaScript compilers are converting one high level language into another. This should be a *big red flag* to anyone who has spent any sigificant time in software development.  This is comparable to translating from German to English.  Neither language is inherently better, yet in the short term, a translator is valuable if we are visiting Germany and only know English.  But if we are going to spend considerable time in Germany, learning the native language is a far better option.  Otherwise, we will need to continue to pay for that expensive, slow, and limited translator indefinitely.

And so it is with JavaScript.  If we are coding for the browser, like it or not, JavaScript is the native language.  And maintaining our translator - the X-to-JavaScript compiler - is expensive.

### What are the costs?
X-to-JavaScript can be very expensive to adopt and maintain. When we enumerate the many of the costs, we can see why:

- First we need to make a large investment to learn, deploy, and maintain a big-to-vast compiler infrastructure.  
- Next we need to learn a big-to-vast Domain Specific Language (DSL) that will constantly be changing as the maintainers vacillate about design fundamentals.  
- Then we will be far worse at core JavaScript and DOM because we spent so much time learning, maintaining, and working around the compiler and it's infrastructure.
- We will also kill our iteration times because running and testing code moves from fast-and-simple to slow-and-complex.  At the very least testing requires a compile step and a multi-step debug process.  Sometimes we need to add a compiler-specific test library.  Or three. Or a even a complete custom test environment.
- We will also get the special bonus: our "new language" will prevent us from doing various operations that are trivial in JavaScript. That's because either the maintainers didn't have the imagination to allow trivial JavaScript operations, or - better yet - because they decided for us using their "superior" wisdom that certain trivial operations should be forbidden because it's good for us.

No discussion of costs would be complete without discussing "technical debt".  Here, the experience gained using and maintaining a specific X-to-JavaScript infrastructure is nearly completely non-portable. That means that changing our mind after investing in one solution is an enormously expensive proposition.  To put it another way, learning how to debug Cappuccino is probably not the most universal of life skills, and will have probably give you little benefit if you decide to switch to TypeScript or native JavaScript.

So what do we get for our big investment?  We'll tackle that next.

### What are the benefits?
So X-to-JavaScript compilers are almost guaranteed to be really, really expensive.  But what about the benefits they provide?  Are they worth the costs?

Some benefits of X-to-JS languages are are pretty silly on their face.  CoffeeScript provides the *make-it-look-like-Ruby* benefit because, you know, a code closure should never be easy to find or compute. And Cappuccino provides the *make-it-look-like-Objective-C* benefit which apparently is a big deal for Tim Cook, Sir Jonny Ive, and some guy in Kansas.  Maybe it's just me, but aren't compilers supposed to convert from high-level to low level languages and not the other way around?

TypeScript, Closure, GWT, and perhaps our X-to-JavaScript-compiler-of-the-week strive to provide more value. However I've found that majority of truly valuable results of a compiler infrastructure can be obtained at a much lower cost by using simple code standards, a commit hook, and a few light-weight utilities to check the code for correctness (see JSLint, uglify, and argc).  For example, Closure uses a very baroque JSDoc + compiler logic to enforce variable typing. That is certainly useful, but for every 4 or 5 lines of code and comments used to enforce a type in Closure, we could add a type suffix to the variable name - like `foo_str` - and adjust our code management tools to get most of the same benefits less than 10% of the effort.  Yes, I've tried both, and I'm sticking with Plan B.

Closure makes JavaScript appear to work much like Java, and the result is not surprising: the code is incredibly verbose, repetitive, slow, and convoluted.  What's worse, it requires an excruciatingly complex and hard-to-maintain compiler infrastructure that makes testing slow and complex.  Not sold yet? But wait, there's more! A few minutes of working with Closure makes one yearn for the comparative simplicity and terseness of COBOL.  And Closure isn't the worst X-to-JavaScript compiler -- not by a long shot.  It *does* add value to a project.  But it comes at such a huge cost that it simply isn't worth it, at least not for new projects.

### Enter the Kraken
Many of us have seen web projects with multiple developers and numerous GUI frameworks selected without a single GUI prototype.  Let's pause for a moment and let that sink in. One should not hire a full developer team and select tools before knowing what to build.  That's like hiring a construction crew and buying building materials before even sketching the design of a building.

I liken X-to-JavaScript compilers to the Kraken, because they are an enormous many-tenacled beast that can slow almost every aspect of development, severely wounding or sinking it.  Indeed, the over-use of libraries, tools, frameworks and compilers is a primary theme of [one presentation](https://github.com/mmikowski/spa/tree/master/slides) I gave at an HTML5 Developer conference in San Francisco and at Developer Week.  If you insist on investing in an X-to-Javascript infrastucture, I strongly suggest you carefully consider your costs, benefits, and technical debt you will realize from you decision.

### The future
Someday JavaScript will no longer the default browser language.  It will probably be replaced by a byte-code driven virtual machine. When that happens, we will be free to select any language syntax that compiles to the byte code specification, and the incumbent advantages of JavaScript will be gone.

But that day isn't here.  JavaScript is the native language of the browser.  And every minute we spend working with, working around, and maintaining an X-to-JavaScript compiler takes away from the core technologies we use in our web application.  It's like avoiding-the-door by using a hand-saw to cut-an-opening in a wall. 

This post was originally published on [Linked-in](https://www.linkedin.com/pulse/web-application-kraken-x-to-javascript-compilers-michael-mikowski)
