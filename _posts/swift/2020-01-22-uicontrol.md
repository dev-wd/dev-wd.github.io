---
title: "Understanding UIControl in Swift"
date: 2020-01-22 15:48:56 -0400
layout: post
categories: swift
---

When I study how to make button programmatically, I found to write __`UIControl.state`__ for setTitle and setTitleColor, and to write __`UIControl.event`__ for addTarget.

```swift 
 myButton.setTitle("This is button", for: .normal)
 myButton.setTitleColor(.white, for: .normal)
 myButton.backgroundColor = .darkGray
        
 myButton.addTarget(self, action: #selector(btnTapped), for: .touchUpInside)
 
 self.view.addSubview(myButton)
```

I wonder the relation between UIButton and UIControl. 

Recently, for studying programmatic UI in swift, it is necessary to know UIKit hierarchy classes. 

<img src="/images/uicontrol/uicontrol1.png" width="700" height="400">

UIButton is child of .. 

__`UIResponder -> UIView -> UIControl`__

Children of UIControl have special characteristics compare than childrens of UIView.

User can __control visual UI changes__, for example button can be clicked, and switch can be toggled.

UIControl supports it by __Target-Action__ mechanism. 

Now we can understand the definition on Apple developer documentation.

__The base class for controls, which are visual elements that convey a specific action or intention in response to user interactions__

From now on, let us study how __Target-Action mechainsm__ support user interaction like above, and then deep dive into properties what I said. 

`UIControl.event` `UIControl.state` properties make __Target-Action__ mechanism working. 

## Target-Action Mechanism
Instead of writing code to track touch events, you write action methods to respond to control-specific events.

It would be supported by the method __`addTarget(_: action:for:)`__.


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
        ...
    }
}
```
From this code, UIButton excutes `myButton.addTarget(self, action: #selector(btnClicked),
            for: .touchUpInside)` method which invokes action trough `btnClicked()`

As a result, if you add __target__ method, and then __action__ would be invoked when __UIcontrol.event `.touchUpInside` is satisfied.__ 

Okay we do not know UIControl.event yet. 
let's dive in.

## UIControl.event
Developer can choose which event invoke actions from target.


For example, if you add __`.touchUpInside`__(UIControl.event) on addTarget method, action will be invoke when you click inside of button. 

<img src="/images/uicontrol/uicontrol2.png" width="400" height="100">

Because there are so many kinds of UIControl.event, it will be better for you to find event which is adapt for your situation
 in apple document.
 
## UIControl.state
Apple document definite it constants describing the state of a control.

#### Characteristics
- UIControl can have multiple states.
- User can set different actions depend on states.

It will be effective to understand with code.

For example, setTitle can be differ depend on states.

```swift
button.setTitle("Button1", for: .normal)
button.setTitle("Button2", for: .selected)
```
From this code, before select the button it shows  "Button1" as title.

After select button, button shows "Button2" as title.

`.normal` and `.selected` is UIControl.state properties.

Especially, `.normal` is default property.
When the control have `.normal` property, other properties should be false. 

__The control which has `.normal` cannot have multiple states.__


As same as UIControl.event, because there are many other states, you can find those things from apple document as well.


## In the end

By studying UIControl, I realize UIKit hierarchy really supports for my understanding.


## Reference
- [UIControl-developer.apple.com](https://developer.apple.com/documentation/uikit/uicontrol)
- [[ios] UIControl-donggeun's blog](https://baked-corn.tistory.com/125)


