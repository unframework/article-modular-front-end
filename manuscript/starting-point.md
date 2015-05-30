# Starting Point

It's a miracle: we just woke up from a 15-year coma that started in 2000 and deprived us from the fun of watching modern web frameworks duke it out in Internet comment threads over the years.

Now, we want to get back to doing what we love - building software that does useful things, on the Web.

We still remember the computer science basics, are comfortable with simple object-oriented programming (OOP) and have quickly brushed up on modern web technologies available in a typical browser. We know JavaScript (and some other languages, because we are hip like that).

So what are we programming when we write front-end code? What is our mental model for the running program?

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

This is a very programmer-centric view, but hey - we are programmers.

## Building Blocks

Nice, readable code uses language constructs to represent real-world concepts. This way it is easier to "think in code" and keep things self-documenting and obvious.

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
