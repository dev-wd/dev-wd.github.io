---
title: "Applying rxSwift to tableview in various situations"
date: 2019-11-24 20:32:03 -0400
layout: collection
collection: swift
categories:
  - swift
  
---

Applying RxSwfit on tableview in various situations is an issue for junier developer like me.

Of course, their are several other ways to implement these tableview. It's not the answer, but one of way.


I hope you save time with these both example code and explanation.

- Overall Code : https://github.com/dev-wd/tableview_with_rxswift_example


### Fruit 3 cases example
- Case 1. TableView + data

- Case 2. TableView + textfield in cell

- Case 3. Tableview + button event in cell


## Case 1. TableView + data

이미지 1

Showing fruit name and image on tableview.

### Declared variable
I used 'BehaviorRelay' in RxCocoa for fruit variable.

'BehaviorRelay' is A Subject that requires an initial value and emits its current value to new subscribers

It support initaial value ,and can be easily bind to tableview. Initial value will support tableview data.

Since TableView IBOutlet support custom tableview size, I usually don't use 'UITableViewController'. 
 

```swift 

@IBOutlet weak var tableRef: UITableView!
    
    var fruitDic: BehaviorRelay<[[String : String]]> = BehaviorRelay(value:[["name":"apple"],["name":"banana"], ["name":"cherries"],["name":"grapes"],["name":"lemon"],["name":"orange"],["name":"strawberry"],["name":"tomato"]])
    var disposeBag = DisposeBag()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        InputTableView()
    }
    
```

### TableView + RxSwift
Setting tableview delegate is not neccessary. In this case, this setting supports tableview cell height.

BehaviorRelay which I mentioned can be used as Observable. You just add 'asObservable()' after the BehaviorRelay variable, and bind it with tableview.

Below tableview rx form supports reusable cell, so you should care about reusing cell issues what you treated on tableview.

To solve the problem of reuesing cell, we need make disposeBag empty with '.disposed(by: disposeBag)'
If you don't add this command, rxBinding can make error.



```swift

func InputTableView() {
        
        // Set tableview delegate. (for setting table view cell height)
        tableRef.rx.setDelegate(self).disposed(by: disposeBag)
        
         // Bind fruit dictionary and tableview
        fruitDic.asObservable().bind(to: tableRef.rx.items(cellIdentifier: "Cell", cellType: FruitEX1Cell.self))
        {index, element, cell in
            // Write image, name for cell label.
            cell.fruitImage.image = UIImage(named: element["name"]!)
            cell.fruitNameLabel.text = element["name"]
        }.disposed(by: disposeBag)
        
        tableRef.tableFooterView = UIView()
    }
    
    
```

