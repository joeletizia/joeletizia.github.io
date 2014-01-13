---
layout: post
title:  "SQL: How to Tie Your Shoelaces Together"
date:   2014-01-13 09:00:00
categories: jekyll update
---

Consider the case where you wish to find records in a table, join those records to another table via a left join (all records of the original table, nulls for the non-matching records on the right) and filter out all the null results on the right hand column. In SQL, that would look something like this:

{% highlight sql %}
SELECT COUNT(*) 
FROM posts
  LEFT JOIN likes on posts.id = likes.post_id
 WHERE likes.id IS NULL
{% endhighlight %}

The above should be rather efficient, yes? It's idiomatic SQL and something you might find out of a standard undergrad textbook. However, consider the restriction added in the `WHERE` clause.

We've restricted against `likes` that have been joined in against `posts` via `likes.post_id` using the `likes.id` field. Even with proper indexing, once these tables have sizeable datasets, this query will take a significant amount of time. Why? I'm not really sure. My best guess is that since we are restricting against `likes.id` rather than the field we used in our join clause (`likes.post_id`), the psql query optimizer has to go back into the joined table, bring in the `likes.id` field, and restrict. Amending our query to the following showed a significant performance increase:

{% highlight sql %}
SELECT COUNT(*) 
FROM posts
  LEFT JOIN likes on posts.id = likes.post_id
 WHERE likes.post_id IS NULL
{% endhighlight %}

In my case, I had a performance improvement from this query taking 7 seconds originally, to ~50 milliseconds after being corrected. My pair for the day and I were both incredibly surprised by this, but we'll certainly take the improvement.
