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

Looking at that code, a developer might either be disgusted or intrigued. Where is the CSS? Aren't inline styles bad?

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

- component files should at least have a matching name (corresponding to the logical component name)
- component files should be placed in the same folder, likely named after the component or the component topic

But there is a bigger Pandora's box that has been opened.

## CSS Classes Are Globals

Assigning a class to a DOM node (as we did using `dom.className = 'standard-button'` above) instructs the browser to dump *any* style instructions attached to that class name onto that node. The same behaviour that allows us to define button styling from the component CSS file allows *everyone else* to override it!

This has been one of the essential behaviours of CSS since it was invented, but a dedicated programmer can see immediately how that spells doom for any attempts to encapsulate component implementation! Where any self-respecting programming language promotes tight scoping rules and keeping things local, CSS allows anyone to hijack the class string and start overriding style properties. The only way to avoid it is by using the newer shadow DOM API (which is part of the Web Components spec) to completely remove DOM elements from page-wide stylesheet influence.

Practically, this does not have to be a source of bugs and spaghetti code, as long as component class names are specific, fully qualified and long. This helps signal intent to teammates - this is a tightly controlled component style, and it should not be overridden.

But there is another CSS-related caveat - the issue is that sometimes developers *need* to override the CSS style inside a component!
