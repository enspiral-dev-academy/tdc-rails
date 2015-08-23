# Setting up the email

We want to send an email to ourselves when the Contact form is submitted. We've got an Inquiry model with name, email, subject, and message fields. Now we just need to set up a mailer.

In Rails, a mailer is a bit like a controller. It uses views to create the HTML or text email bodies. But instead of passing the rendered HTML/text to a browser over HTTP, it uses the Simple Mail Transfer Protocol (SMTP) to send it as email. For this to work, you'll need SMTP set up on your machine, but we can fake it for testing purposes.

That's a good thing because setting up SMTP is a little like putting your head in a vice, welding that vice to a rotary drill, and then turning the drill on. Let's just say it's *unpleasant*.

The gem we want is called "letter_opener". Let's add it to our Gemfile, which now looks like this:

```ruby
source 'https://rubygems.org'

gem 'rails', '4.2.3'
gem 'sass-rails', '~> 5.0'
gem 'uglifier', '>= 1.3.0'

gem 'jquery-rails'
gem 'jbuilder', '~> 2.0'
gem 'sdoc', '~> 0.4.0', group: :doc

gem 'letter_opener', group: :development

group :development, :test do
  gem 'byebug'
  gem 'web-console', '~> 2.0'
  gem 'spring'
  gem 'rspec-rails', '~> 3.0'
  gem 'capybara'
end
```

Then run `bundle` to install the new gem. Don't forget!

Then we just need to peek into `config/environments/development.rb` to add this setting: `config.action_mailer.delivery_method = :letter_opener`, thus:

```ruby
  config.action_mailer.raise_delivery_errors = false
  config.action_mailer.delivery_method = :letter_opener
```

It's good to keep similar configurations together, so we put it right under the `raise_delivery_errors` setting.

Next, we'll want to generate our mailer. We could create one from scratch, but we make them rarely enough that it's easier to look up the [DSL for generating them](http://guides.rubyonrails.org/action_mailer_basics.html#walkthrough-to-generating-a-mailer).

Let's generate one:

```
bin/rails g mailer InquiryMailer
```

This creates a lot of files. We'll look at them all in turn.

![Mailer files](/images/mailer-files-folders.png)

First, notice that it creates mailer test specifications in the `spec` folder. We'll start by creating a mailer test in the `spec/mailers/inquiry_mailer_spec.rb` file.

In RSpec, we can use the `before` method to set up for our tests. By calling `before(:each)` we can ensure that the block of code is run before *each* test:

```ruby
before(:each) do
  ActionMailer::Base.delivery_method = :test
  ActionMailer::Base.perform_deliveries = true
  ActionMailer::Base.deliveries = []

  @inquiry = Inquiry.new
  @inquiry.name = "Joe"
  @inquiry.email = "joe@mama.com"
  @inquiry.subject = "Beer"
  @inquiry.message = "Gimme some."

  InquiryMailer.inquiry_email(@inquiry).deliver_now
end
```

First, we set the delivery_method to test, tell ActionMailer to perform deliveries, and empty the deliveries array. Then we create a new Inquiry model and set its properties, just as we did earlier in the Rails console. We assign this model object to an instance variable, just as we did in the controllers, so that it will be available in the tests (i.e., so it won't be local to this method call).

Then we call the `inquiry_email` class method on the InquiryMailer, passing it our Inquiry instance, and finally tell it to deliver the email immediately.

So we're creating and delivering an email before *each* test. We also want to delete that email before the next test, so we'll use the `after` method to do so:

```ruby
after(:each) do
  ActionMailer::Base.deliveries.clear
end
```

Now we can create the tests themselves. Let's run four, just for fun. First, let's test that the email was sent:

```ruby
it 'should send the email' do
  expect(ActionMailer::Base.deliveries.count).to eq(1)
end
```

Next, let's check to make sure that it is sending to the right email (from the Inquiry object):

```ruby
it 'renders the receiver email' do
  expect(ActionMailer::Base.deliveries.first.to).to eq([ @inquiry.email ])
end
```

Then we'll make sure it has the right subject:

```ruby
it 'should set the subject to the correct subject' do
  expect(ActionMailer::Base.deliveries.first.subject).to eq("Beer")
end
```

Finally, let's make sure that it is being sent from our default sender email:

```ruby
it 'renders the sender email' do
  expect(ActionMailer::Base.deliveries.first.from).to eq([ "from@example.com" ])
end
```

Now when we run the specs, we get, as expected, four failures.

![Failing mailer specs](/images/failing-mailer-tests.png)

Let's see if we can't make all these pass with just one bit of code and a couple of mailer templates.

First, the templates, as they are easiest:

`app/views/inquiry_mailer/inquiry_email.html.erb`:
```erb
<p><%= @inquiry.message %></p>
```

And:

`app/views/inquiry_mailer/inquiry_email.txt.erb`:
```erb
<%= @inquiry.message %>
```

Mailers take both an HTML version and a text version and combine them into a single email that works on both modern and legacy mail applications. You don't have to use both, but it's covering all the bases so we will.

Finally, the mailer itself, which works a lot like a controller:

```ruby
# app/mailers/inquiry_mailer.rb
class InquiryMailer < ApplicationMailer
  def inquiry_email(inquiry)
    @inquiry = inquiry
    mail(to: @inquiry.email, subject: @inquiry.subject)
  end
end
```

And all specs pass. We're gonna be making the big bucks, fer sure.
