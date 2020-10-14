---
layout: post
title: SSRS Font Colour With Placeholder
category : development
tags : [ssrs]
---
{% include JB/setup %}

I've been starting to work with [columnstore indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/columnstore-indexes-overview?view=sql-server-ver15) recently. Here are some of my notes:


# What situations does it help?
* Wide tables with LOTS of columns *where you can't easily predict which set of columns end users / applications may select*
* Very large tables (>100GB)
* ETL workloads -  bulk inserts with no updates.

# Things to avoid
* Inserts smaller than 10,000 rows ALWAYS go into a delta store.
* Updates mark deleted rows and insert new rows into the delta store.
* Rebuilding the index will break segment eliminations