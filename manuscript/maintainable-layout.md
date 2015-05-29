# Maintainable Layout

The web app we are creating is nothing too fancy. It has a top bar. A side bar. Main content. Maybe a profile menu or something. The main content itself usually follows a structure: tables, form layouts, etc.

I guess we will open up an `index.html` and start typing:

```html
<!-- ... -->
<header>
    <div class="logo">Welcome to FooBarApp</div>
    <article class="profile-menu">Profile Menu</article>
</header>
<main>
    Main content here
</main>
<aside>
    Sidebar content here
</aside>
<!-- ... -->
```

As usual - **kidding**! HTML is not a programming language!

## Layout Contract

Let's consider sidebar, top bar and main content to be a separate visual component each. Each has an expectation to render as a simple rectangular block. Top bar and sidebar are typically "sticky" on screen, or at least are absolutely positioned along the sides, and the main content has to avoid appearing "under" those two areas.

We could implement that positioning logic in the components, but that would force them to be "aware" of not just the containing window dimensions, but also each other - to respect each others' size while seamlessly rendering adjoining content.

Alternatively, we can reduce the contract footprint of those components by introducing a page layout component.