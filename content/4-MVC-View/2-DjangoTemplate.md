---
layout: page
title: Template in Django
tagline: 
tags: 
modified: 12-29-2014
comments: true
---


#### 1. Data Binding
Now we assume you have seen HTML, CSS, and Javascript. The first property provided by Django is *Data-binding*: dynamically generate web page contents based on the data passed from *controller*. You might notice that in most controllers defined in <code>app.controllers.webpages.py</code> return like:

{% highlight python %}

return render_to_response('./create_article.html', locals())

{% endhighlight %}

That's the shortcut to bind the data (given by <code>locals()</code>, which returns a key-value dict of all local variables) to the template file <code>create_article.html</code>, and return the rendered html content as response. 

Let's give a more detailed example. Suppose we have a controller:

{% highlight python %}

characters = [{'id': 1, 'name': 'Tyrone', 'dead': True}, 
              {'id': 2, 'name': 'Geoffrey', 'dead': True}, 
              {'id': 3, 'name': 'Cerci'}]
return render_to_response('./characters.html', {'characters': characters})

{% endhighlight %}

and a html template <code>characters.html</code>:

{% highlight html %}
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
{% endhighlight %}

will produce:

{% highlight html %}
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
{% endhighlight %}

More details could be found on [here](https://docs.djangoproject.com/en/1.7/topics/templates/).

#### 2. Template as *Component*

As we know, a typical structure of a web page is more or less like

{% highlight html %}
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

        <!-- Extra scripts. Loading scripts at last can accelerate
         the rendering of the page content -->
        <script type="text/javascript" src="..."></script>
        <script type="text/javascript">...</script>

    </body>
</html>
{% endhighlight %}

Now, suppose all webpages in a website have the above structure. They have exactly the same head, header, footer, extra scripts. The only difference between them is the main content. When implementing these pages, we could copy-and-paste the head, header, footer stuffs into every page and modify the main content part. However in this case if we want to, for example, change the text of the header, we need to go to every single page and make the same modification. That's a bad thing.

Django provides a brilliant solution: **extend**. Here's how it works. First, we define a *base_template.html*:

{% highlight html %}
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
{% endhighlight %}

Now, for every page we want to implement, we can just:

{% highlight html %}
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
{% endhighlight %}

Similarly, if we want to add extra style sheet files in the head or add extra scripts at the end, we can change to base_template.html to support more blocks:

{% highlight html %}
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
{% endhighlight %}

And for pages that need to use these blocks:

{% highlight html %}
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
{% endhighlight %}

You don't need to extend every block defined in the parent-template. If a block is not extended in a child-template, it will be just blank, no content, empty string.

Django also support **include**. Suppose you have a sidebar you want to show in some pages, but not all, you can put it in a separate file, called 'sidebar.html', for example:

{% highlight html %}
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
{% endhighlight %}

Now for every place you want to put this sidebar (insert the content of this html file), you can just:

{% highlight html %}
<div>
    <!-- Main content -->
    ...
</div>
{% raw %}
<!-- There is a sidebar here -->
{% include 'sidebar.html' %}
{% endraw %}
{% endhighlight %}

With these features provided by Django, we can easily *modularize* our views.
