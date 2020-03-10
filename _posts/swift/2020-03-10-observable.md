---
title: RxSwift 01, What is Observable and Observer?
date: 2020-03-10 18:20:19 -0400
layout: post
categories: swift
---


For Arranging the concept of RxSwift, I decided to write series of __`Understanding rxswift`__ posting.

## Why do developers use RxSwift?
When developer develops app, it is neccessary to develope app as __`Reactive`__. 


Swift supports __`@escaping`__ handler to catch the end point of asynchoronous task.

For making task asynchoronously, we should use __`Dispatchqueue`__.   

__`willSet`__ and __`didSet`__ also makes  reactively observing specific variable possible.
 
These things are all you need to know and construct for __reactive programming__.

However, you can do reactive programming just by using RxSwift.


In my opinion, __advantage of using RxSwift__ is..

__RxSwift makes reactive code readable and short.__ ( You don't need to use those `Dispatchqueue`, `willSet`, and `DidSet`)
 
__RxSwift operators make stream extensive.__ 


Now, let us know the concept of RxSwift which support these advantages!


## Concept

For easy understanding I can show a example with youtube.

when a BJ create a personal channel, then it is Observable.

Then, a video viewer can be a observer if he subscribes specfific __video channel(Observable)__


<img src="/images/observable/observable1.png" width="800" height="150">

After video viewer(Observer) subscribes a channel of BJ(Observable), whenever new video of subscribed channel is uploaded, viewer can see the video from his recommanded video part on from now on.

Here, video is an event which is sending from channel(Observable) to subsrcibed viewer(Observer).

Therefore, an video (event of Observable) is shown on viewers(Observer), viewers(Observer) can action for each videos(event of subscribed Observable).

<img src="/images/observable/observable2.png" width="600" height="350">

In ReactiveX, an Observer subsribes an Observable. Then that observer eacts to whatever item or sequence of items the Observable emits.

 
Observable이 배출하는 하나 또는 연속되는 항목에 Observer는 반응한다.

## Example of Observable and Observer.
It is difficult to understand  both Observable and Observer only with concept.

Let us look example first.


<img src="/images/observable/observable3.png" width="550" height="430">

### Observer 
```swift
 checkIsEvenNumberObservable(items: [2,4,6,7,10])
 .subscribe{ event in
         switch event {
            case .next(let value):
              print(value)
            case .error(let error):
              print(error)
            case .completed:
              print("completed")
        }
}.disposed(by: bag)

```
You know now, Observer subscribes Observable.

__Closure after `.subscribe` method is Observer.__

__By forking switch cases (next, error, completed), Observer can implement actions for each cases.__

`print` is action here. 
 

### Observable
We can see Observable now.
You also can see __observer parameter__ in Observable.

Because we can know it from the internal code of RxSwift (you do not need to know it right now), __observer is closure after `.subscribe` method  from previous code.__ 



```swift
func checkIsEvenNumberObservable(items: [Int]) -> Observable<Int> {
    return Observable<Int>.create{ observer -> Disposable in
        for i in items {
            if i % 2 == 0 {
                observer.onNext(i)
            } else {
                 observer.onError(NSError(domain: "ERROR: value is not even.", code: 0, userInfo: nil))
            }
            sleep(1)
        }
        observer.onCompleted()
        return Disposables.create()
    }
```

From the above explaination, we can predict each `.onNext(i)`, `.onCompleted()`, `.onCompleted()` sent to observer as event.



We studied internal flow of rxswift between observable and observer.

Last thing what we should know is just each behavior protocol for sending event.
There are __onNext, onError, and onComplete__

### onNext
onNext is stream which emits one or sequences.
Because Observer still subscribes Observable.

Observer can do some action depend on events from Observabe.

### onError 
When Observerble gets error, it emits error and makes Observerble stream stopped. 

It is important to know you that stopped emittion by error is not same with completion.

Stream is not completed, and just stopped.

### onComplete
Observable emits event which means __completion of stream__, it is also same meaning that there are __no more next event to emit__ from Observable.

## Conclusion 
We will study `Disposable` next time.
Feel free to ask me if you have wondering.

Hope you guys get helped from this posting.


## Reference 
- [ReactiveX](http://reactivex.io/)
- [Understanding hot vs cold Observables](https://medium.com/@luukgruijs/understanding-hot-vs-cold-observables-62d04cf92e03)
- [RxSwift 알아보기(Observable에 대해서) - 02](https://magi82.github.io/ios-rxswift-02/)
- [RxSwift Internal- Let'Swift 2018](https://www.youtube.com/watch?v=DsE-sagI04A&t=767s)
