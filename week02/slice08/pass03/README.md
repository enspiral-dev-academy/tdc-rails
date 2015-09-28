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

Let's add a couple of tests to our model specs for these:

```ruby
# in spec/models/users_spec.rb
it "finds and authenticates a user" do
  user = create(:user)

  expect(User.authenticate(user.email, user.password_digest)).to be_truthy
  expect(User.authenticate(user.email, "")).to be_falsy
  expect(User.authenticate("", user.password_digest)).to be_falsy
end

it "authenticates a user" do
  user = build(:user)

  expect(user.authenticate(user.password_digest)).to eq(true)
  expect(user.authenticate("")).to eq(false)
end
```

For the class method, I am expecting that I'll return the instance of User or nil if the User is not found, so I test for "truthiness" or "falsiness". Truthiness and falsiness exist when the type is not necessarily a true boolean, but something that returns false (nil or false) or true (anything else).

In the instance method, I will return an actual boolean, true or false, so I can test for equality.

![No authenticate methods](/images/no-authenticate-method.png)

Let's add the methods:

```ruby
# in app/models/user.rb
def self.authenticate(email, password)
  begin
    user = User.find_by(email: email)

    # returns the User instance or false
    user if user.authenticate(password)
  rescue
    nil # user not found (wrong email)
  end
end

def authenticate(password)
  self.password_digest == password # returns true or false
end
```

And now the model tests pass, but the feature spec still fails. We need to log the User in for reals and to then display a log out button. Next pass . . .
