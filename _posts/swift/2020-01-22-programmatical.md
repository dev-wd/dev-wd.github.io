---
title: "Swift programmatic UI (Auto Layout)"
date: 2020-01-22 13:30:03 -0400
layout: post
categories: swift
---

When I first study iOS, I wonder which tool should I use between storyboard, programmatic UI, and SwiftUI.

Because I am looking for work, I did not choose swiftUI. __Because swiftUI do not available below iOS 12__, most of company feel difficulty to adapt it now.

So, I used to develope by storyboard at first. However, when I want to initiate ViewController from coordinator, it is too complex to get reference IBOutlet from storyboard.

This moment is the time I feel __the limitation of storyboard__. 

  
From now on, I will make UIButton programmatically.

After following up example, it is important to __make your own code repeatedly__ without referencing the example.

Additionally, __fully understanding the parameters on methods__ are necessary as well.

## UIButton Example

#### Create UIButton and set the frame size

All code references are written from donggeun's blog. (Some of codes are modified)

[Code Reference](https://baked-corn.tistory.com/36)

```swift
class ViewController:UIViewController {
    let myButton = UIButton()
    override func viewDidLoad(){
        super.viewDidLoad()
        
        myButton.setTitle("This is Button", for: .normal)
        myButton.setTitleColor(.white, for: .normal)
        myButton.backgroundColor = .darkGray
        myButton.frame = CGRect(x: 0, y: 0, width: 200, height: 200)
        self.view.addSubview(myButton)
    }
}
```

- __Internal setting__ : Set the button title, title color, background color.
-  __Size setting__ : Set frame size with CGRect
-  __Add in View setting__ : Add button as a subview on superview.

Above settings are set in order. Especially, setting frame size means absolute size.

However, for devices optimizing, it is good to make the size relatively. 

#### Set constraints for UIButton
As we do on storyboard, we can set the constraint on ViewController.

It is important to __set `UIView.translatesAutoresizingMaskIntoConstraints = false`__ for using constraints.

UIButton __anchors should be active__ to set constraints.


```swift
class ViewController:UIViewController {
    let myButton = UIButton()
    override func viewDidLoad(){
        super.viewDidLoad()
        
        myButton.setTitle("This is Button", for: .normal)
        myButton.setTitleColor(.white, for: .normal)
        myButton.backgroundColor = .darkGray
        self.view.addSubview(myButton)
            
        myButton.translatesAutoresizingMaskIntoConstraints = false
        myButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        myButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
        myButton.widthAnchor.constraint(equalToConstant: view.bounds.width*0.5).isActive = true
        myButton.heightAnchor.constraint(equalToConstant: view.bounds.height*0.5).isActive = true
    }
}
```
- __Internal setting__ : Set the button title, title color, background color.
-  __Size setting__ : Set frame size with CGRect
-  __AutoresizingMask setting__: for preventing collision between storyboard ui and code ui.
-  __Constraint setting__: Set constraints X,Y, width, height by using Anchors.

X, Y, width, height is set following percentage from superview. 


#### Add action to UIButton


```swift
class ViewController:UIViewController{
    /*
        code
    */
    override func viewDidLoad(){
        super.viewDidLoad()
        /*
            code
        */
        myButton.addTarget(self, action: #selector(btnClicked),
            for: .touchUpInside)
    }
    
    @objc func btnClicked(){
        let alert = UIAlertController(title: "Click!"
            , message: "You clicked that button"
            , preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "Confirm"
            , style: .default , handler: nil))
        present(alert, animated: true, completion: nil)
    }
}
```
- __Add target on button__ : `#selector` supports method excution. 
- __Write btnClicked() method__ : make obj-c method for selector, set UIAlertController, and UIAlertAction
- __Present AlertController__ : present alert

## Conclusion
We studied to develop code based Autolayout UI.

It is important to fully understand detail methods from apple documentation.
Those methods like UIControl which is super class of UIButton and UILabel, AutiresuzingMask, CGRect 
is difficult to fully explain here.

My following article should be about that.

Thank you for your reading :)
