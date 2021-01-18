---
layout: post
title: Debugging log4net
category : development
tags : [log4net, debugging]
---
{% include JB/setup %}

I recently found that log4net wasn’t logging after my app was first deployed to the integration server. Naturally, this is tricky to debug because log4net doesn’t throw any exceptions when it has a problem itself, so even with console output it’s hard to know what’s going on.

I found a solution to my problem in [another blog post](http://gavindraper.com/2013/07/29/debugging-issues-with-log4net-not-logging/) which details how to enable tracing within log4net and for the application as a whole.

From that article a few simple `app.config` / `web.config` changes are all that is required.

First we enable logging / tracing within log4net itself by adding the following appSetting:

```xml
<appSettings>
    ...
    <add key="log4net.Internal.Debug" value="true" />
    ...
</appSettings>
```

Next, we enable tracing for the application by adding the following element:

```xml
<system.diagnostics>
    <trace autoflush="true">  
    <listeners>
        <add
        name="textWriterTraceListener"
        type="System.Diagnostics.TextWriterTraceListener"
        initializeData="appTrace.txt" />
    </listeners>
    </trace>
</system.diagnostics>
```

Now, log4net will write debug information to the trace, and this will be captured and written to the `appTrace.txt` file for you to view later.

Using this, I found my problem.

My app was being called in the context of a windows service (where I’d been testing it as a local console app).

By default, the working directory for a windows service is `C:/System32/`. Because I was manually configuring log4net (passing in `FileInfo`, it was looking in the working directory for the log4net config file, which wasn’t there.

I updated my code to use an executable relative path and the problem as solved.

Don’t forget to remove the trace configuration once you find your problem!
