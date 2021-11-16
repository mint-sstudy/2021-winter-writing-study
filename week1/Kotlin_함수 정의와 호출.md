# Ch03 함수 정의와 호출
## 📌 학습목표
```
• 컬렉션, 문자열, 정규식을 다루기 위한 함수
• 이름 붙인 인자, 디폴트 파라미터 값, 중위 호출 문법 사용
• 확장 함수와 확장 프로퍼티를 사용한 자바 라이브러리 사용
• 최상위 및 로컬 함수와 프로퍼티를 사용한 코드 구조화
```
<br>
<br>

## 3.1 코틀린에서 컬렉션 만들기
- 코틀린은 자체 컬레션 기능을 따로 제공하지 않고, 기존 자바 컬렉션을 활용한다. <br>
→ 자바 코드와 상호작용하기 훨씬 쉽다.
- 코틀린 컬렉션은 자바 컬렉션과 똑같은 클래스지만 코틀린에서는 자바보다 더 많은 기능을 쓸 수 있다.
<br>
<br>
<br>

## 3.2 함수를 호출하기 쉽게 만들기
- 자바 컬렉션의 디폴트 toString의 출력 형식은 고정되어 있는데, 이 형식 말고 다른 형식으로 출력하고 싶다. <br>
→ 코틀린에는 이런 요구 사항을 처리할 수 있는 함수가 표준 라이브러리에 들어 있다.
```kotlin
fun <T> joinToString(
    collection: Collection<T>
    separator: String,
    prefix: String,
    postfix: String
):String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.sithIndex()) {
      if (index > 0) result.append(separator)
      result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```
→ 디폴트 toString은 `[1, 2, 3]`으로 구현되지만, 코틀린에서 위 함수를 구현해 `(1; 2; 3)`으로 출력되도록 한다. <br>
→ 함수 호출할 때마다 매번 인자 4개를 전달하지 않고 좀 덜 복잡하게 바꿔보자.

<br>

### 3.2.1 이름 붙인 인자
- **개선 사항** `1. 함수 호출 부분의 가독성`
```kotlin
joinToString(collection, " ", " ", ".")
```
→ 인자로 전달한 각 문자열이 어떤 역할을 하는지 호출 부분만 봐서는 알 수가 없다.
```kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = ".")
```
→ 코틀린으로 함수를 호출할 때는 함수에 전달하는 인자 중 일부(또는 전부)의 이름을 명시할 수 있다.
→ 인자 중 하나라도 이름을 명시했으면 혼동을 막기 위해 그 뒤에 오는 모든 인자에도 이름을 꼭 명시해야 한다.
<br>
<br>
  
### 3.2.2 디폴트 파라미터 값
- 코틀린에서는 함수 선언 시 파라미터의 디폴트값을 지정할 수 있어 오버로드 중 상당수를 피할 수 있다.
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = " ",
    postfix: String = " "
):String
```
→ 함수를 호출할 때 모든 인자를 쓸 수도 있고, 일부를 생략할 수도 있다. <br>
→ `이름 붙인 인자`를 사용하면 인자 목록의 중간에 위치한 인자를 생략하고, 지정하고 싶은 인자를 이름을 붙여 순서 상관없이 지정할 수 있다.
```kotlin
>>> joinToString(list, postfix = ";", prefix = "# ")
# 1, 2, 3;
```
- 함수의 디폴트 파라미터값은 함수 선언 쪽에서 지정되므로, 어떤 클래스 내에 정의된 함수의 디폴트값을 바꾸고 그 클래스가 포함된 파일을 
재컴파일하면 해당함수를 호출하는 코드 중에 값을 지정하지 않은 모든 인자에 자동으로 **바뀐 디폴트값**이 적용된다.
<br>
<br>

### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티
- JDK의 collections 클래스: 다양한 정적 메소드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메소드는 없는 클래스 <br>
→ 코틀린에서는 함수를 직접 소스 파일의 최상위 수준, 즉 모든 다른 클래스의 밖에 위치시키면 된다.
→ 이 함수들은 package에 속한 함수이므로, 다른 package에서 사용하고 싶으면 그 함수가 정의된 패키지를 import해야 한다.
<br>

### 최상위 프로퍼티
- 함수와 마찬가지로, 프로퍼티도 파일의 최상위에 놓을 수 있다. <br>
Ex) 어떤 연산을 수행한 횟수를 저장하는 var 프로퍼티를 클래스 바깥에 위치
```kotlin
var opCount = 0 //최상위 프로퍼티 선언

fun performOperation() {
    opCount++ //최상위 프로퍼티 값 변경
    //...
}

fun reportOperationCount() {
    println("Operation performed $opCount times")
}
```
- 최상위 프로퍼티를 활용해 상수를 추가할 수도 있다.
```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```
<br>
<br>
<br>

## 3.3 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
- `확장함수(extension function)`: 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수
```kotlin
package strings

fun String.lastChar() : Char = this.get(this.length - 1)
```
→ 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이면 확장 함수를 만들 수 있다. <br>
→ 클래스 이름: `수신 객체 타입(receiver type)` <br>
  확장 함수가 호출되는 대상이 되는 값(객체): `수신 객체(receiver object)`
<br>
<br>

### 3.3.1 임포트와 확장 함수
- 코틀린에서는 클래스를 import할 때와 동일한 구문을 사용해 개별 함수도 import할 수 있다.
```kotlin
import strings.lastChar

import strings.* //*도 사용 가능
```
- as 키워드를 사용해 다른 이름으로 import 해올 수 있다.
```kotlin
import Strings.lastChar as last
```
<br>

### 3.3.2 자바에서 확장 함수 호출
- 정적 메소드를 호출하면서 첫 번째 인자로 수신 객체를 넘기기만 하면 된다.
```Java
char c = StringUtilKt.lastChar("Java")
```
<br>

### 3.3.3 확장 함수로 유틸리티 함수 정의
- 개선된 joinToString()
```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = " ",
    postfix: String = " "
) : String {
    val result = StringBuilder(prefix)
    for((index, element) in this.withIndex())
        if(index > 0) result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```
→ 컬렉션에 대한 확장을 만들고, 모든 인자에 대한 디폴트 값을 지정
<br>
<br>

### 3.3.4 확장 함수는 오버라이드할 수 없다
- 일반 메소드와 달리 확장함수는 오버라이드할 수 없다
- 확장함수는 클래스 밖에 선언되어 있기 때문에 확장함수를 호출할 때 수신 객체로 지정한 변수의 `정적 타입`에 의해 어떤 확장함수가 호출될지 결정된다.
```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

>>> >>> val view: View = Button() //오버라이드 불가
>>> view.showOff()
I'm a view!
```
→ view가 가리키는 실제 타입은 Button이지만, view의 타입이 View이므로 View의 확장 함수가 호출된다.
<br>
<br>

### 3.3.5 확장 프로퍼티
```kotlin
fun String.lastChar(): Char = this.get(thos.length - 1)
```
→ 위 함수를 **확장 프로퍼티**로 변경해보자
```kotlin
val String.lastChar: Char
    get() = get(length - 1)
```
- 확장 프로퍼티는 일반 프로퍼티에 수신 객체 클래스를 추가한 것
- 변경 갸능한 확장 프로퍼티
```kotlin
var StringBuilder.lastChar: Char
    get() = get(length - 1) //프로퍼티 게터
    set(value: Char) {
        this.setCharAt(length - 1, value) //프로퍼티 세터
    }
```
```kotlin
>>> println("Kotlin".lastChar)
n
>>> val sb = StringBuilder("Kotlin?")
>>> sb.lastChar = '!'
>>> println(sb)
Kotlin!
```
<br>
<br>

## 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원
- 코틀린 표준 라이브러리 함수와 함께 배울 **코틀린 특성**
```
1. vararg 키워드를 사용해 호출 시 인자 개수가 달라질 수 있는 함수 정의
2. 중위(infix)함수 호출 구문을 사용해 인자가 하나인 메소드를 간편하게 호출
3. 구조 분해 선언(destructing declaration)을 사용해 복합적인 값을 분해해 여러 변수에 나눠 담기
```
<br>

### 3.4.2 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의
- 자바의 가변 길이 인자(varargs): 메소드를 호출할 때 **원하는 개수만큼 값을 인자로 넘기면** 자바 컴파일러가 배열에 그 값들을 넣어주는 기능 <br>
→ `코틀린의 가변 길이 인자`: 자바와 문법만 조금 다르다. 타입 뒤에 ...을 붙이는 대신 parameter 앞에 `vararg` 변경자를 붙인다.
- 이미 배열에 속한 원소를 가변 길이 인자로 넘길 때 코틀린에서는 배열을 명시적으로 풀어 배열의 각 원소가 인자로 전달되도록 해야 한다. <br>
→ 전달하려는 배열 앞에 `spread 연산자 (*)` 붙이기  
→ spread 연산자를 이용하면 함수를 호출할 때 배열에 속한 값과 다른 값들을 함께 전달할 수 있다!
```kotlin
fun main(args: Array,String>) {
    val list = listOf("args: ", *args) //args 배열에 속한 값과 속하지 않은 다른 값("args: ")을 함께 전달
    println(list)
}
```
<br>

### 3.4.3 값의 쌍 다루기: 중위 호출과 구조 분해 선언
```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```
→ **Infix call(중위 호출)** 로 `to`라는 일반 메소드를 호출 <br>
<br>

- 중위 호출 시, `수신 객체`와 `하나의 메소드 인자` 사이에 메소드 이름을 넣는다.   
  객체, 메소드 이름, 유일한 인자 사이에는 공백이 들어가야 한다.
```kotlin
1.to("one") //to 매소드를 일반적인 방식으로 호출
1 to "one" //to 메소드를 둥위 호출 방식으로 
```
