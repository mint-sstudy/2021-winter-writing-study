# Ch04 클래스, 객체, 인터페이스
## 📌 학습목표
```
• 클래스와 인터페이스
• 뻔하지 않은 생성자와 프로퍼티
• 데이터 클래스
• 클래스 위임
• object 키워드 사용
```
<br>
<br>

## 4.1 클래스 계층 정의
1. 코틀린에서 `클래스 계층`을 정의하는 방식을 자바와 비교해보고,
2. 코틀린의 `가시성`과 `접근 변경자`에 대해 살펴보며,
3. 코틀린에 새로 도입된 `sealed 변경자`에 대해 알아보자!
<br>
<br>

### 4.1.1 코틀린 인터페이스
- **코틀린 인터페이스**는 자바 8 인터페이스와 비슷한데, 코틀린 인터페이스에는 추상 메소드뿐만 아니라 구현된 메소드도 정의할 수 있다.  
  단, 인터페이스에는 아무런 필드도 들어갈 수 없다.
<br>
  
```kotlin
interface Clickable {
  fun click()
}
```
```kotlin
class Button : Clickable {
  override fun click() = println("I was clicked")
}

>>> Button().click()
I was clicked
```
-  자바의 extends와 implements 키워드 대신, 코틀린은 **클래스 이름** 뒤에 **콜론**을 붙이고 **인터페이스 또는 클래스 이름**을 적어   
   `인터페이스 구현`과 `클래스 확장`을 모두 핸들링할 수 있다.
- 클래스는 인터페이스를 개수 제한 없이 자유롭게 구현할 수 있지만, 클래스는 하나만 확장할 수 있다.
- 자바의 @Override annotation과 비슷한 코틀린의 `override 변경자`는 상위 클래스 또는 인터페이스에 있는 프로퍼티나 메소드를   
  오버라이드한다는 표시다.  
  → 코틀린에서는 실수로 상위 클래스의 메소드를 오버라이드하는 경우를 방지하기 위해 **override 변경자를 반드시 사용해야 한다!**
<br>
<br>

```kotlin
interface Clickable {
  fun click() //일반 메소드 선언
  fun showOff() = println("I'm clickable!") //default 구현이 있는 메소드
}
```
 → showOff()처럼 default 구현을 제공할 수도 있다.  
　자바는 default 키워드를 써줘야 하지만, 코틀린은 **default 키워드 없이** 메소드 본문을 적어주면 된다.
<br>
<br>
<br>

```kotlin
interface Focusable {
  fun setFocus(b: Boolean) = 
    println("I ${if (b) "got" else "lost"} focus.")
  fun showOff() = println("I'm focusable!")
}
```
→ 위와 같이 한 클래스에서 두 interface를 구현했는데 두 interface 모두 디폴트 구현이 있는 같은 이름의 메소드(showOff())가 존재하면, <br> 
둘 중 어느 쪽도 선택되지 않는다. 만약 상위 interface에 정의된 showOff()를 대체할 오버라이딩 메소드를 작성하지 않으면 컴파일러 오류가 발생한다. <br>
즉, 코틀린 컴파일러는 이런 경우 두 메소드를 아우르는 구현을 하위 클래스에 직접 구현하도록 강제한다.
<br>
<br>
<br>

```kotlin
class Button : Clickable, Focusable {
  override fun slick() = println("I was clicked")
  override fun showOff() { //하위 클래스에서 명시적으로 새로운 구현 제공
    super<Clickable>.showOff() //상위 ㅏㅌ입의 이름을 <> 사이에 넣어서 super 지정
    super<Focusable>.showOff()
  }
}
```
→ 이름과 시그니처가 같은 멤버 메소드에 대해 둘 이상의 디폴트 구현이 있는 경우, 인터페이스를 구현하는 하위 클래스에서 명시적으로 새로운 구현을 <br>
  제공해야 한다. <br>
→ 상위 타입의 이름을 <> 사이에 넣어 `super`를 지정하면 어떤 상위 타입의 멤버 메소드를 호출할지 지정할 수 있다.
<br>

```kotlin
override fun showOff() = super<Clickable>.showOff()
```
→ 상속한 구현 중 하나만 구현할 때는 위와 같이 쓸 수도 있다.
<br>
<br>
<br>

### 4.1.2 open, final, abstract 변경자: 기본적으로 final
> 상속을 제어하는 변경자인 open, final, abstract
- 상속이 가능하면 `fragile base class`라는 문제가 발생할 수 있다. <br>
→ 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우에 생긴다. 기반 클래스를 작성한 사람의 의도와 다른 방식으로 메소드를 오버라이드할 위험이 존재하는데, 모든 하위 클래스를 분석하는 것은 불가능하므로 기반 클래스는 이 점에서 취약하다.
- 코틀린의 클래스와 메소드는 기본적으로 `final`이다. 상속을 허용하려면 클래스 앞에 `open`을 붙이자. <br>
  또한 오버라이드를 허용하고 싶은 메소드나 프로퍼티 앞에도 `open` 변경자를 붙여야 한다.
```kotlin
open class RichButton : Clickable { //open
  fun disable() {} //final
  open fun animate() {} //open
  override fun click() {} //상위 클래스의 열려있는 메소드를 오버라이드한 것. 오버라이드한 메소드는 default로 open
}
```
→ 기반 클래스나 인터페이스의 멤버를 오버라이드하는 경우 그 메소드는 기본적으로 열려 있다.
<br>
<br>

- 코틀린에서도 클래스를 `abstract`로 선언할 수 있다.
  - `abstract`로 선언한 추상 클래스는 인스턴스화할 수 없다.
  - 추상클래스에는 추상 멤버가 있기 때문에 하위 클래스에서 그 추상 멤버를 오버라이드해야 하므로, 추상 멤버는 항상 열려있다(open). <br>
    따라서 `open` 변경자를 명시할 필요가 없다.
 <br>
 
- 클래스 내에서 상속 제어 변경자의 의미

|변경자|이 변경자가 붙은 멤버는...|설명|
|:------:|:-----------------:|:--------------------------------:|
|final|오버라이드할 수 없다.|클래스 멤버의 기본 변경자다.|
|open|오버라이드할 수 있다.|반드시 open을 명시해야 오버라이드할 수 있다.|
|abstract|반드시 오버라이드해야 한다.|추상 클래스의 멤버에만 붙일 수 있다.|
|override|상위 클래스나 상위 인스턴스의 멤버를<br> 오버라이드|오버라이드하는 멤버는 default로 open.<br> 하위 클래스의 오버라이드를 금지하려면 final을 명시해야 한다.|
<br>
<br>

### 4.1.3 가시성 변경자: 기본적으로 공개
> Visibility modifier는 코드 기반에 있는 선언에 대한 클래스 외부 접근 제어
- 코틀린의 가시성 변경자는 자바와 동일: `public`, `protected`, `private` <br>
- 자바의 가시성 변경자와의 차이점
  1. 변경자를 명시적으로 표시하지 않은 경우, 기본 가시성은 `public`
  2. 자바의 package-private을 대신해, 코틀린에는 `internal`이라는 새로운 가시성 변경자 추가
      - internal은 모듈 내부에서만 접근 가능함을 나타낸다.
      - module: 한 번에 컴파일되는 코틀린 파일들
  3. 최상위 선언에 대해 private 가시성(비공개 가시성)을 허용한다.(클래스, 함수, 프로퍼티 포함)
  4. 코틀린의 protected 멤버는 어떤 클래스나 그 클래스를 상속한 클래스 안에서만 보인다.
  5. 외부 클래스가 내부 클래스나 중첩된 클래스의 private 멤버에 접근할 수 없다.
<br>

- 코틀린의 가시성 변경자 정리

|변경자|클래스 멤버|최상위 선언|
|:----:|:-------------------:|:--------------------:|
|public(기본 가시성)|모든 곳에서 볼 수 있다.|모든 곳에서 볼 수 있다.|
|internal|같은 모듈 안에서만 볼 수 있다.|같은 모듈 안에서만 볼 수 있다.|
|protected|하위 클래스 안에서만 볼 수 있다.|최상위 선언에 적용할 수 없다.|
|private|같은 클래스 안에서만 볼 수 있다.|같은 파일 안에서만 볼 수 있다.|
<br>
<br>

### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스
> 클래스 중첩은 다른 클래스를 선언했을 때 도우미 클래스를 캡슐화하거나, 코드 정의를 그 코드를 사용하는 곳과 가까이에 두고 싶을 때 유용하다.
- 코틀린은 자바와 달리, 명시적으로 요청하지 않는 한 `중첩 클래스`는 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없다.
- 코틀린 중첩 클래스에 아무런 변경자가 붙지 않으면 자바의 static 중첩 클래스와 같다. <br>
→ 이 중첩 클래스를 **내부 클래스**로 바꿔 바깥쪽 클래스에 대한 참조를 포함하게 만들고 싶으면 `inner` 변경자를 붙여야 한다.
- **중첩 클래스(nested class) vs 내부 클래스(inner class)**
  - Inner class는 nested class와 달리, 외부 클래스(outer class)의 객체와 연결되어 있다.
- 자바와 코틀린의 중첩 클래스와 내부 클래스의 관계 정리

|클래스 B 안에 정의된 클래스 A|Java|Kotlin|
|:-------------------------------------:|:-----------------:|:------------------:|
|중첩 클래스(바깥쪽 클래스에 대한 참조를 저장하지 않는다.)|static class A|class A|
|내부 클래스(바깥쪽 클래스에 대한 참조를 저장한다.)|class A|inner class A|
<br>
<br>

```kotlin
class Outer {
  inner class Inner {
    fun getOuterReference(): Outer = this@Outer
  }
}
```
→ 코틀린에서 outer class의 인스턴스를 가리킬 때는 `this@Outer`라고 써야 한다.
<br>
<br>
<br>

### 4.1.5 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한
- **sealed 클래스**: 상위 클래스에 sealed 변경자를 붙이면 그 상위 클래스를 상속한 하위 클래스 정의를 제한할 수 있다.
  - sealed 클래스의 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 중첩시켜야 한다.
```kotlin
sealed class Expr { //기반 클래스를 sealed로 봉인
  class Num(val value: Int) : Expr() //기반 클래스의 모든 하위 클래스를 중첩 클래스로 나열
  class Sum(val left: Expr, val right: Expr) : Expr()
}

fun eval(e: Expr) : Int =
  when(e) { //when 식이 모든 하위 클래스를 검사하므로 별도의 "else" 분기가 없어도 된다.
    is Expr.Num -> e.value
    is Expr.Sum -> eval(e.right) + eval(e.left)
  }
```
→ when 식에서 sealed 클래스의 모든 하위 클래스를 처리하면 디폴트 분기(else 분기)가 따로 필요 없다. 
<br>
