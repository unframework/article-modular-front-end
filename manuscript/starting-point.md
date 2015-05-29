# Starting Point

It's a miracle: we just woke up from a 15-year coma that started in 2000 and deprived us from the fun of watching modern web frameworks duke it out in Internet comment threads over the years.

Now, we want to get back to doing what we love - building software that does useful things, on the Web.

We still remember the computer science basics, are comfortable with simple object-oriented programming (OOP) and have quickly brushed up on modern web technologies available in a typical browser.

## Runtime Environment

We know JavaScript (and some other languages, because we are hip like that). We know that the code we write will run in some kind of modern browser. What does that mean, really?

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

Given that JavaScript is an object-oriented programming language, we will use the OOP paradigm (as well as others) to model the real world. And at the core of the object-oriented JavaScript is, of course, the object. In our examples and sketches we will stick to the basic idioms of object definition and creation, such as this:

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

It's... rustic? But it works and is universally recognized. The underscore prefix notation for private members is grating to some programmers, but again in many circles it has been accepted as a signal of intent, so we'll stick with it.

Class inheritance is something we just won't even touch - it is the author's belief that JS explicitly forces a preference for composition over inheritance, so we will get by with that just fine.

Of course, many production frameworks won't have us defining components this way. There are good reasons sometimes, although not always. But one way or another we can always *map the OOP concept* onto the framework-specific building blocks. This is still our lingua franca for understanding the underlying strategies of framework writers.

## File Structure

Let's start by mapping out the `javascripts`, `styles`, `images` directories... **kidding**!

Honestly, whatever works. It is worth mentioning that there *is* a strong argument for keeping files organized by topic (e.g. `users`, `payments`, `socialSettings`), not by type of file (e.g. `scripts`, `styles`, `templates`). Any modern front-end codebase will pass through a build pipeline, and most contributors will have some cross-functional ability, so there should be more than enough freedom to lay out the files according to developer convenience, not specific deployment format.

But again, whatever gets us to building the app.
