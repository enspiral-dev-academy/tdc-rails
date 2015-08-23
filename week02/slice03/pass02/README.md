# Adding the Terms of Use page

Let's do the same thing for a Terms of Use page:

## Start with the route

In `config/routes.rb` we add the terms route:

```ruby
Rails.application.routes.draw do
  root 'site#index'

  get '/about' => 'site#about'
  get '/privacy' => 'site#privacy'
  get '/terms' => 'site#terms'
end
```

Now let's add the action to the SiteController (`app/controllers/site_controller.rb`):

```ruby
class SiteController < ApplicationController
  def index
    @title = "Welcome!"
  end

  def about
  end

  def privacy
  end

  def terms
  end
end
```

And we'll add a `app/views/site/terms.html.erb` file:

```erb
<article>
  <header class="page-header">
    <h1>Our terms of use</h1>
  </header>
  <p>You should respect our terms of use. Really!</p>
</article>
```

Test it:

![Terms of use](/images/terms-page.png)

We're getting really fast now.
