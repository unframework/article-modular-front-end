# Starting Point

HTML and CSS is a brittle and convoluted combination of technologies; production front-end frameworks have a steep learning curve. At the same time, best practices for code maintainability are usually left to be an undocumented "black art", even for popular tools. Whatever advice is out there is hotly contested and encumbered with stack-specific assumptions and preferences.

Regardless of experience level in the industry - introductory to seasoned - we don't want to just learn the tutorial trivia of a technology or framework. We want to know how it fits into the general goals of good programming - speed of development and ease of maintainability. A huge amount of software engineering wisdom has been accumulated and documented over the last few decades: surely it can apply just as well to the front-end software domain.

To make a connection from front-end tech to basic foundations of programming, we need to start from scratch. The best way to learn something is to build it.

We will start by revisiting the core problem statements of front-end programming and solve them as simply as we can. We will then apply well-known fundamental patterns of software engineering to factor the solution into a more maintainable state. That will give us a properly anchored, informed perspective on choice and usage of existing production frameworks. The goal, after all, is not to reinvent the wheel, but just get a more comprehensive feel and instinct for using well-known and well-supported tools.

First, let's survey our runtime environment as well as our software engineering toolbox.

*What* exactly are we programming when we write front-end code? Where does our program run, and what are its inputs and outputs?

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

We subdivide code into modules. Each code module is simply a grouping of code that exposes a well-defined interface that can be accessed by outside code.

Object-oriented programming introduces the notion of an object - a set of data (also referred to as *state*) that is hidden from direct access and defines an object interface - methods (functions attached to the object) and fields that outside code can interact with. Object type implementation is a module of code.

Well-designed object interfaces are small, simple and stable. We aim to hide implementation details and direct data access from outside code. Unit tests help ensure that the implementation of the object interface continues to match expectations as it is being updated and refactored. It so happens that code that is easy to test is also easy to maintain.

Our software models the real world - the domain - so naturally our modules and object definitions have to describe real-world concepts. It helps to maintain one-to-one correspondence between code components and specific parts of the domain description. Because we already keep the domain model in our head, it becomes less of a stretch to transfer thinking into written code, and easier to mentally step through interactions between existing modules.

Now we can start looking at typical front-end tasks through the lens of modularization and establishing simple and reliable component architecture.
