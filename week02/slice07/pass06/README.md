# Deleting movies

OK, this will be quick. Let's add our `destroy` method to delete a movie.

But first, why "destroy"? Why not "delete"? Well, it's simple. Delete is a *reserved word* in Ruby. We can't use it for the name of a method (or a variable). So we use `destroy` instead. But HTTP is not Ruby. In HTTP, the method is DELETE.

Just something to remember and get used to.

There isn't an actual "delete" page, so a feature spec doesn't really make sense. Let's try a controller spec instead.

We'll use the rails generator to generate our files and folders. Run this in the root folder of your project:

```
bin/rails generate rspec:controller movies
```

Now we have `spec/controllers/movies_controller_spec.rb`:

```ruby
require 'rails_helper'

RSpec.describe MoviesController, type: :controller do

end
```

We'll add a couple specs for DELETE /movies/:id:

```ruby
require 'rails_helper'

RSpec.describe MoviesController, type: :controller do
  describe "DELETE destory" do
    it "destroys the requested movie" do
      movie = Movie.create(title: "X", director: "Y", genre: "Z", year: 2001)
      expect {
        delete :destroy, :id => movie.id
      }.to change(Movie, :count).by(-1)
    end

    it "redirects to the movies list" do
      movie = Movie.create(title: "X", director: "Y", genre: "Z", year: 2001)
      delete :destroy, :id => movie.id
      expect(response).to redirect_to(movies_url)
    end
  end
end
```

Which returns:

![Movie delete failures](/images/movie-delete-failure.png)

Wow. They both fail. Bummer.

To work. Add this to `app/controllers/movies_controller.rb`:

```ruby
def destroy
  @movie = Movie.find(params[:id])
  @movie.destroy
  redirect_to movies_url
end
```

Run the tests, and as they say, *bada bing, bada boom, bada bam*.
