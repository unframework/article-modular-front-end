
Preface
-------

You are building a web app. You are part of a team. There is backend, database, front-end framework like Angular/Backbone/Ember or maybe server-side templates like Slim/ERB/Twig. Maybe you still call it a website, maybe it integrates with one, but the moment you are talking CI, builds, components, databases, that's an app.

Apps, unlike websites, can have very fast and deep churn in the front-end code. New features are added constantly, usage patterns are discovered/deprecated, and interaction is a lot more complex, plus bug severity threshold is more sensitive. Add in IE8 support (for some of us!) and constant browser compatibility regressions.

To help that, we have a few libraries and frameworks to manage front-end codebase complexity. Not "a few", actually - a huge rat's nest of opinions, each with a dedicated camp of followers and steep learning curve. Not to mention completely incompatible calling conventions in each, so we can't even reuse code written for one framework in another.

There has got to be a roadmap to all of this, a quick compass reading to help make architecture design decisions a little less inertia- and politics-driven, and more grounded in common objective goals that we all share as teammates.

We know about good foundational software engineering rules. Don't Repeat Yourself. Keep things modular and component-driven. Separate concerns, such as business logic and presentation. So let's go back to basics and "reinvent the wheel" a little bit, to have a view of modern UI patterns that is grounded in those timeless principles.

Reset
-----

Let's pretend we completed a computer science degree 15 years ago and then spent the rest of the time until now working on a farm under a rock on the moon... in Jupiter's orbit.

We have now returned to the lucrative world of web-app development, and have joined a "shop" - either a product startup or a consulting firm or freelancing or working a department in a large bank. We are doing "front-end development," according to HR. What is our perspective on the next project assignment we get?

We are still building software, yes. We might have a "front end" title, but we are still contributing to a cohesive software project overall. And as a whole, it is expected to meet some requirements, as software usually would. In this case that usually includes:

- present an interactive user interface on user devices
- effect changes in server state (i.e. database)
- report server state back to the user interface

Ongoing checkmarks to hit are: don't corrupt data, don't allow for a security breach, support devices (browsers) that users actually have, keep things accessible for everyone, and more (in agile-land referred to as definition-of-done).

We have a notion of what is our "production" target environment. It's a survey of all the computing resources that our software is going to be running on. Let's fast forward into a hypothetical moment in time where the project is live, and a user is interacting with it. What are we dealing with?

Runtime Environment
-------------------

We usually have a server to execute trusted business logic. Also referred to as a "backend". Typically a process running on a Linux or Windows box somewhere in a cloud datacenter. The process is a language VM: NodeJS instance, a PHP interpreter plugin for Apache, a Rails or Java VM, etc. There is a database connected, third-party service APIs, a load-balancer proxy to channel requests, yadda, yadda.

And then we have the user's browser.

Since we have been hanging out under a rock for the last 15 years, we have a radically different - naïve - perspective on this ubiquitous piece of software that everyone else takes for granted.

For our application-writing purposes, a Web browser is two things:

- JavaScript VM
- a bunch of useful programmable APIs

> Wow, wow, what about semantic web and markup and search engine bots? Well, those are content website terms. Modern web-apps are not websites: they are behind a login-wall, dealing with highly personal and private data, optimized for specific device presentation. Not to say that search engines can't be helped - a well-architected web-app is easy to render in a crawler-friendly way.

Useful APIs found in the average browser:

- DOM, a hierarchical data structure
- layout and rendering engine: configured via CSS, renders DOM
- canvas 2D, WebGL draw calls
- WebAudio
- soon to be the rest of the operating system, amirite?

Basic Building Blocks
---------------------

Simplistic flow or runtime code: user browses somewhere in a browser or opens a mobile web-view app. The app business logic runs, and the app framework produces a bunch of DOM - `div`s, `span`s and other assorted animals. We also get to inject some CSS on the page - usually with SASS or LESS, but it all boils down to the same old `<link rel="stylesheet" />`.

```
Framework -> DOM + stylesheets <- CSS files/preprocessor
                 |
                 V
        Browser layout engine
           and renderer
                 |
                 V
      Pretty clickable pictures
```

The visible UI ends up existing in the browser memory at runtime as a tree of DOM elements. Some of them are arranged to be siblings, some contain others. The DOM elements are sort of like basic units of display - boxes, really. Each exactly defines what shows up on that corresponding piece of screen: position, size, border, background of a box, some text inside, etc. And while the user is on the page, these element instances just sit there in memory and do their thing.

Even without developer-defined JS-based behaviours, each DOM element runtime instance is affected by others just by virtue of passing through the browser layout engine. Size of one sibling affects the position of the other in the block flow. Children move with the parent. Floated elements "indent" sibling content. With CSS inheritance, font of the parent dictates font of the child, unless overridden. Absolute positioning of a child is sensitive to which ancestor defines the offset origin. Etc, etc.

We are not used to thinking about it that way, but it's as if each DOM element instance is a small application component at runtime, and it interacts with other in-memory DOM instances through all the above layout/CSS relationships. If we were coding a desktop app, these would be like tiny running modules of app architecture, "talking" to each other and affecting each other's behaviour and then dumping pixels on screen. Again, this is before we even take actual JavaScript interaction into account - browser layout logic by itself already provides some runtime behaviour that we can influence via CSS alone.

Problem Area
------------

Now, unlike a typical well-architected bespoke app component, each DOM element instance has *a lot* of entangling influence on fellow element instances, some of it kind of getting in the way. CSS provides a huge set of cascade and flow features, and the full combination of them becomes hard to predict. There may be *unwanted* side-effects of that entanglement of elements.

A classic unwanted side-effect is accidental font family inheritance. If a parent element has default font style, and child DOM CSS is written with that assumption, then adding a `font-family` to the parent will break visual presentation of the children. The developer of that CSS never assumed the need to explicitly override default font family, and the person modifying parent CSS rule does not have a clear picture of what is affected by that change. There are many many more similarly brittle layout behaviours like that.

This is a situation ripe for improvement. The complexity of builtin DOM layout behaviour creates a lot of real-world bugs with your app, and this is part of what good CSS architecture is meant to fix - tame the tangle of raw browser atoms, even *after* your app framework has done its job.

The fact that DOM + CSS has finicky and brittle side-effects is OK - we get a lot of power to build complex layouts and behaviours, like programmers accessing low-level CPU assembly instructions directly. But we can introduce sensible patterns and restrictions to organize DOM state in a more modular way, kind of like building a higher-level language on top of CPU assembly.

Componentization
----------------

Let's invent the notion of a component - a set of DOM elements, with some associated CSS. Purely a mental construct. But we can start treating it as a single unit at runtime, and tweak CSS and class naming so that we can restrict its interaction with the outside world and improve our resilience to bugs.

Naming/Filing Problem
---------------------

Forgetting for a sec about pre-built CSS sets like Bootstrap or Zurb Foundation. Let's say there is a button class.

```
.action-button {
    display: inline-block;
    border: 0;
    border-radius: 4px;

    /* background: #ff0; programmer art! */
    background: #2980b9; /* FlatUI, better */
}
```

Great. This is cool. We use it in about 25 different spots because it is a large app.

A feature comes in: dropdown `div` with a button like the above, and it should extend 100% to the width of the content of the dropdown. What do we do?

```
.foo-brand-signup-page-referral-widget-dropdown .action-button {
    display: block;
    width: 100%;
}
```

Where do we put this? In the `foo-brand-signup-page.css`, I guess? Or in the `global-action-thingies.css`? Typically the former, so let's go with that as an example. Then, two days later, some smart person decides to add `margin:2px` to the `action-button` class. Minor tweak, looks great in those original 25 spots. How do they even know that this will screw up the 26th spot? Search codebase for that class? Lots of discipline, that takes.
