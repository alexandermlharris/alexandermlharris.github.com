---
layout: post
category : development
tags : [command line]
---
{% include JB/setup %}

I recently had to extract data from one of our databases via a batch process, breaking the export into single “days” of data due to the query timeout limit.

This left me with around 180 .csv files, one per day of data, which I needed to stich back together.

The thought of 180 “Copy + Paste” operations filled me with dread and I was reaching to google for “Windows File Concatenate” when my boss pointed out the following little gem:

```console
type *.csv >> combined.csv
```

Run this from the command line and it’ll pipe 

* the content of each `.csv` file (`*.csv`),
* appending it (`>>` instead of `>`) to `combined.csv`. 

So quick and easy, but the much maligned command line is often overlooked!
