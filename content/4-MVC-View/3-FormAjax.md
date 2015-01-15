---
layout: page
title: Form, Ajax
tagline: 
tags: 
modified: 12-29-2014
comments: true
---


There're three main ways to send requests to server:

1.  Directly visit the url. Could be achieved by using the hyperlink tag <code> &lt;a&gt; </code>. Can only support GET method, and will cause a page jump
2.  Submit a *form*. This could be seen as more powerful url visit because it supports other HTTP methods, submitting file stream, etc. This is also a page jump
3.  Use AJAX (*Asynchronous JavaScript + XML*). This is usually achieved with Javascript. And there's no page jump in this case

You're probably familiar with the first way. Here we'll talk about the second the third one.

#### 1. Form

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

#### 2. AJAX

AJAX allow us to send http request and receive responses in the background. Here's an example (using *jQuery*):

    $.ajax({
        url: '/api/login', // the url to request
        type: 'GET', // http method

        // The parameter to contain in the request
        data: {'username': '...', 'password': ...}, 

        // The callback function if the request is success
        success: function(res){
            // res is the http response
            ...
        },
        error: function(e){
            // when some error happens, for example 
            // the status code of the response represents 
            // error (4XX, 5XX), this function will be called. 
            // And the e object contains the error information
            ...
        }
    });

It's very simple, just define:

1.  Where does the request go: **url**
2.  Which http method to use (optional, default "GET"): **type**
3.  What data to encode in the request (optional): **data**
4.  What to do when the request succeed (optional): **success**
5.  What to do when some error happen (optional): **error**

Just one reminder: there's a reason why there's an *Asynchronous* in the name of AJAX. Let's take a look at one example:

    $.ajax({
        url: ..., 
        success: function(res){ 
            console.log('response received!'); 
        }});
    console.log('ajax done!');

This will output (in most cases):

    ajax done!
    response received!

Why? The function we defined for the <code>success</code> during ajax call is a *callback function*. It won't be called until the response is received. Meanwhile, the javascript engine won't halt there and wait for the response. It will continue to the other things, but keep an eye on the request, and when the response is back it will invoke the callback function. This is the tricky part for people who use it the first time. Me too, honestly. But this is why we can still operate on the web page while there're some pending requests. 

#### 3. Ajax Form

That's a weird subsection title. But what I'm trying to say is, we can easily submit a form in the *AJAX-style*, with the help of a jQuery plugin [jQuery.form](http://malsup.com/jquery/form/). During a normal form submission, the whole page blocks when waiting for the response, and the browser will refresh the page with the content it receives from the response; but with this, the form submission happens behind the stage, and the response will be received in the callback function we saw before in the AJAX example. 

The usage is pretty simple. This is an example. You might also see this in later assignments:

    $('#article-form').ajaxForm({
        success: function(res){
            if(res.error)
                alert(res.error);
            else{
                window.location.href = '/article?id=' 
                                       + res.article.id;
            }
        },
        error: function(e){
        },
        dataType: 'json'
    });