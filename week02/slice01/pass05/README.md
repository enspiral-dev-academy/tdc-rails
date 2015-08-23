# Moving the header and footer to the layout template

We don't want to have to copy and paste all that header and footer code in every page, so we should move them to our `app/views/layouts/application.html.erb` template.

Let's copy the header, footer, and the &lt;main&gt; tag (but not its contents) into the `application.html.erb` template, and put the `<%= yield %>` element in the &lt;main&gt; element:

```erb
<!DOCTYPE html>
<html>
  <head>
    <title><%= @title || "TDC Rules!" %></title>
    <%= csrf_meta_tags %>

    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
    <%= stylesheet_link_tag    'application', media: 'all' %>

    <%= javascript_include_tag 'application' %>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
  </head>
  <body>
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
      <%= yield %>
    </main>

    <footer class="footer">
      <div class="container">
        <p class="text-muted">Place sticky footer content here.</p>
      </div>
    </footer>
  </body>
</html>
```

Now let's put our `index.html.erb` template back to something similar to what we had before. In the process, let's use HTML5 properly (Bootstrap tries a little too hard to be backwards compatible).

We should probably be using the &lt;article&gt; element for our main content. The header should be in a &lt;header&gt; element, of course. We'll keep the class attribute as that's how Bootstrap applies the CSS that makes the page look halfway decent:

```erb
<article>
  <header class="page-header">
    <h1><%= @title %></h1>
  </header>
  <p>Welcome to our brave new site!</p>
</article>
```

That seems to work.
