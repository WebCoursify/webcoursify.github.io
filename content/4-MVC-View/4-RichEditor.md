---
layout: page
title: Using Rich Text Editor with Ajax
tagline: 
tags: 
modified: 12-29-2014
comments: true
---


*Rich Text Editor* allows users to add style to their articles (bold or italic font, underline, list item, etc). *Google doc* is a great example. Here we give a simple tutorial on how to integrate a Rich Text Editor into our platform, so our bloggers can write well-styled articles. 

In this example, we'll use [CKEditor](http://ckeditor.com/). It looks like this:

<img src="/images/ckeditor.png" />

To setup it up, we just need:

1.Include the ckeditor script file:

{% highlight html %}
<script src="//cdn.ckeditor.com/4.4.6/standard/ckeditor.js"></script>
{% endhighlight %}


2.Replace the textarea we have for input content with the rick editor:

Html:

{% highlight html %}
...
<textarea name="content" cols=100% rows=12 id="content"></textarea>
...
{% endhighlight %}

Javascript:

{% highlight javascript %}
CKEDITOR.replace('content');
{% endhighlight %}

Then, it should work!

Now, what we need to add is, read the content from the editor, and add content to the post data, right before the data is about to submit to the server:

{% highlight javascript %}
$('#article-form').ajaxForm({
    ...
    beforeSubmit: function(arr, form, option){
        // arr is an array, each element 
        // is {name: ..., value: ...};
        // we set the value of field 'content' to the value 
        // read from the editor

        var contentData = CKEDITOR.instances.content.getData();
        arr.forEach(function(item){
            if(item.name == 'content')
                item.value = contentData;
        });
    },
    ...
});
{% endhighlight %}
