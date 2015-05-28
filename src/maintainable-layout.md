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

A well-designed code component will have a solid stable contract.
