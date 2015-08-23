# Modeling an inquiry

Eventually, we want to send an email from the form, but we probably want more than just a message field. Having several individual fields with no obvious relationship makes it tricky. Isn't there some way we can group properties together and treat them as a single object?

Oh, yeah. It's called object-oriented programming or OOP. We create classes, which are groups of properties and methods (behavior), and then we stamp out individual instances of those classes (called objects). So we could have an inquiry class and stamp out copies for each individual inquiry. Of course the properties would be different.

In rails, we call these classes that represent the things with which we're working in our application, "models". Let's create one for our Inquiry in the `app/models` folder that Rails has conveniently provided for us.

```ruby
# app/models/inquiry.rb
class Inquiry
end
```

Note that the model name is in PascalCase and is always singular, and that the name of the file is the snake_case name of the class, plus .rb. Naming is very important in Rails. Be careful.

The class is very simple, and so far it has no properties. In Ruby, when we add properties to a class, we typically add "getter" and "setter" methods at the same time. We can add both, or one or the other. We use helper methods to do so.

To add both a getter and a setter, we use `attr_accessor`. To add only a setter, we use `attr_writer` and to add only a getter, we use `attr_reader`.

We want to be able to both read and write our properties, so we'll use `attr_accessor`.

Let's give our class name, email, subject, and message fields:

```ruby
# app/models/inquiry.rb
class Inquiry
  attr_accessor :name, :email, :subject, :message
end
```

Now we can spin up the Rails console (which will automatically load our app) in the terminal. The command is `bin/rails console` but as with the server and the generator, we can abbreviate our command. We'll try creating a new Inquiry object and setting and getting fields:

![Getting and setting Inquiry fields](/images/inquiry-get-set.png)

It's a good idea to use the Rails console to test your model regularly.

We can also use RSpec to test our model. Let's generate a model test for the Inquiry model:

```
bin/rails g rspec:model inquiry
```

That created a `spec/models` folder and a `inquiry_spec.rb` file in it. The file contains:

```ruby
require 'rails_helper'

RSpec.describe Inquiry, type: :model do
  pending "add some examples to (or delete) #{__FILE__}"
end
```

If we run rspec, we get:

![Inquiry model pending run](/images/rspec-run-inquiry-model.png)

Let's create a new model in our spec and see if we can set and get properties:

```ruby
require 'rails_helper'

RSpec.describe Inquiry, type: :model do
  it 'gets and sets name, email, subject, and message' do
    inquiry = Inquiry.new
    inquiry.name = "Joe"
    inquiry.email = "joe@mama.com"
    inquiry.subject = "Beer"
    inquiry.message = "Gimme some."

    expect(inquiry.name).to eq("Joe")
    expect(inquiry.email).to eq("joe@mama.com")
    expect(inquiry.subject).to eq("Beer")
    expect(inquiry.message).to eq("Gimme some.")
  end
end
```

OK, it's not a very good test, but then it's not a very good model. Yet. We'll improve it as we go along. For now, it's enough.

Run the tests and they pass:

![Inquiry spec passing](/images/inquiry-get-set-pass.png)
