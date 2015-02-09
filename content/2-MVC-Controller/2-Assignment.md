---
layout: page
title: Assignment for Implementing Controller
modified: 01-06-2015
comments: true
---


In this assignment, we'll need you to implement several controllers according to our specifications. 

**Note**: 

Before you start, you should know that from now on, since we wish you to think independently and figure out the solution, we won't directly tell you exactly what to do step by step; but we'll give me some general guidances. You can try to search on google, or look at the official document of Django, etc. 

>   When you implement a controller that accepts POST requests, you may need to add the <code>csrf_exempt</code> decorator to that controller (if you're using multiple decorators, make sure this is the last one being applied). By default, for POST request, Django would first check if the request contains a valid csrf token; if not will return 403. For making the problem simple, we use this decorator to work around that check. (More information on [Cross Site Request Forgery](https://docs.djangoproject.com/en/1.7/ref/contrib/csrf/))

Now let's begin!

1.  Let's start with something simple: heart beat response. The controller you need to implement is already defined in <code>app.controllers.practise.heart_beat</code>. Specifications are written in the doc string. When finished, use <code>make 2_controller_heart_beat</code> to under the test directory to test 
2.  Now we come to the second question. You need to implement:

    1.   <code>app.controllers.practise.create_file</code>, which processes requests with relative urls exactly the same as '/practise/file/create'
    2.   <code>app.controllers.practise.get_file</code>, which processes requests will relative urls in format '/practise/file/{the file id, alphanumeric(0-9, a-z, A-z)}/get'

    Specifications are in the doc string. You will also need to add the pattern in <code>urls.py</code>.

    *Hints*: 
    
    *   You may use <code>uuid</code> to generate a unique alphanumeric id

    When finished, use <code>make 2_controller_files</code> to test

3.  You may also use <code>make 2_controller</code> to test all assignments in this chapter

**How to debug your server**

When the request goes wrong, the first thing you need to do is to check the response code:

*   200: Server return successfully, but it's probably returning unexpected results
*   404: Server cannot find the request you want. You probably sent the request to the wrong url or you didn't configure the url dispatcher correctly
*   403: This is a tricky one. One possible reason is, your requested with POST, but you didn't use @csrf_exempt decorator to the controller; in this case, Django refuse to process your request
*   500: This is probably the most common one, which means some code in the controller crashed, or threw an exception

Normally you can see the error message in the terminal you're running the server, like this:

{% highlight bash %}
Internal Server Error: /practise/heartbeat
Traceback (most recent call last):
  File "/usr/local/lib/python2.7/site-packages/django/core/handlers/base.py", line 130, in get_response
    % (callback.__module__, view_name))
ValueError: The view app.controllers.practise.heart_beat didn't return an HttpResponse object. It returned None instead.
[09/Feb/2015 17:44:15] "GET /practise/heartbeat HTTP/1.1" 500 55293
{% endhighlight %}

The error message would be very helpful for debugging. If you're using PyCharm, you'll see similar error messages in the console window.



### Summary

After the study of this chapter, you should

1.  Roughly know the content inside a http request and a http response
2.  Understand how to set up Url Dispatcher in Django.
3.  Be comfortable with implementing controllers in Django