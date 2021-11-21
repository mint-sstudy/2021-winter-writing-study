# Ch5 ref: DOM에 이름 달기

> ref(reference): react 프로젝트 내부에서 DOM에 이름을 달 때 사용
→ HTML에서 id를 사용해 이름을 다는 것과 유사
> 
<br>

💡 ***react component 내부에는 id를 사용하면 안될까?*** <br>
id를 사용할 수는 있지만, JSX 안에서 DOM에 id를 달면 해당 DOM을 렌더링할 때 그대로 전달된다. 
그러면 같은 컴포넌트를 재사용할 때 중복 id를 가진 DOM이 여러 개 생기게 되는데, 이는 HTML에서 DOM의 id가 유일해야 한다는 규칙에 어긋나게 된다.
하지만 `ref`는 컴포넌트 내부에서만 작동하기 때문에 이런 문제가 생기지 않는다.

<br>
<br>

## 5.1 ref는 어떤 상황에서 사용해야 할까?

- 특정 DOM을 꼭 직접적으로 건드려야 할 때 `ref` 사용
- 클래스형 컴포넌트에서 `ref`를 사용하고, 함수형 컴포넌트에서는 `React Hooks`를 통해 ref를 사용한다.
<br>

### 5.1.3 DOM을 꼭 사용해야 하는 상황
- state만으로는 해결할 수 없는 기능
→ `ref`를 사용해 DOM에 직접 접근해야 한다.

- `ref`를 사용해야 하는 경우
```
• 특정 input에 포커스 주기
• 스크롤 박스 조작하기
• Canvas 요소에 그림 그리기
```

→ React 공식 문서에는 `ref`를 사용해야 하는 경우를 다음과 같이 정의하고 있다.

> 포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때 <br>
애니메이션을 직접적으로 실행시킬 때 <br>
서드파티 DOM 라이브러리를 React와 같이 사용할 때
> 
<br>

## 5.2 ref 사용
> ref를 사용하는 2가지 방법

### 1. 콜백 함수를 통한 ref 설정

   1) ref를 달고 싶은 요소에 ref라는 콜백 함수를 `props`로 전달한다.
   2) ref(콜백 함수)는 ref값을 `파라미터`로 전달 받는다.
   3) 파라미터로 전달 받은 ref값을 컴포넌트의 멤버 변수로 설정한다.

```jsx
<input ref={(ref) => {this.input=ref}} />
```

→ this.input은 `input 요소의 DOM`을 가리킨다.
    여기서 ref의 이름은 자유롭게 지정 가능하다. input이 아니라 아무 의미 없는 단어를 사용해도 된다.
<br>

### 2. createRef를 통한 ref 설정

- react 내장 함수인 `createRef` 함수 사용

1) 컴포넌트 내부에 멤버 변수로 React.createRef()를 담는다.
2) 해당 멤버 변수를 ref를 달고자 하는 요소에 ref props로 전달한다.

```jsx
import React, { Component } from 'react';

class RefSample extends Component {
	input = React.createRef(); //멤버 변수 input에 React.createRef() 담기

	handleFocus = () => {
		this.input.current.focus(); //ref 설정 뒤 나중에 ref를 설정한 DOM에 접근하려면
	}                             //.current를 넣어주어야 한다.

	render() {
		return (
			<div>
				<input ref={this.input} /> //input을 ref props로 전달
      </div>
    );
  }
}

export default RefSample;
```

→ ref 설정 후 설정한 DOM에 접근하려면 `this.input.current`처럼 뒤에 `.current`를 추가로 
    붙여줘야 한다.
<br>
<br>

## 5.3 컴포넌트에 ref 달기

> 컴포넌트에 ref를 달아 컴포넌트 외부에서 내부에 있는 DOM을 사용한다.

```jsx
<MyComponent
  ref={(ref) => {this.myComponent=ref}}
/>
```

→ 외부에서 MyComponent 내부의 메소드 및 변수에 접근 가능
<br>
<br>
<br>

**ScrollBox 컴포넌트를 만들고, 외부(부모 컴포넌트)에서 스크롤바를 아래로 내리는 작업을 `ref`를 사용해 수행해보자**

### ScrollBox.js

```jsx
import React, { Component } from 'react';

class ScrollBox extends Component {
   scrollToBottom = () => {
      const { scrollHeight, clientHeight } = this.box; //비구조화 할당 문법
      this.box.scrollTop = scrollHeight - clientHeight;
   };

   render() {
      const style = {
         border: '1px solid black',
         height: '300px',
         width: '300px',
         overflow: 'auto',
         position: 'relative',
      };

      const innerStyle = {
         width: '100%',
         height: '650px',
         background: 'linear-gradient(white, black)',
      };

      return (
         <div
            style={style}
            ref={(ref) => {
               this.box = ref;
            }}
         >
            <div style={innerStyle} />
         </div>
      );
   }
}

export default ScrollBox;
```

### App.js

```jsx
import React, { Component } from 'react';
import ScrollBox from './ScrollBox';

class App extends Component {
   render() {
      return (
         <div>
            <ScrollBox ref={(ref) => (this.scrollBox = ref)} />
            <button onClick={() => this.scrollBox.scrollToBottom()}>
               맨 밑으로 이동
            </button>
         </div>
      );
   }
}

export default App;
```

→ ScrollBox에 ref를 적용해 버튼에 onClick 이벤트 발생 시 ScrollBox 컴포넌트의 scrollToBottom 메소드
    수행
<br>
<br>

```jsx
onClick={this.scrollBox.scrollToBottom}
```
→ 위와 같이 작성하면 컴포넌트가 처음 렌더링될 때는 this.scrollBox 값이 `undefined`인 상태이므로, this.scrollBox.scrollToBottom 값을 읽어올 때 오류 발생
<br>

```jsx
onClick = {() => this.scrollBox.scrollToBottom()}
```
→ 화살표 함수로 새로운 함수를 만들고 내부에서 this.scrollBox.scrollToBottom 메소드 실행 <br>
→ 버튼을 누를 때는 이미 this.scrollBox가 설정된 상태이므로, 값을 읽어올 때 오류가 발생하지 않는다.
<br>
<br>
<br>

### ❗️주의
- 서로 다른 컴포넌트끼리 데이터를 교류할 때 ref를 사용하는 것은 잘못된 방법이다. <br>
ref 사용은 최소한으로 하는 것이 좋다! <br>
→ 컴포넌트끼리 데이터를 교류할 때 기본적으로 부모-자식 흐름으로 진행해야 한다.
