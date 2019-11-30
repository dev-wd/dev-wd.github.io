---
title: "Swift Async Programming @escaping closure, and  Completion handler"
date: 2019-12-02 20:46:32 -0400
layout: collection
collection: swift
categories:
  - swift
  
---


When I am using HTTP connection for login system , I should catch the end point of getting data. 

As you know, HTTP connection is done asychronously, I cannot use return value for this case.

I have used Future function in Flutter, but I spent some time to find what is same role in swift.

Escape closure supports @escaping property.
If you add this property after the name of argument(:), this recieve  'Completion handler' which supports sending async task completion state to other side and sending the return value as well.

If @escaping  property is not exist after argument, then this is Nonescape Closure.
If you do not clarify @escaping property, Nonescape property is default.

Nonescape property don't support completion handler.


Because we have used Nonescape closure so far, I will just focus on showing escape closure, and completion handler.




I arranged 2 cases which I used to do.

- Completion Handle with TrailingClosure
- Completion Handle with DeclaredClosure

There are getFruitColor example below.

If you send parameter on getFruitColor function with completionHanlder, then completionHanlder prints the color of given fruit.

- FULL Code : https://github.com/dev-wd/simple_swift_example/blob/master/exscape_closure/ViewController.swift


## Case 1. Completion Handle with TrailingClosure

- Implement case is implemented in 'viewdidload'.

- 'completion' is completionHandler, it should get '@escaping'
- you don't need to send 'completion' parameter, add trailing closure instead.




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

Let's completion handle by completionHandler from now on!



### Reference
- Swift Programming 2 by YAGOM
