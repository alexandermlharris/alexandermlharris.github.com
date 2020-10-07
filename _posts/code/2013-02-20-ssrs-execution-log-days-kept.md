---
layout: post
title: SSRS Exceution Log: Days Kept
category : development
tags : [microsoft, ssrs]
---
{% include JB/setup %}

I recently was trying to collect usage ingormation for our Microsoft SQL Server Reporting Services installation.

We had initially developed some testing and placeholder reports which we wanted to remove, but we wanted to check what usage these were getting in the wild within the organisation.

While looking into the execution log, I found that there were no records older than 60 days - even though the server had been running a good deal longer than that!

I did a bit of digging and found that by default the Reporting Services instance will only keep the most recent 60 days in the execution log.

This duration can be configured via the `ExecutionLogDaysKept` advanced property which can be set on the server. The value is the time in days for log entries to be kept. Valid values are in the range: `0` to `2,147,483,647`, where a `0` indicates that entries are never deleted.

The [Server Properties (Advanced Page) - Reporting Services](http://technet.microsoft.com/en-us/library/bb934303%28v=sql.105%29.aspx) MSDN page gives the details of this, and many other advanced server confiuratio parameters. You can access them as follows:

>To open this page, start SQL Server Management Studio, connect to a report server instance, right-click the report server name, and select Properties. Click Advanced to open this page.

Since the usage of our server was not very high and the log size wasn't an issue, we increased the delay to 365 days to maintain a year's worth of execution log.