# Making a list of movies

OK, on with the Movies index action.

First, let's add a new scenario under our create scenario:

```ruby
scenario 'user can list all movies' do
  Movie.create(title: "Citizen Kane", director: "Orson Welles", genre: "Drama", year: 1941)

  visit movies_path

  expect(page).to have_content('Citizen Kane')
end
```

Run the specs and:

![No movie index](/images/movie-index-not-found.png)

So we add the index to `app/controllers/movies_controller.rb`:

```ruby
class MoviesController < ApplicationController
  def index
  end
  . . .
end
```

Run the specs:

![No template](/images/no-movie-index-template.png)

Easy peasy! Add a template as `app/views/movies/index.html.erb`:

```erb
<article>
  <header class="page-header">
    <h1>Movies</h1>
  </header>
  <p>List of movies here!</p>
</article>
```

As expected, now we can't find "Citizen Kane" on the page:

![No Citizen Kane!](/images/no-citizen-kane.png)

So let's grab all our Movies and spit them out on the page. First we'll need to find them and store them in an @movies instance variable in the MoviesController `index` action. Then we'll loop through them in our template. For now, we'll just output the titles.

```ruby
# in app/controllers/movies_controller.rb
def index
  @movies = Movie.all.to_a
end
```

Mongoid's `all` class method on a model returns a *cursor*. To get the array of Movie instances out of it, we'll call `to_a` (to array) on it. Now in our template:

```erb
<article>
  <header class="page-header">
    <h1>Movies</h1>
  </header>
  <ul>
    <% @movies.each do |movie| %>
      <li><%= movie.title %></li>
    <% end %>
  </ul>
</article>
```

Now when we run the test, it passes. And if in development mode in our browser we add movies using the new movie form, we can go to /movies and see a list of them.

![List of movies](/images/movies-listed.png)
