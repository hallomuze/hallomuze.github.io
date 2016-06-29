---
published: true
title: ScrollView 101 ( Xcode 7 , Swift 2 )
layout: post
---

ScrollView + Autolayout + Storyboard!
Zoom functional

```swift

class ViewController: UIViewController {
    
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var scrollView: UIScrollView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        scrollView.backgroundColor = UIColor.blackColor()
        
        scrollView.contentSize = imageView.bounds.size
        
        scrollView.maximumZoomScale = 5.0
        
        scrollView.delegate = self
    }
  
 }

extension ViewController:UIScrollViewDelegate{
    
    func viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? {
        return imageView
    }
    
}
```