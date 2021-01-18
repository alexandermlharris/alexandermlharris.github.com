---
layout: post
title: SQL Server Row Concatination
category : development
tags : [sql server, query]
---
{% include JB/setup %}

In a recent query, I was reporting figures, grouped by a “Group ID”. This ID related to a group of websites which the figures were for, but an ID is an ugly identifier for a row.

I wanted to replace this with the list of site names which the group contained, but manually doing this was a pain. I wanted a way to concatenate the names of all the groups in SQL.

A quick google found much dicussion of this topic, lamenting the lack of a “CONCAT” function in SQL. However the following answer sorted it out for me: http://stackoverflow.com/a/5558670/15667.

The SQL snippet goes something like this:

```sql
SELECT SiteName + ', ' AS 'data()' FROM SiteGroup WHERE GroupID = 1245 FOR XML PATH('') 
```

I used this in a correlated sub-query to SELECT the “group name” for each row.
