---
layout: post
title: PowerCSS is fast approaching 1.x release
---
*Make {less} and Sass a thing of the past*

![PowerCSS is fast approaching 1.x release](/images/2016-02-22-pcss.png)

If you've been following PowerCSS, then we've got some good news for you.
It's fast approaching 1.0.x release. We're right on schedule according to
our last blog post:

> ... There's lot of documentation and four examples already on the site, 
> and we're hoping to complete all the features by next week. 

Already over 4,100 early-adopters have downloaded from the
[npm site](https://www.npmjs.com/package/powercss), with download currently
hovering around 500 a day. However if you've been sitting on the fence waiting
for a stable API, your wait is over.

As of version 0.5.x, we consider the library feature complete.
This version saw our last big API overhaul, where we cut the number of methods
in half while adding many additional capabilities. The documentation and examples
have also been expanded and updated. We are especially proud of
**The PowerCSS Cookbook** section.

The work remaining between 0.5.x and 1.0.x release includes documentation 
updates, example implementations, and regression tests. We hope to complete
those tasks this week.

For those that came in late, PowerCSS provides a fresh approach to create
dynamic CSS on demand. [I advocated CSS double-buffering 
](https://www.youtube.com/watch?v=rnkMjzhxw4s) years ago, but PowerCSS is
all-new code with many additional capabilities and optimizations:

- **MIT license**
- **No dependencies**
- **Namespaced** - PowerCSS plays well with jQuery, other libraries,
  and third-party JavaScript.
- **Pure JS** - Remove the need for **any** static CSS files.
- **Real-time styling** - Application control of CSS enables custom 
  styling for every user of your application. For example, adjust
  application styling based on device (capabilities, orientation and size),
  GPS coordinates, and ambient conditions (light, temperature, time of day,
  heart rate).
- **Double-buffering** is automatic and minimizes page re-flows. It can speed up
  some styling changes by more than 10x.
- **Merging and caching** - Intelligent time-based minimal processing
  only changes the virtual cascades that need it.
- **Mixins** at multiple levels - virtual stylesheet, virtual cascade, and global.
- **Familiar workflow** - Virtual stylesheets and virtual cascades are used
  to minimize the transition for CSS veterans.
- **Highly optimized CSS** - PowerCSS presents only **one stylesheet**
  for the browser rendering engine to use, and numerous redundancies are removed
  during its preparation. As a result the browser rendering engine can work much
  more efficiently.
- **Highly compressible** - PowerCSS can be compressed to a fraction of static CSS.
- **Quality code** - Expertly written and documented. Passes JSLint as a
  commit hook. We plan to include regression tests as a commit hook 
  prior to 1.x release.

Constructive feedback, question, and collaborators are always welcome!

Cheers, Mike
