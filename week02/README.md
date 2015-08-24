# Week 2: Rails

Building a basic Rails application:

## Set up your GitHub repository

Follow the instructions here: [Creating your git repository](/week02/github/README.md)

You should now have your project open in a text editor or IDE, and your terminal open with the base folder of your repos as your current working directory.

## Set the Ruby version

Make sure you have [rbenv](/week02/rbenv/README.md) and the **ruby-build** plugin installed. Then, using your terminal, set the Ruby version in your project's root folder thus:

```
rbenv local 2.2.3
```

(Version 2.2.3 is the latest version of Ruby as of this writing. You may have to update your local version, or you may be using a newer version.)

This creates a hidden file in your repository called *.ruby-version*. If we look inside that file, we'll see that it is as simple as can be:


![Our project so far](/images/ruby-version.png)

## Create the Rails project

Normally, we'd create our rails project with this command:

```
rails new my-rails-project
```

But as we're already in the project folder, we can simply use the current folder. Rails will use the folder's name for the name of the project, so ensure that you've named the folder correctly. It's a pain to go back and change this!

We're also going to add some arguments to our call to `rails new` to give it a few special instructions. To see what these do (and to see other options), you can try `rails -h new`.

```
rails new . -BGTO --skip-turbolinks
```

This will create a set of files and folders. That's our new Rails app. That was easy!

![New Rails app folders](/images/new-rails-app.png)

Don't let the number of files and folders freak you out. Most of these we'll never bother with, and those you will use you'll use over and over and over again until they become second nature. For now, just follow along.

![New Rails app in Atom](/images/new-rails-in-atom.png)

## Installing the dependencies

A big part of building a Rails application is managing your applications dependencies. These dependencies take the form of ruby "gems"&mdash;small, reusable code libraries that will save us the time and effort of building everything from scratch.

Rails itself is a ruby gem.

We're going to use a gem called [bundler]() to manage our other gems. Bundler uses a Gemfile to list the gems and versions we'll need. A second "Gemfile.lock" file locks in certain versions so we can make sure that all our developers are using the same versions. Don't mess with the Gemfile.lock file.

Let's clean up the Gemfile. You may wish to leave the comments in for later reference. I prefer to keep the file as clean as possible, so I'll delete them.

We'll need Rails, of course. We may use Sass later (for CSS), so let's keep that. And Uglifier is used to compress our JavaScript and CSS files so that they'll load faster on the client. We definitely want that.

But we won't be using CoffeeScript in this course, so we'll delete that gem. The rest should be OK. My Gemfile now looks like this:

![Gemfile](/images/gemfile.png)

Now we need to install them, so we'll run the bundler command in the project's root folder (via the terminal, of course):

```
bundle
```

Note: it's `bundle`, not `bundler`. The former is the command, the latter is the name of the gem. You can also use `bundle install`.

This will take a while. The **nokogiri** gem in particular takes some time as it needs to compile some C code. Take a break.

## Building the application

1. [First slice: Home page](/week02/slice01/README.md)
2. [Second slice: About us](/week02/slice02/README.md)
3. [Third slice: Persistent links](/week02/slice03/README.md)
4. [Fourth slice: Logo link](/week02/slice04/README.md)
5. [Fifth slice: Interactivity](/week02/slice05/README.md)
6. [Sixth slice: Persistence and validation](/week02/slice06/README.md)
7. [Seventh slice: CRUD](/week02/slice07/README.md)
