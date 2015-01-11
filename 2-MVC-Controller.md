---
layout: page
permalink: /mvc-controller/
title: MVC-Controller
tagline: 
tags: 
modified: 01-06-2015
comments: true
---

### <a id="req-and-res"></a> 1. Brief introduction of http request and http response

As we mentioned before, the basic model of how a web system works is:

1.  The client send a request to the server, and wait for the server's response
2.  The server process the request and take some actions. This is the part a controller is most concerned with.
3.  The server return the result to the client

Before looking at the controller, let's first have a basic understanding of what the requests and responses like, so when implementing controllers, you'll be more clear about how to identify and read data from requests and how to return the correct responses. The basic standard protocol here is HTTP, which we won't cover too many details about, since this is not a computer network course; but interested students can refer [here]() for more details

**Http request** contains a URL, which specify the resource address; header, which might contain cookie as well as other information; and probably query parameter or encoded entity (data payload). 

There're many request methods, including but not limited to:

*   GET
*   HEAD
*   POST
*   PUT
*   DELETE
*   ...

More details about these methods can also be found [here](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods). Among them **GET** and **POST** are probably used the most. 

**Http response** contains:

*   Header, which contains information such as the format of return data, etc
*   Status code. This is something you really need to know. Each code has its explicit meaning and should not be misused. Here's some examples (from the [official document](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html))

    *   200: Successful response
    *   302: Found. But the requested resource resides temporarily under a different URI.
    *   400: Bad request. The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.
    *   401: Unauthorized. The request requires user authentication.
    *   404: Not Found. The server has not found anything matching the Request-URI. You're probably very familiar with this one :)
    *   ...

    When implementing a web server, it's very important to send the correct response to clients under different situations. So have in mind about this, and refer to the official documentation when needed.

*   Data payload. The data payload can be text, html, binary data, etc. 

### <a id="django-class"></a> 2. Django's HttpRequest, HttpResponse, and Url dispatching

Django, and basically all other web frameworks, encapsulate the implementation details of http, so when implementing controllers, developer only need to read data from requests and write data to responses with provided interface. 

Let's take a look at one example controller (in <code>app.api.login</code>):

    def login(request):
        email = request.REQUEST.get('email', None)
        password = request.REQUEST.get('password', None)

        # Search for user in DB
        # If valid, then set the session
        user = User.objects.filter(email=email, password=password, deleted=0)
        if not user.exists():
            return HttpResponse(json.dumps({'error': 'Incorrect email/password'}))

        user = user[0]
        request.session['user'] = {'id': user.id, 'username': user.username}
        # The user is log on now

        return HttpResponse(json.dumps({'success': True}))


This is a very good example for what typical controller may be like:

*   A controller takes one or more arguments, and the first argument is always a <code>HttpRequest</code> object. A controller must return an object of <code>HttpResponse</code>(or other sub-classes).
*   <code>request.REQUEST</code> is a dictionary containing the query parameters as well as data payload of the request. Actually, it's the union of <code>request.GET</code> and <code>request.POST</code>. There's something tricky here need to be explained in details:
    
    *   Remember we often see urls like: <code>http://.../search?query=foo&page=1</code>? Things after the <code>?</code> are query parameters. In this case we have two parameters, the first one's name is <code>query</code> and its value is "foo", the second one's name is <code>page</code> and the value is 1. Here, we define **query parameters** as those parameters encoded in the url after the "?". We also define **data payload** as the data contained in http request body
    *   When a http request is using GET method, it can only contain query parameters but no **data payload**
    *   When using some other methods, for example *POST*, we can include **data payload**, but still we can encode query parameters in url
    *   Now we come back to Django. <code>request.GET</code> only contains query parameters; that is, no matter what the http method is, it will contain the parameters encoded in the url in form of <code>?key1=value1&key2=value2...</code>. <code>request.POST</code> contains the data payload encoded in the body of request if the http method is post. At cases where we need information from both query parameters and data payload, we might just use <code>request.REQUEST</code>

*   In this case, the controller respond with a string in json format (you'll need basic understanding of [json](http://en.wikipedia.org/wiki/JSON) to finish the assignment. If not, google it). It's just pure text, so passing the text to construct a new HttpResponse instance and return it is sufficient. The same method works for return HTML script. The <code>HttpResponse</code> can not only convey text, but also image, video, zip file, etc. We can also use built-in classes like <code>HttpResponseBadRequest</code> to return response with specific status code. 

Now, we know the basic strategy to write a controller. What we're going to learn next is how to configure a controller to some url pattern, so the server will know when should call this controller. There're many names for this mapping but here we will call this **Url Dispatching**.

Let's take a look the <code>web_dev_tutorial/urls.py</code>:

    ...
    url(r'^$', webpages.all_articles),
    url(r'^articles$', webpages.all_articles),
    url(r'^article$', webpages.article_detail),
    url(r'^feeds$', webpages.feeds),
    ...

Each line up there defines a dispatching strategy. There're two arguments in the url(...). The first one defines the *url pattern*, the second one gives the corresponding controller. By doing so, every http request with urls match the specific pattern will be processed by the corresponding controller. 

Let's take a closer look at the pattern definition. It's in the form of <code>r'...'</code>, the <code>r</code> prefix indicates this is a regular expression. Note that <code>^</code> indicates the start of a string, meaning there's nothing before; and <code>$</code> indicates the end. Let's see some examples:

    r'^$'                  # Exactly match empty string
    r'^article$'           # Exactly match string 'article'
    r'article$'            # Match all strings end with 'article'
    r'^article'            # Match all strings start with 'article'
    r'article'             # Match all strings contain 'article' as a substring

Django's dispatching support embedded parameter in the url. One example is:

    url(r'^api/article/(?P<article_id>[0-9]+)/update$', api.update_article)

This pattern will match all urls in the form of <code>api/article/{a number here}/update</code>, for example 'api/article/1111/update' or 'api/article/15213/update'. The parameter defined in the url will also be parsed and passed into the controller. The definition of <code>api.update_article</code> will be like:

    def update_article(request, article_id):
        ...



### <a id="asm"></a>3. Assignment: Implementing Controller

In this assignment, we'll ask you to implement several controllers according to our specifications. 

**Note**: 

Before you start, you should know that from now on, since these are assignments, we won't directly tell you exactly what to do step by step, but we'll give me some general guidances, and we expect (as well as encourage) you to figure out the rest by yourself(search on google, look at the official document of Django, etc). 

When you implement a controller that accepts POST requests, you may need to add the <code>csrf_exempt</code> decorator to that controller (if you're using multiple decorators, make sure this is the last one being applied). By default, for POST request, Django would first check if the request contains a valid csrf token; if not will return 403. For making the problem simple, we use this decorator to work around that check. (More information on [Cross Site Request Forgery](https://docs.djangoproject.com/en/1.7/ref/contrib/csrf/))

1.  Let's start with something simple: heart beat response. The controller you need to implement is already defined in <code>app.controllers.practise.heart_beat</code>. Specifications are written in the doc string. When finished, use <code>make 2_controller_heart_beat</code> to under the test directory to test 
2.  Now we come to the second question. You need to implement:

    1.   <code>app.controllers.practise.create_file</code>, which processes requests with relative urls exactly the same as '/practise/file/create'
    2.   <code>app.controllers.practise.get_file</code>, which processes requests will relative urls in format '/practise/file/{the file id, alphanumeric(0-9, a-z, A-z)}/get'

    Specifications are in the doc string. You will also need to add the pattern in <code>urls.py</code>.

    *Hints*: 
    *   You may use <code>uuid</code> to generate a unique alphanumeric id

    When finished, use <code>make 2_controller_files</code> to test

3.  You may also use <code>make 2_controller</code> to test all assignments in this chapter

### Summary

After the study of this chapter, you should

1.  Roughly know the content inside a http request and a http response
2.  Understand how to set up Url Dispatcher in Django.
3.  Be comfortable with implementing controllers in Django
