---
layout: page
permalink: /django-basic/
title: Django Basic
tagline: 
tags: 
modified: 12-23-2014
comments: true
---
In this chapter, we will show what a typical web system project looks like, using our demo project(which is written with [Django](https://www.djangoproject.com/)) as an example. Although using Django for illustration, some concepts here are widely adopted by many different web frameworks instead of only being used in Django. 

### 1. Client/Server Model

Allow me to use an old-fashion image here:

![Image](/resource/clientserver.png)

Basically all web systems follow this model. The web system, as a server, responds to all requests sent by different clients(browers, crawler, etc), using standard protocols. Clients' requests are considered independent of each other. This means when the server is constructing the response to a request sent from one client, it doesn't need to consider the requests from other clients or other requests from the same client, but only the information contained in this request, and probably the information stored in the database or file system on the server side.

### 2. Model-View-Controller Architecture Pattern

This model is widely used in developing softwares that have a GUI, not just in web system. It can be applied to a system as well as a component inside a system. In a word it's a very useful pattern.

A short introduction here is:

1.  **Model**: This component captures the core problem of the system, such as data structure and data storage, core logic code, etc. It's usually the underlying infrastructure of the whole system and it usually doesn't actively interact with the other two components.
2.  **View**: This component focuses on display information to users(for example, display a list or display a visualization chart) and receive users' interaction (such as button click or keyboard input)
3.  **Controller**: This is the glue between Model and View. The functions in this component are usually triggered by an event (the user clicks a button, the state of the Model has changed, etc), and take the corresponding actions (search items in database and return the result to View, or send a message to View to notify user that some states of the Model have changed, etc)

### 3. In Practise: Django's MVC

You're expected to run the demo project successful before we actually start to go through Django framework.

#### 3.1 Configuration of Demo Project

1.  First, ensure your machine have installed **Python** and **Pip**. You can check by simply typing "python" and "pip" in the command line. If not, go to the [Python.org](https://www.python.org/) and [Pip Installation](https://pip.pypa.io/en/latest/installing.html)
2.  Install a MySQL server on your machine (You may use "apt-get" for Ubuntu, homebrew for Mac. Go to google and you'll find everything). Note down your username and password. Also install [MySQL-python](https://pypi.python.org/pypi/MySQL-python/). You can just type "pip install MySQL-python"
3.  Uncompress the code archive provided.
4.  Enter the code directory(<code>src/</code>). Type "make install". This will install all the other library dependencies for you. 
5.  Now we set up the database. First, check that under <code>data/</code> there's a file <code>parse.py</code> and a directory named "rawdata", which contains a lot of text files (the *Reuters-21578* dataset). 

    Now, change directory into <code>test/</code> directory, open the file <code>config.py</code> in text editor, you'll see something like:

        # If you're using locally installed mysql, you don't need to change the host and port
        MYSQL_HOST     = '127.0.0.1'
        MYSQL_PORT     = 3306 
        # Change the user name and password to your owns
        MYSQL_USERNAME = 'root' 
        MYSQL_PASSWORD = 'root' 
        # These should not be changed
        MYSQL_DB_NAME  = 'web_dev_tutorial'

    Under most circumstances, you'll only need to change the **MYSQL_USERNAME** and **MYSQL_PASSWORD** to the username and password of your local mysql installation. You'll also need to create a database called "web_dev_tutorial" in your local mysql server first. To do this, simply typing:

        mysql -u <your username> -p<your password>

    in the command line should make you enter the mysql client. Then type:

        create database web_dev_tutorial;

    will create a new database instance.

    After all of these, type <code>make load</code> under the directory of <code>test/</code>. This will automatically parse all the text files and save the result, then load the result into the database.

6.  Next step, we link the project with our database. Use text editor to open <code>src/web_dev_tutorial/settings.py</code>, find the following configurations:
    
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': "web_dev_tutorial",
                'USER': 'root',
                'PASSWORD': 'root',
            }
        }

    Change the user name and password to yours. 

7.  Under <code>src</code>, type "make". Now it should shows something like:
    
        December 15, 2014 - 15:49:57
        Django version 1.7.1, using settings 'web_dev_tutorial.settings'
        Starting development server at http://127.0.0.1:8000/
        Quit the server with CONTROL-C

    If so then the server is running. You can verify this by visiting [http://localhost:8000](http://localhost:8000). You should see something like:

    ![Index](/resource/index.png)

    This means our blog system is officially up and ready for the next step! 

#### 3.2 Some Notes

The demo project is a some kind of blogging platform. For a simplified design it should have the following features:

1.  Our users, who are bloggers, can create, edit, delete drafts and articles
3.  All published articles can be seen by all users. Users can search for articles they're interested in
3.  Users can comment on or "like" any article
2.  Users can follow other users, so they can receive feeds from them

The demo project is a good start. We expect you to finish it by implementing features above. We'll divide these work into assignments for each chapter and the final project. 

#### 3.3 Show Me the Code

After you extract the demo project, you can see the basic structure of it. It should be more or less like this:

<img src="/resource/django_structure.png" style="width:300px"/>

There're several key components here:

*   **The directory with same name as the project(in this case "web_dev_tutorial")**
    
    This directory can be seen as the entry and configuration of the whole system(website). 

    *   **settings.py**: this file configures basic settings such as Database connection, Log configuration, Template and static files path, Installed Applications, Used middlewares, Language, etc.
    *   **urls.py**: this file defines the *Routing* of the whole website. Routing can be seen as a map from any url to a given resource on the system (a function, a file, a webpage, or 404 not found)
    *   **wsgi.py**: A script file used to run the server. The content of this file is basically fixed so we can ignore it for now.

*   **The directory named "app"**

    This is an "Application". Django framework supports multiple applications existing within the same system, allowing them to support different functionalities of the system. For example, the "admin" system built-in in Django is another Application. Developers can install it to the current system by very simple configurations, and it won't affect the other applications of the system, because they're using different urls(all functions provided by Django admin system use urls with '/admin' prefix, so as long as one doesn't use this prefix in other applications, it should be fine). 

    Here we only have one application, because we don't need more. Now let's take a look the typical components in an Application:

    *   **models.py**: This file defines the *Models*. You can consider them as tables in the database, or entities you're concern about in the application. An example of Model looks like this:

            class User(models.Model):
                ROLE_ADMIN  = 1
                ROLE_AUTHOR = 2

                username = models.CharField(max_length=50)
                email    = models.CharField(max_length=50, unique=True)
                password = models.CharField(max_length=50)
                description = models.CharField(max_length=512, null=True)
                role     = models.SmallIntegerField()
                deleted  = models.BooleanField(default=0)

    *   **controllers.py**: As we said before, a controller is something that responds to actions. In Django, a controller is a function, which takes the client's request as parameter and return a response object. The *URL Dispatching* in Django decides which function to use when the client request resources from a specific url

    *   **urls.py**: An application can define its own urls routing. If all urls this application uses have the same prefix, it's a good idea to define the urls inside the application(without the prefix) and include them in the urls.py outside(with prefix defined). One example is: if a url is defined as "article" and is mapped to a function inside the application, and the most outer urls.py include it with "api" prefix, then the real url for that function is "api/article". But this is really optional, so we don't have it here

*   **static**:

    This directory contains all static resources, such as images, css files, javascript files, etc. 

*   **templates**:
    
    This directory contains html templates. They're essentially html pages, but come with a syntax that allow developers bind data to them dynamically. In the chapter *MVC-View* we'll cover this in more details.

*   **manage.py**:

    This script provides commands such as start the server, synchronize database to us. Sample usages are:

        python manage.py runserver # Run the server on localhost:8000
        python manage.py syncdb    # Synchronize database, create tables for all models in all applications


### 4. Assignment: Automatic tests

In previous configuration we have get the system running. In this assignment you're asked to run automatic tests against the running server. By doing so you'll be familiar with the process, which is essential for the rest of the course because many assignments later will be graded on the result on running auto-testing scripts. 

All test scripts can be found on the <code>test/</code> directory. First make sure your server is running on [http://localhost:8000](http://localhost:8000). Then enter this directory. If it's the first time, type 
    
    make install 

to install dependencies. Then type
    
    make 1_basic

will trigger the test script for this assignment. If everything is fine, it should show something like:

    python test_basic.py
    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.617s

    OK

If you see this, then you should be able to go to the next chapter and start learning some serious stuff.

>   I can give the details about the implementation of this test script. Basically, it reads the index page content by sending a request to [http://localhost:8000](http://localhost:8000). By our design, when the request contains no query parameter, this page will display the newest 10 articles (order by post time in descending order). In html the script searches for all html elements with <code>class="article-list-item-main"</code> and read the title of these articles by reading the attribute <code>data-article-title</code> of these elements. They will be compared with the groundtruths. 

### Summary

By finishing this chapter, you should:

1.  Have a basic understanding of MVC architecture, and Django project structure.
2.  Be familiar with how to start the server, how to run automatic tests. 
3.  Have in mind about what the current platform looks like, and what it will look like when it's completed. 


### Appendix:

1.  It would be a good idea to use IDE help you develop this project. The one I'm using is [PyCharm](), which looks like:

    ![PyCharm](/resource/pycharm.png)

2.  You should use some version control tools to help manage the code better. I recommend [git](http://git-scm.com/). There're many tutorials on that. Google online, learn the basic usage, then learn the advance features during your usage when needed.


