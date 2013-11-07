---
layout: post
title:  "How I Learned to Love Common Table Expressions"
date:   2013-11-07 09:00:00
categories: jekyll update
---

I'm currently working on a large scale ETL (extract, transform, load) project. We have a legacy source data model that, in certain areas, vastly differs from the new generation destination data model. We lean on ruby to do much of this work since we are most comfortable working with ActiveRecord as our ORM. 

One thing that we do for our acceptance criteria is take a count out of the old system for the records we are extracting, manipulate that count to account for any transformations we do in the ETL task that may increase or decrease the number of records, and compare that to the number of records we actually load into the new database. This simply gives us an extra data point to see if we have screwed anything up.

One thing we have begun to rely heavily on is the concept of Common Table Expressions in our count scripts in order to silo responsibility of our joins. For example:

{% highlight sql %}
  active_groups as (
    select id 
    from groups 
    where status = 'ACTIVE'
  ),

  active_players as (
    select id
    from players
    where status = 'ACTIVE' and test is false
  )
{% endhighlight %}

What we have above are two separate queries that contain what is, in essence, a subset of all the records in their respective tables. These sets have been restricted prior to being joined into any related tables, like below:

{% highlight sql %}
  active_groups as (
  select g.id, g.location, g.startdate
  from games g
  join active_groups ag
    on ag.id = g.group_id
  join active_players ap
    on ap.group_id = ag.id
{% endhighlight %}

This does several things:

1. Aesthetically, this is a bit easier to read. Similar to breaking out a class into logical methods like you would do in Ruby, this allows the reader to know what exactly your CTE is bringing to the table. It is properly named, and is very clear.
2. These CTEs will be run before their results are joined into the regular query. These CTEs have already been restricted via their respective where clauses, alleviating the regular query of that responsibility.

Recently, we've seen a great performance increase by using CTEs. Perhaps this can be attributed to postgres' query optimizer not being able to restrict our join tables until the join had already happened, which seems to be the only difference between using several CTEs rather than a single query and many restrictive where predicates. 
