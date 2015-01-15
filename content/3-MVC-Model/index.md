---
layout: page
title: Model Layer in Django
tagline: 
tags: 
modified: 12-23-2014
comments: true
---

In this chapter we'll dive deeper into a web system's core, *Model*. In Django, as well as many other web frameworks, *Model* can be seen as a blackbox, whick encapsulates the implementation details of data storage and retrieval and provides methods for upper layers to manipulate data. Sometimes it might also be the layer which encapsulates complex core logics, such as one method for "transfer money from A to B and charge A for the fee" or one method for "send an email to all registered users", etc, while at other times these complex logic might be encapsulated in another *Service* layer and the *Model* layer will focus on data storage and retrieval. 

Most web systems use database as backend support for Model. We're using MySQL for this tutorial, as you configured before. The following content assumes you have a basic understanding of relational database. If you feel confused about concepts such as: *Table*, *Schema*, *Column*, *Row*, *ForeignKey*, *PrimaryKey*, *Index*, you can go to:

*   [Basic concept](http://www.tutorialspoint.com/sql/sql-rdbms-concepts.htm)
*   [Oracle tutorial](http://docs.oracle.com/javase/tutorial/jdbc/overview/database.html)

...and look for the meaning of the terms I mentioned. In these tutorials you may also learn about **SQL**, a language used for data manipulation in database (which is not essential if you're just using Django because the encapsulation of Django hide this part from developers, but is necessary for ones who want to become real serious web developers---and we'll have assignments for that as well), if you're not familiar with it yet.



