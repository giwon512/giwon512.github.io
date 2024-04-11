---
layout: single
title: 구구단 예제(1) - 클래스형 컴포넌트 선언
categories: React
tag: [Frontend, Javascript]
toc: true
---

인프런에 있는 ‘웹 게임을 만들며 배우는 React’ 강좌를 클론 코딩하면서 리액트에 익숙해지기로 했다. 아무리 강의를 들어도 직접 쳐보지 않으면 아무런 의미가 없다…

**강의링크**

[https://www.inflearn.com/course/web-game-react](https://www.inflearn.com/course/web-game-react)

# 📷 결과화면

먼저 결과 화면을 보고 이에 맞춰서 코딩을 해보려고 한다. 

![gugudan.png]({{"../../../../assets/images/React/gugudanClass/gugudan.png" | relative_url}})

문제에 대한 정답을 입력 받으면 정답을 출력해주는 간단한 구구단 예제이다. 캡처를 해 놓고 보니 이상한데 이전 문제의 정답이 출력되면서 다음 문제로 넘어간 모습이다.

# 📖 클래스형 컴포넌트 선언 방식으로 구현하기

### 초기 state값 지정하기

```jsx
state = {
  first : Math.ceil(Math.random() * 9),
  second : Math.ceil(Math.random() * 9),
  value : '',
  result : '',
};
```

### render() 함수

```jsx
render() {
  //렌더링이 될 때마다 함수가 새로 생기는 것을 방지하기 위해서 함수는 밖으로 빼준다.
  return (
    <React.Fragment>
      <div>{this.state.first} 곱하기 {this.state.second}는?</div>
      <form onSubmit={this.onSubmit}>
        <input ref={this.onRefInput}type="number" value={this.state.value} onChange={this.onChange}/> <button>입력!</button>  
      </form>
      <div>{this.state.result}</div>
    </React.Fragment>
  );
}
```

나타내고자 하는 화면의 내용을 render 함수 내에서 리턴해준다. state 값이 변할 때마다 새로 렌더링이 되는데, render 함수 안에 다른 함수를 선언해버리면 그때마다 새롭게 함수가 생기게 되는 것이므로 따로 밖으로 빼주어야 성능에 도움이 된다.

⚠️ 여기서 주의할 점은, 일반 함수 선언과 화살표 함수 선언은 this가 바인딩되는 방식이 다르기 때문에, **무조건 화살표 함수로 선언**을 해주어야 한다. 자세한 내용은 다른 글에 정리해두었다.

[https://giwon512.github.io/javascript/react/jsThis/](https://giwon512.github.io/javascript/react/jsThis/)

### onChange

```jsx
 onChange = (e) => {
    this.setState({value: e.target.value});
}
```

### onRefInput

```jsx
onRefInput = (c) => {
  return this.input = c;
}
input;
```

input 태그를 선택하기 위해서는 ref를 사용해주면 된다.

### onSubmit

```jsx
onSubmit = (e) => {
  e.preventDefault();
  if(parseInt(this.state.value) === this.state.first * this.state.second){
    //setState는 비동기이기 때문에 현재 state값으로 state 값을 변경해줄 때는 함수를 리턴하는 방식으로 넘겨주는 것을 권장
    this.setState((prevState) => { 
      return {
      result : '정답: ' + prevState.value,
      first : Math.ceil(Math.random() * 9),
      second : Math.ceil(Math.random() * 9),
      value : '',};
    });
  } else {
    this.setState({
      result : '오답',
      value : '',
    });
  }
  //DOM에 직접 접근하고 싶을 땐 ref값에 변수를 준다.
  this.input.focus(); 
};
```

result 값을 리턴할 때와 같이 현재 state 값으로 state값을 변경해줄 때는 헷갈리지 않게 함수를 리턴하는 방식을 권장한다. setState 함수는 비동기식이기 때문에, `prevState.value`를 `this.state.value`로 적어도 똑같이 동작한다. 

## 📝 setState가 비동기로 동작하는 이유

> state값이 변경될 때마다 컴포넌트가 재랜더링이 되기 때문에, 상태값들이 바뀔때마다 동기식으로 재랜더링이 된다면 컴포넌트의 state가 값을 많이 가지고 있는 경우 성능상 문제가 발생할 수 있기 때문이다.
> 

따라서, 상태 값이 바뀐 후 동작해야 하는 코드가 있다면 `useEffect`함수를 활용해주어야 한다.

그 다음 포스팅에서는 함수 컴포넌트 선언 방식으로 위의 구구단 예제를 코딩해보려고 한다.

참고

---

[https://velog.io/@samkong/setState](https://velog.io/@samkong/setState)

[https://velog.io/@daybreak/동기-비동기-처리](https://velog.io/@daybreak/%EB%8F%99%EA%B8%B0-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC)