# Adding the Privacy Policy page

Now that we know the drill, adding new static pages is quick and easy. Let's add a page for our privacy policy.

## Start with the route

In `config/routes.rb` we add the privacy route:

```ruby
Rails.application.routes.draw do
  root 'site#index'

  get '/about' => 'site#about'
  get '/privacy' => 'site#privacy'
end
```

We won't bother to reload the page this time. We know what's next: a controller and action. So let's add the action to the SiteController (`app/controllers/site_controller.rb`):

```ruby
class SiteController < ApplicationController
  def index
    @title = "Welcome!"
  end

  def about
  end

  def privacy
  end
end
```

And we'll add a `app/views/site/privacy.html.erb` file:

```erb
<article>
  <header class="page-header">
    <h1>Our privacy policy</h1>
  </header>
  <p>We respect your privacy. Really!</p>
</article>
```

Test it:

![Privacy policy](/images/privacy-page.png)

That was quick!
