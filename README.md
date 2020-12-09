
# Property Wrapper

Property Wrapper는 클래스의 멤버 프로퍼티를 감싸는 것으로 여러 맴버 변수가 같은 방식으로 값을 처리 할 때 유용 할 수 있다.

```swift
struct PlayerSetting {
    var initialSpeed: Double {
        get {
            return UserDefaults.standard.double(forKey: "initialSpeed")
        }
        set {
            UserDefaults.standard.setValue(newValue, forKey: "initialSpeed")
        }
    }
    var supportGesture: Bool {
        get {
            return UserDefaults.standard.double(forKey: "supportGesture")
        }
        set {
            UserDefaults.standard.setValue(newValue, forKey: "supportGesture")
        }
    }
    
}

```
상위 PlsyerSetting 의 멤버변수들은 UserDefaults의 값을 저장 하는 것이다.<br>
현재는 initialSpeed, supportGesture 2개 밖에 없어 괜찮지만, 맴버 변수가 늘어 날수록 반복 되는 코드는 길어지고, 유지보수에도 문제가 있다.

그렇기 때문에 ProperWrapper가 있다.

```swift
@propertyWrapper
struct UserDefaultsHelper<Value> {
    let key: String
    let defaultValue: Value
    
    var wrappedValue: Value {
        get {
            return UserDefaults.standard.object(forKey: key) as? Value ?? defaultValue
        }
        set {
            UserDefaults.standard.setValue(newValue, forKey: key)
        }
    }
}
```
propertyWrapper를 만들기 위해선 클래스 선언부 앞에 @PropertyWrapper를 추가해주고, 멤버 변수 wrappedValue를 반드시 추가해주어야한다.<br>

그럼 맨위에 있는 클래스 PlayerSetting의 맴버변수들을 PropertyWrapper로 바꾸어보자.

```swift
struct PlayerSetting {
    @UserDefaultsHelper(key: "initialSpeed", defaultValue: 0)
    var initialSpeed: Double
    @UserDefaultsHelper(key: "supportGesture", defaultValue: true)
    var supportGesture: Bool
}
```

맴버 변수 선언 앞에 propertyWrapper로 선언한 클래스 UserDefaultsHelper 앞에 @를 붙여 <br>

맴버변수앞에 @UserDefaultsHelper를 적으면 된다.

> 1. UserDefaultsHelper (propertyWrapper)의 wrappedValue의 타입은 initialSpeed 타입하고 같아야 함
> 2. PlayerSetting 인스턴스를 생성하면 맴버변수 위에있는 UserDefaultsHelper(key:, defaultValue:)가 맴버 변수 갯수만큼 호출
> 3. PlayerSetting 인스턴스의 initalSpeed는 propertyWrapper의 wrappedValue를 통해 값을 읽고 쓸 수 있음. 

<br>
PlayerSeeting의 initialSpeed의 타입은 Double 이다. <br>하지만 특정 조건에 따라 Double이 아닌 UserDefaultsHelper 타입으로 쓸 수 있고 <br>

<b>2가지 방법이 있다.</b>

```swift
@propertyWrapper
struct UserDefaultsHelper<Value> {
    .....
    func reset() {                       // 1
        UserDefaults.standard.setValue(defaultValue, forKey: key)
    }
    
    var projectedValue: Self { return self }   // 2
}

struct PlayerSetting {
    ....
    func resetAll() {                   // 1
//     initialSpeed.reset()
       _initialSpeed.reset()

    }
}
```
### 1
> 주석 added된 함수를 추가하고 실행 해보자. <br>
> initialSpeed는 타입이 Double이기 때문에 reset 함수를 호출 할 수 없다. <br>
> UserDefaultsHelper 타입으로 형 변환이 필요한데 변환 할필요 없이 이미 var _initialSpeed: UserDefaultsHelper가 추가 되어 있는것을 확인 할 수있다.

```swift
var currentSetting = PlayerSetting()
currentSetting.$initialSpeed = 5
// currentSetting._initialSpeed = 5
```

### 2
> _변수 는 접근제어가 private 이기때문에 같은 클래스 내부가 아니면 쓸 수 없다.
> $로 접근 할수 있는데 $로 접근해주기 위해선 projectedValue를 추가 해줘야 한다.



