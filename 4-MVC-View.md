---
layout: page
permalink: /mvc-view/
title: MVC-View
tagline: 
tags: 
modified: 12-29-2014
comments: true
---

In this chapter, we'll look at the *View* layer. This layer handles user interface as well as user interaction. 

###  <a id="htmlcssjs"></a> 1. HTML, CSS, Javascript

HTML, CSS, Javascript are languages used to construct the three core components in modern web pages. 

*   HTML is the "language" used to describe the structure and content of a web page. 
*   *CSS* is a style sheet language used for describing the look and formatting of a document written in a markup language, such as HTML. 
*   *Javascript*, when used on *View*, is used to program the dynamic behaviors of web pages, such as "what happen when the user click this button"; or render contents beyond the representation capability of HTML and CSS, such as 2D & 3D animations. 

There're many fantastic tutorials where you can learn the basic syntax and usage of these three "hammers". 

*Note: when learning javascript, I suggest you first understand the syntax and basic usage; then when you go to the part about how to use javascript to manipulate DOM(html elements in web page), you can also learn how to use [jQuery](http://jquery.com/), which provides a much nicer interface for selecting and do operations on DOM*

*   W3Schools: [HTML](http://www.w3schools.com/html/), [CSS](http://www.w3schools.com/css/default.asp). Great tutorials to help you understand the basic. The [jQuery tutorial](http://www.w3schools.com/jquery/) is also very helpful
*   [Here](http://htmldog.com/guides/html/beginner/) is another tutorial for HTML, with more detailed text explanations
*   [Codecademy](http://www.codecademy.com/en/tracks/web) is also a good resource. It provides really good programming interface for practising

You probably don't want to go through all of the tutorials above, because you don't need to. Neither do you need to completely go through any one of them. Different people have different ways of learning things. The way I see it, you just need to know the basics to move forward, and you'll have a deeper understanding of these basic things and learn more complex things during the rest of this chapter as well as the finishing the assignments. Of course, my approach might not fit you, you have your way of learning. But if you haven't tried this way, you might want to give it a shot and see if it's suitable for you.

### <a id="template"></a> 2. Template in Django

#### 2.1 Data Binding
Now we assume you have seen HTML, CSS, and Javascript. The first property provided by Django is *Data-binding*: dynamically generate web page contents based on the data passed from *controller*. You might notice that in most controllers defined in <code>app.controllers.webpages.py</code> return like:

    return render_to_response('./create_article.html', locals())

That's the shortcut to bind the data (given by <code>locals()</code>, which returns a key-value dict of all local variables) to the template file <code>create_article.html</code>, and return the rendered html content as response. 

Let's give a more detailed example. Suppose we have a controller:

    characters = [{'id': 1, 'name': 'Tyrone', 'dead': True}, {'id': 2, 'name': 'Geoffrey', 'dead': True}, {'id': 3, 'name': 'Cerci'}]
    return render_to_response('./characters.html', {'characters': characters})

and a html template <code>characters.html</code>:

    <div class="container">
        {% raw %}
        {% for character in characters %}
            <div>
                Character #{{character.id}}: {{character.name}}. 
                {% if character.dead %}
                Now dead. 
                {% else %}
                Now alive.
                {% endif %}
            </div>
        {% endfor %}
        {% endraw %}
    </div>

will produce:

    <div class="container">
        <div>
            Character #1: Tyrone. Now dead. 
        </div>
        <div>
            Character #2: Geoffrey. Now dead. 
        </div>
        <div>
            Character #3: Cerci. Now alive. 
        </div>
    </div>

More details could be found on [here](https://docs.djangoproject.com/en/1.7/topics/templates/).

#### 2.2 Template as *Component*

As we know, a typical structure of a web page is more or less like

    <!Doctype html>
    <html>
        <head>
            <!-- Here defines the link recourses, styles or pre-loading scripts -->
            <link rel="stylesheet" type="text/css" href="...">
            <script type="text/javascript" src="..."></script>
            <style type="text/css">...</style>
        </head>
        <body>
            <!-- The things inside body are those to be seen -->

            <!-- Maybe there's a navigational bar here -->
            <header>
                ...
            </header>

            <div class="container">
                <!-- Here is the main content -->
                ...
            </div>

            <!-- And perhaps footer -->
            <footer>...</footer>

            <!-- Extra scripts. Loading scripts at last can accelerate the rendering of the page content -->
            <script type="text/javascript" src="..."></script>
            <script type="text/javascript">...</script>

        </body>
    </html>

Now, suppose all webpages in a website have the above structure. They have exactly the same head, header, footer, extra scripts. The only difference between them is the main content. When implementing these pages, we could copy-and-paste the head, header, footer stuffs into every page and modify the main content part. However in this case if we want to, for example, change the text of the header, we need to go to every single page and make the same modification. That's a bad thing.

Django provides a brilliant solution: **extend**. Here's how it works. First, we define a *base_template.html*:

    <!Doctype html>
    <html>
        <head>
            <link rel="stylesheet" type="text/css" href="...">
            <script type="text/javascript" src="..."></script>
            <style type="text/css">...</style>
        </head>
        <body>
            <header>
                ...
            </header>

            <div class="container">
                {% raw %}
                <!-- See how this works? -->
                {% block content %}
                {% endblock %}
                {% endraw %}
            </div>

            <footer>...</footer>
            <script type="text/javascript" src="..."></script>
            <script type="text/javascript">...</script>

        </body>
    </html>

Now, for every page we want to implement, we can just:

    {% raw %}
    {% extends 'base_template.html' %}

    {% block content %}
        <!-- Put the main content here -->
        <title>...</title>
        <p>
            ....
        </p>
    {% endblock %}
    {% endraw %}

Similarly, if we want to add extra style sheet files in the head or add extra scripts at the end, we can change to base_template.html to support more blocks:

    <!Doctype html>
    <html>
        <head>
            ...
            {% raw %}
            {% block extra_header %}
            {% endblock %}
            {% endraw %}
        </head>
        <body>
            <header>
                ...
            </header>
            <div class="container">
                {% raw %}
                {% block content %}
                {% endblock %}
                {% endraw %}
            </div>
            <footer>...</footer>
            <script type="text/javascript" src="..."></script>
            <script type="text/javascript">...</script>
            {% raw %}
            {% block extra_script %}
            {% endblock %}
            {% endraw %}
        </body>
    </html>

And for pages that need to use these blocks:

    {% raw %}
    {% extends 'base_template.html' %}

    {% block content %}
        ...
    {% endblock %}

    {% block extra_head %}
        ...
    {% endblock %}

    {% block extra_script %}
        ...
    {% endblock %}
    {% endraw %}

You don't extend every block defined in the parent-template. If a block is not extended in a child-template, it will be just blank, no content, empty string.

Django also support **include**. Suppose you have a sidebar you want to show in some pages, but not all, you can put it in a separate file, called 'sidebar.html', for example:

    <div>
        <!-- I'm a sidebar -->
        <form> 
            <!-- Maybe I Have a search input -->
            <input type="text" placeholder="search">
        </form>
        <!-- Maybe I have the log on user's avatar image -->
        <img src="...">
        <!-- So on and so forth -->
        ...
    </div>

Now for every place you want to put this sidebar (insert the content of this html file), you can just:

    <div>
        <!-- Main content -->
        ...
    </div>
    {% raw %}
    <!-- There is a sidebar here -->
    {% include 'sidebar.html' %}
    {% endraw %}

With these features provided by Django, we can easily *modularize* our views.

### <a id="formajax"></a> 3. Form & Ajax

There're three main ways to send requests to server:

1.  Directly visit the url. Could be achieved by using the hyperlink tag <code> &lt;a&gt; </code>. Can only support GET method, and will cause a page jump
2.  Submit a *form*. This could be seen as more powerful url visit because it supports other HTTP methods, submitting file stream, etc. This is also a page jump
3.  Use AJAX (*Asynchronous JavaScript + XML*). This is usually achieved with Javascript. And there's no page jump in this case

You're probably familiar with the first way. Here we'll talk about the second the third one.

#### 3.1 Form

A *form* in HTML is literally, a form. Typically it's where you fill information. Then when you submit it, the browser will put the information you fill in the form in a http request, send to server, wait for server's response, and replace the current page content with the content in the response. 

There're many types of *input* for forms, such as text input, checkbox, radio checkbox, select menu, file, etc. You can refer to the [w3school tutorial](http://www.w3schools.com/html/) for a complete list. When submitted, all information from input elements within the form will be contained in the http request. Different information/fields are identified by the 'name' attribute. This form

    <form method="POST" action="....">
        <input type="text" name="username" value="...">
        <input type="password" name="password" value="...">
        <input type="submit" value="Login">
    </form>

contains two fields, username and password. The name should be unique, except for the case of radio checkbox. All radio checkboxes with the same "name" will be consider as a group, and users are only allowed to check one box in this group. 

>   Note: If you want to submit a file through the form, remember to set the <code>enctype="multipart/form-data"</code> attribute of the form element

The value currently stored in an input is in the "value" attribute. 

In the example above there's an input with <code>type="submit"</code>. This is the submit button, a click on which will trigger the submission of the form. In the form element, <code>method</code> and <code>action</code> represent the http request method and the url the request goes to relatively. 

#### 3.2 AJAX

AJAX allow us to send http request and receive responses in the background. Here's an example (using *jQuery*):

    $.ajax({
        url: '/api/login', // the url to request
        type: 'GET', // http method
        data: {'username': '...', 'password': ...}, // The parameter to contain in the request
        success: function(res){
            // res is the content in the http response
            ...
        },
        error: function(e){
            // when some error happens, for example the status code of the response represents error (4XX, 5XX),
            // this function will be called. And the e object contains the error information
            ...
        }
    });

It's very simple, just define:

1.  Where does the request go
2.  Which http method to use (optional, default "GET")
3.  What data to encode in the request (optional)
4.  What to do when the request succeed (optional)
5.  What to do when some error happen (optional)

Just one reminder: there's a reason why there's an *Asynchronous* in the name of AJAX. Let's take a look at one example:

    $.ajax({url: ..., success: function(res){ console.log('response received!'); }});
    console.log('ajax done!');

This will output (in most cases):

    ajax done!
    response received!

Why? The function we defined for the <code>success</code> during ajax call is a *callback function*. It won't be called until the response is received. Meanwhile, the javascript engine won't halt there and wait for the response. It will continue to the other things, but keep an eye on the request, and when the response is back it will invoke the callback function. This is the tricky part for people who use it the first time. Me too, honestly. But this is why we can still operate on the web page while there're some pending requests. 

#### 3.2 Ajax Form

That's a weird subsection title. But what I'm trying to say is, we can easily submit a form in the *AJAX-style*, with the help of a jQuery plugin [jQuery.form](http://malsup.com/jquery/form/). During a normal form submission, the whole page blocks when waiting for the response, and the browser will refresh the page with the content it receives from the response; but with this, the form submission happens behind the stage, and the response will be received in the callback function we saw before in the AJAX example. 

The usage is pretty simple. This is an example. You might also see this in later assignments:

    $('#article-form').ajaxForm({
        success: function(res){
            if(res.error)
                alert(res.error);
            else{
                window.location.href = '/article?id=' + res.article.id;
            }
        },
        error: function(e){
        },
        dataType: 'json'
    });

### <a id="asm"></a> 4. Assignments

We'll be implementing the front end of the website for the assignments in this chapter. Since auto testing gets a little tricky and limited when it comes to the front end, so our test scripts don't cover all the specifications.

1.  Finish the implementation of homepage (<code>app.controllers.webpages.all_articles</code> and <code>templates/index.html</code>). The implementation should support similar functionality as the article search controller you implemented before.

    The full specification is:

    *   Support searching. User can input the search query to the textbox in the right sidebar, and click the search button to search articles with title containing the search query

    *   Support paging. It looks like this:

        <img src="/resource/pager.png" style="width:75%">

        When user clicks on the pager, it jumps to the corresponding page, with query and article count per page preserved. 

    Use <code>make 4_view_articles</code> to test.

    **Hints**

    *   You've actually implemented the searching and paging at the backend before, so you should try to reuse the code you wrote. One possible way to do this is encapsulate the code you wrote in the *Model* layer as a function, and call the function from different controllers

    *   When implementing the paging, you should consider putting this part in a separate .html file and use {% raw %}{% include %}{% endraw %} to include it, so you can re-use this part of code in other places. Since you need to preserve the previous query and article count per page, you might need to use javascript to substitute the <code>page=...</code> parameter in the url to the one user clicks on. Specifically, you can construct a new url, and redirect the page to that url


2.  Add edit article page. Specifically, you need to:
    
    *   Implement a new controller (<code>app.controllers.webpages.edit_article</code>), corresponding to the url pattern <code>/edit_article?id=[article_id]</code>. This controller returns the html page for user to edit the article with given id. Only the author of the article has access to the page. 404 should be returned for unauthorized visit

    *   For the html page, you can either create a new .html template, or, which we recommend, try to re-use the template of create_article.html

        **Hint**: Re-using an existed template is good because any future modification will be reflected on both page, otherwise you'll need to make the same changes twice. You should start by thinking what parts are different between these two pages and what parts can remain the same. 

        For example, the <code>action</code> attribute of the form is different. You can use {% raw %}{% if ... %}{% endraw %} for rendering different contents in the same template; and you can use <code>&lt;input value="..."&gt;</code> to assign initial values for inputs, etc.

        As you might notice we use the *AJAX FORM* technique mentioned before here. You probably won't need to change this part, but you may want to see how it works

    *   You have already implemented the controller for updating an article in the previous assignment (<code>app.controllers.api.update_article</code>). You just need to make sure that form in the edit article page submits to this controller

    Use <code>make 4_view_edit_article</code> to test.

3.  *Buttons*

    As you might see there're some buttons in the article page now:

    <img src="/resource/article_buttons.png" style="width:80%" />

    Right now they're static and have no behaviors. Now what we're expecting is:

    *   Only author of the article can see this three buttons. When the user is not log on or the log on user is not the author, you should not render these buttons. Use Django's {% raw %}{% if ... %}{% endraw %}

    *   When user click on the *edit* button, he will be redirect to the edit article page

    *   When the user click on the *delete* button, there should be an alert window saying "are you sure?" or similar stuffs and wait for the user's confirmation. If he confirms, delete the article, and redirect to his homepage after the deletion is complete. Use javascript to listen to the click event of this button

    *   For the publish/unpublish button, the text inside it clearly doesn't make any sense. When the article is a draft, the text should be "publish"; if the article is a published public article, the text should be "unpublish". When user click on the button, it should cause the state of the article to change from one to the other; when the update completes, the text on the button should also change. As the previous one, use Javascript for this

    Use <code>make 4_view_article_page</code> to test.

    **Hints**

    We can tell you too ways about passing data into javascript. 

    *   Directly render the data as variables in javascript. This is the brute-force way, not a very good solution

    *   Render the data into html, or attributes inside html elements. For example, you can render the id of the article into an html element like:

            {% raw %}
            <div id="article-data" data-article-id="{{ article.id }}"></div>
            {% endraw %}

        and read the information in javascript with:

            var articleId = $('#article-data').data('article-id');

    **Note** In our testing script we search for these buttons by class names. That is, the edit button has class <code>btn-edit-article</code>, the delete button has class <code>btn-delete-article</code>, the publish/unpublish button has class <code>btn-change-article-state</code>. We don't expect this to change otherwise it will become very tricky for the test script to work properly. 

    
