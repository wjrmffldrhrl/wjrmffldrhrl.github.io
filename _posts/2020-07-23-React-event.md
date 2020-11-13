---
title:  "[React] 이벤트 처리하기"
categories:
  - React
tags:
  - react
  - javascript
header:
  teaser: /assets/images/react.jpg
  image: /assets/images/react.jpg
---  
# 이벤트 처리하기

React와 일반 DOM에서 이벤트를 처리하는 방식에는 차이점이 있다.

- React의 이벤트는 소문자 대신 camelCase를 사용한다.
- JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달한다.

## 예시

### HTML

```html
<button onclick="activateLasers()">
	Activate Lasers
</button>
```

### React

```html
<button onClick={activateLasers()}>
	Activate Lasers
</button>
```

ES6 클래스를 사용하여 컴포넌트를 정의할 때, 일반적으로는 이벤트 핸들러를 클래스의 메서드로 만드는 방법이 있다.

## toggle.js

```jsx
class Toggle extends React.Component{
    constructor(props){
        super(props);
        this.state = {isToggleOn: true};
    
        // 콜백에서 this가 작동하려면 아래와 같이 바인딩 해줘야 한다.
        this.handleClick = this.handleClick.bind(this);
    
    }

    handleClick(){
        this.setState(state => ({
            isToggleOn: !state.isToggleOn
        }));
    }

    render(){
        return (
            <button onClick={this.handleClick}>
                {this.state.isToggleOn ? 'ON' : 'OFF'}
            </button>
        );
    }
}
```

이 코드는 사용자가 `ON` 또는 `OFF`를 선택할 수 있도록 해주는 버튼을 랜더링 해주는 코드다.

## bind

클래스의 생성자를 보면  `handleClick` 메서드를 바인딩 해주는 과정이 있는데, 이는 JavaScript에서 클래스 메서드는 기본적으로 바인딩되어 있지 않기 때문이다.

이것은 React만의 특별한 동작이 아니라 JavaScript에서 함수가 동작하는 방식의 일부이다.

`bind()` 메소드가 호출되면 새로운 함수를 생성한다. 받게되는 첫 인자는 `this` 키워드를 전달하고, 이어지는 인자들은 바인드된 함수에 전달된다.

### bind 예시

```jsx
const module = {
  x: 42,
  getX: function() {
    return this.x;
  }
};

const unboundGetX = module.getX;
console.log(unboundGetX()); 
// expected output: undefined

const boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
// expected output: 42
```

예시와 같이 바인딩 하지 않은 객체의 함수는 호출했을 때 undefined가 반환되지만 바인딩처리를 해주면 정상적인 값이 나오는 것을 확인할 수 있다.

## bind 사용하지 않기

만약 bind를 호출하는 것이 불편하다면 이를 대신하는 방법이 두가지 있다.

**첫번째**는 [퍼블릭 클래스 필드 문법](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/)을 사용할 때 가능한 방법이다.

Create React App에서는 이 문법이 기본적으로 적용되어있다.

```jsx
class LoggingButton extends React.Component {
  
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

이 방법은 실험적인 문법이니 사용에 주의하자.

**두번째**는 콜백에 화살표 함수를 사용하는 방법이다.

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

이 문법은 `this`가 `handleClick` 내에서 바인딩되도록 한다.

그러나 이 문법은 `LoggingButton`이 렌더링될 때마다 다른 콜백이 생성된다는 것이다.

대부분의 경우 문제가 되지 않지만, 콜백이 하위 컴포넌트에 props로 전달된다면 그 컴포넌트들은 추가로 다시 렌더링을 수행할 수 있기 때문에 기존의 방식대로 생성자 안에서 바인딩하거나 클래스 필드 문법을 사용하는 것이 좋다.

# 이벤트 헨들러에 인자 전달하기

이벤트 핸들러에 추가적인 매개변수를 전달할 때 다음과 같은 방법을 사용한다.

```jsx
// 화살표 함수
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>

//Function.prototype.bind
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

두가지 방법은 동등하고 모두 React 이벤트를 나타내는 `e`인자가 ID뒤에 두 번째 인자로 전달되어야 한다.

화사표 함수는 명시적으로 인자를 전달해야 하지만 `bind`를 사용하면 추가 인자가 자동으로 전달된다.

# Reference

[이벤트 처리하기  React](https://ko.reactjs.org/docs/handling-events.html)

[Understanding JavaScript Bind ()  Smashing Magazine](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)
