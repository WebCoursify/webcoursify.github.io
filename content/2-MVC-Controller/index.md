---
layout: page
title: Intro to Django's Controller
modified: 01-06-2015
comments: true
---

In this chapter, we'll first have an basic understand of http request and response, then see how to handle http request in Django.

### Brief introduction of http request and http response

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






