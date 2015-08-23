# Adding the route and controller action

To add a new **About us** page, we'll need to add a route. Remember that our routes are the API that our Users use to access individual pages and/or functions of our application.

We'll put the "About us" page at the `/about` route, and as it is a static page having to do with the site itself, we'll add it to the SiteController as an "about" action:

First, the route. In `config/routes.rb` we add:

```ruby
Rails.application.routes.draw do
  root 'site#index'

  get '/about' => 'site#about'
end
```

You should know the drill by now. Let's check the `rake routes` output to see if it worked:

![rake routes: about](/images/rake-routes-about.png)

Looks like it did. And now we can get the About Us page URL or path using the "about" prefix:

```ruby
about_path # returns /about

about_url # returns http://localhost:3000/about
```

Let's point our page at the `/about` route, and bang!

![No about action](/images/missing-about-action.png)

So let's add the action to our site controller:

```ruby
class SiteController < ApplicationController
  def index
    @title = "Welcome!"
  end

  def about
  end
end
```

Reload the page and, naturally, we get the template error. So let's add the template to `app/views/site/about.html.erb`:

```erb
<article>
  <header class="page-header">
    <h1>About us</h1>
  </header>
  <p>Here you can learn all about us!</p>
</article>
```

Reload the page, and et voilà, we have an About Us page!

That was easy, no? This Rails stuff is simple!
