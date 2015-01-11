---
layout: page
permalink: /topic-user/
title: User Management in WebApp
tagline: 
tags: 
modified: 01-06-2015
comments: true
---

In this chapter, we'll talk about the user authentication, session and cookie.

### <a id="userauth"></a> 1. User Authentication

The most basic, perhaps most common scenario of user authentication is "log in". The client sends a unique representation of a user account (user name, or email, for example) as well as the password to the server, the server checks whether the password is valid for this user. If so, mark the client as "log on". Subsequent operations from this accounts will thus be considered as issued by this authenticated user, until the client disconnect, log out or log in with another account. 

#### Simple approach

The most simple approach is, store the user's password as plain text in the database. When a client submits the user id and the password, find the corresponding password in the database, and compare it with the submitted password. The problem here is, once someone somehow manage to hack into the database, he/she can obtain the passwords, because they're stored as plain text. 

#### Hash approach

One way to prevent the aforementioned problem is store the one-way encrypted hash values of passwords in database. One-way means one can only get the hash value from plain text, but cannot directly restore the plain text with the hash value (unless with brute-force). When a client submits the password, apply the same encryption algorithm to the password, and compare the result to the value in database. 

#### Salt

We can use **SALT** together with hashing. Adding *Salt* means we append a random string after each password and then do the hashing. Of course, the salt itself need to be stored. During authentication, we retrieve the salt for the user, append it to the submitted password and do hashing, then compare with the value in database. There're two advantages in using salt:

1.  Make it more time-consuming to crack passwords. Without salt, the attacker just need to generate a random string each time, hash it and see if it matches any hash value (using a hash table is very convenient for this). But with salt, the attacker need to find out the salt, which is more complex. What're more, each user's salt could be (and should be) different, so the cracking time for a set of user passwords will be significantly longer. 

2.  Salt can help generate different hash values for users with the same password. This can increase the security because a partial leak of the user passwords won't affect the security of other users' account. 

#### Use Post instead of GET

As we mentioned, when using GET, the parameter will be encoded in the url. Most web servers today save access logs which contains the full urls. So, if the client use GET, the server will save the url, which contains the password. So never use GET for authentication, use POST instead.

#### Use Secure HTTP

[HTTP Secure](http://en.wikipedia.org/wiki/HTTP_Secure)(*https*) is a more secure version of HTTP. It's not a new protocol itself; rather, it's HTTP based on [SSL/TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security) protocols, which are cryptographic protocols used for secure communication over the Internet. Using HTTPS can greatly improve the security of communications between client and server.

Typically HTTPS would be a little bit slower than HTTP. So if you really care about performance, you can use HTTPS in authentications such as registeration or logging in, and use normal HTTP in other cases where there's no much sensitive information.

### <a id="cookie"></a> 2. Cookie

[Cookie](http://en.wikipedia.org/wiki/HTTP_cookie) is a small piece of data sent from a website and stored in a user's web browser while the user is browsing that website. When a user accesses a website with a cookie function for the first time, a cookie is sent from server to the browser and stored with the browser in the local computer. Later when that user goes back to the same website, the website will recognize the user because of the stored cookie with the user's information. 

Cookie can be used to stored a variety of information, such as user's preference in filling web forms; items in the shopping cart; or session token(we'll get to this later). It can also be used for tracking user behaviors.

Here're some more facts:

1.  Cookie has expiration time. The browser will automatically delete expired cookie. In Javascript, the way to delete a cookie is to reset its expiration time and wait for it to expire and be cleaned out

2.  According to the [RFC](http://www.ietf.org/rfc/rfc2965.txt) specification(5.3), general-use user agents SHOULD provide each of the following minimum capabilities individually:

	*   at least 300 cookies

    *   at least 4096 bytes per cookie (as measured by the characters that comprise the cookie non-terminal in the syntax description of the Set-Cookie2 header, and as received in the Set-Cookie2 header)

    *   at least 20 cookies per unique host or domain name

    While implementation the server we should realize that the data stored in cookie can not be infinitely large. If it gets too large it may not be supported by the client. And as long as it doesn't exceed the aforementioned limitation, it'll work in most cases. 

3.  Most modern browser support cookies, but always allow users to disable them. If you don't wanna be tracked, you might consider starting with disabling the cookie

### <a id="session"></a> 3. Session

[Session](http://en.wikipedia.org/wiki/Session_%28computer_science%29) is like a serial of conversations between the server the client. It's *stateful*: states from the previous communication will be saved for the subsequent communications. The most common example is, a user log on once, and then he can access information that requires authentication. He doesn't need to provide password every time he want to access these information, because the server recognizes him to be log on in a previous communication. 

Session can be implemented through Cookie. The server return a session token in the response when a client access the website for the first time. The subsequent requests from that client will contain the session token in the cookie, so the server can read the session token and decide which session it is. The server can store many useful information associated with the session, such as the log on user's id. In our project, that's how we "mark" a client as log on:

	...
	request.session['user'] = {'id': user.id, 'username': user.username}
	...

### <a id="emailveri"></a> 4. Email verification

Most websites need to verify the user's email. To do that, the website would send a link to the email used by the user during registration. Clicking that link will verify the mail. If the email is invalid or doesn't belong to the person, he/she won't be able to see and click that link. 

One way to implement this, is to take some data that uniquely represent the user(concatenate the user id and the email, for example), apply a two-way encryption algorithm on it with a private key only you have access to, and as the result get a token. Then, put that token in the url, like this:

	http://YOUR_WEBSITE/verification?token=asdy7y2ehnjfajsdhuybje1

Send that url to the user. When the user visits the url, you can retrieve the token from the request, recover the user identifier from the token with your private key, and finish the verification of that user's email.

Django provides interface to send emails conveniently:

	from django.core.mail import send_mail

	send_mail('Subject here', 'Here is the message.', 'from@example.com',
	    ['to@example.com'], fail_silently=False)

More details [here](https://docs.djangoproject.com/en/1.7/topics/email/)

### <a id="asm"></a> 5. Assignments

*Registration*: Implement the registration controller and web page. Specifically, you need to:

1.  Finish the Registration page (<code>templates/register.html</code>). The implementation should:

	*   Send request the server when the user finishs inputting the information correctly and hit submit button (with ajax or ajaxForm). When the server gives successful response, redirect the user to the log in page; if the response contains error information (there're already another user registered with the same email, for example), use <code>alert</code> to show user the error

	*   Validate the input before submitting to server. The <code>jquery.form</code> plugins provides an interface <code>beforeSubmit</code> which allows you to validate the input before sending the data to server. Here're several examples for validation:

		*   It should be a valid email address
		*   The two passwords should be the same
		*   The username should not be too long (should not exceed the limitation of database column, unless you use <code>TextField</code> for username in the User table)
		*   ...
	
2.  Finish the controller <code>api.register</code>. You may also need to add url pattern <code>/api/register</code>
	
	In your implementation, you should:

	*   Check whether the email exists. If so, return json string <code>{'error': 'whatever message you want to tell client'}</code>
	*   Create a user with <code>role=User.ROLE_AUTHOR</code>. Remember you need to apply encryption algorithm on the password first. Take a look at the login controller
	*   Return <code>{'success': True}</code> when the registeration is finished

Use <code>make 5_user</code> for testing.




