---
title: "Swift Async Programming @escaping closure, and  Completion handler"
date: 2019-12-02 20:46:32 -0400
layout: post
categories: swift
---



When I use HTTP connection for login system , I should catch the end point of getting data. 

As you know, when HTTP connection is done asychronously, you should not use return value for this case.

I have used Future function in Flutter, but I had struggle to find the similar function in swift.

Escape closure supports @escaping property.
If you add @escaping property after the name of argument(:), escape closure recieves 'Completion handler' which supports sending async task completion state to other side and sending the return value as well.

If @escaping  property is not exist after argument, then this is Nonescape Closure.
If you do not add @escaping property, Nonescape property is default.

Nonescape property does not support completion handler.


Because we have used Nonescape closure so far, I just focus on showing escape closure, and completion handler.




I arranged 2 cases which I used to do.

- Completion Handle with TrailingClosure
- Completion Handle with DeclaredClosure

There are getFruitColor examples below.

If you send parameter on getFruitColor function with completionHanlder, then completionHanlder prints the color of given fruit.

- FULL Code : https://github.com/dev-wd/simple_swift_example/blob/master/escape_closure/ViewController.swift


## Case 1. Completion Handle with TrailingClosure

- Implement case is implemented in 'viewDidLoad()'.

- 'completion' is completionHandler which includes '@escaping' property.
- you don't need to send 'completion' parameter, but add trailing closure instead.




```swift

// case 1 : Completion Handle with TrailingClosure
    
    func getFruitColorwithTrailingClosure(_ fruitName : String, completion: @escaping (String)-> Void) {
        
        var color = "Not yet"
        
        if fruitName == "banana" {
            color = "yellow"
        }
        
        if fruitName == "apple" {
            color = "red"
        }
        
        if fruitName == "melon" {
            color = "green"
        }
        
        completion(color)
    }
    
    
// Implement case 1
        
        getFruitColorwithTrailingClosure("banana") {
            color in
            
            print(color)
        }
        
        
```



## Case 2. Completion Handle with DeclaredClosure


- Declare closure for completionHandler.
- Because you declared closure first, you don't need to add trailing closure when you implement getFruitColor function.

```swift
// Declare closure for case 2.
    
    let getColorClosure : (String) -> Void = {
        color in
        print(color)
    }
    
    
 // case 2 : Completion Handle with DeclaredClosure
    
func getFruitColorwithClosure(_ fruitName : String, completion: @escaping (String)-> Void) {
        var color = "Not yet"
        
        if fruitName == "banana" {
            color = "yellow"
        }
        
        if fruitName == "apple" {
            color = "red"
        }
        
        if fruitName == "melon" {
            color = "green"
        }
        
        completion(color)
        
    }
    
     // Implement case 2
        
        getFruitColorwithClosure("melon",completion: getColorClosure)
    
```

## Conclusion

Asynchoronous function's end state can be catched by EscapingClosure.

Do not return the value on your service API.

Let's get completion state by completionHandler from now on!



### Reference
- Swift Programming 2 by YAGOM
