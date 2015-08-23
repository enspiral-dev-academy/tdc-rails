# Adding Bootstrap

In the long run, we'll probably want to design our own CSS. Or, if we're working on a team, there will almost certainly be a professional designer who will do the design for us. We may or may not be called upon to implement it.

But while we're getting the site together and testing the basic functionality, it's nice to make the CSS simple. And that's what [Twitter's Bootstrap](http://getbootstrap.com/) is for.

We'll add Bootstrap using a content-delivery network (CDN) to keep it simple. A CDN is simply a website that hosts the bootstrap code for us, so all we have to do is link to it in the head of our HTML document.

We'll use the [Bootstrap CDN](http://getbootstrap.com/getting-started/) from the Getting Started page:

Copy the CSS link element from that page to our `app/views/layouts/application.html.erb` page thus:

![Bootstrap CSS CDN link](/images/bootstrap-css-cdn-link.png)

Make sure you add it **above** the `stylesheet_link_tag` line.

For now we won't worry about the "theme", but let's add the JavaScript, too, just in case, but this time we'll add it **below** the `javascript_include_tag`:

![Bootstrap JS CDN link](/images/bootstrap-js-cdn-link.png)

Notice, I've rearranged my &lt;head&gt; element to separate the different parts and to make the ordering clearer. We'll be adding more to this soon. Always make sure your &lt;head&gt; is on straight!

## Adding a Bootstrap template

Now let's add a Bootstrap basic template to give our home page some form. If we scroll down on the Bootstrap Getting Started page, we'll see some [example templates](http://getbootstrap.com/getting-started/#examples). Let's use the [Sticky Footer with Navbar](http://getbootstrap.com/examples/sticky-footer-navbar/) template.

Go to that page and look at the page source (in Chrome on a Mac this is Cmd-Alt-u). Copy the content inside the body tag and paste it into your `app/views/site/index.html.erb` file, deleting the current contents of that file.

**DON'T COPY THE SCRIPT TAGS AT THE BOTTOM OF THE BODY**. Just grab from the HTML comment that says "Fixed navbar" down to the closing &lt;/footer&gt; tag.

We'll also need the special CSS code for this particular template, so look in the &lt;head&gt; of the source code for a &lt;script&gt; element with an HTML comment above it that says "Custom styles for this template". Click on the link to the [CSS style sheet](http://getbootstrap.com/examples/sticky-footer-navbar/sticky-footer-navbar.css) for the custom CSS (sticky-footer-navbar.css). You should see two sets of CSS properties. Grab the one called "Sticky footer styles". It should look like this:

```css
html {
  position: relative;
  min-height: 100%;
}
body {
  /* Margin bottom by footer height */
  margin-bottom: 60px;
}
.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  /* Set the fixed height of the footer here */
  height: 60px;
  background-color: #f5f5f5;
}
```

Paste this code into your `app/assets/stylesheets/application.css` file below all the comments:

![Sticky footer CSS](/images/sticky-footer-css.png)

Now we should be able to reload our page and see the template. When I do this, however, I find that the main body of the page has slid up behind the navbar. This is probably because of some CSS that Rails is inserting and the fact that we've inserted the Bootstrap **before** rather than **after** the Rails CSS.

Really, we want to insert it in the middle&mdash;after the Rails CSS but before our own. We can do this using the "asset pipeline", but it's complicated so we'll leave it for later.

For now we'll just work around it by adding some margin to the top of our main elements. Let's change our main &lt;div&gt; element to use the HTML &lt;main&gt; element. But because this isn't completely supported in Internet Explorer yet (what a surprise!), we'll add the ARIA role of "main" to it. More on ARIA roles later. We'll also add the ".main" class, just to be safe.

```erb
<!-- Begin page content -->
<main class="container main" role="main">
  . . .
</main>
```

Now in our `app/assets/stylesheets/application.css` file, let's add some top margin to our main element:

```css
.main {
   margin-top: 50px;
 }
```

Reload the page and it looks good now!

Here's our current CSS:

```css
html {
  position: relative;
  min-height: 100%;
}

body {
  /* Margin bottom by footer height */
  margin-bottom: 60px;
}

.main {
  margin-top: 50px;
}

.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  /* Set the fixed height of the footer here */
  height: 60px;
  background-color: #f5f5f5;
}
```

And our current `app/views/site/index.html.erb` template:

```erb
<!-- Fixed navbar -->
<nav class="navbar navbar-default navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Project name</a>
    </div>
    <div id="navbar" class="collapse navbar-collapse">
      <ul class="nav navbar-nav">
        <li class="active"><a href="#">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#contact">Contact</a></li>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li role="separator" class="divider"></li>
            <li class="dropdown-header">Nav header</li>
            <li><a href="#">Separated link</a></li>
            <li><a href="#">One more separated link</a></li>
          </ul>
        </li>
      </ul>
    </div><!--/.nav-collapse -->
  </div>
</nav>

<!-- Begin page content -->
<main class="container main" role="main">
  <div class="page-header">
    <h1>Sticky footer with fixed navbar</h1>
  </div>
  <p class="lead">Pin a fixed-height footer to the bottom of the viewport in desktop browsers with this custom HTML and CSS. A fixed navbar has been added with <code>padding-top: 60px;</code> on the <code>body > .container</code>.</p>
  <p>Back to <a href="../sticky-footer">the default sticky footer</a> minus the navbar.</p>
</main>

<footer class="footer">
  <div class="container">
    <p class="text-muted">Place sticky footer content here.</p>
  </div>
</footer>
```
