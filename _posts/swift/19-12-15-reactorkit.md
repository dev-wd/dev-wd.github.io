---
title: "ReactorKit Example, Let us study the flow of Reactor"
date: 2019-12-15 20:15:01 -0400
layout: post
categories: swift
---


I often have heard that ReactorKit usefully give structure of RxSwift.

Because I am junior developer, I am not sure that I can learn ReactorKit at first. However, I realized that Reactor pattern is similar with many other patterns. I heard that it is same pattern with Redux, and additionally I can know it is also similar with Bloc Pattern in Flutter as well.

Because I used to use flutter bloc in flutter, It is possible to learn at once for me.


## Concept
<img src="/images/reactorkitexample/reactorkit4.png" width="800" height="400">
__ref__ : github.com/ReactorKit/ReactorKit

In this image, Reactor's role is like state manager. As you guys know, When the action comes asynchoronously from View, there exists delay to complete the action and to make a state. 

Mutate() process action, convert to mutation, and send the mutation to reduce() function. Because reduce() return state which is converted from mutation, we can reflect state from the reactor immedietly on View.

Furthermore, Some cases we need to bind servies api on reactor to show view state.
In this case, mutate() send state to the view based on action, and service. Mutate() also can send state to service as well.

From the reactor, Action is input, and state is output. these action (input) and state (output) is stream, and we can implement it with RxSwift. 


Next, Here are one-line explaination for each reactor functions from [ReactorKit](https://github.com/ReactorKit/ReactorKit)

- __mutate()__ :  receives an Action and generates an Observable<Mutation>.
- __reduce()__ : generates a new State from a previous State and a Mutation.
- __transform__ :  transforms each stream. There are three transform() functions:


My examples are modified version of [ReactorKit counter example](https://github.com/ReactorKit/ReactorKit/tree/master/Examples/Counter), and I will explain the example which I modified above.

- FULL Code : [LINK](https://github.com/dev-wd/simple_swift_example/tree/master/reactorKit)

Let's dive into the example.

## Fruit Example

It is smiple example which can show fruit image when the user clicks the fruit named button.

<img src="/images/reactorkitexample/reactorkit3.png" width="300" height="600">

When the user clicks the orange, then reactor immediatly changes the state as 'loading' until downloading image(In this example, it just give 500 miliseconds time delay).



<img src="/images/reactorkitexample/reactorkit2.png" width="300" height="600">

 If image finished to download image, then state changed to 'orange' and view will show the orange image.
 
<img src="/images/reactorkitexample/reactorkit1.png" width="300" height="600">
 
 
This example has two classes
 
- FruitViewController
- FruitViewReactor
 
 
## FruitViewController


### Declared Variable

As I mentioned above, there are five fruit buttons.

Additionally, we binds FruitViewReactor on ViewController.


```swift
class FruitViewController: UIViewController {

    @IBOutlet weak var appleButton: UIButton!
    @IBOutlet weak var grapesButton: UIButton!
    @IBOutlet weak var bananaButton: UIButton!
    @IBOutlet weak var cherriesButton: UIButton!
    @IBOutlet weak var orangeButton: UIButton!
    
    @IBOutlet weak var fruitImage: UIImageView!
    var disposeBag = DisposeBag()
    
    let fruitReactor = FruitViewReactor()
    ...
}

```

### Bind Reactor

Here are the function which binded with FruitViewReactor.

Each button IB is tapped by RxSwift, and sends transformed event which is 'reactor action' to the binded RruitViewReactor.

```swift
class FruitViewController: UIViewController {

   ...
    override func viewDidLoad() {
        super.viewDidLoad()
        
        bind(reactor: fruitReactor)
    }
    
    func bind(reactor: FruitViewReactor) {
        
        appleButton.rx.tap
            .map{ FruitViewReactor.Action.apple }
            .bind(to: reactor.action)
            .disposed(by: disposeBag)
        
        grapesButton.rx.tap
            .map{ FruitViewReactor.Action.grapes }
            .bind(to: reactor.action)
            .disposed(by: disposeBag)
        
        bananaButton.rx.tap
            .map{ FruitViewReactor.Action.banana }
            .bind(to: reactor.action)
            .disposed(by: disposeBag)
        
        cherriesButton.rx.tap
            .map{ FruitViewReactor.Action.cherries }
            .bind(to: reactor.action)
            .disposed(by: disposeBag)
        
        orangeButton.rx.tap
            .map{ FruitViewReactor.Action.orange }
            .bind(to: reactor.action)
            .disposed(by: disposeBag)
        
        ...
}
```

Above one is reactor's input, but this one is output of the reactor.

State of reactor send to viewcontroller.
After subscribing Reactor state from the viewcontroller, viewcontroller would set the fruit image with the state value.
Because the state value is same as image name, the fruit image can be shown by UIImageView.




```swift
class FruitViewController: UIViewController {

    ...
    
    func bind(reactor: FruitViewReactor) {
        
       ...
        reactor.state.map { $0.fruitName }
          .distinctUntilChanged()
          .map { "\($0)" }
            .subscribe(onNext: { val in
                self.fruitImage.image = UIImage(named: val)
            })
          .disposed(by: disposeBag)
        
        reactor.state.map { $0.isLoading }
        .distinctUntilChanged()
          .subscribe(onNext: { val in
            if val == true{
                self.fruitImage.image = UIImage(named: "loading")
            }
          })
        .disposed(by: disposeBag)

    }
}
```


## Fruit Reactor
Until now, We saw the input and output streams from the view. Next, we can dive into the reactor's internal side which converts action(event) to state.


### Declared Variable
we should declare action and mutation by enum.

In this example, Reactor gets action as kind of fruits, and converts as mutation which  is asking the fruit image.

State is declared as struct which is initialized by the mutation.  



```swift
final class FruitViewReactor: Reactor {

    // Action
    enum Action {
        case orange
        case apple
        case cherries
        case banana
        case grapes
    }
    
   
    enum Mutation {
        case orangeImage
        case appleImage
        case cherriesImage
        case bananaImage
        case grapesImage
        case setLoading(Bool)
    }
    
   // State
    struct State {
        var fruitName: String
        var isLoading: Bool
    }
    
    let initialState: State
    
    init() {
        self.initialState = State(
            fruitName: "",
            isLoading: false
        )
    }
    
   ...
}
```
### Action -> Mutation


As I mentioned above, I give 500 miliseconds delay during occuring mutation of fruit image. 

Concat operater in RxSwift send serialized events, and those events are adding as group after previous event observed. 

Because it is between 'setLoading' mutation, mutate() can send loading state to the view during the delay. 

```swift
final class FruitViewReactor: Reactor {
    ...
    
    // Action -> Mutation
    func mutate(action: Action) -> Observable<Mutation> {
        switch action {
        case .orange:
            return Observable.concat([
                Observable.just(Mutation.setLoading(true)),
                Observable.just(Mutation.orangeImage).delay(.milliseconds(500), scheduler: MainScheduler.instance),
                Observable.just(Mutation.setLoading(false)),
                
            ])
        case .apple:
            return Observable.concat([
                Observable.just(Mutation.setLoading(true)),
                Observable.just(Mutation.appleImage).delay(.milliseconds(500), scheduler: MainScheduler.instance),
                Observable.just(Mutation.setLoading(false)),
            ])
        case .cherries:
            return Observable.concat([
                Observable.just(Mutation.setLoading(true)),
            Observable.just(Mutation.cherriesImage).delay(.milliseconds(500), scheduler: MainScheduler.instance),
                Observable.just(Mutation.setLoading(false)),
            ])
        case .banana:
            return Observable.concat([
                Observable.just(Mutation.setLoading(true)),
                Observable.just(Mutation.bananaImage).delay(.milliseconds(500), scheduler: MainScheduler.instance),
                Observable.just(Mutation.setLoading(false)),
            ])
        case .grapes:
            return Observable.concat([
                Observable.just(Mutation.setLoading(true)),
                Observable.just(Mutation.grapesImage).delay(.milliseconds(500), scheduler: MainScheduler.instance),
                Observable.just(Mutation.setLoading(false)),
            ])
        }
    }
    
    ...
}
```
### Mutation -> State

reduce() returns the state which image name in assets is stored.

this return value is given to viewcontroller UIimageview.image. Finally, viewcontroller will show the image because of this return value.


```swift
final class FruitViewReactor: Reactor {
    ...
    
    // Mutation -> State
    func reduce(state: State, mutation: Mutation) -> State {
        var state = state
        switch mutation {
            
        case .orangeImage:
            state.fruitName = "orange"
        case .appleImage:
            state.fruitName = "apple"
        case .cherriesImage:
            state.fruitName = "cherries"
        case .bananaImage:
            state.fruitName = "banana"
        case .grapesImage:
            state.fruitName = "grapes"
        case .setLoading(let val):
            state.isLoading = val
        }
        return state
    }
}
```
## Conclusion

Because I also studing with example, I can make mistake or omit some details. Feel free to comment me what you want to modify from this example and explaination.
