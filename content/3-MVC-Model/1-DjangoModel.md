---
layout: page
title: Using Model in Django
tagline: 
tags: 
modified: 12-23-2014
comments: true
---

Here we'll learn how to **define** and **use** Model in Django. 

#### 1. Model definition

Let's see the following example:

    class User(models.Model):
        username = models.CharField(max_length=50)
        email    = models.CharField(max_length=50, unique=True)
        password = models.CharField(max_length=50)
        description = models.CharField(max_length=512, null=True)
        role     = models.SmallIntegerField()
        deleted  = models.BooleanField(default=0)

    class Article(models.Model):
        author   = models.ForeignKey('User')
        title    = models.CharField(max_length=100)
        content  = models.TextField()
        time_create = models.DateTimeField()
        time_update = models.DateTimeField(auto_now=True)
        state    = models.SmallIntegerField()
        deleted  = models.BooleanField(default=0)

Each class corresponds to a table in the database. <code>username</code>, <code>email</code> and other fields are columns in the table. Django provides many built-in fields such as integer, string(CharField or Text), datetime, etc, and they're sufficient for developers' need at most of the time. 

That's basically how to define the models, now let's see how to use them. 

#### 2. Model usage

There're basically 4 different types of operations:

1.  Insert
    
        new_user = User.objects.create(username='...', 
                                       email='...', 
                                       password='...')

    or 

        new_user = User(username='...', 
                        email='...', 
                        password='...')
        new_user.save()

2.  Query(Search)

        # Find a list of users
        users = User.objects.filter(role=1, deleted=0) 

        # Find one unique row. When there's no matching row or more
        # than one matching rows, an exception will be raise
        user  = User.objects.get(username='...', deleted=0) 

    And we can read the information like:

        username = user.username
        # Note that Article.author is a ForeignKey to the User
        # So article.author will return the User object
        author   = article.author 
        author_name = author.username

3.  Update
        
        User.objects.filter(username='...').update(password='...')

    or

        user = User.objects.get(username='...')
        user.password = '...'
        user.save()

4.  Delete

        User.objects.filter(username='...').delete()

    or

        user = User.objects.get(username='...')
        user.delete()

#### 3. More complex things: Relations

Between *entities* there're three different relations:

1.  *One-to-One*: For example, if EmailAccount is a new entity, then a User will have one and only one EmailAccount, while an EmailAccount will be owned by one and only one User. 
2.  *One-to-Many*: *Author-to-Article* is an example. An author can have no article, one article, or multiple articles; but an article has one and only one author (at least in our design, which doesn't consider co-author situation)
3.  *Many-to-Many*: *User-follow-User*. A user can following no user, one user, or many users; on the other hand, a user can have no follower, one follower or many followers.

They can be represented in the database as:

1.  *One-to-One*: A **unique, not null foreign key** from A to B or from B to A
2.  *One-to-Many*: A **non-unique, not null foreign key** from B to A
3.  *Many-to-Many*: Define another *relationship* table. Each row in this table has at least two columns, with one be a **non-unique, not null foreign key** to A and the other be a **non-unique, not null foreign key** to B.

In Django, there're built-in fields to represent these relations([reference](https://docs.djangoproject.com/en/1.7/topics/db/examples/)):

1.  *One-to-One*: Use <code>OneToOneField</code>
2.  *One-to-Many*: Use <code>ForeignKey</code>
3.  *Many-to-Many*: Use <code>ManyToManyField</code>

#### 4. What else can be done besides defining tables?

Previously in the design phase of Model layer, we only see how to define tables. This is actually sufficient under many simple cases, since Django has provided us with nice and neat, easy-to-use interfaces to manipulate the data. On the other hand, when things get a little more complicated, we might want to turn some frequently used operations into a method provided by this layer. Here we'll show some examples, and with the same method we can also encapsulate complicated logic inside the Model layer to make the architecture neater. 

For instance, in many different places you need to retrieve all followers of a user. You should put the code for this inside a function and call the function no matter where you need it. Here's how you can put this in the *Model* layer in a neat and simple way:

In <code>models.py</code>

    class UserManager(models.Manager):
        def get_followers(self, user_id):
            """
            :param: user_id, int 
            :return: Users who are following 
                     the user with the given id
            """
            # Search in the db
            # self.filter(...) is equivalent to 
            # User.objects.filter(...)
            self.filter(...) 

    class User(models.Model):
        objects  = UserManager()
        username = models.CharField(max_length=50)
        ...

Here's how to use it:

    followers = User.objects.get_followers(log_on_user_id)

Another example:

    created_articles = Article.objects \
                       .filter(author_id=log_on_user_id, deleted=0)

    published_articles = created_articles \
                         .filter(state=Article.STATE_PUBLISHED)

can be written as:

    created_articles = Article.objects \
                       .get_articles_by_author_id(log_on_user_id)
    published_articles = created_articles.published()

You already know how to make the first line works. Making the second line works is also simple --- all you need to do is to extend the QuerySet class:

    class ArticleQuerySet(models.query.QuerySet):
        def published(self):
            return self.filter(state=Article.STATE_PUBLISHED)

    class ArticleManager(models.Manager):
        def get_queryset(self):
            return ArticleQuerySet(self.model)

        def get_articles_by_author_id(self, author_id):
            ...

    class Article(models.Model):
        objects = ArticleManager()
        ...

