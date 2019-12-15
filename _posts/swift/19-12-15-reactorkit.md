---
title: "ReactorKit Example, Let us study the flow of Reactor"
date: 2019-12-15 20:15:01 -0400
layout: post
categories: swift
---
I often heard that ReactorKit usefully give structure of RxSwift.

Because I am junior developer, I am not sure that I can learn ReactorKit at first. However, I realized that Reactor pattern is similar with many other patterns. I heard that it is same pattern with Redux, and additionally I can know it is also similar with Bloc Pattern in Flutter as well.

Because I used to use flutter bloc in flutter, It is possible to learn at once for me.


## Concept
// reactorkit supported image

In this image, Reactor's role is like state manager. As you guys know, When the action comes from View,  there exists delay to complete the action and make a state for. 

Mutate() function treat Action and process  and pass it to reduce() function. Because reduce() send state from reactor to view, we can reflect state from the reactor immedietly on View.

Furhermore, Some cases we need to bind servies api on reactor to show view state.
In this case, mutate() occur state based on action, and service. Mutate() also can send data to service as well.

From the reactor, Action is input, and state is output. these action (input) and state (stream) is stream, and we can implement it with RxSwift. 


Next, Here are one-line explaination for each reactor functions from [ReactorKit](https://github.com/ReactorKit/ReactorKit)

- __mutate()__ :  receives an Action and generates an Observable<Mutation>.
- __reduce()__ : generates a new State from a previous State and a Mutation.
- __transform__ :  transforms each stream. There are three transform() functions:


My examples are modified version of [ReactorKit counter example](https://github.com/ReactorKit/ReactorKit/tree/master/Examples/Counter), and I will explain the example what I modified with.

- FULL Code : [LINK](https://github.com/dev-wd/simple_swift_example/tree/master/reactorKit)

Let's dive into the example.


## Fruit View

It is smiple example which can show fruit image when the user click the fruit named button.

// image 1

When we click the orange, then reactor immediatly change the state as 'loading' until downloading image(In this case, The example just give 500 milisecconds).



// image 2

 If image finish to download image, then state changed to 'orange' and view will show the orange image.
 
 // image 3
 
 
 
### Declared Variable

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
### Declared Variable
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
