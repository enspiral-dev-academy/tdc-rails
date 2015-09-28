# Adding a log in form page

So if we're going to add authentication, we'll need a log in page.

Before we start, what, exactly, is authentication? Put simply, it is the process of ensuring that the user is who she says she is. Generally, we do this by asking for some kind of credential, for example, a username and password.

Please don't ever require a username. Logging in with a username means that your users have to remember yet another username. Usernames need to be unique if we're to use them as log in credentials, but usernames never are. So you end up with numbers appended. And then you end up with endless variations of your preferred username on multiple sites.

Worse, if we use a username for log in, and the user forgets it, then how do we retrieve it for them? Odds are they just create a new account, abandoning their old one. What a mess!

And if we have a "retreive your username" feature that requires them to supply their email address, well, then we need to have that email address on file somewhere and keep it up to date.

Why add this extra step? We already have a *unique* username: the user's *email address*. So let's use that as the credential instead.

For our site, we'll require an email address and a password.

Another common mistake is to limit what characters users can include in their password, e.g., no spaces or no punctuation marks or whatever.

*Why by all that is sacred or profane would we **ever** do that?** The *entire point* of the password is to be *difficult to crack*. Why would we force the user to use a *worse* password? If anything, we should force them to use spaces and punctuation in their passwords!

So the password might have a minimum length, or a minimum quality (difficulty to crack), but it should *never* have a maximum length (subject to the size of the datatype) or prevent the use of any characters.

![Password Strength XKCD](/images/password-strength.png)

On to the form!

First, let's add a feature spec. We'll call it `spec/features/login_spec.rb`:

```ruby
require 'rails_helper'

RSpec.feature "Login", type: :feature do
  scenario 'user can log in' do
    visit login_path

    fill_in 'Email', with: "joe@mama.com"
    fill_in 'Password', with: "^YHNmju7"

    click_button 'Log in'

    expect(page).to have_content('Log out')
  end
end
```

This, of course, fails:

![No login path](/images/no-login-path.png)

So let's add the path:

```ruby
# in /config/routes.rb
get '/login' => 'authentication#new'
```

We could call this route and the controller that handles it anything we want. But first let's think about user friendliness. To the user, what does "/session/new" mean? Probably nothing. But "/login"? That's pretty clear.



As for the controller, we want to handle both log in and log out, which are the flip sides of the authentication process, so it makes more sense to call it the AuthenticationController than the LoginController.

And for the method, we already associate `new` with the add (or new) form. Logging in means creating a new login session, so it makes sense to put this in the `new` action. And when we post the email address and password to the server to create our new session, we'll undoubtedly use `create`. That makes sense, no?

Our first pass at `app/controllers/authentication_controller.rb:

```ruby
class AuthenticationController < ApplicationController
  def new
  end
end
```

And . . .

![No login template](/images/no-login-template.png)

So:

```html
<article>
  <div class="row">
    <div class="col-sm-4 col-sm-offset-4">
      <header class="page-header">
        <h1>Please log in</h1>
      </header>
      <%= form_for :user, url: login_path do |f| %>
        <div class="form-group">
          <%= f.label :email, class: "sr-only" %>
          <%= f.email_field :email, placeholder: "Email address", class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.label :password, class: "sr-only" %>
          <%= f.password_field :password, placeholder: "Password", class: "form-control" %>
        </div>
        <div class="form-group">
          <%= f.submit "Log in", class: "btn btn-primary" %>
        </div>
      <% end %>
    </div>
  </div>
</article>
```

The form will now display properly, but we're still getting errors in our feature spec:

![No post route](/images/no-post-login-route.png)

Let's add the route and controller in one fell swoop:

```ruby
# in config/routes.rb
post '/login' => 'authentication#create'
```

```ruby
def create
  redirect_to root_url
end
```

Well, this redirects nicely to the home page, but our spec still fails because we lack a Log out button. That's because we aren't really logged in yet.

Let's do that in the next pass.
