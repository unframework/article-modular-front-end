# Starting Point

HTML and CSS make for a brittle and convoluted combination of technologies, and most front-end frameworks add a steep complexity of their own. At the same time, best practices for in-browser code maintainability are still a surprisingly under-documented "black art"; whatever advice does exist out there is either outdated or encumbered with framework-specific assumptions and preferences. How do we learn not just to program for the Web, but also do it right?

Regardless of experience level in the industry - entry-level to seasoned - we strive to know how the tools we use fit into the general philosophy of good, productive programming. Software success means improving speed of development and ease of maintainability. It is not even possible to grow a codebase past a certain level of complexity without applying careful thought to its structure. And, of course, project source code needs to stay transparent and easy to learn, so that new team members can get onboarded more easily.

As we explore the front-end software stack, we want to know how to stay true to those goals when coding in the browser. There is already a huge amount of software engineering wisdom that has been accumulated and documented over the last few decades. This book will survey and re-frame current in-browser technologies and tools through that timeless lens - with a focus on building solid, simple and resilient application codebases.

## From Scratch

To make a connection from front-end tech to basic foundations of maintainable programming, we need to start from scratch. Instead of getting lost in the patchwork of well-intentioned but overly specific advice, we will learn by building.

We will start by revisiting the typical problem scenarios of front-end programming and solve them as simply as we can. We will then apply well-known fundamental patterns of software engineering to factor the solution into a more maintainable state. That will give us a properly anchored, informed perspective on choice and usage of existing production frameworks. The goal, after all, is not to reinvent the wheel, but just get a more comprehensive feel and instinct for using well-known and well-supported tools.

First, let's survey our runtime environment as well as our software engineering toolbox.

So, *what* exactly are we programming when we write front-end code? Where does our program run, and what are its inputs and outputs?

## Runtime Environment

For our application-writing purposes, a Web browser is two things:

- JavaScript runtime VM
- a bunch of useful APIs that can be called from JavaScript

Those useful APIs include:

- layout and rendering engine (that uses DOM and CSS as inputs)
- input event stream (keyboard, mouse, other events)
- networking library (`XMLHttpRequest`, WebSockets)
- media upload API
- canvas 2D, WebGL draw calls
- WebAudio
- kitchen sink

Importantly, we do not put HTML and CSS at the center of attention. In the application-centric worldview, they are merely companion technologies used to render the interface and collect interactive input. Running code is still the main focus point.

Next, let's cover the strategies that we can use to manage complexity of that code.

## Building Blocks

We subdivide code into modules. Each program module is simply a grouping of code that exposes a well-defined interface that can be accessed from other modules. This helps naturally organize the written software "by topic" and control expectations and dependencies between groupings of functionality.

Object-oriented programming introduces the notion of an object - a set of data (also referred to as *state*) that is hidden from direct access and defines an object interface - methods (functions attached to the object) and fields that outside code can interact with. Object type implementation is a module of code.

Well-designed object interfaces are small, simple and stable. We aim to hide implementation details and direct data access from outside code. Unit tests help ensure that the implementation of the object interface continues to match expectations as it is being updated and refactored. It so happens that code that is easy to test is also easy to maintain.

Our software models the real world - the domain - hence naturally our modules and object definitions have to describe real-world concepts. It helps to maintain one-to-one correspondence between code components and specific parts of the domain description. Because we already keep the domain model in our head, it becomes less of a stretch to transfer thinking into written code, and easier to mentally step through interactions between existing modules.

As we implement user interfaces, we start noticing a pattern: a continuous flow of data from "pure model" code to the presentation and layout code. As the business logic data structures change (e.g. user information, item counts, incoming notifications), those changes propagate down to the code that displays the data on screen or other devices. Streamlining that change management is a topic of *reactive programming*, and it is clearly a huge benefit to use it as one of our code structuring strategies. However, it needs to naturally fit into our modularization efforts, which is not always trivial.

This is just a brief inventory of some of the strategies at our disposal; we will get into more depth as we go along with the example scenarios.

We can now start examining typical front-end tasks through the lens of modularization and exploring component architecture patterns that will keep production code simple and reliable.
