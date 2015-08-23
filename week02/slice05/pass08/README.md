# Sending the email when the form is posted

Now we want to create the Inquiry model when the Contact form is posted, and use that model to send an email.

First, let's improve our form so that it contains all four fields. And let's let Rails help us to create it.

Rails has a `form_for` helper method that will help us to get the form right. We can check out the [API documentation](http://api.rubyonrails.org/v4.2.3/) for it (search for "form_for"). Here is the example from the API:

```erb
<%= form_for :person do |f| %>
  First name: <%= f.text_field :first_name %><br />
  Last name : <%= f.text_field :last_name %><br />
  Biography : <%= f.text_area :biography %><br />
  Admin?    : <%= f.check_box :admin %><br />
  <%= f.submit %>
<% end %>
```

We're using Bootstrap, and we can do better than the above. We'll also take advantage of the helper for labels. Here's our updated Contact form. To simplify it a little, I've ditched the &lt;fieldset&gt;, at least for the moment, and I've wrapped it in a Bootstrap "row" class:

```erb
<div class="row">
  <div class="col-sm-6">
    <%= form_for :inquiry do |f| %>
      <div class="form-group">
        <%= f.label :name %>
        <%= f.text_field :name, class: "form-control" %>
      </div>
      <div class="form-group">
        <%= f.label :email %>
        <%= f.email_field :email, class: "form-control" %>
      </div>
      <div class="form-group">
        <%= f.label :subject %>
        <%= f.text_field :subject, class: "form-control" %>
      </div>
      <div class="form-group">
        <%= f.label :message %>
        <%= f.text_area :message, class: "form-control" %>
      </div>
      <div class="form-group">
        <%= f.submit %>
      </div>
    <% end %>
  </div>
</div>
```

That gives us:

![Contact form](/images/contact-form.png)

Note that Rails decided to name our button "Save inquiry". We can either force rename it by passing a string to the submit method, thus:

```erb
<%= f.submit "Send message" %>
```

Or we can change it in the feature spec:

```ruby
click_button 'Save Inquiry'

```

I'll choose to override the button (I like "send message" better), and while I'm at it I'll restore the Bootstrap classes:

```erb
<%= f.submit "Send message", class: "btn btn-info btn-sm" %>
```

Now the test passes again.

Let's handle the submission in the controller. First, we can try submitting the form and looking to see what the parameters send in:

```ruby
# in app/controllers/inquiries_controller.rb
def create
  flash[:notice] = "Thank you for sending us a message."
  redirect_to root_url
end
```

I fill out the form and submit it, and when I do I find this in the server's output:

```
Parameters: {"utf8"=>"✓", "authenticity_token"=>"z1BSBHKz6SJ8eqvjv3egKfjIf4BF6nL+CN3pwyDyVt44hPO/mweiJDXZZHI9fYvygreLpV+4QJwyEGEwABjlFA==", "inquiry"=>{"name"=>"Joe", "email"=>"joe@mama.com", "subject"=>"Beer", "message"=>"Gimme some."}, "commit"=>"Send message"}
```

Some things to note. First, Rails has added two hidden fields to the form. The first sets the charset to UTF-8, the second adds the authenticity_token (to prevent cross-site request forgery) of which we spoke earlier. Now we can turn that feature back on. Let's uncomment that line in the `app/controllers/application_controller.rb` file:

```ruby
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  protect_from_forgery with: :exception
end
```

We can also see that Rails has wrapped the form fields in a hash keyed to "inquiry", so if we want to access, for example, the subject, we would go through the inquiry hash:

```ruby
params[:inquiry][:subject]
```

We could just assign these directly to the fields of our model, and for the moment, that's what we'll do. But later we'll learn a shortcut.

```ruby
inquiry = Inquiry.new
inquiry.name = params[:inquiry][:name]
inquiry.email = params[:inquiry][:email]
inquiry.subject = params[:inquiry][:subject]
inquiry.message = params[:inquiry][:message]
```

Then we'll create and send the email. Here is our final (for this slice) controller action:

```ruby
class InquiriesController < ApplicationController
  def new
  end

  def create
    inquiry = Inquiry.new
    inquiry.name = params[:inquiry][:name]
    inquiry.email = params[:inquiry][:email]
    inquiry.subject = params[:inquiry][:subject]
    inquiry.message = params[:inquiry][:message]

    InquiryMailer.inquiry_email(inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message."
    redirect_to root_url
  end
end
```

Complete the form, click the "Send message" button, and letter_opener shows us our test email right in the browser:

![Test contact form email](/images/test-email.png)

We've pretty much got a working Contact form. One last quick thing to do: Let's fix the link in the navbar. So, in `app/views/layouts/application.html.erb` we set the contact link thus:

```erb
<li>
  <%= link_to_unless_current "Contact", contact_path do |label|
    content_tag(:span, label, class: "this-page")
  end %>
</li>
```

Now that we have a working form, we'll add, in the next slice, the ability to keep track of all Inquiries that have been sent. We'll do this by persisting them to a database. And we'll polish up the form a bit more, and we'll learn some new Rails helpers.

Stay tuned.

