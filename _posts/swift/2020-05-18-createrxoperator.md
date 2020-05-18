---
title: RxSwift 02, Creating Operators (of, just, from, create..) with example code
date: 2020-05-18 21:14:23 -0400
layout: post
categories: swift
---

# RxSwift 02, Creating Operators (of, just, from, create..) with example code

Personally, I thought I understood RxSwift well, but it is still hard to adapt various operators on projects.

It is neccessary to arrange all of operators for using freely!

This posting is for __beginer who want to know the concept of RxSwift operator__.

## What is Operator and when do we need to use?

Operator supports operation on stream.
We usually operate Observables after subscription from observer.

However, Sometimes it is neccesary to __calculate or create Observables before subscription.__

Additionally, using operator is __more intuitive and giving us clean code__ compare than only rely on calculating (actially it is impossible.. ) after subscription  before subscription.


__Operator gives you a lot of extensions to manage stream of RxSwift!__

## Kind of Operators

- __`Creating Observables`__
- Transforming Observables
- Filtering Observables
- Combining Observables
- Error Handling Operators
- Observable Utility Operators
- Conditional and Boolean Operators
- Mathematical and Aggregate Operators
- Connectable Observable Operators

Today, we will __study about Operators for creating observable!__

Observable creation is the most basic action on RxSwift. 

There are various operators to create observable.

You can use these __depend on situations.__

It is good to know well about those three operators __`of`, `just`, `from`__ which are often used.

I will show more operator like __`range`, `repeatElement`, `timer`, `interval`__ for creating observable which are not often used but for reference as also.
 

## Create Operator

Using __`create()`__ is the basic way to create Observabale.

For using __`create()`__, you should define all of events manually.

Altough it is hardest way to create Observable, Advantage of using __`create()`__ is that you can define custom logic for Observable.

From __`observer -> Disposable`__, we can predict __input of Observable is Observer__.

When you __subscribe()__ Observable, observer would be binded.

You can define events which should be emit to observer by adding __`onNext()`, `onError()`, `onComplete()`__ behind observer.


```swift
let ob = Observable<Int>.create{ observer -> Disposable in
            observer.onNext(1)
            return Disposables.create()
        }
```

#### Execution
```
1
```
## Of Operator

__`of()`__ operator is simple form of emitting observable events in order.

```swift
let ob = Observable.of(1,2,3,4,5)
```
In this case, This observable emits 5 events.

Using __Of__ operator do same action as below code. 

```swift
let ob = Observable<Int>.create { observer -> Disposable in
            for i in 1...5 {
                observer.onNext(i)
            }
            observer.onCompleted()
            return Disposables.create()
        }
```

From above code, observable emits 5 events by using __`onNext()`__.

This code is kind of often used form, RxSwift supports simple form of this logic by supporting __`of operator`__ .

#### Execution
```
1
2
3
4
5
```
## Just Operator
__`just()`__ just emits a event in __`()`__

```swift
let ob = Observable.just(1)
```

It is same logic of `create()` example which we saw at first.


#### Execution
```
1
```
## From Operator
__`from()`__ converts various objects like __collection types into observables.__

```swift
let ob = Observable.from([1,2,3,4,5])
```

You can see the input is array.

It emits element of array in order.

#### Execution
```
1
2
3
4
5
```
Then how does it action for other collection types?

This is the example of dictionary


```swift
let ob = Observable.from([1:1, 2:2, 3:3, 4:4, 5:5])
```

As you can see below, element of dictionary is emitting in order.

You can use __`from()`__ operator for all collection types like these things.
 
#### Execution
```
(key: 2, value: 2)
(key: 4, value: 4)
(key: 3, value: 3)
(key: 1, value: 1)
(key: 5, value: 5)
```

## Range Operator
From now, you might predict the action..

__`range()`__ creates an Observable that emits a range of sequential integers

```swift
let ob = Observable.range(start: 1, count: 5)
```

#### Execution
```swift
1
2
3
4
5
```
## RepeatElement Operator
__`repeatElement()`__ is  hot observable, emitting same event repeatly to observer.


```swift
let ob = Observable.repeatElement(1)
```

#### Execution
```
1
1
1
1
...
```

## Timer Operator
__`timer()`__ gives defined delayed time.

you can also define the scheduler for streaming.

It is not hot observable and __`onComplete()`__ after emitting a event.

```swift
let ob9 = Observable<Int>.timer(4, scheduler: MainScheduler.instance)
```

#### Execution
```swift
0
```
## Interval Operator
__`interval()`__ gives defined interval time between each events.

you can also define the scheduler for streaming.

```swift
let ob = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
```

#### Execution

```swift
0
1
2
3
4
...

```

## Conclusion

I have never used __`Timer`, `ReapetElement`, `Interval`, `Range`__.

However, I try to use and practice all of operator. 

The reason is for reference which I should use for coding clean code on unusal situation.

If you are first time to study operator, you don't need to understand all. 

Hope this posting is helpful ☺️


