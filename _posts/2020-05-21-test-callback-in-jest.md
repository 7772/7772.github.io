---
layout: post
title: Jest에서 callback 실행 테스트하는 방법
comments: true
categories: Javascript 
---

<br/><br/>

<h2>기존에 처리하던 방식</h2>
<p />

```
this.setState(state, () => {
  this.props.requestAPI();
});
```

<p>위의 코드가 있다고 하자.</p>
<p>기존에는 setState 함수를 <code>jest.fn</code> 으로 mock 시켜버리니, callback의 실행을 테스트할 방법이 없다고 생각했다.</p>
<p>그래서 <code>requestAPI</code>의 실행은 검증할 수 없었고, 아래와 같이 expect문을 작성하여 익명의 함수가 전달되었다는 테스트만 작성했다.</p>

```
expect(mockSetState).toBeCalledWith(state, expect.any(Function));
```

<br/>

<h2>mockImplementaion 사용하기</h2>

<p>혹시나 어떤 특별한 방법이 있을까? 하고 궁금하여</p>
<p><code>jest.fn how to run callback</code> 이라고 구글에 검색했다.</p>
<p>그런데.. 기존에 알고있던 <code>mockImplementation</code> 함수를 사용하여 아주 쉽게 테스트할 수 있었다.</p>

```
ClassToTest.prototype.setState = jest.fn().mockImplementation((state, callback) => {
  callback();
});
```

<p>위와 같이 <code>mockImplementation</code> 함수를 통해 setState 함수의 구현을 재정의 해준다.</p>
<p>코드를 보면 인자로 받은 <code>callback</code> 을 실행시킨다.</p>
<p>그러면 Test에 의해 실행된 구현 코드는 실행당시에 setState 함수의 구현부를 

```
(state, callback) => {callback()}
```

으로 인지하게되고, 그냥 인자로 받은 callback을 실행한다.</p>
<p />
<p>이제</p>

```
expect(mockRequestAPI).toBeCalled();
```

<p>이렇게 검증하면 callback의 실행여부를 검증할 수 있다.</p>

<br/><br/>
