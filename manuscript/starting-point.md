# Starting Point

We want to do what we love - build software that does useful things, on the Web.

We know computer science basics, are comfortable with simple object-oriented programming (OOP) and have quickly brushed up on modern web technologies available in a typical browser. We know JavaScript (and some other languages, because we are hip like that).

But there are 1001 different front-end frameworks en vogue, each with its own lingo, vocal fan-base and often a completely custom set of command-line tools. Showing text and pictures on screen has never seemed so complicated.

Of course, it's not just about showing text and pictures. Codebases have to be maintained and added to, and a production framework helps manage code complexity to keep development speedy and reduce bugs. However, there is still clearly enough difference of opinion between framework writers about *how* the complexity is to be managed. Also, maintainability best practices are often left to be an undocumented "black art" even for popular tools.

The best way to learn a tool is to rebuild it. Let's get back to timeless fundamentals that we learned in Software Engineering 101 and work through basic problem statements of front-end development with an open mind and playful attitude. That will give us an informed perspective on choice and usage of existing production frameworks.

Any framework is just a structure blueprint for a program, accompanied by useful bits of code. But *what* exactly are we programming when we write front-end code? Where does our program run, and what are its inputs and outputs?

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

## Building Blocks

Readable, maintainable code uses language constructs to represent real-world concepts. This way it is easier to "think in code" and keep things self-documenting and obvious.

Given that JavaScript is an object-oriented programming language, we will use the OOP paradigm (as well as others) to model the real world. And at the core of the object-oriented JavaScript is, of course, the object. Here is a vanilla object prototype definition (kind of like a class but not quite):

```js
function MyObjectType() {
    this._foo = 1; // underscore prefix conventionally denotes private state
    this.baz = 2; // public field
}

MyObjectType.prototype.applySomeBehaviour = function () {
    this._foo += 5;
}

var obj = new MyObjectType();
```

There are other conventions, of course, but this will do for now.

## Objects Everywhere

Actual production code rarely looks like that today. No framework en vogue asks you to just define a vanilla class. For example, AngularJS allows defining "services", "controllers", "directives" and other types of components, React has a special notation to declare "component" classes, Backbone has "models" and "views". JQuery widgets work by attaching special message passing mechanisms to DOM element objects.

But we can always link the framework-specific building blocks back to the underlying OOP concept. There is always some encapsulated state, and some exposed contract for manipulating the state. So we can still use that as our lingua franca for planning out our own intent, as well as understanding the underlying strategies of existing framework code.
