---
layout: page
title: Django's HttpRequest, HttpResponse, and Url dispatching
modified: 01-06-2015
comments: true
---

Django, and basically all other web frameworks, encapsulate the implementation details of http, so when implementing controllers, developer only need to read data from requests and write data to responses with provided interface. 

Let's take a look at one example controller (in <code>app.api.login</code>):

    def login(request):

        #########################
        # Read the data we need #
        #########################
        email = request.REQUEST.get('email', None)
        password = request.REQUEST.get('password', None)
        if email is None or password is None:
            return HttpResponseBadRequest('email and password required')

        ########################
        # Process the business #
        ########################
        password = md5(password)
        user = User.objects.filter(email=email, password=password, deleted=0)
        if not user.exists():
            return HttpResponse(json.dumps({'error': 'Incorrect email/password'}))

        user = user[0]

        # Here's how we mark a user as log on
        request.session['user'] = {'id': user.id, 'username': user.username}

        #######################
        # Return the response #
        #######################
        return HttpResponse(json.dumps({'success': True}))


This is a very good example for what typical controller may be like:

*   A controller takes one or more arguments, and the first argument is always a <code>HttpRequest</code> object. A controller must return an object of <code>HttpResponse</code>(or other sub-classes).
*   <code>request.REQUEST</code> is a dictionary containing the query parameters as well as data payload of the request. Actually, it's the union of <code>request.GET</code> and <code>request.POST</code>. There's something tricky here need to be explained in details:
    
    *   Remember we often see urls like: <code>http://.../search?query=foo&page=1</code>? Things after the <code>?</code> are query parameters. In this case we have two parameters, the first one's name is <code>query</code> and its value is "foo", the second one's name is <code>page</code> and the value is 1. Here, we define **query parameters** as those parameters encoded in the url after the "?". We also define **data payload** as the data contained in http request body
    *   When a http request is using GET method, it can only contain query parameters but no **data payload**
    *   When using some other methods, for example *POST*, we can include **data payload**, but we can also encode query parameters in url
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
    r'article'             # Match all strings contain 'article' 
                           # as a substring

Django's dispatching support embedded parameter in the url. One example is:

    url(r'^api/article/(?P<article_id>[0-9]+)/update$', 
        api.update_article)

This pattern will match all urls in the form of <code>/api/article/{a number here}/update</code>, for example '/api/article/1111/update' or '/api/article/15213/update'. The parameter defined in the url will also be parsed and passed into the controller. The definition of <code>api.update_article</code> will be like:

    def update_article(request, article_id):
        ...