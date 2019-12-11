---
title: "Applying UITableview with RxSwift in various situations"
date: 2019-11-24 20:32:03 -0400
layout: post
categories: swift
---

Applying UITableview with RxSwift in various situations is an difficult issue for junior developer including me.

Of course, there are other ways to implement these tableview. Nevertheless, it is the one of answers 


I hope my example codes and explanation help you to save your time.

- FULL Code : [LINK] (https://github.com/dev-wd/tableview_with_rxswift_example)


### Fruit 3 cases example
- Case 1. TableView + data

- Case 2. TableView + textfield in cell

- Case 3. Tableview + button event in cell


## Case 1. TableView + data

<img src="/images/rxswift_tableview/example1.png" width="300" height="600">

Showing fruit name and image on tableview.

### Declared variable
I used 'BehaviorRelay' in RxCocoa for fruit variable.

'BehaviorRelay' is a subject that requires an initial value and emits its current value to new subscribers

It supports an initial value, and can be easily bind to tableview. Initial value will support tableview data.

Since TableView IBOutlet supports custom tableview size, I usually don't use 'UITableViewController'. 
 

```swift 

class FruitEX1ViewController: UIViewController, UITableViewDelegate{
    @IBOutlet weak var tableRef: UITableView!
    
    var fruitDic: BehaviorRelay<[[String : String]]> =
        BehaviorRelay(value:
            [["name":"apple"],
             ["name":"banana"],
             ["name":"cherries"],
             ["name":"grapes"],
             ["name":"lemon"],
             ["name":"orange"],
             ["name":"strawberry"],
             ["name":"tomato"]])
    
    var disposeBag = DisposeBag()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        inputTableView()
    }
    ...
}
```

### TableView + RxSwift
Setting tableview delegate is not neccessary. In this case, this setting supports tableview cell height.

BehaviorRelay which I mentioned can be used as Observable. You just add 'asObservable()' after the BehaviorRelay variable, and bind it with tableview.

Below tableview ReactiveX form supports reusable cell, so you should care about reusing cell issues what you treated on tableview.

We need to make disposeBag empty with '.disposed(by: disposeBag)'
If you don't add this command, rxBinding will be remained on memory.


```swift

func inputTableView() {
        
    // Set tableview delegate. (for setting table view cell height)
    tableRef.rx
        .setDelegate(self)
        .disposed(by: disposeBag)
        
    // Bind fruit dictionary and tableview
    fruitDic.asObservable()
        .bind(to: tableRef.rx
            .items(cellIdentifier: "Cell", cellType: FruitEX1Cell.self))
        { index, element, cell in
                
            // Write image, name for cell label.
            cell.fruitImage.image = UIImage(named: element["name"]!)
            cell.fruitNameLabel.text = element["name"]
    }.disposed(by: disposeBag)
        
    tableRef.tableFooterView = UIView()
}
     
```

## Case 2. TableView + textfield in cell

 <span style="color:red">This example has recycling issue. I will fix it as soon as possible.</span>
 
<img src="/images/rxswift_tableview/example2_1.png" width="300" height="600">

Showing number Label and textfield on tableview. If you put the fruit name on textfield, and then you will see the images of each fruits.

<img src="/images/rxswift_tableview/example2_2.png" width="300" height="600">

Here are the images of fruit which you typed on previous page.


You know previous example just send data from tableview to cell. 

The most different thing from example 1 is 
that we need input stream from cell as well.

Let us know about the details below.


### Declared variable

Other variables are same, but we declare two more BehaviorRelay Dictionaries.

BehaviorRelay basically supports only one-direction event. In this example, we cannot use a BehaviorRelay for both input and output.

So use two FruitDic for each input and output.

```swift

class FruitEX2ViewController: UIViewController , UITableViewDelegate{
    @IBOutlet weak var tableRef: UITableView!
    @IBOutlet weak var nextPageButton: UIButton!
    
    
    var firstFruitDic: BehaviorRelay<[[String : String]]> =
        BehaviorRelay(value:
            [["number":"1.","name":""],
             ["number":"2.","name":""],
             ["number":"3.","name":""],
             ["number":"4.","name":""],
             ["number":"5.","name":""],
             ["number":"6.","name":""],
             ["number":"7.","name":""],
             ["number":"8.","name":""]])
    
    var updatedFruitDic: BehaviorRelay<[[String : String]]> =
        BehaviorRelay(value:
        [["number":"1.","name":""],
         ["number":"2.","name":""],
         ["number":"3.","name":""],
         ["number":"4.","name":""],
         ["number":"5.","name":""],
         ["number":"6.","name":""],
         ["number":"7.","name":""],
         ["number":"8.","name":""]])
    
    var disposeBag = DisposeBag()
    var temDic : [[String: String]] = []
    ...
}
    
```

### TableView + RxSwift

Binding rule is same with previous one.

We should focus on two points here.

Change the value on temDic and accept it from updatedFruitDic. This updatedFruitDic will show you images.


 ```swift 
func inputTableView() {
        
   // Set tableview delegate. (for setting table view cell height)
   tableRef.rx
      .setDelegate(self)
       .disposed(by: disposeBag)
        
   // Bind fruit dictionary and tableview
   fruitDic.asObservable()
       .bind(to: tableRef.rx
           .items(cellIdentifier: "Cell", cellType: FruitEX1Cell.self))
       { index, element, cell in
                
           // Write image, name for cell label.
           cell.fruitImage.image = UIImage(named: element["name"]!)
           cell.fruitNameLabel.text = element["name"]
   }.disposed(by: disposeBag)
        
   tableRef.tableFooterView = UIView()
}
 ```




Dispose cell diseposebag for each cell

As you know, Tableview reuses cell for memory saving. That's why you must erase cell data. 

It's not critial on this, but critial issue for example3 like the case erasing cell.

Also, you should 'prepareForReuse' on your cell like below.

```swift 
// Make disposbag empty for reusing cell.
override func prepareForReuse() {
    super.prepareForReuse()
    bag = DisposeBag()
}
```
 
 
 
## Case 3. Tableview + button event in cell


<img src="/images/rxswift_tableview/example3.png" width="300" height="600">

Showing fruit name, image, and remove button on tableview.

If you click 'Remove' button, then cell will be removed from tableview.

### Declared variable
Because tableview will not get input from cell, We don't need multiple Dictionary here.


```swift 
class FruitEX3ViewController: UIViewController, UITableViewDelegate {
    @IBOutlet weak var tableRef: UITableView!
    
    var fruitDic: BehaviorRelay<[[String : String]]> = BehaviorRelay(value:
        [["name":"apple"],
         ["name":"banana"],
         ["name":"cherries"],
         ["name":"grapes"],
         ["name":"lemon"],
         ["name":"orange"],
         ["name":"strawberry"],
         ["name":"tomato"]])
    
    var disposeBag = DisposeBag()
    ...
}
    
```
### TableView + RxSwift


Dispose cell diseposebag for each cell

As you know, Tableview reuses cell for memory saving. That's why you must erase cell data. 

It's critical issue here.

Also, you should put 'prepareForReuse' on your cell like below. 

```swift 
// Make disposbag empty for reusing cell.
override func prepareForReuse() {
    super.prepareForReuse()
    bag = DisposeBag()
}
```
 
 As same with example2, tempDic is accepted by fruitDic. 
 
 you can remove from tempDic from the remove button event of each cells.
 

 
```swift 
func inputTableView() {
        
    // Set tableview delegate. (for setting table view cell height)
    tableRef.rx
        .setDelegate(self)
        .disposed(by: disposeBag)
        
    // Bind fruit dictionary and tableview.
    fruitDic.asObservable()
        .bind(to: tableRef.rx
            .items(cellIdentifier: "Cell", cellType: FruitEX3Cell.self))
        {index, element, cell in
                
            // Write image, name for cell label.
            cell.fruitImage.image = UIImage(named: element["name"]!)
            cell.fruitNameLabel.text = element["name"]
                
            // Remove cell by cancel button.
            cell.cancelButton.rx.tap
               .subscribe(onNext: { [weak self] in
                    let curIndex = index
                    var tempDic = self!.fruitDic.value
                    tempDic.remove(at: curIndex)
                    self!.fruitDic.accept(tempDic)
                    print("ViewModel.QRDid: ",self!.fruitDic.value)
                })
                .disposed(by: cell.bag)
    }.disposed(by: disposeBag)
        
    tableRef.tableFooterView = UIView()
}
```

## In the end

I feel hard to deal with various situation.

- Reusing cells on tableview

- RxSwift trait like one direction event 

- Nested RxSwift Handling

These reasons makes RxSwift + Tableview difficult. 

Of course there can be exist a better way, but as a junior developer, I want to show you what makes you mistakes easily about this subject.

I hope this helps you:)
