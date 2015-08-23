# Adding interactivity with a Contact form

This is going to be a big slice with many passes, so gird thy loins!

We're going to try to add a basic contact form. For this slice we'll add the route to the form, the controller and action to display the form, and the view with a template. We'll keep it very simple: the form will include only a message field.

Then we'll add a route to which the form may be submitted. We'll add a controller action to handle the submission, and we'll redirect back to another page with a "flash" message telling the user that their message has been delivered.

We won't actually do anything with the message, though. We'll save that for another slice.

But it gets more complex! We're going to use Behavior Driven Development to build our form. That means RSpec and a feature spec.

Let's get started!

1. [Pass 1](/week02/slice05/pass01/README.md): Adding RSpec
2. [Pass 2](/week02/slice05/pass02/README.md): Feature testing
3. [Pass 3](/week02/slice05/pass03/README.md): Adding Capybara
4. [Pass 4](/week02/slice05/pass04/README.md): Testing the form
5. [Pass 5](/week02/slice05/pass05/README.md): Flash messages
6. [Pass 6](/week02/slice05/pass06/README.md): Modeling an inquiry
7. [Pass 7](/week02/slice05/pass07/README.md): Setting up the email
8. [Pass 8](/week02/slice05/pass08/README.md): Sending the email
