---
title: "Hit Testing in Swift"
date: 2020-01-27 14:07:10 -0400
layout: post
categories: swift
---


My previous postings are [Responder chain](https://dev-wd.github.io/swift/responderchain/), and [UIControl](https://dev-wd.github.io/swift/uicontrol/). 

These things are all about __understanding internal event handling.__ Today, let us know the last thing of event handling __Hit test__

From previous posts, we studied __first responder__ (child of UIResponder) which manages events. 

We studied kind of events as well.

However, how event can be occured?

Hit Testing is __finding the view in which touch event is occured.__

Hit testing makes it by finding superview on that position.

Superview means not a UIResponder class on the hierarchy of UIKit, but a view on the stack of View layer.

View can have two relationship

- Parent and child
- Sibling

Of course, child is superview of parent view on the stack of layer, but then who is superview between siblings?

```swift
self.view.addSubview(viewA)
self.view.addSubview(viewB)
self.view.addSubview(viewC)
```
viewA, viewB, viewC are all child view of `self.view`.

In this case, order of __addSubview()__ decides superview. 
First defined view is superview.

## Process of finding superview by Hit testing

We knew how to decide superview, let us know the process of finding superview by hit testing in order.


<img src="/images/hittest/hittest.png" width="600" height="300">

From the above figure, viewA, viewB, viewC is in relation of sibling. Between those three views, viewC depth is the most.

1. At first, UIWindow which is highest class of UIKit hierarchy call `hitTest(_:with:)` method.
After that, `point(inside:with:)` method judge whether the point which event is occured is in UIWindow or not. Because it is true, return true and check from `MainView` which is the subview of UIWindow.

2. MainView also call `hitTest(_:with:)` method and repeats the previous process. Of course it is true as well, return true and check viewC(highest depth) first between subclasses.

3. However, touched position is not in viewC. Therefore, `point(inside:with:)` method return false, and then `hitTest(_:with:)` method return nil.  As a result, omit the subclasses of viewC and check viewB which is the next highst depth view.

4. Beceause viewB pass the check from `hitTest(_:with:)` and `point(inside:with:)`, check would be procceeded. Highest depth of sibling between viewB subViews is viewB.2. viewB.2 should be checked first.

5. As same as process 3, because viewB.2 do not include the touch point, pass viewB.2 and check viewB1.

6. Finally the check of viewB.1 is procceeded and as same process as the above, ViewB.1 is choosed touched superview.

After finishing above process, View which is detected by hit testing is given opportunity to handle events as __first responder__.

## The things which can do with hit testing.
Before studying what can we do by hit testing, let us know `jotTest(_:with:)` method from Apple developer Doocumentation first.
##### Declaration
```swift
func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView?
```

Returns the farthest descendant of the receiver in the view hierarchy (including itself) that contains a specified point.

##### Parameters
`point` : A point specified in the receiver's local coordinate system(bounds).

`event` : The event that warranted a call to this method. If you are calling this method from outside your event-handling code, you may specify nil.


We already know some options of controlling UI which are isUserInteractionEnabled, isHidden, alphal

These options can be helpful to customize hitTest function.

```swift
override func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
    if !isUserInteractionEnabled || isHidden || alpha <= 0.01 {
        return nil
 
    }
 
    if self.point(inside: point, with: event) {
        for subview in subviews.reversed() {
            let convertedPoint = subview.convert(point, from: self)
            if let hitTestView = subview.hitTest(convertedPoint, with: event) {
                return hitTestView
 
            }
        }
 
        return self
    }
 
    return nil
}

```
Here is extension of  hit test.
From now on, we modify it for customizing hit testing.

### 1. Widen touch area
If you do not set the area from hit test, touching area is same as the size of UIView by defualt.

Because we change condition __from pooint area to the scope of `techRect` boundary__ , size 10 is widen here.

```swift
override func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
    if !isUserInteractionEnabled || isHidden || alpha <= 0.01 {
        return nil
    }
 
    let touchRect = bounds.insetBy(dx: -10, dy: -10)
    if touchRect.contains(point) {
        for subview in subviews.reversed() {
            let convertedPoint = subview.convert(point, from: self)
            if let hitTestView = subview.hitTest(convertedPoint, with: event) {
                return hitTestView
            }
        }
 
        return self
    }
 
    return nil
}

```

It is useful when a button is too small to touch, developer could make the touch area widen.

### 2. Pass touch event 
When we adding addSubView from ViewController, __we cannot give touch event below the superview.__

```swift
override func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
    let hitTestView = super.hitTest(point, with: event)
    if hitTestView == self && touchPassEnable {
        return nil
    } else {
        return hitTestView
    }
}

```

### 3. Send touch event to subview
It is similar with above example.
However we want to give touch event directly from superview to subview.

Change return value from nil to specific subview.

```swift
override func hitTest(_ point: CGPoint, with 
event: UIEvent?) -> UIView? {
    let hitTestView = super.hitTest(point, with: event)
    if hitTestView == self && touchPassEnable {
        return self.targetView
    } else {
        return hitTestView
    }
}
```

## Conclusion

This time, we studied hit testing, relation between hit testing and responder chain, customizing hit testing depend on situations.


## Reference
- [[ios] Hit Testing in iOS](https://baked-corn.tistory.com/128)
- [iOS/swift. hitTest 이용하기](https://mrgamza.tistory.com/526)
