# Navigation

First, let's add a link to our navbar and get rid of the useless dropdown:

In `app/views/layouts/application.html.erb`:

```erb
<ul class="nav navbar-nav">
  <li>
    <%= link_to_unless_current "About", about_path do |label|
      content_tag(:span, label, class: "this-page")
    end %>
  </li>
  <li>
    <%= link_to_unless_current "Contact", contact_path do |label|
      content_tag(:span, label, class: "this-page")
    end %>
  </li>
  <li>
    <%= link_to_unless_current "Movies", movies_path do |label|
      content_tag(:span, label, class: "this-page")
    end %>
  </li>
</ul>
```

Looks good:

![Movies on navbar](/images/movies-on-navbar.png)

Now let's add some links to our index page at `app/views/movies/index.html.erb`:

```erb
<article class="col-md-6">
  <header class="page-header">
    <div class="pull-right">
      <%= link_to "add", new_movie_path, class: "btn btn-sm btn-primary" %>
    </div>
    <h1>Movies</h1>
  </header>
  <% @movies.each do |movie| %>
    <div class="row">
      <div class="col-xs-10">
        <%= link_to movie.title, movie_path(movie) %>
      </div>
      <div class="col-xs-1">
        <%= link_to "edit", edit_movie_path(movie), class: "btn btn-sm btn-primary" %>
      </div>
      <div class="col-xs-1">
        <%= link_to "delete", movie_path(movie), method: :delete,
          confirm: "Are you sure?", class: "btn btn-sm btn-danger" %>
      </div>
    </div>
  <% end %>
</article>
```

And we notice that the form is the same on both the new and edit pages. Let's make this DRYer (Don't Repeat Yourself) by moving the form into a "partial template" and linking to it from the new and edit templates:

`app/views/movies/edit.html.erb`:

```erb
<% if @movie.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(@movie.errors.count, "issue") %> prevented your movie from being updated:</h2>

    <ul>
    <% @movie.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
<article>
  <header class="page-header">
    <h1>Edit <%= link_to @movie.title, movie_path(@movie) %></h1>
  </header>
  <div class="row">
    <div class="col-sm-6">
      <%= render partial: "form" %>
    </div>
  </div>
</article>
```

Note that we also changed the &lt;h1&gt; element to display the title and we've made it link to the show page.

`app/views/movies/new.html.erb`:

```erb
<% if @movie.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(@movie.errors.count, "issue") %> prevented your movie from being added:</h2>

    <ul>
    <% @movie.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
<article>
  <header class="page-header">
    <h1>Add a new movie</h1>
  </header>
  <div class="row">
    <div class="col-sm-6">
      <%= render partial: "form" %>
    </div>
  </div>
</article>
```

And we've added an edit link to the show page:

```erb
<article>
  <header class="page-header">
    <h1><%= @movie.title %></h1>
  </header>
  <dl>
    <dt>Director</dt>
    <dd><%= @movie.director %></dd>
    <dt>Genre</dt>
    <dd><%= @movie.genre %></dd>
    <dt>Year</dt>
    <dd><%= @movie.year %></dd>
  </dl>
  <div>
    <%= link_to "edit", edit_movie_path(@movie) %>
  </div>
</article>
```

Is this ugly as sin? You bet. But the designer's just going to come along and tell us to redo anything anyway, so for right now we only care about functionality and being able to hop around and test the pages. So, good enough!

