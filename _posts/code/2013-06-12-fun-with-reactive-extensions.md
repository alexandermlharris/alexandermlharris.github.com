---
layout: post
category : development
tags : [microsoft, c#, linq, reactive excensions]
---
{% include JB/setup %}

Recently at work I sent out a report before the data was ready because I forgot to check the “status” website for our data warehouse and the ETL was still loading data for one of the days in question.

Being a Lazy Coder™, I thought I’d spend a little time writing a tray notification app to pull status updates from a web service we have and pop up notifications about the ETL status, saving much time over checking a webpage once a day!

I also thought this would be a perfect opportunity to fiddle with the C# [reactive extensions|http://msdn.microsoft.com/en-us/data/gg577609.aspx] as it seemed like a good fit.

What I wanted to achieve was:

1. Create a tray app with notification baloons.
2. Check the ETL status periodically.
3. Pop up notifications whenever it changed.

## Tray App & Notifications

Creating a tray app with notifications was easy, I followed the excellent minimal tray applications template [here|http://alanbondo.wordpress.com/2008/06/22/creating-a-system-tray-app-with-c/].

Notification balloons are then simply a case of:

    trayIcon.ShowBalloonTip(100000, "Title Text", "Description Text", ToolTipIcon.Info);

## Reactive Extensions to work

Using Rx, the work of periodically checking the web service, and notifying my application of changes in status was really simple, and elegant. The core code is below:

    // Create a 1 minute *interval* sequence (not a timer which only fires once...).
    var source = Observable.Interval(TimeSpan.FromSeconds(60))
    // Set the initial seq value so that it runs immediatly.
                                    .StartWith(-1)
    // Get the current status each minute.
                                    .Select(x => GetStatus())
    // Ignore duplicates, so won't notify unless the status changes.
                                    .DistinctUntilChanged();

This does the following:

* Start with an Interval sequence (which fires once every x Timespan)
* Adds a StartsWith so that the sequence triggers immediately, not just when the first TimeSpan elapses.
* Select()‘s the data I want (the function call GetStatus() makes a WebRequest to get some XML and lookup a static instance of a class I defined). This transforms the sequence from an IObservable<long> to an IOvservablee<ETLStatus>
* Finally, we call DistinctUntilChanged() so that the sequence won’t signal changes for duplicate entries. This means that we’ll only see changes when the status changes, not each time the status is requested.

To make this work I have a set of pre-constructed instances of my `ETLStatus` class held in static dictionary and my `GetStatus()` function uses the status it gets from the XML to return one of these. This way the object only changes when the status changes and `DistinctUntilChanged()` works as expected. I probably could have used `DistinctUntilChanged()` on the raw XML text that is returned in `GetStatus()`, and then done a further `Select()` afterwards to convert it to the full object which I could have just constructed at runtime.

So now I have an Observable sequence which signals on ETL status changes. Now I subsubscribe to this sequence and push tray noitifications which pop up a baloon and set the tray icon.

    var sub = source.Subscribe(next => Notify(next));