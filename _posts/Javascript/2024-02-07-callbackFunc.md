---
layout: single
title: JS filter, reduce, map 함수
categories: Javascript
tag: [Frontend]
toc: true
---



# Callback Function

> 파라미터로 함수를 전달하는 함수
> 

# 📘 Filter()

자바스크립트의 filter함수는 다른 언어들과 똑같이 배열의 각 요소를 순회하면서 조건에 맞는 요소만을 갖는 배열을 리턴한다.

## 📖 Parameter

1. callback function
    
    배열의 현재요소, 현재요소의 인덱스(선택), 호출한 배열(선택) 3가지의 인자를 가지는 콜백 함수를 첫 번째 인자로 갖는다.
    
2. thisArg(선택)
    
    콜백함수를 실행할 때 this로 사용될 값을 정한다.
    

## 📖 활용 예시

1. 홀수만 갖는 배열 만들기

```jsx
const numbers = [1,2,3,4,5];
const filter1 = numbers.filter(curr => curr % 2 === 0);
console.log(filter1); //[1, 3, 5] 출력
```

1. 오브젝트 배열 중 원하는 오브젝트만 속한 배열 만들기

```jsx
const foods = [
    {
        name: 'salad',
        meat : false,
    },
    {
        name: 'chicken',
        meat: true,
    },
    {
        name: 'pork cutlet',
        meat: true,
    },
]
const filter2 = foods.filter(function(food){
    return food.meat;
})
console.log(filter2);
```

**결과**

![filter.png]({{"../../../../assets/images/Javascript/callbackFunc/filter.png" | relative_url}})

내가 원하는 대로 좋아하는 육류만 잘 나왔다.

---

# 📘 Reduce()

reduce함수는 배열의 각 요소를 순회하면서 콜백 함수의 실행 값을 누적해 단 하나의 결과값 만을 리턴한다.

## 📖 Parameter

1. callback function
    
    반환값을 축적할 accumulator, 배열의 현재요소, 현재요소의 인덱스(선택), 호출한 배열(선택) 순으로 4가지의 인자를 가지는 콜백 함수를 첫 번째 인자로 갖는다.
    
2. accumulator 초기값(선택)
    
    초기값을 제공하지 않을 경우 배열의 첫 번째 값이 들어간다.
    

## 📖 활용 예시

1. 1부터 10까지 더하기 (초기값 100)

```jsx
const numbers = [1,2,3,4,5,6,7,8,9,10];
const sum = numbers.reduce((acc, curr) => acc + curr, 100);
console.log(sum); //초기값 100 + 배열을 다 더한 값 55 = 155 출력
```

1. 오브젝트 배열 중 원하는 요소의 값만 더하기

```jsx
const foods = [
{
    name: 'salad',
    meat: false,
    price: 10000,
},
{
    name: 'chicken',
    meat: true,
    price: 18000,
},
{
    name: 'pork cutlet',
    meat: true,
    price: 12000,
},
]
const sum2 = foods.reduce(function(acc, curr) {
return acc + curr.price;
}, 0);
console.log(sum2); //40000 출력
```

1번 예제의 경우 초기 값이 없어도 잘 실행되지만 2번의 경우에는 초기 값을 지정해주지 않으면 acc의 초기 값이 오브젝트 타입이 되어 에러가 발생하니 주의해야한다.

# 📘 Map()

map함수는 배열의 모든 요소 각각에 콜백 함수의 실행 값을 적용해 새로운 배열을 리턴한다.

## 📖 Parameter

1. callback function
    
     배열의 현재요소, 현재요소의 인덱스(선택), 호출한 배열(선택) 순으로3가지의 인자를 가지는 콜백 함수를 인자로 갖는다.
    

## 📖 활용 예시

1. 배열의 각 원소에 3 곱하기

```jsx
const numbers = [1, 2, 3, 4, 5];
const mulNum = numbers.map(num => num * 3);
console.log(mulNum); //[3, 6, 9, 12, 15] 출력
```