---
layout: single
title: 구구단 예제(2) - Hooks를 활용한 함수 컴포넌트 선언
categories: React
tag: [Frontend, Javascript]
toc: true
---


# 📔 React Hooks란?

> Hook은 함수형 컴포넌트에서 React state와 생명주기 기능(lifecycle features)을 “연동(hook into)“할 수 있게 해주는 함수. Hook은 class 안에서는 동작하지 않으며, 대신 class 없이 React를 사용할 수 있게 해준다.
> 

## 📘 useState

> const [state, setState] = useState(initialState);
> 

리액트의 훅중 하나이며 현재의 state 값과 state 값을 업데이트하는 함수를 반환하는 함수. 일반적으로 함수 내의 변수는 함수가 종료되면 사라지지만, useState를 사용해 변수의 상태를 초기화하면 리액트에 의해 해당 변수는 사라지지 않고 계속 관리된다.

## 📘 useEffect

useEffect는 화면이 렌더링 된 이후에 최초 한번 실행되고 이후 상태가 변경될 때마다 계속 실행된다. React는 effect가 수행되는 시점에 이미 DOM이 업데이트되었음을 보장한다.

## 📘 useRef

> const inputEl = React.useRef(null);
> 

useRef를 사용하여 ref를 설정하면, useRef를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가리키게 된다. 

이벤트가 발생했을 때 e.target.value로 해당 값을 가져오는 방법을 useRef를 사용하여 값을 가져 올 수 있다.

# 📖 함수 컴포넌트 선언 방식으로 구구단 구현하기

### 초기 state값 지정하기

```jsx
const [first, setFirst] = React.useState(Math.ceil(Math.random() * 9));
const [second, setSecond] = React.useState(Math.ceil(Math.random() * 9));
const [value, setValue] = React.useState(Math.ceil(Math.random() * 9));
const [result, setResult] = React.useState(Math.ceil(Math.random() * 9));
const inputRef = React.useRef(null);
```

### return 문

```jsx
return (
  <React.Fragment>
    <div>{first} 곱하기 {second}는?</div>
    <form onSubmit={onSubmit}>
      <input ref={inputRef} type="number" value={value} onChange={onChange}/> 
      <button>입력!</button>  
    </form>
    <div>{result}</div>
  </React.Fragment>
);
```

render() 함수를 더 이상 쓰지 않을 뿐 그 안 리턴 문의 내용은 거의 비슷하다

### onChange

```jsx
 onChange = (e) => {
    setValue(e.target.value);
}
```

### onSubmit

```jsx
onSubmit = (e) => {
  e.preventDefault();
  if(parseInt(value) === first * second){
    setFirst(Math.ceil(Math.random() * 9));
    setSecond(Math.ceil(Math.random() * 9));
    setValue('');
    setResult('정답');
  } else {
    setValue('');
    setResult('땡');
  }
  inputRef.current.focus(); 
};
```

## 📝 전체 코드

```jsx
<script type="text/babel">
  'use strict';
  const GuGuDan = () => {
    const [first, setFirst] = React.useState(Math.ceil(Math.random() * 9));
    const [second, setSecond] = React.useState(Math.ceil(Math.random() * 9));
    const [value, setValue] = React.useState('');
    const [result, setResult] = React.useState('');
    const inputEl = React.useRef(null);

    const onSubmitForm = (e) => {
      e.preventDefault();
      if (parseInt(value) === first * second) {
        setResult('정답');
        setFirst(Math.ceil(Math.random() * 9));
        setSecond(Math.ceil(Math.random() * 9));
        setValue('');
        inputEl.current.focus();
      } else {
        setResult('땡');
        setValue('');
        inputEl.current.focus();
      }
    };
    return (
      <React.Fragment>
        <div>{first} 곱하기 {second}는?</div>
        <form onSubmit={onSubmitForm}>
          <input
            ref={inputEl}
            type="number"
            value={value}
            onChange={(e) => setValue(e.target.value)}
          />
          <button>입력!</button>
        </form>
        <div id="result">{result}</div>
      </React.Fragment>
    );
  };
</script>
<script type="text/babel">
  ReactDOM.createRoot(document.querySelector('#root')).render(<GuGuDan/>);
</script>
```

클래스형 컴포넌트의 경우에는 값이 변하면 render() 함수 내부만 다시 렌더링 되었지만, 함수 컴포넌트의 경우에는 값이 변할 때마다 함수 전체가 다시 렌더링되므로 속도가 더 느려질 수 있음을 유의해야 한다.

참고

---

[https://funveloper.tistory.com/34](https://funveloper.tistory.com/34)

[https://velog.io/@vvvvwvvvv/React-8.-react-hooksuseState-useEffect-useRef](https://velog.io/@vvvvwvvvv/React-8.-react-hooksuseState-useEffect-useRef)