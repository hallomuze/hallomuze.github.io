---
layout: post
title:  snippet UIAlertController 101
date:   2015-08-28 08:31:00
categories: snippet
---
This code makes "Delete All confirmation" Alert Popup.
 
Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
let alertController = UIAlertController(title: "Please Confirm", message: "Delete all messages?", preferredStyle: UIAlertControllerStyle.Alert)

let deleteAction = UIAlertAction(title: "Delete", style: UIAlertActionStyle.Destructive, handler: {(alert :UIAlertAction!) in
   #=>some code here
})
alertController.addAction(deleteAction)

let okAction = UIAlertAction(title: "Cancel", style: UIAlertActionStyle.Default, handler: {(alert :UIAlertAction!) in
    println("OK button tapped")
})
alertController.addAction(okAction)

presentViewController(alertController, animated: true, completion: nil)
{% endhighlight %}

usage: just use it. 
