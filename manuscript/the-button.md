# The Button

A good app has a consistent visual language, and a well-maintained style guide. One typical element of such a style guide is the standard action button.

It is likely to be rectangular, with somewhat rounded corners, some sort of pseudo-relief effect and various styles of shadow. Font is, of course, very significant. Colour options are also a defining feature. Bootstrap 3, for example, defines "default" and "primary" types of buttons (in addition to a few others). The primary button is prominent and colourful, and the default button, well, sort of blends in.

Let's use the standard button as an opportunity to introduce visual components as a concept, and look at how classic code modularity techniques plug into the browser world.

## Visual Contract

A standard app button as a component has a simple expectation: ensure that the corresponding DOM element exists in a given spot of the page DOM hierarchy, and is displayed a specific way.

Naïve starting point:

```js
function createStandardButton() {
    return document.createElement('button');
}
```

Wait, the displayed button is not looking quite right, let's make it look closer to our hypothetical style guide:

```js
function createStandardButton() {
    var dom = document.createElement('button');

    // style inspired by Bootstrap 3
    dom.style.display = 'inline-block';
    dom.style.marginBottom = '0';
    dom.style.border = '1px solid transparent';
    dom.style.borderRadius = '3px';

    dom.style.padding = '5px 10px';
    dom.style.fontSize = '14px';
    dom.style.fontWeight = '400';
    dom.style.lineHeight = 1.5;
    dom.style.textAlign = 'center';
    dom.style.verticalAlign = 'middle';

    dom.style.whiteSpace = 'nowrap';
    dom.style.cursor = 'pointer';

    return dom;
}
```

Looking at that code, a developer might either feel disgust or intrigue. Where is the CSS? Aren't inline styles bad?

Let's recall our component contract: just make a button happen, and make it look the right way. The rest is a black box: implementation of a component is free be anything that fulfills the contract expectations. So what matters primarily is the *runtime computed style* of the `button` DOM node. What we have so far is not pretty, and is certainly not typical, but it matches the component specification exactly. If we had a unit test for this piece of code, it would pass just fine.

There is a growing movement defending inline styles, with a lot of merit going for it. But we are going to be pragmatic and return to a more traditional CSS authoring approach.

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
function createStandardButton() {
    var dom = document.createElement('button');
    dom.className = 'standard-button';

    return dom;
}
```

We are now using familiar CSS markup, and that allows for easier reading from a conventional front-end point of view. But this is still a software component, and there are specific caveats to be aware of, so as not to lose the benefits of modularity.

The simplest concern is that we used to have a single file describing a single component, and now we have two: `.css` and `.js`. To deal with that structural split, there are some conventions that help:

- component files should at least have a matching name (corresponding to the logical component name: `StandardButton`)
- component files should be placed in the same folder, likely named after the component or the component topic

But there is a bigger Pandora's box that has been opened.

## CSS Classes Are Globals

Assigning a class to a DOM node (as we did using `dom.className = 'standard-button'` above) instructs the browser to dump *any* style instructions attached to that class name onto that node. The same behaviour that allows us to define button styling from the component CSS file allows *everyone else* to override it!

This has been one of the essential behaviours of CSS since it was invented, but only because the creators of CSS were targeting traditional web-pages and treated them as one large component. We want to promote tight scoping and keeping things local instead, to help our encapsulation, but CSS allows anyone to hijack our component class string and start overriding style properties.

The newer shadow DOM APIs (part of the Web Components spec) can completely hide DOM elements from page-wide stylesheet influence, which negates the unwanted default CSS behaviour, but the current generation of browsers and frameworks in the wild can't fully support that spec just yet.

Simply being aware of that concern is already enough to prevent issues in practice. Keeping component class names specific, fully qualified and long helps signal intent to teammates: that the CSS declaration is a tightly controlled component style, and should not be overridden.

But there is another CSS-related caveat - the issue is that sometimes developers *need* to override the CSS style inside a component!

## Component CSS Modifiers

A teammate has added an instance of our standard button component inside a sidebar menu. But the button is auto-sized to fit around the text inside it - and the teammate needs that particular instance of the button to fill the width of the sidebar.

What immediately comes to mind:

```css
.sidebar-menu .standard-button {
    box-sizing: border-box;
    width: 100%;
}
```

But of course this is completely counter to our modular code instincts. Now changing the standard button style means also following up on every single instance of this kind of context-specific "tweak". If this additional CSS is in the sidebar-related stylesheet file, that means it might be forgotten. If this CSS tweak is in the same file as the main standard button stylesheet, then modifying or removing the sidebar might leave this rule behind as "cruft" that never gets cleaned up.

Instead, we will add this special case to the standard button stylesheet, and explicitly present it as a modification of the typical standard button display:

```css
.standard-button.-full-width {
    box-sizing: border-box;
    width: 100%;
}
```

```js
function createStandardButton(isFullWidth) {
    var dom = document.createElement('button');
    dom.className = 'standard-button';

    if (isFullWidth) {
        dom.className += ' -full-width';
    }

    return dom;
}
```

The standard button component now clearly declares the kinds of display styles it supports (normal and full-width for now), and no other code needs to break its encapsulation boundary. The sidebar will simply ask the button to render full-width.

## CSS Inheritance and Tag Selectors

Style entanglement and encapsulation-breaking can happen when a project has blanket tag-based rules like this:

```css
button {
    vertical-align: middle;
}
```

Specific component style may either have to override it or, worse yet, *rely on it*. The latter is problematic because now changing that generic `button` style to anything else will possibly break everything in the project.

Another dangerous pattern is inheritance of properties like font family or sizing. It may seem convenient to just define the font on the body and add `font-family: inherit` everywhere else. However, what we want as our intent is "make my element use main body font" and yet what we get as behaviour is "make my element use parent element font". Which breaks spectacularly when a given parent element decides to use, let's say, red Papyrus as its font face instead of the main body font!

Given that most project build pipelines use a CSS preprocessor like SCSS or LESS, it is trivial to define the intended body font as an app-wide theme variable and then just explicitly use that. In fact, to help immediately detect font leaks the actual body element stylesheet could include a red Papyrus or Comic Sans setting - a quick way to highlight component styles vulnerable to broken encapsulation.

## Sole Sourced CSS Rule

We can see the broad logic behind specific CSS restrictions and naming rules now.

Every part of computed element style should come from the owner component's code (either JS or CSS). Opening the browser developer console and inspecting any DOM element should show that its entire set of rendering instructions are coming from just one specific spot in the source code - a screenful of stylesheet markup or a small well-contained piece of JS.

That is *sole-sourced CSS*.
