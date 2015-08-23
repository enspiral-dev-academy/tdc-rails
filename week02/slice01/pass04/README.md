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
