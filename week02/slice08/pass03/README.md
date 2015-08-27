# Logging in

Now that we have a user model, we'll need to add the ability to log in.

The easiest way to do this is to add a method to the User to allow us to authenticate that user. We can do this in one of two ways. To demonstrate, we'll do both!

One way is to add the method as a class method on User itself. This method will have to take two parameters, obviously, as we won't know *which* user to authenticate unless we pass the email address. So our class method will have this shape:

```ruby
def self.authenticate(email, password)
end
```

We can also do this as an instance method. In this case, we have the User already, so we only need a password:

```ruby
def authenticate(password)
end
```
