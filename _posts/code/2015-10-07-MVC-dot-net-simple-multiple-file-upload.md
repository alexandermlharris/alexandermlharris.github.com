---
layout: post
title: MVC.Net simple multiple file upload
category : development
tags : [mvc.net, c#, web development]
---
{% include JB/setup %}

One of our webapps allows for attaching a file when creating a new “Widget”. We don’t do anything fancy here and simply use a HttpPostedFileBase field on the view model. Overall the relevant bits look like this:

### View Model

    public class WidgetCreateVM
    {
        public HttpPostedFileBase Document { get; set; }
    }

### View

    @Html.TextBoxFor(model => model.Document, new { type = "file" })

### Controller

    [HttpPost]
    public ActionResult Create(WidgetCreateVM vm)
    {
        if(vm.Document != null)
        {
            // Do stuff
        }
    }

## Change Request
There was a task to allow attaching multiple files rather than just the single one, and I wasn’t sure of the best approach. I’d used fancy javascript multi-file upload utilities in the past, but I wanted something simpler (for now). A search around turned up [this article](http://haacked.com/archive/2010/07/16/uploading-files-with-aspnetmvc.aspx/) from Phil Haack where he shows how easy it is.

As he puts it:

> In this scenario, we want to upload a set of files. We can simply have multiple file inputs all with the same name.
> 
> …
> 
> Now, we just tweak our controller action to accept an IEnumerable ofHttpPostedFileBase instances.

So, in my case all I did was:

* In the view, added a button with some javascript to append a new `<input>` element to the form, making sure it had the same name attribute. You could easily do this with knockout (or a similar library) in a more elegant way.
* Update the view model property to an `IEnumerable<HttpPostedFileBase>` and update the code appropriately.
* The model binder / value providers / mvc magic takes care of the rest!

### Watch the gotcha
It’s worth noting that any file inputs which are added to the form but *not used* (no file picked) will come through as null elements in the `IEnumerable` and will be in form order.

Make sure your code handles this and finds all the valid files from the view model.