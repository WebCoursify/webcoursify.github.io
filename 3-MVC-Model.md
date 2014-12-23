---
layout: page
permalink: /mvc-model/
title: MVC-model
tagline: 
tags: 
modified: 12-23-2014
comments: true
---
In this chapter we'll dive deeper into a web system's core, *Model*. In Django, as well as many other web frameworks, *Model* can be seen as a blackbox, whick encapsulates the implementation details of data storage and retrieval and provides methods for upper layers to manipulate data. Sometimes it might also be the layer which encapsulates complex core logics, such as one method for "transfer money from A to B and charge A for the fee" or one method for "send an email to all registered users", etc, while at other times these complex logic might be encapsulated in another *Service* layer and the *Model* layer will focus on data storage and retrieval. 

Most web systems use database as backend support for Model. We're using MySQL for this tutorial, as you configured before. The following content assumes you have a basic understanding of relational database. If you feel confused about concepts such as: *Table*, *Schema*, *Column*, *Row*, *ForeignKey*, *PrimaryKey*, *Index*, you can go to:

*   [Basic concept](http://www.tutorialspoint.com/sql/sql-rdbms-concepts.htm)
*   [Oracle tutorial](http://docs.oracle.com/javase/tutorial/jdbc/overview/database.html)

...and look for the meaning of the terms I mentioned. In these tutorials you may also learn about *SQL*, a language used for data manipulation in database (which is not essential if you're just using Django because the encapsulation of Django hide this part from developers, but is necessary for ones who want to become real serious web developers---and we'll have assignments for that as well), if you're not familiar with it yet.

### 1. Django's Model

Here we'll learn how to **define** and **use** Model in Django. 

#### Model definition

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

#### Model usage

There're basically 4 different types of operations:

1.  Insert
    
        new_user = User.objects.create(username='...', email='...', password='...')

    or 

        new_user = User(username='...', email='...', password='...')
        new_user.save()

2.  Query(Search)

        # Find a list of users
        users = User.objects.filter(role=1, deleted=0) 

        # Find one unique row. When there's no matching row or more
        # than one matching rows, an exception will be raise
        user  = User.objects.get(username='...', deleted=0) 

    And we can read the information like:

        username = user.username
        author   = article.author # Note that Article.author is a ForeignKey to the User
                                  # So article.author will return the User object
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

#### More complex things: Relations

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

#### What else can be done besides defining tables?

Previously in the design phase of Model layer, we only see how to define tables. This is actually sufficient under many simple cases, since Django has provided us with nice and neat, easy-to-use interfaces to manipulate the data. On the other hand, when things get a little more complicated, we might want to turn some frequently used operations into a method provided by this layer. Here we'll show some examples, and with the same method we can also encapsulate complicated logic inside the Model layer to make the architecture neater. 

For instance, in many different places you need to retrieve all followers of a user. You should put the code for this inside a function and call the function no matter where you need it. Here's how you can put this in the *Model* layer in a neat and simple way:

In <code>models.py</code>

    class UserManager(models.Manager):
        def get_followers(self, user_id):
            """
            :param: user_id, int 
            :return: Users who are following the user with the given id
            """
            # Search in the db
            self.filter(...) # self.filter(...) is equivalent to User.objects.filter(...)

    class User(models.Model):
        objects  = UserManager()
        username = models.CharField(max_length=50)
        ...

Here's how to use it:

    followers = User.objects.get_followers(log_on_user_id)

Another example:

    created_articles = Article.objects.filter(author_id=log_on_user_id, deleted=0)
    published_articles = created_articles.filter(state=Article.STATE_PUBLISHED)

can be written as:

    created_articles = Article.objects.get_articles_by_author_id(log_on_user_id)
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




### 2. Discussions

We present several discussions here.

#### Index

Index is some kind of additional data structure used for faster data retrieval. The most widely used index type is BTree, or some variants of it. To say it in a simple way, BTree puts nodes with smaller value on the left and nodes with larger value on the right, so it puts all rows in order with respect to specific field(s). For example, an index with respect to the <code>id</code> field will put all rows in the ascending order of id, while an index with respect the <code>age</code> will put all rows in the ascending order or age, which is different from the order with respect to id. 

All primary keys and foreign keys are accompanied with index. We can define our own index on one or more than one fields. The advantage here is we can search faster. If we want users with age=18 we can do this by searching a very small part of the index we have on the age field. If we do not, we'll need to scan the whole table. However, the disadvantage is we need to pay the maintainence cost for this structure when we need to insert/update rows. 

#### You see that <code>deleted</code> field?

When I first see such a field I was confused, as you might be now. What's it for? Sounds like a field used to mark whether a row is deleted or not. If so, why do we need this field? Why not just delete the row, but border to keep the row and mark it as deleted? 

There're some discussions around this and some people might say this is judgement call. Here's Pros and Cons I could think of or find online:

Pros:

*   Doing so makes it possible to restore the data
*   It's usually faster to update a field than actually deleting a row
*   ...

Cons:

*   It's easier to introduce bugs in the program. People might just forget to add 'deleted=0' condition in the SQL, for exmaple
*   The table will be larger, so data retrieval may be slower
*   ...

It might indeed be a judgement call and I just put it here for demonstration. You can choose whether or not use this strategy when you're desgining new tables in later courses (but please keep the original tables in this way, because our test cases in based on this).

### 3. Assignment

For this chapter, you'll need to implement new functionalities for the platform using what you learn in this chapter as well as the last Chapter(*Controller*).

1.  *Searching!* Implement <code>app.controllers.api.get_articles</code> as specified by the docstring. You can look for query operators supported by Django on the [official site](https://docs.djangoproject.com/en/1.7/topics/db/queries/).

    Notes:
    
    *   Use case-insensitive match
    *   When the query parameter is invalid, you should return 400
    *   Use <code>make 3_model_search</code> to test *(Score: 20)*


2.  *Everything about Article*. Implement three controllers for:

    *   Update article. Url pattern is <code>/api/article/{article_id}/update</code>. Controller is defined in <code>app.controllers.api.update_article</code>

    *   Change article state. Url pattern is <code>/api/article/{article_id}/set_state</code>, which you need to add to <code>urls.py</code>.

        **Specification**: It looks for **"state"** in the data payload, which can be either "published" or "draft". "published" means setting the article's state to published and can be viewed by everyone, while "draft" means setting the article to be a draft and can only be seen by the author.

    *   Delete article. Url pattern is <code>api/article/{article_id}/delete</code>, which you need to add to <code>urls.py</code>. What the controller does is simply remove this article. (*Hint* Remeber what we discuss above?)

    Notes: For all these three controllers:

    *   Only allow POST method
    *   If the user is not log on, return 401. If the log on user is not the author, return 403
    *   If the article is not found. Return 404.

    Use <code>3_model_article</code> for testing. *(Score: 25)*

    BY THE WAY, have you thought about using the same controller for the first two? 

3.  *Following Relation*. Add four new controllers to:

    *   Follow a user. Url pattern is <code>/api/user/follow</code>. Only support POST. The target user id is given as "target_user_id" in the posted data. The controller will make the **log on user** a follower of the **target_user**. It returns <code>{'success': True}</code> or <code>{'error': error_message}</code>

    *   Unfollow a user. Url pattern is <code>/api/user/unfollow</code>. Requirement is similar to the one above, just doing the opposite thing

    *   Get the follower list of a user. Url pattern is <code>/api/user/followers</code>. Only support GET. If "user_id" is provided in the query parameter, return the followers of this user; if not provided and the requesting client is logged on, return the followers of the log on user; otherwise response with 401 (return a HttpResponse with status code set to 401).
        
        The response data should be a json array, with each element be <code>{'id': the user id, 'username': ...}</code>

    *   Get the list of users a user is following. Url pattern is <code>/api/user/followings</code>. Only support GET. If "user_id" is provided in the query parameter, return the followers of this user; if not provided and the requesting client is logged on, return the following list of the log on user; otherwise response with 401.
        
        The response data should be a json array, with each element be <code>{'id': the user id, 'username': ...}</code>

    Notes:

    *   Follow a followed user, or unfollow a user currently not following should not yield any error
    *   Can not follow or unfollow oneself. When this happend, return 400
    *   To get this done, you'll need to modify the table schemas (the definition of classes in <code>models.py</code>), either update the existed class(es) or add new class(es). Recall how to represent such a *User-follow-User* relationship in database. And check for Django documentation about how to manipulate such a relation.
    *   The order of returned users doesn't matter

    Use <code>3_model_follow</code> for testing. *(Score: 25)*

**Some additional hints** 

*   To see if the request comes from a log on client, simply use <code>if 'user' in request.session</code>. If <code>True</code>, one can get the logged on user's id by <code>request.session.get('user')['id']</code>
*   You can use <code>make 3_model</code> to test all questions here
