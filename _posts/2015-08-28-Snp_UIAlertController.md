---
layout: post
title:  snippet UIAlertController 101
date:   2015-08-28 08:31:00
categories: snippet
---
This code makes "Delete All confirmation" Alert Popup.
 
{% highlight swift %}
let alertController = UIAlertController(title: "Please Confirm", message: "Delete all messages?", preferredStyle: UIAlertControllerStyle.Alert)

let deleteAction = UIAlertAction(title: "Delete", style: UIAlertActionStyle.Destructive, handler: {(alert :UIAlertAction!) in
{% endhighlight %}

usage: just use it. 
