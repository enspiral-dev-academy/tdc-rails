# Refactoring our home page

Let's do a quick refactoring.

Looking at our `config/routes.rb` file, we can see that we have a single route to our home page:

```ruby
get '/' => 'site#index'
```

Turns out that Rails has a shortcut for setting the **root** route. We can simply call the `root` helper and provide it with the "controller#action" that it should call:

```ruby
Rails.application.routes.draw do
  root 'site#index'
end
```

Let's use that instead and reload our page to see if it still works. And it does. (Seek help if it doesn't.)

Nice! How do we know what route this is creating? We can use **rake** (it runs scripts to do repetitive tasks for us, remember?) to list our routing table:

```
rake routes
```

When we run this, we see:

![Rake routes](/images/rake-routes-root.png)

This tells us that when we use the HTTP verb (method) "GET" to ask for the URI (URL) "/", then Rails will call the "index" action on the "site" controller.

The "prefix" can be matched with a suffix of either \_url or \_path to provide a method that gives us the actual URL for that route. The \_path suffix will give us only the path:

```ruby
root_path # returns /
```

Whereas the \_url suffix will give us the whole URL:

```ruby
root_url # returns http://localhost:3000/
```

We can use these "route helper" methods throughout our application. It sure beats hard-coding all those URLs. What if one changes? Would you rather fix it in just the `config/routes.rb` file, or in every template in which it appears.

(That's a rhetorical question. The answer should be obvious.)

Rails automatically creates route helper methods for most of the routes we add to the `config/routes.rb` file. You can use the rake command `rake routes` to see what routes Rails is responding to, where they go, and what the prefixes are for the route helper methods.
