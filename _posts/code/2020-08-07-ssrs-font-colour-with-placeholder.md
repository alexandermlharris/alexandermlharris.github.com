---
layout: post
title: SSRS Font Colour with a Placeholder
category : development
tags : [ssrs]
---
{% include JB/setup %}

I was creating a placeholder SSRS report and wanted to format a text "status" column with colour to indicate the status.

I found that the `Switch` statement was the easiest way to let me match a set of possible status values to colours I'd like, with a default from within SSRS, without having to add this as a column to the SQL query OR having to write more longwinded code.

See the gist below for how I did it on this occasion.

{% include JB/gist gist_id="19595621eb1a7818c51c83155d1f5375" %}

This pattern can easily be used for pretty much any expression for any property on a SSRS report.