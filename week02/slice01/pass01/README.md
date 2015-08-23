# Building the first slice

We're going to build our Rails application one "vertical slice" at a time, starting from the front (client-facing) side and working towards the back.

Then we'll repeat this process with the next slice, or we may iterate through an already completed slice to refactor or improve it.

Get used to this pattern of reiterating, or making passes, through your code. It's the Agile way.

## Websites have Application Programming Interfaces (APIs)

A web site or web application is simply a piece of software. That's what we're doing: we are software developers and we are building software.

The software we build runs in a "virtual machine" called the **browser**, although it may interact with other types of "user agents", such as web spiders (indexing robots from sites such as Google), phone applications, other web applications, and more.

The API of our web app is nothing more than the set of HTTP verbs and URLs to which the application responds. For example, the first and most basic command the User might issue to our site is to display the site's "home" page. Typically, this is an HTTP GET command to the root folder:

```
GET / HTTP/1.1
```

So the first thing we need to do is to create something in our server to respond to this request.

We're going to use **Controllers** to handle interactions between the User and our Application. To decide which controller will handle which command, we place an application **Router** in front of the controllers.

The application router listens for particular HTTP commands and then routes those commands to the correct Controller and Controller method (called an "action").

## Creating our first routes

In Rails, we configure our routes in a configuration file called `config/routes.rb`, surprisingly enough.

If you look in your application's `config` folder and open the `routes.rb` file, you'll find it has many comments explaining how to set up various types of routes. Rails is nice this way. Much of what you need to know is added as comments right where you need them. I delete these comments because I'm an expert at Rails. You may wish to leave them around for later reference. But remember that you can always create a blank Rails app for reference!

We're going to start with the home page, so let's add a route to make our `config/routes.rb` file look like this:

```ruby
Rails.application.routes.draw do
  get '/' => 'site#index'
end
```

This tells our application to listen for a GET HTTP request to /. When our Rails application receives such a request, it will *instantiate* (make a new copy of) our **SiteController** and will then call that object's *index* method (action).

To test our new route, we can start an HTTP server and run our new rails app. Conveniently, Rails comes with a program to run scripts, called **rake**, and there is a rake task to run a built in "WEBrick" HTTP server, typically on port 3000.

We can run it with the `bin/rails server` command, but we can also abbreviate server:

```
bin/rails s
```

This should start our application on port 3000:

![App running on port 3000](/images/port-3000.png)

If we point our browsers to `http://localhost:3000/`, then we should see this:

![Routing error](/images/routing-error.png)

WTF? We got an error? How is that possible?

Well, let's look at what the error tells us. What it says is "unititialized constant SiteController". In Ruby, constants are used for, among other things, the names of *classes*. What this is telling us is that the router is trying to initialize (instantiate a copy of) the SiteController but it can't find a class with that name.

Well, that's because we haven't created that class yet! So let's create it:

## Adding the Site controller

Our applications controllers should go in the `app/controllers` folder. Who knew?

The name of the controller should be in PascalCase (also called BumpyCase or UpperCamelCase) and should end with the word, "Controller". The file name *must* be the snake_cased version of our PascalCase class name. Note that there is already a controller file that provides an example, the `application_controller.rb` file.

Also, note the `concerns` folder. We won't be using this anytime soon, and I like to keep things clean, so let's delete that for now. We can always add it back later.

All (or almost all) of our applications controllers will *inherit* from the ApplicationController. This means that anything we want to put in *every* controller can just go in the ApplicationController. For now, leave it alone.

Add a new file in the `app/controllers` folder called `site_controller.rb` and add the following code:

```ruby
class SiteController < ApplicationController
end
```

Our text editor should look like this now:

![New site controller](/images/base-site-controller.png)

If we now reload our home page, we get a new error! What gives?

![Missing action](/images/missing-action.png)

Doh! We've added a SiteController, but the router is looking for the "index" method (action) and we haven't provided one. So let's do so:

## Adding the index action

Let's add an index method to our SiteController:

```ruby
class SiteController < ApplicationController
  def index
  end
end
```

Now let's reload our page. Does it work?

![Missing template](/images/missing-template.png)

Ack! No. Still no joy. What's wrong now?

Well, the error says "Template is missing", and it appears that it has searched in the `app/views` folder for either `site/index` or `application/index`. It's looking for something in `html` format, and it has tried a variety of template handlers, including the one we'll be using, `erb`.

Of course! We've routed the User's command to our Controller and action, but we have not told our Controller what to send back. It's looking for some kind of template.

For now, let's just return some plain text. We can do this by telling the `index` action to `render` and passing it a key-value pair where the key is `text` and the value is the text we'd like to render (send back to the browser), like this:

```ruby
class SiteController < ApplicationController
  def index
    render text: "Hi, you found me! I'm the home page."
  end
end
```

And if we reload our page, we can see that we no longer have an error! Congratulations. You've just built your first Rails web application. It doesn't do much, yet, but just wait. Pretty soon we'll be ruling the Web!
