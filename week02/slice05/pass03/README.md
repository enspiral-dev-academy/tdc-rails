# Adding Capybara

We're going to need [Capybara](https://github.com/jnicklas/capybara#setup) for feature testing, so let's add it.

```ruby
gem 'capybara'
```

Our `Gemfile` looks like this now:

```ruby
source 'https://rubygems.org'

gem 'rails', '4.2.3'
gem 'sass-rails', '~> 5.0'
gem 'uglifier', '>= 1.3.0'

gem 'jquery-rails'
gem 'jbuilder', '~> 2.0'
gem 'sdoc', '~> 0.4.0', group: :doc

group :development, :test do
  gem 'byebug'
  gem 'web-console', '~> 2.0'
  gem 'spring'
  gem 'rspec-rails', '~> 3.0'
  gem 'capybara'
end
```

Run the bundler to install it:

```
bundle
```

Good. Well, now it turns out that we need to edit that `rails_helper.rb` file we deliberately ignored earlier. But it's not too tough. We just need to add this line to it: `require 'capybara/rails'`

So here are lines 6-8 of our updated file:

```ruby
require 'spec_helper'
require 'rspec/rails'
require 'capybara/rails'
```

So now let's update our inquiries spec to test a real feature:

```ruby
require 'rails_helper'

RSpec.feature "Inquiries", type: :feature do
  scenario 'they see the form on the page' do
    visit contact_path

    expect(page).to have_selector('form')
  end
end
```

Here we'll load the Contact page and we'll check that it has an HTML form element on it. Capybara gives us the `visit` method, which will visit the page, and the `have_selector` method, which will look for a specific CSS selector on the page. Here we're looking for a form element.

The `expect` syntax is part of RSpec. We pass it an object&mdash: here Capybara's `visit` method returns a `page` object if it finds one&mdash;call `to` on it, and then compare it to a "matcher". In this instance, the "matcher" is the `have_selector('form')` part. There are lots of matchers.

When we run this we see:

![Find inquiry form scenario fails](/images/find-inquiry-form-fail.png)

**This is what we expected, right?** We haven't written the code yet, so how could this pass? It can't. When we do behavior-*driven* development, the scenarios come first and the code comes afterward.

We call this process red-green refactor, because first we see the red of failing tests, then, after we write the code, we see the green of passing tests. Then we refactor and do it again, building our application one tested step at a time.

Now let's write the code to make this test pass.

We'll need a route:

```ruby
get '/contact' => 'inquiries#new'
```

Note that our URL does not need to match the name of the controller. We use "contact" to match the User's expectation, but internally we use Inquiry, which works better for us. We can do this because to the User the website is a "black box". All they see is what we let them see: the implementation is secret.

Of course, we'll need a new controller and a "new" action:

```ruby
# app/controllers/inquiries_controller.rb
class InquiriesController < ApplicationController

  def new
  end
end
```

And we'll need a view template, `app/views/inquiries/new.html.erb` that includes a &lt;form&gt; element:

```erb
<form/>
```

Whoa! That form is never going to work! It's empty!

This is an important point. When we write code to make a scenario pass, we write the *minimum* amount of code necessary to make it pass. We don't rush ahead! That wouldn't be *behavior-driven*.

Does our secnario pass?

![Passing first scenario for contact form](/images/first-contact-scenario-pass.png)

Next pass we'll extend our scenario to expect more, *then* we'll extend our code to pass the scenario.

Red. Green. Refactor.

Now to refactor . . .
