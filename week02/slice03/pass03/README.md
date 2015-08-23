# Adding the links to the footer

Now we need to add the persistent links. They are called persistent because they appear on every page.

Except, of course, for when they are the current page. Then we want to use a placeholder.

Let's add our persistent links to the footer, which is where we generally expect to find links to legal documents such as the terms of use and the privacy policy.

Here is our current footer (from `app/views/layouts/application.html.erb`):

```erb
<footer class="footer">
  <div class="container">
    <p class="text-muted">Place sticky footer content here.</p>
  </div>
</footer>
```

It looks a bit ugly on the page. Maybe we can fix that.

![Default sticky footer](/images/default-sticky-footer.png)

We need some padding. And let's change that text to our site copyright, just for fun. We'll change our class to one that has *semantic meaning* for us, too.

```erb
<footer class="footer">
  <div class="container">
    <p class="copyright">&copy; 2015 Enspiral Dev Academy. Yeah.</p>
  </div>
</footer>
```

Now, before we reload the page, let's see what that "text-muted" class was giving us. Right click on the text, choose "Inspect element", and take a look:

![text-muted](/images/text-muted.png)

Hmm. Not much. All we need is to set the color to #777. We can do that. And while we're at it, let's add some padding-top:

```css
.copyright {
  color: #777;
  padding-top: 15px;
}
```

A quick look at the page shows that 15px is not enough. Let's try 20px.

Also, I notice that I've got a mix of rgb and hex color values in my CSS. Better to pick one. I'll choose rgb because it's easier to think about&mdash;unless you think in hexadecimal.

I can tell Chrome Developer Tools to use the rgb color format in the settings:

![Color format setting](/images/color-format.png)

Now my CSS looks like this:

```css
.copyright {
  color: rgb(119, 119, 119);
  padding-top: 20px;
}
```

And my page looks better:

![Copyright fixed](/images/copyright-fixed.png)

So now let's add the links to the Privacy policy and Terms of use pages:

```erb
<footer class="footer">
  <div class="container">
    <p class="pull-right">
      <%= link_to_unless_current "Privacy policy", privacy_path do
        content_tag(:span, "Privacy", class: "this-page")
      end %> &middot;
      <%= link_to_unless_current "Terms of use", terms_path do
        content_tag(:span, "Terms of use", class: "this-page")
      end %>
    </p>
    <p class="text-muted copyright">&copy; 2015 Enspiral Dev Academy. Yeah.</p>
  </div>
</footer>
```

I've used the "pull-right" Bootstrap class to float the links to the right. *Note that I have placed the right-floated paragraph ABOVE the other paragraph.* You can play with this, but you'll find it does not work the other way around. That's because the copyright paragraph is *not floated*, so it will fill the space and push the links below it. I don't want that, so I float the links first.

I've used an HTML character entity to place a small dot between the links so it's clear that they are different links.

Here's what it looks like now:

![First pass at persistent links](/images/first-pass-persistent-links.png)

And here's what it looks like when we're on one of the pages:

![Persistent link mess](/images/persistent-link-mess.png)

Yikes!

How did we screw that up so badly? And what happened to "policy"?

Oh, I see, I forgot to include policy in the span text. Well, that's a pain. Isn't there some way to avoid having to type it twice?

Well, turns out there is. The text of the link is passed in as a parameter to the block, so we can reuse it. Let's rewrite our ERB code to take advantage of this feature:

```erb
<p class="pull-right">
  <%= link_to_unless_current "Privacy policy", privacy_path do |label|
    content_tag(:span, label, class: "this-page")
  end %> &middot;
  <%= link_to_unless_current "Terms of use", terms_path do |label|
    content_tag(:span, label, class: "this-page")
  end %>
</p>
```

Much better. Now we have this mess:

![Persistent links with label block param](/images/dry-persistent-links.png)

Better, but the styles are still messed up. And I can see why: We've used the header styles for our "this-page" span. Let's go back to our stylesheet and make sure that those properties apply only when the span is in the header navbar. I see that the outermost nav tag has a class of "navbar-fixed-top", which clearly applies only to the top navigation links, so let's update our CSS in `app/assets/stylesheets/application.css`:

```css
.navbar-fixed-top .this-page {
  background-color: rgb(231, 231, 231);
  color: rgb(85, 85, 85);
  display: block;
  padding: 15px;
}
```

Much better:

![Persistent links repaired](/images/persistent-links-repaired.png)

Now let's style them properly. One thing I noticed, though, is that the top navbar was in a &lt;nav&gt; element. That seems semantically correct. Shouldn't this one be, too?

So first let's fix our HTML:

```erb
<nav class="pull-right">
  <%= link_to_unless_current "Privacy policy", privacy_path do |label|
    content_tag(:span, label, class: "this-page")
  end %> &middot;
  <%= link_to_unless_current "Terms of use", terms_path do |label|
    content_tag(:span, label, class: "this-page")
  end %>
</nav>
```

Now let's add the same 20px padding-top, and see how that looks:

```css
.footer nav {
  padding-top: 20px;
}
```

Looking good now!

![Final persistent links](/images/final-persistent-links.png)
