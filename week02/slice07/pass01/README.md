# Rails CRUD (create, retreive, update, delete)

Rails was one of the first web application frameworks (if not *the* first) to embrace "Representational State Transfer", better known as REST.

There is a lot to REST, and Rails doesn't do it all, but what it does do it does pretty well.

The fundamental idea is to restrict the possible VERBS we can use for our CRUD activities, and then to apply them to an infinite number of NOUNS, where the noun is a resource.

For example, our noun (resource) might be movies. Or users. Or widgets. Or whatever. We want to create, retrieve, update, and delete movies/users/widgets/whatever.

REST says "let's limit our verbs to the HTTP methods". Those methods are, primarily, GET, POST, PUT, PATCH, and DELETE. So everything we do with CRUD is going to use one of those methods (verbs) and apply it to a resource, or rather to a *collection* of resources.

So if we're working with movies for example, we'll have a collection called movies in which the individual instances are of class Movie.

Here is how REST maps to CRUD for our Movie collection:

```
GET      /movies        returns the collection of movies (as an array)
GET      /movies/1      returns only the movie with ID #1

POST     /movies        creates a new movie and assigns it an ID

PUT      /movies/2      updates the movie with ID #2
PATCH    /movies/2      an alternate (preferred) method to do the update

DELETE   /movies/2      deletes the movie with ID #2
```

So you can see that we have two routes that return a movie or movies, one that creates a new movie, two equivalent routes for updating a movie (we prefer PATCH), and one for deleting a movie. That's all of CRUD, right?

In Rails, we're going to map these six (five, really) routes to actions on our MoviesController:

```ruby
get      '/movies'        => 'movies#index'
get      '/movies/:id'    => 'movies#show'

post     '/movies'        => 'movies#create'

put      '/movies/:id'    => 'movies#update'
patch    '/movies/:id'    => 'movies#update'

delete   '/movies/:id'    => 'movies#destroy'
```

So the two GET routes return HTML, one (index) for the list of movies, and one (show) for an individual movie's details.

The POST, PATCH, and DELETE (let's forget PUT for now) routes create, update, and delete movies respectively. After they've done so, they'll redirect to either the index or show routes.

But we still need two more routes to provide an ADD form for adding new movies, and an EDIT form for editing current movies. These return HTML, so they'll be GETs:

```ruby
get      '/movies/new'       => 'movies#new'
get      '/movies/:id/edit'  => 'movies#edit'
```

This means that our controller needs seven actions: index, show, new, create, edit, update, and destroy. And we need eight routes (if we include the PUT option).

Conveniently, Rails gives us a shortcut to create all eight routes:

```ruby
resources :movies
```

That gives us our new `config/routes.rb` file:

```ruby
Rails.application.routes.draw do
  root 'site#index'

  resources :movies

  get '/contact' => 'inquiries#new'
  post '/contact' => 'inquiries#create'

  get '/about' => 'site#about'
  get '/privacy' => 'site#privacy'
  get '/terms' => 'site#terms'
end
```

And when we run `rake routes`, this is what we see:

![Resource routes](/images/resource-routes.png)

Now that we have some routes, let's set up a feature spec and get cracking on the code.
