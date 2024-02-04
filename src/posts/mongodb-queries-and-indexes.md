---
title: MongoDB queries & indexes
date: "2020-08-29"
description: Improve query performance using indexes
---

At work, I was told to use add `sort` to a `mongo` query & I ended up doing just that but something I never realized was performance aspect of the query.

While locally, the modified query will run just fine. It's the production performance you should always be concerned about. 

So I was pointed to `explain` function that will spit out plans that will let you optimize the query.

From what I have gathered, we should avoid queries that have to scan all documents & it will show up as `COLLSCAN` in the `explain` function results. That means you would want to create an index for every parameter (or combination of parameters) you're going to use in your `mongo` queries. 

This blog post explains the `explain` function very well: https://www.percona.com/blog/2018/09/06/mongodb-investigate-queries-with-explain-index-usage-part-2/
