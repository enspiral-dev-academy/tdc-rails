# Setting up MongoDB and Mongoid

MongoDB can be installed on any operating system. If it's not already installed on your system, then follow the instructions on the [MongoDB website](https://www.mongodb.org/downloads).

Next, we'll need to add the [mongoid](http://docs.mongodb.org/ecosystem/tutorial/ruby-mongoid-tutorial/#ruby-mongoid-tutorial) gem to our Gemfile:

```ruby
gem 'mongoid', '~> 5.0.0.beta'
```

Then run bundler:

```
bundle
```

Then we'll generate the mongoid config file:

```
bin/rails g mongoid:config
```

Hooray! Now let's convert our Inquiry model to use Mongoid for persistence. First, we'll use composition to mix in the Mongoid methods:

```ruby
class Inquiry
  include Mongoid::Document
  attr_accessor :name, :email, :subject, :message
end
```

Then we'll create Mongoid fields to replace the standard ruby getters and setters:

```ruby
class Inquiry
  include Mongoid::Document

  field :name, type: String
  field :email, type: String
  field :subject, type: String
  field :message, type: String
end
```

Mongoid gives us some wonderful new methods. We can now spin up the Rails console and take Mongoid and MongoDB for a ride:

```
bin/rails c
```

Let's create our inquiry. The `create` method not only initializes the object (accepting the properties in the initializer, so we can pass them to `create`), but also saves it to the database (as opposed to `new`, which requires a separate call to `save`):

![Console example of Mongoid create](/images/console-mongoid-create.png)

To prove that we've persisted this Inquiry instance to the database, let's close the console, reopen it, and see if we can load Joe's inquiry back up from the database. There should be only one inquiry in the database at the moment, so we'll just use `Inquiry.first` to grab it:

![Joe's first inquiry](/images/joe-persisted.png)

Now we'll need to update our controller. We can use a shortcut and just pass the params hash into the Inquiry.create method. But this isn't safe. A devious user could pass anything in. So we'll add a private method to our controller where we'll make sure that nothing gets through that we don't want to see in our database.

This method is called **strong parameters**. Typically, the method is the name of the class followed by "\_params":

```ruby
private

def inquiry_params
  params.require(:inquiry).permit( :name, :email, :subject, :message )
end
```

Now we can call that method in our action to create our new Inquiry. Here is the entire controller:

```ruby
class InquiriesController < ApplicationController
  def new
  end

  def create
    inquiry = Inquiry.create(inquiry_params)

    InquiryMailer.inquiry_email(inquiry).deliver_now

    flash[:notice] = "Thank you for sending us a message, #{inquiry.name}"
    redirect_to root_url
  end

  private

  def inquiry_params
    params.require(:inquiry).permit( :name, :email, :subject, :message )
  end
end
```

Let's try the form:

![Sending a message from Bob](/images/bobs-message.png)

Click the "Send message" button and we get this email:

![Bob's email](/images/bobs-email.png)

Now we can spin up the Rails console, find Bob's inquiry by his email address, and check to make sure that the information was stored correctly. It was:

![Bob's inquiry](/images/bobs-inquiry.png)

It works.

Note: Mongoid has many different data types other than Strings. Here is a complete list:

- Array
- BigDecimal
- Boolean
- Date
- DateTime
- Float
- Hash
- Integer
- BSON::ObjectId
- BSON::Binary
- Range
- Regexp
- Symbol
- Time
- TimeWithZone

We'll be using more of these shortly.
