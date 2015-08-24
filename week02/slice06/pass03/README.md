# Adding more validation

Let's add some more validation to our Inquiry model.

We'll presume the following:

- :name is optional
- :email is required and must be close to a valid email (this is difficult)
- :subject is required and must be at least 4 characters but no more than 32 (yeah, it's arbitrary . . . get over it)
- :message is required and must be at least 12 characters long

Kind of boring, but it's kind of a boring form anyway.

If you read through the [Rails Guides](http://guides.rubyonrails.org/active_record_validations.html#validation-helpers) on validation, you'll see that this works:

```ruby
class Inquiry
  include Mongoid::Document

  field :name, type: String
  field :email, type: String
  field :subject, type: String
  field :message, type: String

  validates :email, presence: true
  validates :subject, presence: true,
    length: { in: 4..32 }
  validates :message, presence: true,
    length: { minimum: 12 }
end
```

Note: `4..32` is a Ruby "range". It includes all the integers between 4 and 32 *inclusive*.

That takes care of requiring the email, subject, and message, and it handles the length requirements, but we still need to test the email for "email-ness".

We can check the format of the email address using a regular expression (a pattern that matches strings of characters) and the `format` key. The problem is coming up with a regular expression that will match all good email addresses and no bad ones.

You can spend some time Googling this if you like, but you will eventually discover the sad truth: you can't get there from here. There simply is no way to test for email address validity without either missing some bad ones or forbidding some good ones. And even if we could, we'd only be testing that the format of the email was valid: we still wouldn't know if such an email actually existed.

So it's pointless to try. All we really want to do is to make sure that it is an "email-addressy" thing that's entered. It's probably enough to test for the presence of an @ sign with text on either side of it. We can do that like this:

```ruby
validates :email, presence: true, format: /\A.+@.+\z/
```

The `/^.+@.+$/` part is a regular expression (the / define the start and end, they are not part of the expression itself). The \A means start at the beginning of the string and the \z means end at the end of the string, so this regular expression must match the whole string.

Then the .+ means "one or more (+) of any character (.)". So one or more characters, followed by an @ sign, followed by one or more characters.

Pretty sloppy, yes. But good enough for our purposes. That said, feel free to join the flame wars online around this topic&mdash;flame wars that have been raging for decades now, with no solution in sight.

Now that we have more validation, let's focus on providing our user with a more accurate error message. To do that, we first need to see what those error messages are. We can spit them out by using a little trick with our flash hash:

```ruby
def create
  @inquiry = Inquiry.create(inquiry_params)

  if (@inquiry.valid?)
    InquiryMailer.inquiry_email(@inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{@inquiry.name}"
    redirect_to root_url
  else
    flash.now.alert = @inquiry.errors.messages.inspect
    render :new
  end
end
```

Now when we make an error, our flash will contain an inspection of the errors hash. Let's try it with a blank form.

![Form error messages dump](/images/form-errors-dump.png)

Nice. But difficult for the user to read. There must be a better way.

Well, you may have noticed that we're pulling this out of the @inquiry object and inserting it into the flash "now" hash. But we're also passing the @inquiry object directly to the ERB template for the form to use. So why go through the flash, then?

Why, indeed. Let's add some code to our template to handle the errors directly. We'll also take advantage of the "full_messages" array to make our messages easier to understand. Keep reading.

Let's check if the `errors` object has any errors, and if it does, display them in a list. We can add this to the top of our `app/views/inquiries/new.html.erb` template:

```erb
<% if @inquiry.errors.any? %>
  <div id="error_explanation">
    <h2>
      <%= pluralize(@inquiry.errors.count, "issue") %> prevented your message from being sent:
    </h2>

    <ul>
    <% @inquiry.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

Now we can just remove the flash line from our controller action:

```ruby
def create
  @inquiry = Inquiry.create(inquiry_params)

  if (@inquiry.valid?)
    InquiryMailer.inquiry_email(@inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{@inquiry.name}"
    redirect_to root_url
  else
    render :new
  end
end
```

And we can resubmit an empty form. We should see something like this:

![List of errors](/images/list-of-errors.png)

Clearly, this could still use some improvement. For example, do we really need to say that something is blank *and* the length is too short? But that's more work than I want to do today, so we'll leave it as an "exercise for the reader". Heh.

But wait! There's more! If we take a look at our code (after we've submitted an empty form), we'll see something interesting:

![field_with_errors](/images/field-with-errors.png)

Where did these &lt;div&gt; elements with the class "field_with_errors" come from? Well, it turns out that when we use the Rails `form_for` helper to create our form, it wraps any field that has errors with this extra div. Now all we need to do is to add some CSS for those fields:

```css
/* in app/assets/stylesheets/application.css */
.field_with_errors input,
.field_with_errors textarea,
.field_with_errors select {
  background-color: rgb(255, 255, 153);
  border-color: rgb(255, 0, 0);
}
```

Here's what our form looks like now when we submit it with blank fields:

![Final contact form errors](/images/final-form-errors.png)

Is it an attractive form? Um, no. But it will do until the designers come along, and it's nicely functional. It would be nice to have a way to check that the email address was a working address, that the mail actually went out *and was delivered* and seen, but that's beyond the scope of this course. Maybe later.
