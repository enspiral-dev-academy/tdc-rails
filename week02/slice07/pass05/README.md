# Editing our movies

It's nice that we can add movies, list them, and view the details of an individual movie, but what if we want to update a movie. Maybe we typed something wrong when we added it? I know: not possible. But our site might be used by mere mortal humans, so editability is probably a good idea, even if we never need it ourselves.

You know the drill. New scenario:

```ruby
# in spec/features/movies_spec.rb
scenario 'user can update a specific movie' do
  movie = Movie.create(title: "Citizen Kane", director: "Orson Welles", genre: "Drama", year: 1941)

  visit edit_movie_path(movie)

  fill_in 'Title', with: "Casablanca"
  fill_in 'Director', with: "Michael Curtiz"
  fill_in 'Genre', with: "Drama"
  fill_in 'Year', with: 1942

  click_button 'Update Movie'

  expect(page).to have_content('Casablanca')
end
```

Run the specs:

![No edit action](/images/no-movie-edit-action.png)

Heh. No surprise there. Add the action to `app/controllers/movies_controller.rb`:

```ruby
def edit
end
```

Missing template! Let's copy the `new.html.erb` template to `edit.html.erb`:

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
    <h1>Edit movie</h1>
  </header>
  <div class="row">
    <div class="col-sm-6">
      <%= form_for @movie do |f| %>
        <div class="form-group">
          <%= f.label :title %>
          <%= f.text_field :title, class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.label :director %>
          <%= f.text_field :director, class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.label :genre %>
          <%= f.text_field :genre, class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.label :year %>
          <%= f.number_field :year, class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.submit class: "btn btn-info btn-sm" %>
        </div>
      <% end %>
    </div>
  </div>
</article>
```

Did you see the changes? I read through this line by line and made the necessary changes. If you find yourself missing things, try reading it backwards from the bottom up, right to left. That will force you to look at each word or symbol in turn.

Run the specs and:

![Undefined errors for nil class](/images/undefined-errors-for-nil-class.png)

WTF? *Undefined method `errors`*? But errors was defined before! How is it suddenly undefined.

Here is a situation where you should read your error message *carefully*. This is an error that throws many beginners and even a few intermediate developers because they don't bother to read it carefully.

The full line says (emphasis mine):

undefined method `errors' **for nil:NilClass**

The key is FOR THE NIL CLASS. We're calling `errors` on @movie, so where is this "nil class"?

What this is telling us is that it is trying to call `errors` on **nil**. Which means that @movie *is nil*. We didn't find a movie. So the `errors` method exists, but on the Movie class, *and we don't got no stinkin' Movie*.

So let's go add it to our controller action in `app/controllers/movies_controller.rb`:

```ruby
def edit
  @movie = Movie.find(params[:id])
end
```

Now run the specs:

![No update action](/images/no-movie-update-action.png)

As expected. Let's add the update action to `app/controllers/movies_controller.rb`. First we'll find the movie we want to edit (just as we did in the edit and show actions), then we'll update it and, if successful, redirect to the **show** page. That's the usual practice. If it fails to update, then it will re-render the edit form.

```ruby
def update
  @movie = Movie.find(params[:id])

  if (@movie.update(movie_params))
    flash[:notice] = "Thank you! #{@movie.title} has been updated."
    redirect_to movie_path(@movie)
  else
    render :edit
  end
end
```

Run the specs, and it works! One more thing to do: delete movies.
