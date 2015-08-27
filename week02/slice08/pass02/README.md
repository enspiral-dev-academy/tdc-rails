# Adding the model

If we're going to log in, we'll need a User model to save the email address and password for the User who is logging in.

Our user will have, as a minimum, an email address and an encrypted password, but for our first version of the model, we'll leave the password unencrypted. That said, we'll call it `password_digest` in the expectation that it will eventually be "digested"&mdash;encrypted.

Let's start by generating both the empty model and the test. We can do this in the terminal with our Rails generator:

```
bin/rails g model User email password_digest
```

![Genarating the User model](/images/generate-mongoid-user.png)

This gives us the following model:

```ruby
class User
  include Mongoid::Document
  field :email, type: String
  field :password_digest, type: String
end
```

And this blank test:

```ruby
require 'rails_helper'

RSpec.describe User, type: :model do
  pending "add some examples to (or delete) #{__FILE__}"
end
```

Let's add some specs to our User specification. But first, there are a couple of tools that will help us to make our tests easier to write.

One of these is [**Factory Girl**](https://github.com/thoughtbot/factory_girl), which provides us with an easy way to create (or build) instances of our models for testing. The other is [**Faker**](https://github.com/stympy/faker), which generates random data to help us to populate our model objects (and saves a *lot* of time).

We'll use a version of Factory Girl optimized for Rails.

And we'll throw in [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner) for good measure.

In our Gemfile in the testing and development group, add the gems:

```ruby
gem 'factory_girl_rails'
gem 'faker'
gem 'database_cleaner'
```

And run the bulder in the terminal to download the new dependencies:

```
bundle
```

Now we'll need to add these to RSpec and configure them. We can use the
`before` method to tell RSpec to run a few things *before* it runs the test suite. (We could also do `before(:each)` to run things before each test.)

Let's update our `spec/rails_helper.rb` file to look like this:

```ruby
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
abort("The Rails environment is running in production mode!") if Rails.env.production?

require 'spec_helper'
require 'rspec/rails'
require 'capybara/rails'

RSpec.configure do |config|
  config.include FactoryGirl::Syntax::Methods

  config.before(:suite) do
    begin
      DatabaseCleaner.start
      FactoryGirl.lint
    ensure
      DatabaseCleaner.clean
    end
  end

  config.infer_spec_type_from_file_location!
end
```

The first line in the `config` block above includes the FactoryGirl methods so we don't have to preface every method call in our specs with `FactoryGirl::`.  The `begin-ensure-end` block catches any errors. Before the suite is run, RSpec will call `DatabaseCleaner.start` which does exactly what it says it does, and `FactoryGirl.lint` which will make sure that any instances we generate with FactoryGirl are valid (by calling the `valid?` method).

Ensure will run *for sure* even if something breaks. That's the point of the `ensure` branch. In the `ensure` branch, we tell DatabaseCleaner to "clean" the database. This will truncate the database collections (delete all documents in them) before the specs are run so we can ensure that we always have a clean database before we start the suite.

Now we can create our first Factory. This one will generate Users.

Create a new folder in the `spec` folder called `factories` and then add the `spec/factories/users.rb` file as follows:

```ruby
FactoryGirl.define do
  factory :user do
    email { Faker::Internet.email }
    password_digest { "123" }
  end
end
```

Remember, we're not encrypting our password yet. We'll update the digest later.

So now we can add our model specs. Let's check to see if the model is invalid without an email or without a password_digest&mdash;it should require both, right? In `spec/models/user_spec.rb` we put:

```ruby
require 'rails_helper'

RSpec.describe User, type: :model do
  it "is invalid without an email" do
    user = build(:user, :email => nil)

    expect(user).not_to be_valid
  end

  it "is invalid without an password_digest" do
    user = build(:user, :password_digest => nil)

    expect(user).not_to be_valid
  end
end
```

Now we can run the suite:

```
bin/rspec
```

When I do this, I get an error that says ". . .database_cleaner/moped/truncation_base.rb:14:in `block in clean': undefined method `remove_all' for #<Mongo::Collection::View:0x007f855ac4e2d0>".

Now what? So I did a little Googling and discovered that Mongoid had abandoned "Moped" in its version 5 (which is what we're using). Sigh. A little more Googling revealed that DatabaseCleaner has been updated to handle this, but not in its current stable version (this might have changed by the time you read this).

So there's a fixed version in GitHub, but the gem isn't fixed yet. What do we do?

How about if we pull the gem from GitHub instead of the RubyGems repository?

Change the `database_cleaner` line in your Gemfile to this:

```ruby
gem 'database_cleaner', github: 'geerzo/database_cleaner'
```

Now let's run `bundle` to install the gem from GitHub. I'm actually going to run `bundle update` which will update any gems that have updates (subject to the versions listed in my Gemfile). You'll need to be careful doing this (make sure you have a copy of your old Gemfile.lock file) because updates can break things. But this early in the development process, I like to keep things as current as possible.

Once the bundle is updated, let's try `bin/rspec` again. And it works. But I'm running a lot of specs that I don't care about, so it's getting slow and dumping a lot of output to the terminal. I can run individual specs, however, by calling rspec with the file I want to run (I can even call individual scenarios out by line number). So I'm going to run it this way:

```
bin/rspec spec/models/user_spec.rb
```

As expected, the specs fail:

![User model specs fail](/images/user-model-specs-fail.png)

Let's update our model to see if we can make them pass. We'll require that the email and the password_digest be present, and we'll check the format of the email with the same validation we used in our Inquiry model:

```ruby
# in app/models/user.rb
class User
  include Mongoid::Document
  field :email, type: String
  field :password_digest, type: String

  validates :email, presence: true, format: /\A.+@.+\z/
  validates :password_digest, presence: true
end
```

Now when I run my specs, they pass:

```
bin/rspec spec/models/user_spec.rb
```

So let's get back to our feature spec. We can run that one individually as well:

```
bin/rspec spec/features/login_spec.rb
```

Still failing, naturally:

![Login spec still failing](/images/login-spec-still-failing.png)

Now that we have a working User model, we can use that in our AuthenticationController. Let's work on that in our next pass.
