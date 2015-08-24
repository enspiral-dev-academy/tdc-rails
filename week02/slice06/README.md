# Persisting data and polishing the Contact form

Now we're going to take a look at persisting data. That means that instead of throwing away the Inquiries immediately after sending the emails, we're going to store them in a database.

We could store them in flat files somewhere on the server. But databases have evolved to make storing data much faster, easier, more robust, and more scalable. Databases can be split across multiple drives or even multiple machines. The data in them can be strongly typed, ensuring that dates are dates and strings are strings and not vice versa.

The most commonly used databases remain the Relational Database Management Systems (RDBMSs). They've been in development since the 1970s and are now extremely rugged, reliable, and powerful.

But over the past few years there has been an explosion of non-RDBMS databases, often called NoSQL databases for either "no SQL" or "not only SQL", depending on who you talk to.

Initially, we're going to use the MongoDB document database. It stores data in a binary form of JSON called BSON. It is schemaless, which means we can vary the names and types of keys in a document at will. Documents are stored in collections. So we'll create a MongoDB with a collection of Inquiry documents (called "inquiries"). And we'll use a ODM&mdash;a object-document mapper&mdash;called **mongoid** to do so.

1. [Pass 1](/week02/slice06/pass01/README.md): Persistence with Mongoid
2. [Pass 2](/week02/slice06/pass02/README.md): Validation
3. [Pass 3](/week02/slice06/pass03/README.md): More validation
4. [Pass 4](/week02/slice06/pass04/README.md): Feature test?
