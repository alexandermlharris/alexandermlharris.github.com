---
layout: post
title: SSRS Report Timespan Formatting
category : development
tags : [ssrs]
---

{% include JB/setup %}

A little while ago I was creating a SSRS report where one of the columns displayed how long the duration of the activity in question. This was extracted from the database simply enough as a whole number of seconds by a `DATEDIFF(SECONDS, StartDate, EndDate)`. However, a raw seconds total is less than useful to end users, so I needed to format it as `HH:MM:SS`.

Googling around, I came across [How to display a time span of seconds in hh:mm:ss format in Reporting Services](http://stackoverflow.com/questions/5332217/how-to-display-a-time-span-of-seconds-in-hhmmss-format-in-reporting-services) on Stack Overflow, which got me most of the way to what I needed. The only wrinkle was that the accepted answer didn't work how I needed for times longer than 24 hours, which I had several of.

I ended up finding several resources, some of which linked back to the SO question and pulled together a number of difference approaches. My original answer is on the SO question for future reference, but for reference, the different options are given here


## Simple Formula

Like [Chriss Latta's accepted answer](http://stackoverflow.com/a/5332844/15667), you can use a single formula on the field, such the following from [this thread][1], (which also links back to the SOquestion)!

```VB
=int(sum(Fields!Sec_Online.Value)/3600) & ":" & int((sum(Fields!Sec_Online.Value) Mod 3600)/60) & ":" & (sum(Fields!Sec_Online.Value) Mod 3600) Mod 60
```

If you need to pad each time part to 2 characters (I did) you can wrap a `RIGHT("0" & {X}, 2)` around each sub-section, where `{x}` indicates one of the individual calculations in the above formula. IE:

```VB
RIGHT("0" & sum(Fields!Sec_Online.Value)/3600), 2) & ...
```

This works great, but means duplicating your code if there is more than one field in the report which needs formatting this way.

## Code Behind

Another approach, also suggested in this thread, is to use `TimeSpan.FromSeconds`([doc][2]), and there is an implementation of that on this [blog][3], using custom code behind in the report.

I ended up using the custom code approach (as I had lots of fields which I needed to format in this way), and combining it with something more like the first method as I didn't want days to start appearing I just wanted hours to count up bigger than 23.

I added some [custom code][3] to the report as follows which pads all values to at least 2 characters, and allows hours to hours count up > 23.

```VB
Public Function ConvertSecondsToHourMinSec(ByVal intTotalSeconds) As String
    Dim hours As String =INT(intTotalSeconds/3600)
    If Len(hours) < 2 Then
        hours = RIGHT(("0" & hours), 2)
    End If
    Dim mins As String = RIGHT("0" & INT((intTotalSeconds MOD 3600)/60), 2)
    Dim secs AS String = RIGHT("0" & ((intTotalSeconds MOD 3600) MOD 60), 2)

    ConvertSecondsToHourMinSec = hours & ":" & mins & ":" & secs

End Function
```

and then called this from each cell in questions as follows:

```VB
=code.ConvertSecondsToHourMinSec(Fields!MyField.Value)
```

  [1]: http://social.msdn.microsoft.com/Forums/is/sqlreportingservices/thread/99eebfd7-4ba7-4242-ab5e-a327586694de
  [2]: http://msdn.microsoft.com/en-us/library/system.timespan.fromseconds.aspx
  [3]: http://www.myedocs.com/%5CKB%5CSSRS%20TimeSpan.aspx
