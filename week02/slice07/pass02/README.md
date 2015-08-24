# Feature testing our CRUD

Time for some cruddy feature testing.

Let's begin by creating a new file in our `spec` folder:

`spec/features/movies_spec.rb`

We can copy and paste our `inquiries_spec.rb` file and then change it to this:

```ruby
require 'rails_helper'

RSpec.feature "Movies", type: :feature do
  pending 'user can create a new movie'
end

```

Now when we runt `bin/rspec` we see:

![Pending movies spec](/images/pending-movies-spec.png)

So . . . what do we want in our new movie? Let's keep it simple; we can always add more fields later. How about title, director, year, and genre?

And again, we'll look for a Thank You message when it has successfully submitted the movie.

How about this? It should look familiar.

```ruby
RSpec.feature "Movies", type: :feature do
  scenario 'user can add a movie' do
    visit new_movie_path

    fill_in 'Title', with: "Citizen Kane"
    fill_in 'Director', with: "Orson Welles"
    fill_in 'Genre', with: "Drama"
    fill_in 'Year', with: 1941

    click_button 'Save Movie'

    expect(page).to have_content('Thank you')
  end
end
```

When we run this, we get:

![No MoviesController](/images/no-movies-controller.png)

Which is exactly what we expected, right? It's what I expected. Was it what you expected?

This just means that we need a MoviesController, so let's make one:

Add a new file: `app/controllers/movies_controller.rb` and we might as well add the `new` action as well:

```ruby
class MoviesController < ApplicationController
  def new
  end
end
```

Rerun the spec:

![Missing template](/images/missing-movies-template.png)

This time you expected it, right? OK, we need a `app/views/movies/new.html.erb` template for our form. Now people will tell you that you should never do this&mdash;never!&mdash;but c'mon, you know you're going to. So just be careful.

We already have a nice form, let's just copy our inquiry form over and change it to a movie form:

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

Rerun the features spec:

![No Movie class](/images/no-movie-class.png)

Hmm. Looks like we don't have anything in @movie. Well, of course not. We haven't added it to our MoviesController yet. Let's do that:

```ruby
class MoviesController < ApplicationController
  def new
    @movie = Movie.new
  end
end
```

Of course, still no joy as there is no Movie class, so let's add that to our models as `app/models/movie.rb`:

```ruby
class Movie
  include Mongoid::Document

  field :title, type: String
  field :director, type: String
  field :genre, type: String
  field :year, type: Integer
end
```

(Of course, I copied the Inquiry class over and changed it. Saves remembering all that stuff.)

When we rerun the tests we get a funny result:

![No "Save Movie" button](/images/no-save-movie-button.png)

That's weird. Let's load the page and see what our form looks like:

!["Create Movie" button](/images/create-movie-button.png)

WTF? The name of the button changed.

Actually, that's a Rails "feature". We won't get into it now, but when we create our edit/update form we'll see why this is true. For now, we can just fix the feature test:

```ruby
require 'rails_helper'

RSpec.feature "Movies", type: :feature do
  scenario 'user can add a movie' do
    visit new_movie_path

    fill_in 'Title', with: "Citizen Kane"
    fill_in 'Director', with: "Orson Welles"
    fill_in 'Genre', with: "Drama"
    fill_in 'Year', with: 1941

    click_button 'Create Movie'

    expect(page).to have_content('Thank you')
  end
end
```

And . . .

![No Movie create action](/images/no-movie-create-action.png)

Sigh. But this is what we expected right? We're submitting the form just fine. Now we need to finish up our `create` route through to actual persistence of the new Movie.

Taking a quick look at the InquiriesController we see that we'll need to do that "strong parameters" thing again and add a private method to strip anything bad out of our params hash before we use it to create a new Movie.

```ruby
# app/controllers/movies_controller.rb
class MoviesController < ApplicationController
  def new
    @movie = Movie.new
  end

  def create
    @movie = Movie.create(movie_params)

    if (@movie.valid?)
      flash[:notice] = "Thank you! #{@movie.title} has been added."
      redirect_to new_movie_path
    else
      render :new
    end
  end

  private

  def movie_params
    params.require(:movie).permit(:title, :director, :genre, :year)
  end
end
```

Now when we run the spec, it passes. And that's this pass complete! And if we play with the form, we can see that it does indeed add movies. Now to list the ones we've added.

