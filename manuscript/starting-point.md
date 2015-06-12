# Starting Point

HTML and CSS is a brittle and convoluted combination of technologies, and the current production front-end frameworks built on top of them have a very steep learning curve. At the same time, best practices for code maintainability are usually left to be an undocumented "black art", even for popular tools. Whatever advice is out there is hotly contested and encumbered with stack-specific assumptions and preferences.

Regardless of experience level in the industry - entry-level to seasoned - we always learn technologies and frameworks with a goal in mind. We need to know how the tools we use fit into the general philosophy of good, productive programming. What is good programming? It is more than just the DRY principle and separation of concerns.

Software process success means improving speed of development and ease of maintainability. Modern applications are in a constant state of flux: our understanding of the user and app functionality constantly gets refined and pivoted, and the specifications from one week ago might be flipped upside down one week from now. Most new code we write is actually extending or replacing existing code. Nimble architectures help sustainably keep responding to that change while preserving a high quality bar.

Additionally, as time goes on some team members leave and new ones join: precious project knowledge is lost to attrition, and new contributors have to be efficiently on-boarded to become productive. A good codebase, then, is transparent and delightful to learn and makes it easier to see past developers' intent.

As we explore the front-end software stack, we want to know how to stay true to those goals when coding in the browser. There is already a huge amount of software engineering wisdom that has been accumulated and documented over the last few decades. This book will survey and re-frame current in-browser production code tools and techniques through that timeless lens - with a focus on building solid, simple and resilient application codebases.

## From Scratch

To make a connection from front-end tech to basic foundations of maintainable programming, we need to start from scratch. The best way to learn something is to build it.

We will start by revisiting the core problem statements of front-end programming and solve them as simply as we can. We will then apply well-known fundamental patterns of software engineering to factor the solution into a more maintainable state. That will give us a properly anchored, informed perspective on choice and usage of existing production frameworks. The goal, after all, is not to reinvent the wheel, but just get a more comprehensive feel and instinct for using well-known and well-supported tools.

First, let's survey our runtime environment as well as our software engineering toolbox. *What* exactly are we programming when we write front-end code? Where does our program run, and what are its inputs and outputs?

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

We are putting the JavaScript runtime at the center of the picture because that is, after all, where actual code runs. Focusing on the pure code first helps us relate programming wisdom from other environments (such as servers or embedded devices) to front-end development.

Next, let's cover the strategies that we can use to manage complexity of that code.

## Building Blocks

We subdivide code into modules. Each program module is simply a grouping of code that exposes a well-defined interface that can be accessed from other modules. This helps naturally organize the written software "by topic" and control expectations and dependencies between groupings of functionality.

Object-oriented programming introduces the notion of an object - a set of data (also referred to as *state*) that is hidden from direct access and defines an object interface - methods (functions attached to the object) and fields that outside code can interact with. Object type implementation is a module of code.

Well-designed object interfaces are small, simple and stable. We aim to hide implementation details and direct data access from outside code. Unit tests help ensure that the implementation of the object interface continues to match expectations as it is being updated and refactored. It so happens that code that is easy to test is also easy to maintain.

Our software models the real world - the domain - hence naturally our modules and object definitions have to describe real-world concepts. It helps to maintain one-to-one correspondence between code components and specific parts of the domain description. Because we already keep the domain model in our head, it becomes less of a stretch to transfer thinking into written code, and easier to mentally step through interactions between existing modules.

This is just a brief inventory of some of the strategies at our disposal; we will get into more depth as we go along with the example scenarios.

We can now start examining typical front-end tasks through the lens of modularization and exploring component architecture patterns that will keep production code simple and reliable.
