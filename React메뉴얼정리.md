## React 메뉴얼 + 교과서



1. 선언형 스타일 (vs 명령형) -> 간결함

2. 가상 DOM을 사용 -> 브라우저에 이미 반영된 뷰랑 새로운 뷰의 차이점을 찾아내어(diffing, reconciliation ) 갱신하는데 이때 렌더링 성능이 뛰어남.

3. 단방향 데이터 흐름, 바인딩(unidirectional data flow) - 복잡도 줄임 -> Redux 활용하여 깔끔하게 구현 가능

   > AngularJS는 양방향이고 이런 이것에 익숙한 사람들은 코드를 더 작성해야하는 것에 불편해함

4. 컴포넌트 기반 아키텍쳐 -> 재사용성과 유지보수 좋음

5. 컴포넌트를 js로만 구현(마크업과 js를 분리하지 않는 방식) -> 특정 템플릿 언어를 따로 배우지 않아도 됨(AngularJS와 비교함)

6. 서버사이드 렌더링 기능 -> SEO 최적화에 도움



#### JSX

javascript + xml스타일 -> 자바스크립트 확장 문법으로 html과 javascript를 결합했다고 보면 됨

렌더링 로직은 ui 로직(event handling, data preparing for display, state change)과 언제나 연관되어있음.

따라서 기존의 마크업과 로직을 분리하여 관리하는 것에서 탈피함 -> jsx가 필수는 아니지만 편함

```react
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

자바스크립트 표현방식으로 위와 같이 메소드를 통해 생성되는 이름 값을 {}안에 넣고 element 생성하여 rendering할 수 있다.

> **react 패키지**와 **렌더링 패키지들로 분리**하여 웹, 모바일 등 여러 가지 렌더링 대상에 react를 적용할 수 있게 됨. -> 작성된 다양한 컴포넌트들을 여러 곳에서 렌더링해 쓰기가 쉬워짐.
>
> **웹은 ReactDom.render 메소드를 써야함**.
>
> 웹 : react-dom -> ReactDom
>
> 터미널 인터페이스 : react-blessed https://github.com/Yomguithereal/react-blessed
>
> vr 인터페이스 : react-vr



트랜스파일(컴파일) 후 jsx 표현은 일반 자바스크립트 함수를 호출하는 것과 마찬가지로 되고, 결국 객체로 된다. 따라서 아래와 같이 리턴되는 값으로도 사용 가능.

<u>고로 **jsx**는 **자바스크립트 객체**라고 봐도 무방</u>

```react
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```



속성값도 html과 유사하게 적용 가능

단, 

- javascript이므로 html에서 쓰이는 속성 이름들을 <u>camelCase로 작성</u>해야함.
- class, for -> className, htmlFor 로 사용(자바스크립트 예약어라서)

```react
const element = <img src={user.avatarUrl}></img>;
```



디폴트값으로 ReactDom이 rendering전에 jsx안의 값들을 **escape시켜주**기 때문에 태그로 injetion 공격에 안전함.

```react
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>; -> 알아서 escape처리됨
```



jsx는 **Babel을 사용해 compile + transform = transpile 트랜스파일해야** 브라우저에서 실행 가능.(babel은 원래 es6를 변환하는게 주요 기능이고 jsx도 변환해줌)

> Webpack 같은 많이 사용되는 빌드 도구에 Babel 플러그인이 포함되어 있음

```react
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```react
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

React.createElement 메소드가 코드에 버그가 있는지 체크한 후 결과적으로 아래의 **React element** 가 됨.

```react
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

> You can think of them **as descriptions of what you want to see on the screen**.
> React reads these objects and **uses them to construct the DOM** and keep it up to date.



#### Rendering Elements

```react
const element = <h1>Hello, world</h1>;
```

브라우저 DOM element들과 다르게 React 엘리먼트들은 plain javascript object이다.

-> React DOM은 이 React element와 DOM을 맞추기 위해 DOM을 업데이트시키게 됨.

Component > element



```html
<div id="root"></div>
```

위는 'root' dom node로 내부의 모든 것들이 React DOM에 의해 관리된다.

> Applications built with just React usually have a single root DOM node.

이 안에 엘리먼트들을 렌더링하기 위해 ReactDOM.render를 사용하게 된다.



리액트 **엘리먼트들은 immutable**하다.

> React elements are [immutable](https://en.wikipedia.org/wiki/Immutable_object). Once you create an element, you can’t change its children or attributes.
>
> An element is like a single frame in a movie: it represents the UI at a certain point in time.

상태에 관한 설명이 아직 안나왔으니, 현재 UI를 업데이트 시키는 방법은 엘리먼트를 새로 생성하는 방법 밖에 없다.

```react
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```

<u>그리고 리액트는 기존의 엘리먼트와 비교해 변경되는 부분만 찾아서 바꿈</u>

> React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

![DOM inspector showing granular updates](https://reactjs.org/granular-dom-updates-c158617ed7cc0eac8f58330e49e48224.gif)

> In practice, most React apps only call `ReactDOM.render()` once. <- 실제로는 render를 한번만 사용하고 상태를 사용하여 업데이트함





#### Components and Props

컴포넌트는 독립적, 재사용 가능한 단위임.

개념적으로 자바스크립트 함수와 같음.

<u>속성값(props)를 받아 엘리먼트들이 조합된 객체를 리턴한다.</u>

> Conceptually, components are like JavaScript functions. 
> They accept arbitrary inputs (called “props”) and return React elements describing what should appear on the screen.

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

```react
//es6문법 - class
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

위 두개는 같은 컴포넌트임

아래의 React.Component를 상속한 클래스 방식을 이용할 때는 return 값으로 하나의 엘리먼트가 반환될 수 있게 해야함

-> ReactDOM.render와 같이 엘리먼트 하나만 반환한다. - <u>보통 여러 엘리먼트들 있으면 div로 묶음</u>



아래와 같이 <u>사용자가 정의한 컴포넌트를 DOM tag 대신 적용</u>할 수 있음

```react
const element = <Welcome name="Sara" />;
```

이러면 리액트는 **jsx 속성값들(여기선 name)을 <u>props라는 하나의 객체</u>에 담아 Welcome이라는 컴포넌트에 넘겨줌.**

jsx의(정확히 말하면 React element) 속성은 **html 속성을 작성하는 것과 유사**함

- 일반적인 html 요소의 속성 : href, title, style, class ..

- React 컴포넌트의  props안의 값들(클래스에서는 this.props)

  ​

리액트는 내부적으로 속성 이름은 html 표준 속성과 대조한 결과에 따라,

1. 일치하는 html 속성이 있음 -> 해당 엘리먼트 html 속성으로 사용하여 <u>렌더링됨</u> + this.props.foo로도 접근 가능

2. ~~일차하는 html 속성이 없음 -> 렌더링되지 않음 + this.props.foo로 접근 가능~~

   <- 버전 16부터 렌더링 된다고 함.



간단한 예제 - DOM이 업데이트되는 step 설명

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

1. We call `ReactDOM.render()` with the `<Welcome name="Sara" />` element.
2. React calls the `Welcome` component with `{name: 'Sara'}` as the props.
3. Our `Welcome` component returns a `<h1>Hello, Sara</h1>` element as the result.
4. React DOM efficiently updates the DOM to match `<h1>Hello, Sara</h1>`.

> React treats components starting with lowercase letters as DOM tags.
> So, always start component names with a capital letter.



아래와 같이 여러 컴포넌트를 합쳐서 쓸 수 있음

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

> Typically, new React apps have a single `App` component at the very top.



재사용할 가능성이 높은 컴포넌트들에 대해서는 작은 단위로 쪼개는 걸 두려워 말라

```react
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

->

```react
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```



**Props are Read-Only**

어떤 컴포넌트를 작성하면, props는 절대 변경되서는 안됨.

-> props에 관해서는 순수 함수(pure function)처럼 되야함. => <u>no input change + same input, same result</u>

> All React components must act like pure functions with respect to their props.

-> 뒤에 나오는 state를 사용하여 내부에서 컴포넌트의 output을 변경가능하게 함.

stateless한 컴포넌트가 진짜 순수함수이고, 컴포넌트 작성할 때 최대한 state를 없애는 방향이 예측하기 쉬워 유지보수, 디버깅이 편리함.

> State allows React components to change their output over time in response to user actions, network responses, and anything else, without violating this rule.