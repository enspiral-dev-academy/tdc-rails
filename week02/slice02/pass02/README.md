# Adding the navbar links

How do we get to our About Us page and back to our Home page? We don't really want to force our users to type "/about" and "/" into the browser's address bar each time, do we? (Say no.)

So let's add some links in our navbar. Currently, we have this in our `app/views/layouts/application.html.erb` file, lines 27-29:

```erb
<ul class="nav navbar-nav">
  <li class="active"><a href="#">Home</a></li>
  <li><a href="#about">About</a></li>
```

So we have links (HTML &lt;a&gt; "anchor" tags), but they go to the wrong places. We also have that "active" class on the &lt;li&gt; tag to worry about.

Let's start with the actual links. We're going to use a Rails helper to insert the anchor tags. This will also allow us to use the route helper to add the proper URL.

The Rails `link_to` helper is what we want. Naturally, we have the [Rails API documentation](http://api.rubyonrails.org/v4.2.3/) open as we work, so it is a simple evolution to pull up the `link_to` docs. Just search for "link_to" and click on the first result in the search results (the others are for other versions, such as `link_to_unless`).

If we scroll down to the Examples, we'll see one that looks a bit like what we probably want:

```ruby
link_to "Profiles", profiles_path
# => <a href="/profiles">Profiles</a>
```

Let's try this in our `application.html.erb` file, but with our about page, and we'll do the same for our home page:

```erb
<ul class="nav navbar-nav">
  <li class="active"><%= link_to "Home", root_path %></li>
  <li><%= link_to "About", about_path %></li>
```

Reload the page and click on the Home and About navbar links repeatedly. See how the pages load properly? Looks like it's working!

In the next pass, we'll clean this up a bit.
