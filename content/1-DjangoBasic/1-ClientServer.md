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
2.  **View**: This component focuses on display information to users(for example, display a list or display a visualization chart) and receive users' interaction (such as button click or keyboard input)
3.  **Controller**: This is the glue between Model and View. The functions in this component are usually triggered by an event (the user clicks a button, the state of the Model has changed, etc), and take the corresponding actions (search items in database and return the result to View, or send a message to View to notify user that some states of the Model have changed, etc)

