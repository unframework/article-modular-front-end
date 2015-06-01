# Starting Point

Typical developer training provides one with basic understanding of computer science, a level of comfort with simple object-oriented programming (OOP) and some exposure to the modern web technologies available in browsers. There is, of course, knowledge of JavaScript, but ideally also experience in some of the other well-known programming languages, for perspective.

Importantly, we as programmers are also taught to use software engineering patterns to manage application complexity as codebases grow and change.

However, as we dive into building real production software for the Web, we quickly discover that it can be trying to make sense of it all. HTML and CSS are brittle and convoluted, and production front-end frameworks have a steep learning curve but often still lack real-world usage advice for software of any non-trivial size. Best practices for code maintainability are usually left to be an undocumented "black art" even for popular tools. Whatever advice is out there is hotly contested and encumbered with stack-specific assumptions and preferences.

To take control of this chaotic situation, we need to start from scratch. The best way to learn something is to build it.

We will start by revisiting the core problem statements of front-end programming and solve them as simply as we can. We will then apply well-known fundamental patterns of software engineering to factor the solution into a more maintainable state. By doing that we will build up an informed perspective on choice and usage of existing production frameworks.

First, let's survey our runtime environment as well as our software engineering toolbox.

So *what* exactly are we programming when we write front-end code? Where does our program run, and what are its inputs and outputs?

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

Object-oriented programming introduces a notion of an object - a set of data (also referred to as *state*) that is hidden from direct access and defines an object interface - methods (functions attached to the object) and fields that outside code can interact with. Object type implementation is a module of code.

Well-designed object interfaces are small, simple and stable. We aim to hide implementation details and direct data access from outside code. Unit tests help ensure that the implementation of the object interface continues to match expectations as it is being updated and refactored. It so happens that code that is easy to test is also easy to maintain.

Our software models the real world - the domain, so naturally our modules and object definitions have to describe real-world concepts. It helps to keep a one-to-one correspondence between code components and specific parts of the domain description. It's not just using a `Car` object class to describe a car, but also modeling behaviours and flows as separate modules too, sometimes.
