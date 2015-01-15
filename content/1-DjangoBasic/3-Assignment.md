---
layout: page
title: Assignment-Django Basic
modified: 12-23-2014
comments: true
---

### Assignment: Automatic tests

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