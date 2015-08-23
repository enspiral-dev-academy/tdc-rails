# Feature testing

We're building a contact form, but "contact" can mean many things, and is often used for names in an Address Book. To keep it simpler, we'll call the page "Contact" (for usabilities sake), but on the server side we'll refer to the contacts as *inquiries*.

So let's build a spec to test our "inquiry" feature.

RSpec adds generators to Rails to allow us to generate the files we need for our specs. We can choose from a long list of different types of specs, but for now, let's just work with features.

We generate the skeleton for our Inquiry feature spec like this:

```
bin/rails g rspec:feature inquiry
```

This outputs:

![Generating a feature spec](/images/generate-feature-spec.png)

Now when we look in our `app/spec` folder, we find a `features` folder and a file called `inquiries_spec.rb`. Files in the `spec` folder whose names end with `_spec.rb` will automatically be run when we run `bin/rspec`.

Here's the contents of our file:

```ruby
require 'rails_helper'

RSpec.feature "Inquiries", type: :feature do
  pending "add some scenarios (or delete) #{__FILE__}"
end
```

Some things to notice:

Rails has an "inflector". This allows it to inflect the English language, which means that it can turn plurals to singulars and vice versa. Here we generated a feature spec for an "inquiry", but it properly inflected the noun to "inquiries" for the test.

This does not always work, so keep an eye on it.

Also notice that the file requires the 'rails_helper' file. This means that anything we put in `rails_helper.rb` will be available (and used) in this (and all) spec files.

Finally, note that it creates a "pending" scenario. This is a sort of placeholder for our coming test (scenario in BDD speak). If we run our specs again, we get this:

![Pending inquiries scenario](/images/pending-inquiries-scenario.png)

So we appear to be ready to implement our first feature scenario. Hooray.
