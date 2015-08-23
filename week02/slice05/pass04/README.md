# Testing the Contact form

So we have a passing feature test (scenario) for the Contact (inquiry) form. What's next?

Well, we want our form to have a "message" field and a "send message" button, and when the user completes the form and clicks the button, we want her to see a message saying "Thank you".

We can use Capybara to fill out the form and click the button, and then we can check that we get "Thank you" back from the server:

```ruby
RSpec.feature "Inquiries", type: :feature do
  scenario 'user can complete and submit the form' do
    visit contact_path

    fill_in 'Message', with: "This is my message"

    click_button 'Send message'

    expect(page).to have_content('Thank you')
  end
end
```

We run the test and we get a failure, as expected:

![First contact form failure](/images/form-failure-first.png)

Looks like it can't find a message field. So let's give it one. And while we're at it, we'll add in the correct Bootstrap to make the page like our other pages.

```ruby
<article>
  <header class="page-header">
    <h1>Contact us</h1>
  </header>
  <form method="post" action="/contact">
    <fieldset class="col-sm-6">
      <legend>Send us a message</legend>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea id="message" name="message" class="form-control"></textarea>
      </div>
    </fieldset>
  </form>
</article>
```

Run the scenario again, and:

![Second contact form failure](/images/form-fail-second.png)

Ah, yes. We need the submit button, too:

```ruby
<article>
  <header class="page-header">
    <h1>Contact us</h1>
  </header>
  <form method="post" action="/contact">
    <fieldset class="col-sm-6">
      <legend>Send us a message</legend>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea id="message" name="message" class="form-control"></textarea>
      </div>
      <div class="form-group">
        <input type="submit" value="Send message" class="btn btn-info btn-sm">
      </div>
    </fieldset>
  </form>
</article>
```

Let's run our scenario. We'll just show the failure part to save space and reduce redundancy:

![Third contact form failure](/images/form-fail-third.png)

Of course. We need a route and a controller/action pair. Let's add them, and we'll return a "Thank you" text:

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root 'site#index'

  get '/contact' => 'inquiries#new'
  post '/contact' => 'inquiries#create'

  get '/about' => 'site#about'
  get '/privacy' => 'site#privacy'
  get '/terms' => 'site#terms'
end
```

```ruby
# app/controllers/inquiries_controller.rb
class InquiriesController < ApplicationController

  def new
  end

  def create
    render text: "Thank you"
  end
end
```

Note: the convention is to use "new" for an "add" or "new" form (here a new inquiry), and "create" for the route that creates a new object (here the inquiry that will be stored, eventually, and sent to the site owner).

Now let's run the scneario . . . and it passes. Does yours?

We can also test it in the browser. But when I complete the form and submit it, instead of "Thank you" I see this:

![Invalid authenticity token](/images/invalid-auth-token.png)

What does this mean?

Rails provides a special token to the user on each page load, and when the user submits a form, the browser is supposed to send that token back to the server. This helps to prevent cross-site request forgery.

Later we will see how Rails can do this for us automatically. For now, let's just *temporarily* turn this feature off. In the `app/controllers/application_controller.rb` file, we can comment out the line that protects us from forgery. *Never do this on a production site!* Make your application controller look like this:

```ruby
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  # protect_from_forgery with: :exception
end
```

Now we can resubmit the form and we get "Thank you" back as plain text. And that's it for this pass.
