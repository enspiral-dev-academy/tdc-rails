# Validating input

We have a contact form with four inputs: name, email address, subject line, and message. We're using the HTML input field of type "email", which provides a very minor check on its input: the input must contain an @ sign. But other than this mild, client-side check, the User can enter pretty much anything into the form inputs, or nothing at all.

That's not good.

Mongoid provides methods to validate our inputs and to reject bad input. Let's spiff up our form a bit.

Mongoid uses the same validators (literally) as ActiveRecord, so we can just look them up in the Rails Guides (the Mongoid documentation is useless on this front). Let's start by adding a check to make sure that something is entered in the email field:

```ruby
# in app/models/inquiry.rb
validates :email, presence: true
```

This will only test that the email property is not blank. But if the email property is blank, Mongoid will prevent the Inquiry from being persisted, and it will add an error message to the object (in memory) explaining why.

We can test this by calling `.valid?` on the object we tried to save. Let's use this method to decide whether we send the email and redirect to the home page or just rerender the form. Here is our new `create` method:

```ruby
def create
  inquiry = Inquiry.create(inquiry_params)

  if (inquiry.valid?)
    InquiryMailer.inquiry_email(inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{inquiry.name}"
    redirect_to root_url
  else
    render :new
  end
end
```

Now if we try using the form with an email address, it works. But if we leave the email field blank, it reloads a blank form. Hmm. That's not good. What's that supposed to mean? Did it send or didn't it?

We should display a flash message, right? The problem is, we're not doing a redirect. We're rendering the page *now*. So any flash message we set won't be seen until the *next* page load. That obviously won't work.

Luckily, the Rails folks have thought of this. Heck, they've thought of *everything*. They've been doing this for a decade or more.

The Rails Flash object provides a `now` method that let's us send the message on *this* render. Right *now*.

```ruby
def create
  inquiry = Inquiry.create(inquiry_params)

  if (inquiry.valid?)
    InquiryMailer.inquiry_email(inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{inquiry.name}"
    redirect_to root_url
  else
    flash.now.alert = "There is a problem with your form. Please check it."
    render :new
  end
end
```

If we test it, it works well. But the form is empty. Can't we do something about that?

Of course we can. What if we passed our inquiry object back to the form and used it to fill the form fields (in the ERB template, not on the browser)?

Turns out that Rails `form_for` helper can take an instance of a class instead of just a symbol for the class name. So if we change `form_for :inquiry` to `form_for @inquiry` (and we have an @inquiry object), then it should fill the fields for us *automagically*!

Let's update the form:

```erb
<%= form_for @inquiry do |f| %>
```

And try it. And uh, oh:

![ArgumentError](/images/argument-error-contact-form.png)

Well, let's look at what our argument says. It says, "First argument in form can't contain nil or be empty". What is the first argument to our form? Oh, it's `@inquiry`. I guess we didn't supply one, did we? So let's add it to our new method. A blank one will do: we just need the structure of the object so the `form_for` method has something to work with. (Argument can't be nil or empty does not mean that the *fields* have to be set, just that we must have an object and not a nil value for the `@inquiry` instance variable.)

```ruby
# in app/controllers/inquiries_controller.rb
def new
  @inquiry = Inquiry.new
end
```

**Remember that the `new` method does not automatically save the new Inquiry instance to the database, it just creates one in memory.** We can use that blank Inquiry instance in our ERB form template at `app/views/inquiries/new.html.erb` to fill in the fields on our form.

So we test the form and we get this error:

![Bad URL on form](/images/form-for-bad-path.png)

Now what?!!

Oh, yeah. We're posting this form to `/contact`. But we've told the form that it's a form for an Inquiry object, so Rails assumes we'll have routes for an "inquiries" collection. It's expecting to do a POST to /inquiries, not /contact. Can we override that?

Of course we can. Let's change the `form_for` helper call to this:

```erb
<%= form_for @inquiry, url: contact_path do |f| %>
```

Now, if we reload the form it should work. Fill it out and submit it though and we get another error:

![First argument empty](/images/first-argument-empty.png)

Urk. We forgot to make the `inquiry` variable in the `create` method an instance variable. It's not visible in the template. Let's go back and add the @:

```ruby
# in app/controllers/inquiries_controller.rb
def create
  @inquiry = Inquiry.create(inquiry_params)

  if (@inquiry.valid?)
    InquiryMailer.inquiry_email(@inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{@inquiry.name}"
    redirect_to root_url
  else
    flash.now.alert = "There is a problem with your form. Please check it."
    render :new
  end
end
```

Now if we submit the form with the email field blank, we should get the form back with our data, and if we put something in the form, it should create the object and try to send an email. I say "try" because the user could still put a bad email address in the form. But more on that in a moment.

That's good for this pass. In the next pass, we'll add more validation, then we'll figure out how to provide good error messages to the user.
