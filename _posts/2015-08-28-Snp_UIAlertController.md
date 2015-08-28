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

  let realm = RLMRealm.defaultRealm()
  realm.beginWriteTransaction()
  realm.deleteAllObjects()
  realm.commitWriteTransaction()
    
  self.loadingCellContents()
  self.filterCells()
  self.tableView.reloadData()
})
alertController.addAction(deleteAction)

let okAction = UIAlertAction(title: "Cancel", style: UIAlertActionStyle.Default, handler: {(alert :UIAlertAction!) in
//          println("OK button tapped")
})
alertController.addAction(okAction)

presentViewController(alertController, animated: true, completion: nil)


{% endhighlight %}

usage

{% highlight swift %}
just use it.
{% endhighlight %}
