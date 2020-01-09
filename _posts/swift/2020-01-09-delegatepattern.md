---
title: "Delegate pattern example in Swift"
date: 2020-01-09 15:28:23 -0400
layout: post
categories: swift
---


This time, let us study about __`delegate pattern`__ in swift.

In iOS, __`delegate pattern`__ is used many times including tableview, colelctionview.. etc.

When I first used it from tableview, because I have no understanding about protocols as well, it is like a magic.

Just adding `TableViewDelegate` makes it works.

So, my first question at that time is not `Why?`, but `How?`.

That's why I introduce you the example code of delegate pattern here.

First, we study the concepts, and then study the examples.


## Concepts

I will represent two classes as two people.
They are Amanda and Yulia.

<img src="/images/delegate/delegate1.png" width="450" height="350">

Amanda is packing boxes for delivery.
Because yulia manages addresses to deliver, amanda should ask address to Yulia.

<img src="/images/delegate/delegate2.png" width="450" height="350">

After get the address from Yulia, Amanda inform completion and results to Yulia.

<img src="/images/delegate/delegate3.png" width="450" height="350">

Amanda knows all process about packing box, but Yulia do not need to know whole proecess.

Because Amanda knows whole process, she ask some works (delegate protocol) to Yulia.

As a result, Yulia just gives addresses.

We do not want to know whole process when we develope tableview or collectionview.. 

We just be informed what to do for those views from TableView and CollectionView.

In Swift, ViewController is Yulia, and Both TableView and CollectionView are Amanda.


That is why we use delegate pattern often in swift.



## Examples

### UITableView
I want you to explain it by following the way which I studied.

In the tableview, 

```swift
class MyTableViewController : UIViewController, UITableViewDelegate {
	let myTableView: UITableView = UITableView() 
    override func viewDidLoad() {
      super.viewDidLoad()
	   self.myTableView.delegate = self
    }
    ...
}

```

Just typing `self.myTableView.delegate = self` and adding `UITableViewDelegate` makes us to use tableview.

Okay.. Delegate means do the work alternatively as you know,

What a magic by adding assigning self on the tableview variable delegate!

For further understanding, I dive into the internal of UITableView.

```swift
open class UITableView : UIScrollView, NSCoding, UIDataSourceTranslating {

    
    public init(frame: CGRect, style: UITableView.Style) // must specify style at creation. -initWithFrame: calls this with UITableViewStylePlain

    public init?(coder: NSCoder)
    
    weak open var dataSource: UITableViewDataSource?

    weak open var delegate: UITableViewDelegate?
    ...
}

```

Humn:open_mouth:..  delegate is declared on UITableView class which __type is UITableViewDelegate__.

Okay.. then What is UITableViewDelegate?


```swift
public protocol UITableViewDelegate : UIScrollViewDelegate {

    @available(iOS 2.0, *)
    optional func tableView(_ tableView: UITableView, willDisplay cell: UITableViewCell, forRowAt indexPath: IndexPath)

    @available(iOS 6.0, *)
    optional func tableView(_ tableView: UITableView, willDisplayHeaderView view: UIView, forSection section: Int)

    @available(iOS 6.0, *)
    optional func tableView(_ tableView: UITableView, willDisplayFooterView view: UIView, forSection section: Int)
    ...
}
```

Some methodes which are changable depend on situation is defined at delegate protocol.

 So, `self.myTableView.delegate = self` __fill the abstracted method__ from the class, and then it is __excuted from the UITableView which delegate property is declared__ on UITableView!!

I understand the flow.. but not fully understand.

For making my own delegate pattern, I wrote an example.

### AnimalNameShowDelegate

I want to store animal name on Animal class , and  show animal name. This should be proccessed on AnimalNameShowClass.

Overall functions are supported by Animal class, but the printing strings should be set by AnimalNameShowClass.

AnimalNameShowClass __do delegated works__ that setting animal name and printing animal name.



From AnimalNameShow, works of animal will be proceeded by __`let animal = Animal()`__

Just __`func resultOfAnimalName(value : String) `__ is filled by AnimalNameShowClass

```swift
class AnimalNameShowClass: AnimalNameShowDelegate {
    let animal = Animal()
    
    init() {
        animal.delegate = self
        animal.animalName = "lion"
        print("init")
    }
    deinit{
        print("deinit")
    }
    
    func resultOfAnimalName(value : String) {
        print("Animal name : \(value)")
    }
}
```
 
 In protocol, we declare method which code is supported by AnimalNameShowClass.
 
 By adding __`AnimalNameShowDelegate`__ protocol on __`AnimalNameShowClass`__, We can forcing the work to define from __`AnimalNameShowClass`__
 
 ```swift
 protocol AnimalNameShowDelegate: class {
    func resultOfAnimalName(value : String)
}
 ```

Animal class has the code about animals except for __`resultOfAnimalName`__.

```swift
class Animal {
    init() {
        print("init")
    }
    deinit{
        print("deinit")
    }
    var delegate: AnimalNameShowDelegate?
    var animalName : String {
        set {
            self.delegate?.resultOfAnimalName(value: newValue)
        }
        get {
            return "No Animal"
        }
    }
}

```


## Coupling Issues

## Conclusion




