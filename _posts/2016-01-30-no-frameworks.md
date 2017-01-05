---
layout: post
title: Do you really want an SPA framework?
---
*First, sell your soul to the framework maintainer...*

# What's wrong with an SPA Framework?
I author and maintain quite a bit of software. Much of my code is available
in commercial applications (SnapLogic, Qualaroo), OSS libraries on [Github][26]
and [NPM][25], or demonstration applications like [Typebomb][24].
I am the co-author of the book that Dr. Dobb's Journal named as one of the
best developer books of 2014 and called it the "[Master Handbook][27]"
for SPAs. I have more years experience **developing commercial web
applications** than most Angular maintainers have **breathing**. Yet
I have not published an **SPA framework** despite numerous requests.

Why? Because I don't want to lock developers into a platform that must be,
by definition, opinionated and limited. An SPA framework provides safety
at the cost of an investment into their platform and a loss of flexibility
and control. They can hinder our ability to innovate and build quality
software. For these reasons, I advocate developers focus on JavaScript
language mastery and SPA architecture rather than the promise of a **silver
bullet** SPA framework.

Before we proceed please note that everything has its place. SPA frameworks
can be useful to new or casual developers who don't know or care how an SPA
works. But before you hitch your star to Ext, Angular, Ember, Aurelia, Meteor,
Backbone, Knockout, Vue, or Mercury or dozens of others, please consider that
every shiny new tool has its cost. Here are some precautions you might want
to consider before you dive in.

# Frameworks and the inversion of control
When we use an SPA framework, the quality and capabilities of
our application are [strongly limited by it][4]. This [inversion of
control][28] is a major impediment in building a a nimble, flexible,
testable, and maintainable application that can stand the test of time.

When we use a sound architecture and libraries instead, however, we can
swap libraries out when they are updated or better one becomes available.
**Or we can decide not to change a thing** if an update doesn't suit our
needs. We can mix and match the **best-for-our-purpose** libraries instead
of using a framework's mishmash of solutions of varying quality. Why,
for example, should one bother *overriding* the mediocre mechanisms of
a framework when just removing the framework can results in simpler
and easier to maintain code?

# Does this mean we need to write everything from scratch?
**Absolutely not.**  jQuery and other best-in-class libraries can provide a
more capable and complete foundation for building a modern SPA compared
to many frameworks. We can start with a simple and clean
[SPA architecture, p10][1] as detailed in
[Single page web applications, JavaScript end-to-end][2]
(also available directly from [Manning][3]), and then add libraries that
are best suited to our application. We can **leverage** jQuery's maturity,
performance, excellent tools, and vast ecosystem instead of **competing**
with it. We also avoid ceding control of our application to an SPA framework.

# Frameworks and complexity === insanely long cycle times
SPA frameworks these days are approaching or exceeding the complexity of
[JavaScript-to-X compilers][29] with sadly similar results. If we want to
go all-in, we can get a "two-fer" by selecting Angular 2 + TypeScript.
That way we can shoot ourselves in **both** feet instead of one and greatly
increase our cycle times. Who needs short cycle times anyway?

Well, actually, **we** do. If our overhead for producing working development
code is greater than say, oh, 5 seconds, then somebody out there is
definitely kicking our ass on cycle times. We can fail 300 times an hour with
a 5 second cycle time. In other words, if it takes 200 failures before success,
we will need a minimum of 40 minutes to resolve an issue. However, if our
cycle time is 5 minutes because of the multi-compile overhead of an SPA
framework, our minimum time before success will be 72 *times* longer, or over
two full work-days. You make the call.

Long cycle times not only kill productivity, but they also stifle innovation
because only so many solutions can be tried within any given period of time.
To paraphrase Thomas Edison, the key to innovation is to learning how to fail
really fast.

# Frameworks DSLs aren't portable
SPA frameworks DSLs **aren't** the most portable of
life skills, just like [JavaScript-to-X compilers][29] DSLs.
How many developers have moved from GWT to YUI to Dojo to Ext to Backbone
to Closure to Knockout to Knockback to Ember to Angular?  How about sprinkling
some Bootstrap, Sass, TypeScript, CoffeeScipt,
Cappuccino, HAML, and a few more compile steps in there just for the fun of
it?  Maybe one is better at learning HTML5, CSS3, JS really well and taking
all the noise about these "silver bullets" with a grain of salt. See [The fog
of SPA][30]

# Been there, got the T-shirt, bombed the airport
I once used a framework (not my choice) and had to wait months for a new
version to support a desired feature. Once the framework was updated,
I discovered excruciating pain of trying to find and fix all the regressions.
It wasn't easy, of course, because (a) some of the most dastardly bugs were
within the framework itself, and (b) frameworks tend to intermingle display
and business logic, so testing was tedious and difficult. Which, of course,
brings us to our next subject...

# What about testing?
Testing a Single Page Framework Web Applications (SPFWA?) often requires
an **additional** framework for testing the simplest of logic. Selenium,
ZombieJS, and other intricate solutions are often employed to test the most
basic model logic that *shouldn't require display testing at all.*
Yet there it is. Complexity breeds complexity.

When we use a sound architecture and libraries instead, however, we can
easily decouple display and business logic so we can
[regression test our application in less than a second][5].
My commit hook for the above application eventually ran the full regression
suite *and* JSLint for all changed files in less than 4 seconds with over
600 assertions and ~95% coverage. We could refactor and reorganize the
code *at-will* because only the most obscure bugs could sneak past the
regression tests. The architecture and method of testing scales very
well on larger projects too.

# What are your preferred libraries
**Updated 2016-11-20**. Here are libraries we recommend:

| Capability   | Library              | Notes                             |
| :----------- | :------------------- | :-------------------------------- |
| DOM + Util   | [jQuery][6]          | A powerful, stable, tight library |
| AJAX         | [jQuery][6]          | ... but prefer WebSockets, see below |
| Client Data  | [TaffyDB][7]         | A powerful and flexible SQL-like client data management tool |
| DynamicCSS   | [PowerCSS][8]        | Insanely fast and efficient JS-CSS engine |
| Linting      | [JSLint][9]          | Avoid stupid mistakes with a commit hook |
| Events, promises | [Global Events][10] | Use the same event and promise methods for both logical and browser events |
| Routing      | [uriAnchor][11]      | A jQuery plugin for robust routing that includes support for dependent and independent query arguments |
| SVG          | [D3][12]             | Easy graphs and charts            |
|              | [SVG][13]            | Low-level jQuery plugin           |
| Templates    | [Dust][14]           | Uses a powerful template DSL that minimizes the temptation to intermingle  business and display logic |
| Testing      | [Nodeunit-b][15]     | Create a lightening fast regression test suite and use it as a commit hook |
| Touch        | [Unified events][16] | Unified desktop and touch events  |
| WebSockets   | [Socket io][17]      | The WebSockets protocol is faster and more flexible than AJAX for most applications. Consider using [pure websockets][18] client with a [websocket][19] server on a NodeJs with modern browsers (IE10+) |

# But I want the comfort of a Framework [Updated 2016-11-20]
The NPM package [hi\_score][21] is moving along nicely.
It includes all the tools needed to deploy a modern, well packaged SPA for
production. It includes architecture diagrams, example code, code compression
and obsfucation, and dynamic installation and linking of best-in-class libraries
You can install and inspect the cod in about a minute:

```bash
  npm install hi_score
  cd node_modules/hi_score
  npm install
  npm run prep-libs
  npm test
  npm run cover
  google-chrome coverage/lcov-report/index.html
  google-chome index.html
```

**hi\_score** continues to evolve, and there are a few bits that still need
to be hooked up.  For example, the compression routines need a little more
polish.  However,  we think it is already quite valuable and usable.  And it's
all libraries.

All `xhi` code uses [typecasting][22] to minimize type errors.
Check out the [code coverage][23] on coveralls, and watch this blog for
future [hi\_score][21] announcements.

# A parting thought
I recently saw [this article on Angular][20] which echoes many of
the issues discussed above.

Cheers, Mike

# END

[1]:https://github.com/mmikowski/spa/blob/master/slides/2013-10-22-make_it_rock.pdf
[2]:http://www.amazon.com/dp/1617290750
[3]:http://manning.com/mikowski
[4]:https://aerotwist.com/blog/the-cost-of-frameworks
[5]:https://youtu.be/aoH0J6lL2w0?t=47m15s
[6]:http://jquery.com/download
[7]:https://github.com/typicaljoe/taffydb
[8]:https://www.npmjs.com/package/powercss
[9]:https://www.npmjs.com/package/jslint
[10]:https://github.com/mmikowski/jquery.event.gevent
[11]:https://github.com/mmikowski/urianchor
[12]:https://github.com/mbostock/d3
[13]:http://keith-wood.name/svg.html
[14]:http://linkedin.github.io/dustjs
[15]:https://www.npmjs.com/package/nodeunit-b
[16]:https://github.com/mmikowski/jquery.event.ue
[17]:http://socket.io
[18]:https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_client_applications
[19]:https://www.npmjs.com/package/websocket
[20]:https://medium.com/@mnemon1ck/why-you-should-not-use-angularjs-1df5ddf6fc99#.wivhdcf4g
[21]:https://github.com/mmikowski/hi_score
[22]:../type-casts
[23]:https://coveralls.io/github/mmikowski/hi_score
[24]:http://michaelmikowski.com/typebomb
[25]:https://www.npmjs.com/~mikem
[26]:https://github.com/mmikowski
[27]:http://www.drdobbs.com/joltawards/jolt-awards-the-best-books/240169070?pgno=5
[28]:http://martinfowler.com/bliki/InversionOfControl.html
[29]:http://mmikowski.github.io/the_kraken/
[30]:https://github.com/mmikowski/spa/raw/master/slides/2013-04-02-the_fog_of_spa.pdf
[31]:

