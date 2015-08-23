# Adding RSpec

To add RSpec for behavior-driven development (BDD) and to run feature tests, we need two new gems: RSpec and Capybara. The latter gem let's us run test through the browser itself.

We add new gems the same way we did old gems: via the Gemfile.

So add these gems to the Gemfile. We'll only need them during testing, so we can add them to the :development and :test group at the bottom of the file. We'll begin with [rspec-rails](https://github.com/rspec/rspec-rails):

```ruby
gem 'rspec-rails', '~> 3.0'
```

Now we run `bundle` to install it. Once it is installed, we need to generate the rspec files necessary to run it. We can use the rails generator for this.

We use `bin/rails generate` to run the generator (which generates various types of objects, as you'll soon learn). But we can also abbreviate "generate" as "g", just as we can abbreviate "server" as "s":

```
bin/rails g rspec:install
```

This creates a few files for us:

![RSpec install files](/images/rspec-install-files.png)

This created a hidden `.rspec` configuration file for us. It's pretty simple, and we needn't worry about it:

```
--color
--require spec_helper
```

It also created the `spec` folder and two files in it: `spec_helper.rb` and `rails_helper.rb`. What do these do? Who cares? We'll worry about that when the time comes. Maybe we'll never have to look in them at all. For now, let's figure out how to set up some specs and start testing.

There is one more trick we want to use on this pass. To understand this, we need to realize that gems can be installed locally with bundler, or globally. Sometimes we'll have the same gem installed both locally and globally, and that can cause problems.

To make sure that we run the gems that were installed with our bundle, we can prepend all commands with `bundle exec`. This means to execute the command in the scope of our bundle of gems, rather than in the global scope.

But what a pain to type this all the time. One option is to create a shell alias such as `be` by adding something like this to your `.bash_profile`, `.bashrc`, `.zshrc`, or equivalent:

```
alias be='bundle exec'
```

But rails also allows you to generate "binstubs" which will handle this for you. These are just links in the `/bin` folder that point to the right gems. You've used them already, such as when you used `bin/rails` instead of just `rails` (the latter will work, if you try it).

We can generate binstubs for RSpec as well:

```
bundle binstubs rspec-core
```

Now we can run our tests with `bin/rspec` instead of `rspec` and  be sure they'll work properly. So let's run that command.

Now if I try running `bin/rspec` in the root of my project, I get the following output:

![RSpec with no examples](/images/rspec-no-examples.png)

That's because we haven't written any tests yet! Next pass.
