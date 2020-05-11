---
layout: post
title: React Native에서 단위 테스트 작성하기
comments: true
categories: Javascript
---

# Prototype

<br/>

1. class 문법으로 Foo 클래스(함수) 생성하기

<br/>

```
class Foo1 {
  methodA() {
    console.log('method A');
  }
}
```

<br/>

2. 전통적인 방식으로 Foo 함수 생성하기

<br/>

```
var Foo2 = function() {};

Foo2.prototype.methodA = function() {
  console.log('method A');
};
```

<br/>

3. 사용해보기

<br/>

```
var foo1 = new Foo1();
foo1.methodA();

var foo2 = new Foo2();
foo2.methodA();

Foo1.prototype.methodA();

Foo2.prototype.methodA();
```

<br/>

두가지 방식은 같은 결과를 보여준다.

<br/>

React Native에서 View Component를 작성하는 방식은 기본적으로 Javascript의 class 문법을 사용한다.
따라서 class 문법으로 선언된 React N서tive View Component는 jsx 태그를 사용하여 인스턴스화되지 않아도 class 내부의 함수로 접근할 수 있다.

<br/>


# React Native Unit Test

<br/>

1. 구현해야할 내용 생각해보기

<br/>

`MyButton` React Natvie View Component의 onPress 함수가 실행되면 requestAPI 함수를 실행해야 한다.

<br/>


> 주의! onPress 함수의 역할은 버튼을 클릭하는 것이 아니라, 클릭이 이뤄진 후의 동작이다. 따라서 onPress 함수의 테스트 내용은 버튼 클릭과는 무관하다.

<br/>


2. Test Case 작성하기

<br/>


> Test Library로 jest를 사용한다.

<br/>


단위테스트를 좀 더 쉽게 작성하고, 읽기 쉽도록 만들기 위한 방법론으로 Given, When, Then이 있다.

<br/>


`1. 구현해야할 내용 생각해보기`에서 정했던 내용을 `Given`, `When`, `Then`으로 각각 분리하여 표현해야 한다.

<br/>


- `When` : 테스트할 상황. `onPress` 함수가 실행되면 발생하는 상황을 테스트하고자 하므로 `onPress` 함수를 실행시켜야 한다.

<br/>


- `Then` : 테스트할 상황이 발생하면 벌어질 결과. 우리는 `requestAPI`가 호출될 것이라고 예상했으므로 이 내용을 검증해야 한다.

<br/>


- `Given` : 테스트할 상황이 벌어졌을 때, 예상된 결과를 도출하기 위해 필요한 값. 우리는 `requestAPI`가 호출되길 원한다. 따라서 `requestAPI`가 호출되기 위해 필요한 값을 정의해주어야 한다.

<br/>


> jest 사용법은 공식문서를 통해 확인하자.

<br/>

```
// MyButton.test.js

describe('MyButton : onPress', () => {
  it('requestAPI 함수를 호출할 수 있다.', () => {
    // Given
    const MyButton = require('./your/path/MyButton').default;

    const mockRequestAPI = jest.fn();

    MyButton.prototype.props = {};
    MyButton.prototype.props.requestAPI = mockRequestAPI;

    // When
    MyButton.prototype.onPress();

    // Then
    expect(mockRequestAPI).toBeCalled();
  });
});
```

<br/>

3. `MyButton` 구현하기 

<br/>

```
class MyButton extends React.Component {
  onPress() {
    this.props.requestAPI();
  }

  render() {
    return (
      // Button
    );
  }
}

export default MyButton;
```

<br/>

# 이 방식의 장점

<br/>

React Native에서 단위 테스트를 하고자 jsx 태그를 활용한 인스턴스화(`<MyButton/>`) 방식으로 접근한다면 Redux, Mobx, Navigation Library 등의 복잡한 props 관계때문에 쉽지 않다. (사실 굉장히 어렵다.)

<br/>

그 이유는 React Native View Component Class는 자신만의 Life cycle을 가진다. 컴포넌트가 렌더링되면 `componentDidMount`가 호출되고... 와 같은 일련의 과정이 jsx 태그를 활용한 인스턴스화(`<MyButton/>`) 시점에 똑같이 동작한다.

<br/>

이미 당신의 코드에는 `componentDidMount`나 `componentDidUpdate`에서 동작하는 수많은 `props`와 `state`들이 존재할 것이기 때문에, 모든 내용을 mock으로 준비해주어야 테스트가 올바르게 동작할 것이다.

이 쯤되면 간단한 동작을 테스트하는 코드를 작성하기 위해 이렇게 많은 고생을 해야한다는 점에 테스트를 포기해버리는 경우가 부지기수다.

<br/>

그러나, prototype을 이용한 단위테스트는 다르다.

<br/>

Javascript는 class가 인터프리터에 의해 읽혀 메모리에 적재되는 시점에 prototype 객체도 함께 적재되기 때문에 인스턴스 변수와 메소드로 활용될 prototype 내부의 프로퍼티들이 인스턴스화를 거치기도 전에 사용할 수 있다.

<br/>

사실 class를 인스턴스화 시키지 않고도 인스턴스 변수나 메소드로 접근할 수 있는 것은 (내가 아는 몇 안되는 언어 중에서는) Javascript만이 가지는 장점이다. 

<br/>

이 방식을 활용하면 prototype을 통해 접근한 해당 함수 스코프에서 사용되는 변수나 함수만을 mocking 하여도 완전한 단위테스트가 가능하다.

<br/><br/>
