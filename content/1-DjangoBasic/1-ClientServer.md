---
layout: page
title: Client-Server Model and MVC Design Pattern
short_title: Client-Server & MVC
modified: 12-23-2014
comments: true
---

### Client/Server Model

Allow me to use an old-fashion image here:

![Image](/resource/clientserver.png)

Basically all web systems follow this model. The web system, as a server, responds to all requests sent by different clients(browers, crawler, etc), using standard protocols. Clients' requests are considered independent of each other. This means when the server is constructing the response to a request sent from one client, it doesn't need to consider the requests from other clients or other requests from the same client, but only the information contained in this request, and probably the information stored in the database or file system on the server side.

### Model-View-Controller Architecture Pattern

This model is widely used in developing softwares that have a GUI, not just in web system. It can be applied to a system as well as a component inside a system. In a word it's a very useful pattern.

A short introduction here is:

1.  **Model**: This component captures the core problem of the system, such as data structure and data storage, core logic code, etc. It's usually the underlying infrastructure of the whole system and it usually doesn't actively interact with the other two components.

2.  **View**: This component focuses on display information to users(for example, display a list or display a visualization chart) and receive users' interaction (such as button click or keyboard input). In a web system, this is usually the layer of frond-ends: html pages, mobile apps, etc

3.  **Controller**: This is the glue between Model and View. The functions in this component are usually triggered by an event (the user clicks a button, the state of the Model has changed, etc), and take the corresponding actions (search items in database and return the result to View, or send a message to View to notify user that some states of the Model have changed, etc). In web systems, this layer processes http request and return with http response (we'll cover this later)

**Note:**

The terminology we use here is actually different from Django's naming convention. In Django's documentation, the layer of processing http request and return response is called **View**, and html pages are usually called **Template**. Even though this course is based on Django, we decide not to follow their convention, for two main reasons:

1.  It's confusing. In Django official documents what they call *View* is kind of a combination of backend and frontend. But we want to give **Controller** and **View** clear and decoupled definitions. **Controller** is backend, **View** is frontend. 

	To be clearer, let's use a simple, brute-force rule to distinguish between Controller and View, in our terminology: the code written in Python all belongs to **Controller**, while the codes written in html, javascript, css all belong to **View**

2.  The terminology of *M-V-C* is more general. That's, it's not restricted in Django, and can be used for all web systems. 

