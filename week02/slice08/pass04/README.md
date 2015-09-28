# Completing the authentication process

So our feature spec is still failing, but now we can authenticate a user. Let's update our authentication controller to do the authentication.

How will we know when a User has been authenticated. We need to maintain this information (state) between requests. The way we maintain state between requests is with the User's "session" hash, which is provided by the rack server on which our Rails app runs. The rack server uses a cookie stored on the User's browser to "remember" which user is which. When the User makes a request from our server, the rack app will receive the cookie, decode it, and then look up the session information for that user and make it available to us in the session hash. (This information can be store in a file, in memory, or in the database.)

We could store the entire User object in the session, but then we won't know if the User's status has changed until the next log in. A better idea is to store just the User's ID, and to look up the user anew on each request. This adds a little overhead (connecting to the database and retrieving data takes a bit of time), but it's more secure. If I ban a User or delete a User then the next request will fail.

In our AuthenticationController, we simply look up the User, authenticate the User, and, if authentication succeeds, store the User's ID in the session. To log the User out, we'll just set the user_id key in the session to nil.

Here's our revised AuthenticationController:

```ruby
# in app/controllers/authentication_controller.rb
def create
  if (user = User.authenticate(user_params[:email], user_params[:password]))
    session[:user_id] = user.id
    redirect_to root_url
  else
    flash.now.alert = "Sorry, unable to log you in. Please try again."
    render :new
  end
end

private

def user_params
  params.require(:user).permit(:email, :password)
end
```

Let's test this in the browser. To do that, we'll need to create a User for testing purposes. We can use the `db/seeds.rb` file for this purpose. Open that file, and replace its contents with:

```ruby
User.destroy_all

users = User.create([
  email: "joe@example.com",
  password_digest: "12345"
])
```

Now we can see the database by running the following in the terminal:

```
bin/rake db:seed
```

Once you've done that, run the `bin/rails s` server, go to http://localhost:3000/login in your browser, and try logging in with that email address and password. If you get redirected to the home page, then it worked.

It worked for me. So now we need to add a Log out button to the home page, which means updating the scenario in our `bin/features/login_spec.rb` file:

```ruby
require 'rails_helper'

RSpec.feature "Login", type: :feature do
  scenario 'user can log in and out' do
    visit login_path

    fill_in 'Email', with: "joe@mama.com"
    fill_in 'Password', with: "^YHNmju7"

    click_button 'Log in'

    expect(page).to have_content('Log out')

    click_button 'Log out'

    expect(page).to have_content('Please log in')
  end
end
```

So let's add the 'Log out' button to the home page:

```html
<article>
  <header class="page-header">
    <h1><%= @title %></h1>
  </header>
  <p>Welcome to our brave new site!</p>
  <p>
    <form method="POST" action="/logout">
      <input type="hidden" name="_method" value="delete">
      <input type="submit" value="Log out" class="btn btn-warning">
    </form>
  </p>
</article>
```

We've created the Log out button as a submit button on a form. Since we used POST and create to create the new session, we should use DELETE and destroy to destroy it. We can't do a true DELETE from a form, but we can fake it by adding a hidden field with the name "_method" (note the underscore) and the value "delete". The rails server will recognize this as an HTTP DELETE request and will call the `destroy` method accordingly.

Our test still fails, so I guess we need to add the DELETE route:

```ruby
# in config/routes.rb
delete '/logout' => 'authentication#destroy'
```

Now we just need to add the controller action to complete the log out function. Here is the full controller at `app/controllers/authentication_controller.rb`:

```ruby
class AuthenticationController < ApplicationController
  def new
  end

  def create
    if (user = User.authenticate(user_params[:email], user_params[:password]))
      session[:user_id] = user.id
      redirect_to root_url
    else
      flash.now.alert = "Sorry, unable to log you in. Please try again."
      render :new
    end
  end

  def destroy
    session[:user_id] = nil
    redirect_to login_url
  end

  private

  def user_params
    params.require(:user).permit(:email, :password)
  end
end
```

We can test this in the browser, but when we do we get this:

![Invalid authenticity token](/images/invalid-authenticity-token.png)

That darn authenticity token. How can we add that to our log out form? Well, we can Google it, right?

Well, this turns out to be quite the pain to find (Rails punishes you if you don't use the magic helpers), but here it is: you add another hidden field to your form. The final form should look like this:

`app/views/site/index.html.erb`:

```html
<article>
  <header class="page-header">
    <h1><%= @title %></h1>
  </header>
  <p>Welcome to our brave new site!</p>
  <p>
    <form method="POST" action="/logout">
      <input type="hidden" name="_method" value="delete">
      <input name="authenticity_token" value="<%= form_authenticity_token %>" type="hidden">
      <input type="submit" value="Log out" class="btn btn-warning">
    </form>
  </p>
</article>
```

A quick test in the browser works. But our feature test still fails. Let's take a look at the test. We have this:

```ruby
require 'rails_helper'

RSpec.feature "Login", type: :feature do
  scenario 'user can log in and out' do
    visit login_path

    fill_in 'Email', with: "joe@mama.com"
    fill_in 'Password', with: "^YHNmju7"

    click_button 'Log in'

    expect(page).to have_content('Log out')

    click_button 'Log out'

    expect(page).to have_content('Please log in')
  end
end
```

Uh, where did this email and password come from? Nowhere. No wonder we can't log in and out. Remember that the tests run in the **test** database, not the *development* database. So the user we created with our `db/seeds.rb` file is not visible here (not that the email and password are right anyway). So let's use our FactoryGirl method to create the user before we test:

```ruby
RSpec.feature "Login", type: :feature do
  scenario 'user can log in and out' do
    user = create(:user)

    visit login_path

    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password_digest

    click_button 'Log in'

    expect(page).to have_content('Log out')

    click_button 'Log out'

    expect(page).to have_content('Please log in')
  end
end
```

Now you might expect this to work. I did. But it seems that Capybara can't read the text on the submit button. Not with `have_content` anyway. So we'll have to try something else. How about if we access it via the CSS selector? Let's try it:

```ruby
RSpec.feature "Login", type: :feature do
  scenario 'user can log in and out' do
    user = create(:user)

    visit login_path

    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password_digest

    click_button 'Log in'

    expect(page).to have_css("input[value='Log out']")

    click_button 'Log out'

    expect(page).to have_content('Please log in')
  end
end
```

Now it works. In the next pass we'll encrypt the password.
