---
layout: page
permalink: /project-spec/
title: Capstone Project Specification
tagline: 
tags: 
modified: 01-08-2015
comments: true
---

This is the specification for the capstone project. Based on what you learnt in the course, you're expected to add the rest of features into the website to make it a complete platform. 

Ready?...Go!

### <a id="profile"></a> 1. Profile Management

Users are allowed to manage their profile. This means they can modify their username, change their password, and upload pictures as their avatar image.

1.  Implement a page where users can change personal information: their names and descriptions. 
	
	*   The url for the edit page should be: <code>/profile/edit</code>. Only log on user can access this page
	*   The data should be submitted to <code>/api/profile/update</code>. The controller takes only POST method. Specifically, it reads the <code>username</code> and <code>description</code> field from the post data and update the corresponding user row

2.  Implement a page a user can change his/her password. 

	*   Url for the page should be <code>/profile/change_password</code>
	*   Data should be submitted to <code>/api/password/update</code>. The old password and the new password are stored in the POST data with name 'original_password' and 'new_password' relatively

3.  Implement a page a user can upload avatar image

	*   Url for that page: <code>/profile/avatar</code>
	*   Data submission: <code>/api/avatar/update</code>. The image file stream is stored with name 'avatar'
	*   You'll need to alter the User table so it can store avatar information. There're many different ways to do this. A common way is to save the image on the file system of the server, and store the relative path to that file in the database

4.  Implement *Reset Password*

	What should people do when they forget passwords? There're many different ways to handle this situation. Some websites send a reset link to the user that has expiration time. User can click that and enter the page before it expires and update the password. Some websites simply reset the user's password to a random string, and send that string to user. With that string the user can log on the website and alter the password afterwards. For simplicity, let's take the second approach.

	*   Implement a web page for the url <code>/reset_password</code>. Users can input their emails in this page and click submit. The email data will be submitted to <code>/api/reset_password</code>
	*   In the controller for <code>/api/reset_password</code>, it should:
		*   Search for the user with that given email. If not found, return 404
		*   Generate a random string as the new password for that user. Send the password to that email

### <a id="user-interfaction"></a> 2. User Interactions

1.  Implement the user search. You'll need to finish <code>controllers.webpages.people</code> and modify the html template. You should also add page in this case

2.  Implement the *Follow/Unfollow* Button in Front end. You've implemented the API before, so here you just need to connect the front end with the backend. 

3.  Implement the *LIKE* function. User can *LIKE* an article or cancel their *LIKEs*

	*   Implement controller for <code>/api/article/{article_id}/set_like</code>:

		*   Parameters: <code>like</code>: 1 or 0. 1 means the user likes the article, 0 means the user cancels the like for the article
		*   Only log on users have access
		*   When the article doesn't exist or is not published to the public, return 404

	*   Implement the front end. You'll need to add a button in the article page. The user can click to LIKE or cancel the LIKE by clicking the button

4.  Implement *Comment* function. User can add comments to an article

	*   Implement controller for <code>/api/article/{article_id}/comment/add</code>:

		*   Only accept POST. Only log on users have access
		*   Parameters:
			*   content: the content of the comment
		*   When the article doesn't exist or is not published to the public, return 404
		*   When finished, return json string in format:

				{
					"id": 1,
					"content": "It's awesome!",
					"user": {
						"id": 2,
						"username": "Jack",
						"avatar": "..." // The url to the avatar image
					},
					"time": "2015-02-02 12:22:11"
				}

	*   Implement controller for <code>/api/article/comment/{comment_id}/delete</code>, allowing user to delete comment

		*   Only the author the this comment can delete it

	*   Implement the front end. When the user click the "Add comment" button, use ajax to send the data to server. When the request succeeds, append the new comment to the comments without refreshing the whole page. User can also delete the comments he made

### <a id="feed"></a> 3. Feed

1.  Implement the <code>webpages.feed</code> controller. In this page, it should shows all articles created by users that the log on user is following. It should also have pagination, support query by keyword, just like in the index page. 



