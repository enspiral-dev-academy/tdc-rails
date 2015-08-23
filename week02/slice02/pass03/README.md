# Refactoring our navbar links

So, we have working links to the Home and About pages in our navbar. That's great!

But we have a couple of problems. First, the highlight that tells me which page is "active" does not move. Second, the link is a link even when it is on that page. As we know from our discussions on usability, that's a no-no. A link should always link to somewhere&mdash;never back to the same page. That's confusing for the user.

So we don't want a link when we're on the page. But we do want the link text and we want it to be highlighted to show that we're on that page.

We can accomplish the second of these tasks by using a slightly different `link_to` helper: the `link_to_unless_current` helper.

Checking the [API documentation](http://api.rubyonrails.org/v4.2.3/) for the `link_to_unless_current` helper, we see that we can pass this helper a block that will be executed when the link is for the current page. Let's try something like this:

```ruby
<%= link_to_unless_current "About", about_url do
  "About"
end %>
```

This should substitute plain text "About" for the link when we're on the About page. A quick page reload and test in the browser proves that it does.

But now we've lost the proper formatting for Bootstrap! Evidently Bootstrap was applying some CSS to that &lt;a&gt; tag, and we've removed it:

![Bad About link](/images/bad-about-link.png)

What is it we really want? We want it to look the way the Home link looks with the "active" class on the li tag. But we don't want to add an "active" class to the li tag (how would we do that? it's *outside* the `link_to_unless_current` block).

Chrome to the rescue. Let's right click on the Home link and choose "Inspect element" from the context menu. Then we'll see what it is that gives the "active" class its look and feel:

First, we'll look at the computed (not inherited) properties for the active link:

![Computed link properties](/images/computed-link-properties.png)

Now we'll need something to apply these to, so let's wrap our "About" text in a span tag and we'll give it the class name "this-page". We can use the Rails `content_tag` helper (look it up!) to do this:

Here's an example:

```ruby
content_tag(:div, content_tag(:p, "Hello world!"), class: "strong")
 # => <div class="strong"><p>Hello world!</p></div>
```

We don't need the nested tag, so we'll just do this:

```ruby
content_tag(:span, "About", class: "this-page")
```

That changes our ERB code to this:

```erb
<li><%= link_to_unless_current "About", about_path do
  content_tag(:span, "About", class: "this-page")
end %></li>
```

A quick look at our page source (after reloading) confirms that it's working:

```html
<li><span class="this-page">About</span></li>
```

So now let's add our CSS:

```css
.this-page {
  background-color: rgb(231, 231, 231);
  box-sizing: border-box;
  color: rgb(85, 85, 85);
  cursor: auto;
  display: block;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 14px;
  line-height: 20px;
  list-style-image: none;
  list-style-position: outside;
  list-style-type: none;
  padding-bottom: 15px;
  padding-left: 15px;
  padding-right: 15px;
  padding-top: 15px;
  position: relative;
  text-align: left;
  text-decoration: none;
}
```

Do we need all this? Probably not. Right off I notice that there are list-style properties. Those are probably for the &lt;li&gt; element, so we don't need them here (we're working with a span). Also, the padding is the same all around, so I can replace it with a single property. That gives us this:

```css
.this-page {
  background-color: rgb(231, 231, 231);
  box-sizing: border-box;
  color: rgb(85, 85, 85);
  cursor: auto;
  display: block;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 14px;
  line-height: 20px;
  padding: 15px;
  position: relative;
  text-align: left;
  text-decoration: none;
}
```

Let's see how that looks on the page:

![Updated this-page link](/images/updated-this-page-link.png)

That looks right, but how can we tell if we really need all those properties? How about if we use the Chrome Developer Tools to turn each off in turn seeing which make a difference and which don't. Try it yourself.

If you turn one off and it changes nothing, then leave it off. If it changes the look, turn it back on. When you're done, you know which we really need. I did this and reduced the CSS to this:

```css
.this-page {
  background-color: rgb(231, 231, 231);
  color: rgb(85, 85, 85);
  display: block;
  padding: 15px;
}
```

That looks like it works! Now let's fix the Home link as well. We no longer need the "active" class on the &lt;li&gt; element:

```erb
<ul class="nav navbar-nav">
  <li><%= link_to_unless_current "Home", root_path do
    content_tag(:span, "Home", class: "this-page")
  end %></li>
  <li><%= link_to_unless_current "About", about_path do
    content_tag(:span, "About", class: "this-page")
  end %></li>
```

Reload the page and play with it. Looks good to me. If yours doesn't, ask for help.
