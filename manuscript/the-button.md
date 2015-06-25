# The Button

A good app has a consistent visual language, and a well-maintained style guide. And almost every visual language includes a notion of the standard action button. It is used to let the user trigger form submission, popup prompt acceptance/dismissal, and informs the style of some of the other clickable/interactable parts of the user interface.

This component is ubiquitous but also simple to implement. It is a great first sample scenario for applying modularization patterns.

## Visual Contract

Trivial advice would be to use an HTML button element, and hang some CSS on it. Legacy "Web-dev muscle memory" has us believe that this is a solved problem. But our strategy of reinventing from the ground up suggests that we look deeper.

The standard action button is a software component, and as such it has a contract: a set of expectations that it upholds. That expectation does not stop at the DOM structure, though: it includes *everything*, even the appearance of what the user sees, down to the pixels! Of course, we don't actually render individual pixels - we delegate that to the browser - but the actual brunt of responsibility that the component as a whole isn't "broken" is still on us as maintainers.

This approach of taking the entirety of responsibility for a component's presentation can also be described as *visual encapsulation*. In other words, our component takes over a "patch" of screen area and enforces a certain look of it, as part of its promise to the rest of the codebase.

> This is in contrast to the conventional wisdom of Web development. Legacy advice tells us to separate HTML and CSS and JavaScript, to write markup and then style it separately. That works well for small content-driven websites (which was the majority of the Web when HTML/CSS was being invented) and for the typical team skill make-up of the early 2000s. But for application development, where teams are more cross-functional and where most of the time is spent *pivoting* code, this creates unexpected friction. Treating CSS as a separate layer essentially makes it a distinct "component" of its own, except with an exceedingly complex interface. Stopping at DOM level instead of "pixel" level when designing our contracts actually exponentially multiplies the resulting code interdependencies.

Our contract starts with standard action button appearance itself.

## Naïve Implementation

The standard action button is likely to be rectangular, with somewhat rounded corners, some sort of pseudo-relief effect and various styles of shadow. Font is, of course, very significant. Colour options are also a defining feature. Bootstrap 3, for example, defines "default" and "primary" types of buttons (in addition to a few others). The primary button is prominent and colourful, and the default button, well, sort of blends in.

Let's use the standard button as an opportunity to introduce visual components as a concept, and look at how classic code modularity techniques plug into the browser world.

A standard app button as a component has a simple expectation: ensure that the corresponding DOM element exists in a given spot of the page DOM hierarchy, and is displayed a specific way.

Naïve starting point:

```js
function StandardButton() {
    this.dom = document.createElement('button');
}
```

Simple object-oriented programming: we represent the component at runtime as a vanilla JavaScript object instance. All the implementation does right now is create a DOM element to help render the desired button look, but we keep the clear distinction that the component is not the DOM element *itself*. It's just an object instance. We do not promise anything else, yet.

Outside calling code does, of course, need to attach the DOM element that we created to the visible page content tree, but that step is outside of our implementation's concern. We just expose the `button` reference as a public `dom` property for anyone to access it (but pinky-swear to not modify to respect our encapsulation intent).

Wait, the displayed button is not looking quite right - it's missing the styling and the text. Let's make it look closer to our hypothetical style guide:

```js
function StandardButton(labelText) {
    this.dom = document.createElement('button');
    this.dom.appendChild(document.createTextNode(labelText));

    // style inspired by Bootstrap 3
    this.dom.style.display = 'inline-block';
    this.dom.style.marginBottom = '0';
    this.dom.style.border = '1px solid transparent';
    this.dom.style.borderRadius = '3px';

    this.dom.style.padding = '5px 10px';
    this.dom.style.fontSize = '14px';
    this.dom.style.fontWeight = '400';
    this.dom.style.lineHeight = 1.5;
    this.dom.style.textAlign = 'center';
    this.dom.style.verticalAlign = 'middle';

    this.dom.style.whiteSpace = 'nowrap';
    this.dom.style.cursor = 'pointer';
}
```

Looking at that code, a developer might either feel disgust or intrigue. Where is the CSS? Aren't inline styles bad? But this is actually not inline style! These CSS instructions are encapsulated, named and normalized - but in a JavaScript function as opposed to a CSS class declaration.

Let's recall our component contract: just make a button "happen", and make it look the right way. The rest is a black box: implementation of a component is free be anything that fulfills the contract expectations. So what matters primarily is the *runtime computed style* of the `button` DOM node. What we have so far is not pretty, and is certainly not typical, but it matches the component specification exactly. If we had a unit test for this piece of code, it would pass just fine.

There is a growing movement defending JavaScript-defined styling like this, with a lot of merit going for it. But we are going to turn down the novelty factor and return to a more traditional CSS authoring approach.

## Introducing CSS

The simplest way to replicate the above behaviour using CSS markup is to just specify the above styling under a unique class name.

```css
/* style inspired by Bootstrap 3 */
.standard-button {
    display: inline-block;
    margin-bottom: 0;
    border: 1px solid transparent;
    border-radius: 3px;

    padding: 5px 10px;
    font-size: 14px;
    font-weight: 400;
    line-height: 1.5;
    text-align: center;
    vertical-align: middle;

    white-space: nowrap;
    cursor: pointer;
}
```

```js
function StandardButton(labelText) {
    this.dom = document.createElement('button');
    this.dom.className = 'standard-button';
    this.dom.appendChild(document.createTextNode(labelText));
}
```

We are now using familiar CSS markup, and that allows for easier reading from a conventional front-end point of view. But this is still a software component, and there are specific caveats to be aware of, so as not to lose the benefits of modularity.

The simplest concern is that we used to have a single file describing a single component, and now we have two: `.css` and `.js`. To prevent confusion, the files should have the same name (matching the component) and possibly even placed into a dedicated sub-folder.

But there is a bigger Pandora's box that has been opened. CSS is incredibly leaky and hard to encapsulate! Creators of the CSS spec were originally only targeting traditional web-pages and treated them as one large component. They were not planning for application-grade features such reliable module scoping that a full programming language would support.

To help clean up unwanted CSS leaks and cruft there is a simple rule to stick by: ensure sole-sourced CSS.

## Sole-Sourced CSS

Every part of computed element style should come from the owner component's code (either JS or CSS). That, and only that. Built-in browser style is the only exception.

In other words, inspecting any DOM element using browser developer tools should show that its entire set of rendering instructions are coming from just one specific spot in the source code - a screenful of stylesheet markup or a small well-contained piece of JS.

That is *sole-sourced CSS*. There is one authority for displayed element style, and it is the component codebase that creates and manages it. No overrides by other components, no "mystery inheritance" from a DOM parent, etc.

Let's get into deeper detail as to what that means.

## CSS Classes Are Globals

Assigning a class to a DOM node (as we did using `this.dom.className = 'standard-button'` above) allows us to give it style instructions based on that class name in the component CSS code. But the same browser behaviour allows *anyone else* to override it!

There are newer APIs like shadow DOM that actually implement proper CSS isolation, but many production browsers are not yet ready for that.

For now, simply being aware of that concern is already enough to prevent issues in practice. Keeping component class names specific, fully qualified and long helps signal intent to teammates: that the CSS declaration is a tightly controlled component style, and should not be overridden.

## Component CSS Modifiers

Sometimes developers *need* to override the CSS style inside a component!

Let's say a teammate has added an instance of our standard button component inside a sidebar menu. But the button is auto-sized to fit around the text inside it - and the teammate needs that particular instance of the button to fill the width of the sidebar.

What immediately comes to mind is to override the original component CSS rule:

```css
.sidebar-menu .standard-button {
    box-sizing: border-box;
    width: 100%;
}
```

But of course this is completely counter to our modular code instincts. The sidebar CSS code is now interfering with the button CSS code, kind of like a piece of JavaScript function monkey-patching private state of another object. The sidebar and the button are too "aware" of each other now, and there is no clear interface between them.

Instead, we will extend the standard button interface to support a full-width mode in addition to the normal sizing mode.

```css
/* ... added right after existing standard button code */
.standard-button.-full-width {
    box-sizing: border-box;
    width: 100%;
}
```

```js
function StandardButton(labelText, isFullWidth) {
    this.dom = document.createElement('button');
    this.dom.className = 'standard-button';
    this.dom.appendChild(document.createTextNode(labelText));

    if (isFullWidth) {
        this.dom.className += ' -full-width';
    }
}
```

The standard button component now clearly declares the kinds of display styles it supports (normal and full-width), and no other code needs to break its encapsulation boundary. The sidebar will simply ask the button to render full-width, as can any other component.

> The `-` prefix in `-full-width` is inspired by the so-called BEM naming convention. BEM describes its own naming scheme and methodology which we are not using, but we stick to its pattern of applying the `-` symbol to denote modifiers.

## CSS Inheritance and Tag Selectors

Style entanglement and encapsulation-breaking can happen when a project has blanket tag-based rules like this:

```css
button {
    vertical-align: middle;
}
```

Specific component style may either have to override it or, worse yet, *rely on it*. That is problematic because now changing that generic `button` style to anything else will possibly break everything in the project.

Another dangerous pattern is inheritance of properties like font family or sizing. It may seem convenient to just define the font on the body and add `font-family: inherit` everywhere else. However, what we want as our intent is "make my element use main body font" and yet what we get as behaviour is "make my element use parent element font". Which breaks spectacularly when a given parent element decides to use, let's say, red Papyrus as its font face instead of the main body font!

Most production projects use a CSS preprocessor like SCSS or LESS. Those tools offer much more flexible and maintainable ways to reuse CSS code and settings than naïve inheritance and tag-only selectors: mixins (extensions) and variables. If the developer intent is to use a common app-wide theme setting, like a main body font, then that is what variables are for. If the intent is to reuse utility code, then mixins/extensions are the right tool for the job. That produces less bugs and code that is much more readable.

## Multi-Element Component CSS

Let's imagine that the standard button style got an upgrade. It now includes a faux-3D inset groove around it. Instead of playing around with `::before` and `::after` elements (seeing as the width of the button is dependent on label text), we will just surround it with a `span` node and add the extra styling to that.

Because we were strict about encapsulation, there is no change to the code outside of the standard button component. The component implementation will have to be adjusted to create the extra DOM, of course.

The `standard-button` class will be on the surrounding `span`, since it is the component base element now. But how are we going to target the button in our component CSS? The easiest thing to do is use a selector as follows:

```css
.standard-button > button {
    /* ... moving existing button style here */
}
```

The `>` (immediate-child) selector ensures that if we ever decide to allow adding arbitrary content deeper inside our component we will not accidentally style its own `button` elements with that rule. It is fast, and has been supported in every browser released in the last 8 years.

The component JS is changed accordingly:

```js
function StandardButton(labelText, isFullWidth) {
    this.dom = document.createElement('span'); // base element is now a SPAN
    this.dom.className = 'standard-button';

    if (isFullWidth) {
        this.dom.className += ' -full-width';
    }

    var buttonNode = document.createElement('button');
    buttonNode.appendChild(document.createTextNode(labelText));
    this.dom.appendChild(buttonNode);
}
```

Now, imagine that we need to add an icon of some kind to the standard button component. It may not be visible at first but show up if we pass a corresponding flag during component creation. We need to add another DOM element under the base `span`, to be the sibling of our `button` node, but since it is purely decorative, we will make it a `span` tag itself. How do we target the child `span` node?

We can add a CSS class to it, of course. But it should not be something like `icon`, because there might be an app component called `icon` as well - they would step on each others' toes! Given that this icon DOM node is "private" to our standard button component, we want to mark it accordingly: `_icon`.

```css
.standard-button > ._icon {
    /* ... add icon display CSS here */
}
```

> The `_` prefix in `_icon` is also inspired by the BEM naming convention. Just like in BEM, we use `_` as prefix to denote a "child" class, scoped to a particular component.

This covers most implementation needs of rendering a component under our new guidelines of visual encapsulation and sole-sourced CSS.
