## 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언
> 코틀린은 주(primary)생성자와 부(secondary)생성자를 구별하고, 초기화 블록(initializer block)을 통해 초기화 로직을 추가할 수 있다.
<br>

### 4.2.1 클래스 초기화: 주 생성자와 초기화 블록
- `주생성자(primary constructor)`: 생성자 파라미터를 지정하고, 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의한다.
```kotlin
class User constructor(_nickname: String) {
  val nickname: String
  
  init {
    nickname = _nickname
  }  
}
```
- `constructor`: 주생성자나 부생성자 정의를 시작할 때 사용하는 키워드 <br>
- `init`: 초기화 블록을 시작하는 키워드
  - 초기화 블록에는 클래스의 객체가 만들어질 때(= 인스턴스화 될 때) 실행될 초기화 코드가 들어간다.
  - 초기화 블록은 주생성자와 함께 사용되며 주생성자는 별도의 코드를 포함할 수 없기 때문에 초기화 블록이 필요하다.
  - 초기화 블록을 여러 개 두는 것도 가능하다.
<br>

- 위의 예제에서는 nickname 프로퍼티를 초기화하는 코드를 nickname 프로퍼티 선언에 포함시킬 수 있어서 초기화 코드를 초기화 블록에 <br>
넣을 필요 없다. 또한 주생성자 앞에 별다른 annotation이나 가시성 변경자가 없으면 constructor 키워드를 생략해도 된다.
```kotlin
class User(_nickname: String) {
  val nickname = _nickname //프로퍼티를 주생성자의 파라미터로 초기화
}
```
<br>

- 주생성자 파라미터 이름 앞에 val을 추가해 프로퍼티 정의와 초기화를 간략히 할 수 있다.
```kotlin
class User(val nickname: String)
```
<br>

- 생성자 파라미터에도 디폴트 값을 정의할 수 있다.
```kotlin
class User(val nickname: String,
           val isSubscribed: Boolean = true)
```
```kotlin
>>> val hyun = User("현석") //isSubscribed 파라미터에 디폴트 값
>>> println(hyun.isSubscribed)
true

>>> val gye = User("계영, false) //모든 인자를 파라미터 선언 순서대로 지정
>>> println(gye.isSubscribed)
false

>>> val hey = User("헤원", isSubscribed = false) //생성자 인자 중 일부에 대해 이름 지정
>>> println(hey.isSubscribed)
false
```
<br>

 - 기반 클래스가 있다면 주생성자에서 기반 클래스의 생성자를 호출해야 한다. <br>
    - 기반 클래스를 초기화하려면 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘긴다.
```kotlin
open class User(val nickname: String) { ... }
class TwitterUser(nickname: String) : User(nickname) { ... }
```
<br>

- 클래스를 정의할 때 별도로 생성자를 정의하지 않으면 컴파일러가 자동으로 아무일도 하지 않는, 인자가 없는 디폴트 생성자를 만든다.
```kotlin
class RadioButton: Button()
```
→ Button의 생성자는 아무 인자도 받지 않지만, Button 클래스를 상속한 하위 클래스는 반드시 Button 클래스의 생성자를 호출해야 한다. <br>
→ 위의 규칙때문에 기반 클래스의 이름 뒤에는 반드시 빈 괄호가 들어간다.(생성자 인자가 있으면 괄호 안에 인자가 들어간다.) <br>
  반면 인터페이스는 생성자가 없기 때문에 상위 클래스 목록에 있는 인터페이스 이름 뒤에는 괄호가 없다.
<br>
<br>

- 어떤 클래스를 외부에서 인스턴스화하지 못하게 하고 싶다면, 모든 생성자를 `private`으로 지정하면 된다. 
```kotlin
class Secretive private constructor() {} //Secretive 클래스의 주생성자는 비공개이므로, 외부에서 Secretive를 인스턴스화할 수 없다.
```
<br>
<br>

### 4.2.2 부생성자: 상위 클래스를 다른 방식으로 초기화
- 일반적으로 코틀린에서 생성자가 여러 개 있는 경우가 자바보다 훨씬 적은데, 이는 자바에서 오버로드한 생성자가 필요한 상황 중 상당수는 <br>
  코틀린의 **디폴트 파라미터 값**과 **이름 붙인 인자** 문법을 사용해 해결할 수 있기 때문이다. <br>
  <br>
  
> 생성자가 여러 개 필요한 경우
1. 프레임워크 클래스를 확장해야 하는데 여러 가지 방법으로 인스턴스를 초기화할 수 있도록 다양한 생성자를 지원해야 하는 경우
```kotlin
open class View {
  constructor(ctx: Context) { //부생성자
    //...
  }
  constructor(ctx: Context, attr: AttributeSet) { //부생성자
    //...
  }
}
```
```kotlin
class MyButton : View {
  constructor(ctx: Context) //부생성자
    : super(ctx) { //상위 클래스의 생성자 호출
      //...
  }
  constructor(ctx: Context, attr: AttributeSet) //부생성자
    : super(ctx, attr) { //상위 클래스의 생성자 호출
      //...
  }
}
```
→ 주생성자 없이 2개의 `부생성자`를 가지는, View의 하위 클래스 MyButton (부생성자는 constructor 키워드로 시작) <br>
→ 두 부생성자는 **super()** 를 통해 각자 대응하는 상위 클래스 생성자를 호출한다. <br>
`MyButton의 constructor(ctx: Context)` → `View의 constructor(ctx: Context)` <br>
`MyButton의 constructor(ctx: Context, attr: AttributeSet)` → `View의 constructor(ctx: Context, attr: AttributeSet)`
<br>
<br>

```kotlin
class MyButton : View {
  constructor(ctx: Context) : this(ctx, MY_STYLE) { //this()를 통해 클래스 내 다른 생성자 호출
    //...
  }
  constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr) { 
    //...
  }
}
```
→ 생성자에서 **this()** 를 통해 클래스 내의 다른 생성자를 호출할 수도 있다. <br>
　첫 번째 생성자는 두 번째 생성자를 호출하고 있고 두 번째 생성자는 여전히 super를 통해 상위 클래스인 View에서 상응하는 생성자를 호출하고 있다.
`constructor(ctx: Context) : this(ctx, MY_STYLE)` → `constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr)` → `constructor(ctx: Context, attr: AttributeSet)`
<br>

- 클래스에 주생성자가 없으면, 모든 부생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자를 호출해야 한다. <br>
→ 결국 부생성자의 호출 경로를 따라 가다 보면 그 끝에는 반드시 상위 클래스 생성자를 호춣해야 한다.
<br>
<br>

### 4.2.3 인터페이스에 선언된 프로퍼티 구현
- 인터페이스에 추상 프로퍼티 선언을 넣을 수 있다.
```kotlin
interface User {
  val nickname: String //추상 프로퍼티
}
```
→ User interface를 구현하는 클래스가 nickname의 값을 얻을 수 있는 방법, 즉 뒷받침하는 필드나 getter 함수 등을 구현해야 한다.
<br>
<br>
<br>

```kotlin
class PrivateUser(override val nickname: String) : User

class SubscribingUser(val email: String) : User {
  override val nickname: String
    get() = email.substringBefore('@') //custom getter
}

class FacebookUser(val accountId: Int) : User {
  override val nickname = getFacebookName(accountId) //프로퍼티 초기화
}
```
```kotlin
>>> println(PrivateUser("test@kotlinlang.org").nickname)
test@kotlinlang.org

>>> println(SubscribingUser("test@kotlinlang.org").nickname)
test
```
- `PrivateUser`
  - 주생성자 안에 프로퍼티 직접 선언
  - nickname 프로퍼티는 User의 추상 프로퍼티를 구현하고 있는 것이므로, **override**를 표시해줘야 한다.
- `SubscribingUser`
  - nickname 프로퍼티를 custom getter로 설정
  - 필드에 따로 값을 저장해두지 않고 이메일 주소를 받아 매번 nickname을 구해 반환한다.
- `FacebookUser`
  - 초기화 식을 활용해 nickname 값 초기화
  - getFacebookName()이 다른 곳에 구현되어 있다고 가정하고 accountId를 이용해 nickname을 반환해준다. 
> **SubscribingUser와 FacebookUser의 nickname 구현 차이** <br>
SubscribingUser의 nickname은 **custom getter**를 활용해 호출될 때마다 substringBefore를 호출해 계산하는데 비해, <br>
FacebookUser의 nickname은 객체 초기화 시 계산한 데이터를 필드에 저장했다 불러오는 방식을 사용한다.
<br>
<br>

- 인터페이스에는 추상 프로퍼티뿐만 아니라 getter와 setter가 있는 프로퍼티를 선언할 수도 있다.
```kotlin
interface User {
  val email: String
  val nickname: String
    get() = email.substringBefore('@') //프로퍼티에 뒷받침하는 필드 대신 매번 결과를 계산해 돌려준다.
}
```
→ 추상 프로퍼티인 email과 custom getter가 있는 nickname 프로퍼티 <br>
→ 하위 클래스에서 추상 프로퍼티인 eamil은 반드시 오버라이드해야 하지만, nickname은 오버라이드하지 않고 상속할 수 있다.
