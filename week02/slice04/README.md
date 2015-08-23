# Using the logo as a link to home

An important usability issue is having a link back to the home page from every page on the site. Over time, the convention has become to put the company/site logo in the upper left hand corner of the page, and to make it a link back to the home page.

Convention: good. Strange, unexpected interfaces: bad. Unless your users are expecting that&mdash;in which case it's not really unexpected, is it?

Right now our Logo is missing and what is holding that spot doesn't really link to anything. Also, we have a "Home" link in the navbar, which is a bit redundant (though <abbr title="Your mileage may vary">YMMV</abbr>).

Here is the current code:

```erb
  <a class="navbar-brand" href="#">Project name</a>
</div>
<div id="navbar" class="collapse navbar-collapse">
  <ul class="nav navbar-nav">
    <li><%= link_to_unless_current "Home", root_path do
      content_tag(:span, "Home", class: "this-page")
    end %></li>
```

Let's start by moving that Home link up and changing it to the name of our web site/application:

```erb
  <a class="navbar-brand" href="#">Project name</a>
  <%= link_to_unless_current "Home", root_path do
    content_tag(:span, "Home", class: "this-page")
end %>
</div>
<div id="navbar" class="collapse navbar-collapse">
  <ul class="nav navbar-nav">
```

Next, let's change the name from Home to our project (I'll just use Enspiral Dev Academy). And we need to get that class (navbar-brand) in there, too. Oh, and let's pass in the label rather than typing it twice (we'll fix that on the About link, too).

A quick look at the [API docs]() reveals that the `link_to_unless_current` takes an :options parameter and an :html_options parameter. Well, which is which.

And here is where you learn that the Rails API documents *kind of suck*. Yes, despite years of effort and lots of nice features, they are still woefully incomplete. I guess you're just supposed to *know* what those options are.

Here's one trick: As the `link_to_unless_current` helper is a variation on the base `link_to` helper, let's check the API docs for the `link_to` helper.

And here we find that the :options parameter is for just a few options (data, method, etc.). So where would class go? Well, it's an HTML attribute, so mabye in the :html_options? Let's try it:

```erb
<%=
  link_to_unless_current "Enspiral Dev Academy",
    root_path, html_options: { class: "navbar-brand" } do |label|
      content_tag(:span, label, class: "navbar-brand")
    end
%>
```

And we get:

![Broken navbar](/images/broken-navbar-brand.png)

Oh, that's not right! Let's look at the HTML:

![Broken navbar HTML](/images/broken-navbar-html.png)

Grrr. Seems we don't need that outer key (html_options). Let's try it with just the class key:

```erb
<%=
  link_to_unless_current "Enspiral Dev Academy",
    root_path, class: "navbar-brand" do |label|
      content_tag(:span, label, class: "navbar-brand")
    end
%>
```

Which yields:

![Repaired navbar brand](/images/repaired-navbar-brand.png)

Now we're talking! Does it work when we're on the home page, though? Yes! Looks good, and it's not a link on the home page.

Got lucky, there. Bootstrap isn't always so easy.
