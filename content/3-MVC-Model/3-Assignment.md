---
layout: page
title: Assignments for Model
tagline: 
tags: 
modified: 12-23-2014
comments: true
---

For this chapter, you'll need to implement new functionalities for the platform using what you learn in this chapter as well as the last Chapter(*Controller*).

1.  *Searching!* Implement <code>app.controllers.api.get_articles</code> as specified by the docstring. You can look for query operators supported by Django on the [official site](https://docs.djangoproject.com/en/1.7/topics/db/queries/).

    Notes:
    
    *   Use case-insensitive match
    *   When the query parameter is invalid, you should return 400
    *   Use <code>make 3_model_search</code> to test 


2.  *Everything about Article*. Implement three controllers for:

    *   Update article. Url pattern is <code>/api/article/{article_id}/update</code>. Controller is defined in <code>app.controllers.api.update_article</code>

    *   Change article state. Url pattern is <code>/api/article/{article_id}/set_state</code>, which you need to add to <code>urls.py</code>.

        **Specification**: It looks for **"state"** in the data payload, which can be either "published" or "draft". "published" means setting the article's state to published and can be viewed by everyone, while "draft" means setting the article to be a draft and can only be seen by the author.

    *   Delete article. Url pattern is <code>api/article/{article_id}/delete</code>, which you need to add to <code>urls.py</code>. What the controller does is simply remove this article. (*Hint*: Remeber what we discuss above?)

    Notes: For all these three controllers:

    *   Only allow POST method
    *   If the user is not log on, return 401. If the log on user is not the author, return 403
    *   If the article is not found. Return 404.

    Use <code>3_model_article</code> for testing. 

    BY THE WAY, will you ever think of considering about using the same controller for the first two? 

3.  *Following Relation*. Add four new controllers to:

    *   Follow a user. Url pattern is <code>/api/user/follow</code>. Only support POST. The target user id is given as "target_user_id" in the posted data. The controller will make the **log on user** a follower of the **target_user**. It returns <code>{'success': True}</code> or <code>{'error': error_message}</code>

    *   Unfollow a user. Url pattern is <code>/api/user/unfollow</code>. Requirement is similar to the one above, just doing the opposite thing

    *   Get the follower list of a user. Url pattern is <code>/api/user/followers</code>. Only support GET. If "user_id" is provided in the query parameter, return the followers of this user; if not provided and the requesting client is logged on, return the followers of the log on user; otherwise response with 401 (return a HttpResponse with status code set to 401).
        
        The response data should be a json array, with each element be <code>{'id': the user id, 'username': ...}</code>

    *   Get the list of users a user is following. Url pattern is <code>/api/user/followings</code>. Only support GET. If "user_id" is provided in the query parameter, return the followers of this user; if not provided and the requesting client is logged on, return the following list of the log on user; otherwise response with 401.
        
        The response data should be a json array, with each element be <code>{'id': the user id, 'username': ...}</code>

    Notes:

    *   Follow a followed user, or unfollow a user currently not following should not yield any error
    *   Can not follow or unfollow oneself. When this happend, return 400
    *   To get this done, you'll need to modify the table schemas (the definition of classes in <code>models.py</code>), either update the existed class(es) or add new class(es). Recall how to represent such a *User-follow-User* relationship in database. And check for Django documentation about how to manipulate such a relation.
    *   The order of returned users doesn't matter

    Use <code>3_model_follow</code> for testing.

**Some additional hints** 

*   To see if the request comes from a log on client, simply use <code>if 'user' in request.session</code>. If <code>True</code>, one can get the logged on user's id by <code>request.session.get('user')['id']</code>
*   You can use <code>make 3_model</code> to test all questions here
