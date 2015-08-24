---
layout: post  
title:  printable enum snippet   
date:   2015-8-22 21:31:00  
categories: snippet  
---
This sample code will make println powerful to print out enum value to debugging window.
 
{% highlight swift %}
enum eeSpaceOrLine:Int , Printable {  case sSpace = 0 , sLine, sBoth
    var description:String{ // for println
        get{
            switch self{
            case .sSpace: return "space only"
            case .sLine : return "line only"
            case .sBoth: return "space and line"
            }
        }
    }
}
{% endhighlight %}

usage

{% highlight swift %}
let option:eeSpaceOrLine =  eeSpaceOrLine(rawValue:0)!
println("option Value is [\(option)]")
{% endhighlight %}

The result will be : `option Value is [space only]`
