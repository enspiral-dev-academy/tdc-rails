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

```
2.2.3
```

[Our project so far](/images/ruby-version.png)
