# Authentication

We'll be doing authentication from scratch. Later, you may wish to use a gem for this. Most people use `devise`. I'm not a fan.

My belief on gems is that you should use them sparingly. Every bit of code created and maintained by someone else is a dependency. The more dependent you are on other people to make their code secure, bug-free, and to keep it up to date, the more suffering will enter into your life.

Save the gems for those things you can't or don't want to build yourself. For example, you don't want to build Rails from scratch, so using the Rails gems is a good idea. They are widely available, scrupulously maintained, and they aren't going anywhere.

Another gem you might use is the `mongoid` gem we're using to connect to the Mongo database. This is definitely not something you'd want to build for yourself.

`devise` might be a good gem if it stuck to back end features and implemented (in a modular fashion) OAuth login to other sites. That's the way the `passport` node module works. But instead `devise` tries to be all things to all people. It crosses boundaries. For example, it handles the back end, but it also provides built in templates for your log and sign up forms. Why? How likely are those to be useful out of the box?

Instead, most developers either end up with a cookie-cutter look and feel, or they end up overriding most or all of the `devise` functionality.

Authentication is *easy to implment*, so what is the benefit? I really don't see one.

But a lot of Rails developers **love** the magic. It has become a joke. "There's a gem for that!" There's a gem for *everything*. But this is the difference between *programmers* (or web developers) and *application experts*. Being able to load and configure a lot of gems is like being able to use all the features of Word. *Using* a word processor is not the same a *programming* one.

Furthermore, the more time you spend memorizing magic Rails code&mdash;the *domain-specific language* (DSL) of Rails&mdash;the more tied you become to the Rails framework and the more likely you are to see it as the solution to *everything*. Instead of using the right tool for the job&mdash;screwdriver, wrench, chisel, hammer&mdash;everything starts to look a bit like a nail.

Not only is this deleterious for the individual "developer", but it creates a massive inertia in the industry that makes it difficult for us to move forward. The more each individual developer has *invested* in using a particular, specific framework, the less willing he or she will be to learn and adopt new tools. This is a drag, literally, on the entire industry, developers and users alike.

So don't use gems unless it makes sense to. Prefer to write code yourself whenever possible. Avoid using helpers and other features of the language if they don't provide obvious and demonstrable benefits. Avoid locking yourself in.

- [Pass 1](/week02/slice08/pass01/README.png): Log in form
- [Pass 2](/week02/slice08/pass02/README.png): User model
- [Pass 3](/week02/slice08/pass03/README.png): Logging in
- [Pass 4](/week02/slice08/pass04/README.png): Completing the process
- [Pass 5](/week02/slice08/pass05/README.png): Adding encryption
- [Pass 6](/week02/slice08/pass06/README.png): Using has_secure_password
