
# Selector

Selector 는 함수를 가르키는 것?

```swift
class Figure {
    @objc var color: UIColor = .blue
    
    @objc func draw() {
        print("draw")
    }
}

var selector = #selector(Figure.draw)

```
주로 특정 함수에 event를 처리 할때 사용할수 있다. 

#selector를 쓰기위해서는 해당 함수 앞에 @objc가 붙어야 한다.

@objc 는 class 내부에서만 사용이 가능하므로, struct 에서는 사용할수 없다. 


# KeyPath String Expression

```swift
class Person: NSObject {
    @objc let name = "Jane Doe"
    @objc var age: Int = 0
}

let p = Person()

p.value(forKey: "name")  // 오타가 생길수 있ㄷ.

p.value(forKey: #keyPath(Person.name))

let key = #keyPath(Person.name)    
```

keyPath는 주로 Key Value Observing 에서 사용 되어진다.

keyPath 가 있기 전에는 문자열로 값을 전달했다. 이 과정에서 오타가 있을 수 있고 이는 크래쉬로 이어진다.

그래서 나온게 #keyPath 이며 컴파일 시점에 오류를 확인하기 때문에 크래쉬를 방지 할수있다. 

# Keypath Expression 

KeyPath String Expression와 Keypath Expression의 차이 

|Keypath String Expression|Keypath Expression|
|:------:|:-----:|
|String|AnyKeyPat, PartialKeyPath, KeyPath, WritableKeyPath, ReferenceWritableKeyPath|
|클래스만 지원|클래스, 구조체 지원|
|NSObject 상속, @objc 특성 추가|-|
|컴파일 타임체크|컴파일 타임체크, 제네릭타입, 다양한 키패스 조합|


```swift
struct Person2 {
    let name = "Jane Doe"
    var age: Int = 0
}

let name2: KeyPath<Person2, String> = \Person2.name  
let age2: WritableKeyPath<Person2, Int> = \Person2.age


var p2 = Person2()

let nameValue = p2[keyPath: name2]
let ageValue = p2[keyPath: age2]

p2[keyPath: age2] = 0

```
Keypath Expression는 구조체에서도 사용이 가능하고, @objc 를 붙일 필요도 없다.

#keyPath 와 \. 리턴타입이 다르기 때문에 사용하는곳에 똑같을려나...?
