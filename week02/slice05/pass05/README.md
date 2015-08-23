# Using flash messages

One of the problems with our form as it currently stands is that it submits itself to the server with the form data, and then the server *redirects* the browser to a new page.

As you may know, the server views each page request as a completely separate transaction. The Web is a "stateless" environment. That means that it doesn't maintain "state" between requests.

State in this instance is like the state of the union, or the state of a light switch: it refers to a specific set of conditions that apply at this moment. Change those conditions, change the state.

For example, a light switch has two states: off and on. It is always in either one state or another.

When we say that the World Wide Web does not maintain state, what we mean is that each request-response cycle between a client (browser) and a server happens in a sort of vacuum. It doesn't know that this user is the same user that made an earlier request.

This becomes a problem when we want to "remember" things between requests. For example, with our contact page, we want to redirect the user to a new page&mdash;maybe the home page, for example&mdash;and then when the user requests that page, we want the server to "remember" to thank the User.

How can we do this? Well, the most common way is to give the user's browser a password of sorts, and to tell the browser, "Whenever you come back to this same server, say the password". That way the server will know that it's the same person.

We don't really use a password. Instead the server creates a unique key, which is a very long random number. Something unguessable. Then it sends that number to the browser the first time the browser requests anything from the server. It calls this bit of information (which also includes an expiration time for the key), a *cookie*.

The browser saves this cookie (unless the user tells it not to), and every time it returns to that server (until the cookie expires), the server looks up that key and says, "Oh, right. It's Bob again. Hi, Bob!" OK, not really, but it remembers that this is the same browser that made an earlier request.

And the server gives you, the developer, a little virtual box into which you can store information about that user. And we can use this little box, called the "session", to pass things *between* requests.

As part of this session (but not the whole thing), Rails gives you an object called the Flash. If you save messages in the Flash, then the next time this user visits the server, those messages will be made available (and then deleted). So it's a one-time thing.

We can take advantage of this in our InquiriesController by adding our "Thank you" message as a "notice" in the Flash object for later retrieval. Then we can cause a 302 redirect to be sent to the browser with the "location" header set to the root URL like this:

```ruby
class InquiriesController < ApplicationController

  def new
  end

  def create
    flash.notice = "Thank you for sending us a message."
    redirect_to root_url
  end
end
```

If we submit the form now, we'll see that it redirects to the home page. But we see no flash message. That's because we need to retrieve it and display it on the page.

There are two kinds of flash messages (and can be more): alerts and notices. We'll use Bootstrap's success and danger [alert boxes](http://getbootstrap.com/components/#alerts) for them

We'll probably want to display flash messages on many pages, so it makes sense to add them to the `app/views/layouts/application.html.erb` template:

```erb
    <main class="container main" role="main">
        <% if flash.alert %>
          <div class="messages">
            <div class="alert alert-danger" role="alert">
              <%= flash.alert %>
            </div>
          </div>
        <% end %>

      <% if flash.notice %>
        <div class="messages">
          <div class="alert alert-success" role="alert">
            <%= flash.notice %>
          </div>
        </div>
      <% end %>

      <%= yield %>
    </main>
```

We'll also add a bit of CSS to space the messages out:

```css
 .messages {
   margin-top: 20px;
 }
```

Now if we submit the form, the page redirects to the home page, and the success message displays:

![Success message](/images/inquiry-success-flash.png)

And if we reload the page, it's gone. So it's a one-time message. And our feature test scenario still passes. Nice.

