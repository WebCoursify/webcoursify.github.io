---
layout: page
title: Further discussion with regard to Model
tagline: 
tags: 
modified: 12-23-2014
comments: true
---

We present several discussions here.

#### 1. Index

Index is some kind of additional data structure used for faster data retrieval. The most widely used index type is BTree, or some variants of it. To say it in a simple way, BTree puts nodes with smaller value on the left and nodes with larger value on the right, so it puts all rows in order with respect to specific field(s). For example, an index with respect to the <code>id</code> field will put all rows in the ascending order of id, while an index with respect the <code>age</code> will put all rows in the ascending order or age, which is different from the order with respect to id. 

All primary keys and foreign keys are accompanied with index. We can define our own index on one or more than one fields. The advantage here is we can search faster. If we want users with age=18 we can do this by searching a very small part of the index we have on the age field. If we do not, we'll need to scan the whole table. However, the disadvantage is we need to pay the maintainence cost for this structure when we need to insert/update rows. 

#### 2. You see that <code>deleted</code> field?

When I first see such a field I was confused, as you might be now. What's it for? Sounds like a field used to mark whether a row is deleted or not. If so, why do we need this field? Why not just delete the row, but border to keep the row and mark it as deleted? 

There're some discussions around this and some people might say this is judgement call. Here's Pros and Cons I could think of or find online:

Pros:

*   Doing so makes it possible to restore the data
*   It's usually faster to update a field than actually deleting a row
*   ...

Cons:

*   It's easier to introduce bugs in the program. People might just forget to add 'deleted=0' condition in the SQL, for exmaple
*   The table will be larger, so data retrieval may be slower
*   ...

It might indeed be a judgement call and I just put it here for demonstration. You can choose whether or not use this strategy when you're desgining new tables in later courses (but please keep the original tables in this way, because our test cases in based on this).