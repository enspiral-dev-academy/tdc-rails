# Showing the details of an individual movie

Again, let's add a feature scenario:

```ruby
scenario 'user can show a specific movie' do
  movie = Movie.create(title: "Citizen Kane", director: "Orson Welles", genre: "Drama", year: 1941)

  visit movie_path(movie)

  expect(page).to have_content('Citizen Kane')
end
```

When we run it:

![No movie show action](/images/no-movie-show-action.png)

Add the action:

```ruby
# in app/controllers/movies_controller.rb
def show
end
```

Run:

![No show template](/images/no-movie-show-template.png)

Add the template at `app/views/movies/show.html.erb`:

```erb
<article>
  <header class="page-header">
    <h1>Movie</h1>
  </header>
  <p>Here's a movie!</p>
</article>
```

Run:

![No Citizen Kane again](/images/no-citizen-kane-again.png)

OK, let's add the find to the `app/controllers/movies_controller.rb` file:

```ruby
def show
  @movie = Movie.find(params[:id])
end
```

And list the details in the template at `app/views/movies/show.html.erb`:

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
</article>
```

Run the scenario and it works! Now to allow for updates . . .
