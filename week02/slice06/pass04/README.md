# Updating our feature test

So our form looks great, but when we added validation, we broke our test harness. The truth is, we probably should have updated our tests first. But better late than never!

When we run the tests we see this:

![Broken feature spec](/images/broken-contact-feature-spec.png)

Can you guess what the problem is? It's pretty clear to see. Let's look at our spec:

```ruby
RSpec.feature "Inquiries", type: :feature do
  scenario 'user can complete and submit the form' do
    visit contact_path

    fill_in 'Message', with: "This is my message"

    click_button 'Send message'

    expect(page).to have_content('Thank you')
  end
end
```

Hmm. Looks like the only field we fill in is the Message field. No wonder we have problems. Let's fill in the other fields and see if it will pass:

```ruby
RSpec.feature "Inquiries", type: :feature do
  scenario 'user can complete and submit the form' do
    visit contact_path

    fill_in 'Name', with: "Joe Mama"
    fill_in 'Email', with: "joe@mama.com"
    fill_in 'Subject', with: "More beer!"
    fill_in 'Message', with: "And let's get some donuts in here!"

    click_button 'Send message'

    expect(page).to have_content('Thank you')
  end
end
```

Run the tests, and we're mean and GREEN again. Nice.

(We might come back to this later and look at other kinds of tests.)
