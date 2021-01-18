---
layout: post
category : development
tags : [winforms]
---
{% include JB/setup %}

I was adding some features to a bare-bones WinForms app which I’m fiddling and wanted to add place-holder (cue) text to a text box so as not to need labels. Having been doing a lot of web-dev, this has become a fairly trivial operation in my day to day, but I realised I had no idea how to achieve it in WinForms.

# The Winforms way

A quick google turned up [this question over on Stack Overflow](https://stackoverflow.com/questions/11873378/adding-placeholder-text-to-textbox). This shows that there’s not an out-of-the box WinForms solution (no designer property which does this), but can be done by handling the `OnFocus` and `LoseFocus` events. For example, see the following psudo-code from the [accepted answer.](http://stackoverflow.com/a/11873521/15667)

```csharp
Textbox myTxtbx = new Textbox();
myTxtbx.Text = "Enter text here...";

myTxtbx.OnFocus += OnFocus.EventHandle(RemoveText);
myTxtbx.LoseFocus += LoseFocus.EventHandle(AddText);

public RemoveText(object sender, EventArgs e)
{
    myTxtbx.Text = "";
}

public AddText(object sender, EventArgs e)
{
    if(myTxtbx.Text == "")
        myTxtbx.Text = "Enter text here...";
}
```

# The dirty way

This seemed a little clunky for what I’d hoped would be a one liner. [Another answer](http://stackoverflow.com/a/22405509/15667) had a simpler solution which I ended up using. It’s possible to achieve this in a couple of lines by using the Windows `SendMessage` function to set the *cue text* (as it refers to it).

First, declare the external function from the windows .dll:

```csharp
private const int EM_SETCUEBANNER = 0x1501;

[DllImport("user32.dll", CharSet = CharSet.Auto)]
private static extern Int32 SendMessage(IntPtr hWnd, int msg, int wParam, [MarshalAs(UnmanagedType.LPWStr)]string lParam);
```

Then, call this function passing in the handle of the text box in question and the text to send, as follows:

```csharp
SendMessage(textBox1.Handle, EM_SETCUEBANNER, 0, "Username");
SendMessage(textBox2.Handle, EM_SETCUEBANNER, 0, "Password");
```
