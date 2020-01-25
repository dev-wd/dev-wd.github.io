---
title: Responder Chain and Touch Event : UIResponder, UIEvent,UIControl.
date: 2020-01-25 15:38:41 -0400
layout: post
categories: swift
---

My previous posting is about UIControl.

During studying UIControl.event ,I got additional question about event.

<img src="/images/responder/responder1.png" width="600" height="600">

__What is UIEvent?__

__What is UIResponder which includes UIViewController and UIView?__

I searched the word `UIResponder`, then I found out `Responder Chain and Touch Event`.

Humn.. I realized both `UIEvent` and `UIResponder` have __relation in responder chain__.

I decide to study Responder Chain, UIResponder, UIEvent accordingly.

## Responder chain
App manages events by using Responder Object. 
Responder objects are subclass instances of UIResponder which is UIView, UIViewController, UIApplication.

Responder object should manage events by itself or transfer to other responder object.

When a event is occured, app transfers the event to suitable resonder object which is called __first responder__

The way how to find suitable responder object can be known by __hit testing__(I will upload it as  following posting)

<img src="/images/responder/responder2.png" width="600" height="400">

Hit testing identifies the appropriate responder object, usually the lowest child in the hierarchy is first responder.

When a system event like a screen touched is detected, UIKit internally creates `UIEvent` instances and dispatches it to the __system event queue__ by calling `UIApplication.shared.sendEvent()`. When the event is retrieved from the queue, UIKit internally determines the first UIResponder capable of handling the event and sends it to the selected one(__hit testing__). The selection process differs depending on the event type - while touch events go directly to the touched view itself, other event types will be dispatched to the so called __first responder__.

For example, If assuming that an event has entered a UITextField, then if the UITextField does not handle the event, the event object is passed to the parent view, UIView. However, events that have not been processed to the end will be discarded.

we call this situation as __Responder chain__.

Okay then, laet us know about UIResponder and UIEvent. 


## UIResponder
Instance of UIResponder constructs __a backborn of App event management.__

Most of visualizing objects like __UIApplication, UIViewController, UIView, UIWindow__ are subclasses of  UIResponder.

Therefore, easily saying, __UIApplication, UIViewController, UIView, UIWindow__  can manage events whenever a event is occured.


## UIEvent
a single UIKit event that contains a type (touch, motion, remote-control and press) and an optional sub-type (like a specific device motion shake)

- __touch__ : The most common and are delivered to the view in which the touch originally occurred.
- __motion__ :  UIKit triggered and are separate from the motion events reported by the Core Motion framework.
- __remote-control__ :  allow a responder object to receive commands from an external accessory or headset so that it can manage manage audio and video.
- __press__ : interactions with a game controller, AppleTV remote, or other device that has physical buttons. 

These events can be determined by adding subtype `UIEvent.EventSubtype` on UIEvent.

## UIControl
I wonder the differ between UIEvent and UIControl. UIControl basic concepts are written on [my previous posting](https://dev-wd.github.io/swift/uicontrol/). UIControl is executed with __Target-Action mechanism__.

__Is UIControl.event totally independant from UIEvent?__

I found the internal code of UIControl.event. 

```swift
let event = UIEvent(...) //An UIKit-generated touch event containing the touch position and properties.
//Dispatch a touch event.
//Through `hitTest()`, determine which UIView was "selected".
//Because an UIControl was selected, directly invoke its target:
UIApplication.shared.sendAction(#selector(myMethod), to: myView, from: button, for: event)
```
As a result, we found out selecting uicontrol also invoked by hit test.
__UIControl is child of UIResponder__ and it can be first responder and accepts specific event which is define as UIControl.event.

After accepting specific event as UIControl.event, __Target-Action mechanism__ would be working.
Action could be supported by UIApplication `UIApplication.shared.sendAction(#selector(myMethod), to: myView, from: button, for: event)`

## Conclusion
Knowing UI event handling concepts on App really helps me to develope services.

Deeper understanding brings me to develope services with confidence.

My next posting would be about __Hit Testing__.

Thank you for reading my post :) 

## Reference
- [iOS Responder Chain: UIResponder, UIEvent, UIControl and uses](https://swiftrocks.com/understanding-the-ios-responder-chain.html)
- [[ios] Responder Chain and Touch Event](https://baked-corn.tistory.com/129)
- [UIEvent-Apple Developer](https://developer.apple.com/documentation/uikit/uievent)
