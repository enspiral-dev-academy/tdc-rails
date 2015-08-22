# Using rbenv

As with most languages and software, new versions of Ruby are released regularly. This creates a problem for us as software developers.

If we build an application with one version of Ruby, updating that application to newer versions may prove difficult. Or we may be too busy. Or a client may not wish to invest more time and money into an application. For whatever reason, we will probably have applications running on our development machine for which we do not want to upgrade Ruby.

At the same time, we'll want to be continually upgrading our own skills and knowledge, and for current and future projects we'll want to use the most current stable version of Ruby.

For learning purposes, we may even wish to experiment with unstable (future) versions.

For all these reasons, we need the ability to control our Ruby environment and to run multiple versions of Ruby simultaneously.

[rbenv](https://github.com/sstephenson/rbenv)&mdash;the ruby environment&mdash;to the rescue.

rbenv should already be installed on your machines. If not, you can follow the instructions on the [rbenv](https://github.com/sstephenson/rbenv) page to install it.

The one tricky thing about rbenv is that it depends on the [ruby-build](https://github.com/sstephenson/ruby-build) plugin. You can use rbenv without ruby-build, but ruby-build makes it much easier. That said, you have to keep ruby-build up-to-date if you want to install the latest versions of Ruby.

 
