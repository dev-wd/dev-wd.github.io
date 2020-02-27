---
title: "What is difference between init and convenience init in SwiftS"
date: 2020-02-27 18:08:41 -0400
layout: post
categories: swift
---


To develope my own app, I have browsed others github swift code. From a extension for making UIColor possible to use hex, I found __`convenience init`__.

This is the reason I studied it.

```swift
extension UIColor {
    convenience init(hex: Int) {
        self.init(red: CGFloat((hex & 0xFF0000) >> 16) / 255.0, green: CGFloat((hex & 0x00FF00) >> 8) / 255.0, blue: CGFloat(hex & 0x0000FF) / 255.0, alpha: 1)
    }
}
```

They did not add those things on `init()` but on `convenience init()`.

Therefore, the question is..

## What is the defference between (designated) init and convenience init?

__Designated init__ is default initatializer in swift.

`Designated init` helps initializing all of properties in that class or structure.

It has a condition to work which should initiate __whole properties__.

Below example shows us well about it.

```swift
class Fruit {
    var name: String
    var color: String
    var price: Int
    init(name: String, color: String, price: Int) {
        self.name = name
        self.color = color
        self.price = price
    }
}
```

What if remove `self.price = price`?

<img src="/images/init/init.png" width="800" height="200">

Yes, compilier warns developer to initiate whole properties.

But then, how could you __initialize only specific properties?__

Yes, use __convenience init__

`convenience init` set some parameters of `designated init` which is given from developer as default value, and then initialize it.

From above sentence, you can predict `designated init` should declared first before declaring`convenience init`.

Additionaly, `convenience init` should call other initializer on same class.

Above things are __two important rules__ to use `convenience init`


By adding `convenience init` on Fruit class, developer can just type name and color.

Price will be fixed as 10.

```swift
class Fruit {
    var name: String
    var color: String
    var price: Int
    init(name: String, color: String, price: Int) {
        self.name = name
        self.color = color
        self.price = price
    }
    
    convenience init(name: String, color: String) {
        self.init(name: name, color: color, price: 10)
    }
}
```


## Conclusion
__Convenience init__ is good to use as extension for additional initializer based on default.

UIColor example which I show you first is usueful way to use it!



## Reference
- [Swift ) init과 convenience init의 차이](https://zeddios.tistory.com/141)

