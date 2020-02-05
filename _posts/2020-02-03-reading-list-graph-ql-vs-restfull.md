---
layout: post
title: GraphQL과 Restfull
comments: true
categories: Blog
---

# GraphQL vs Restfull

<br/><br/>

### GraphQL 을 익히기 전에

GraphQL이 Server와 Client 사이의 데이터를 주고받는 하나의 방식이라는 점을 잊지말자.

근본적으로 클라이언트가 서버로부터 필요로 하는 데이터를 획득하는 방법은 요청과 응답을 하나의 쌍으로 묶어 이를 *약속*해 두고, 다음부터 그 데이터가 필요할 땐 이 *약속*을 활용하는 것이다.

이 낯선 새로운 개념안에 친숙한 것들에 집중해보자.

<br/><br/>

### 변하지 않은 것들

1. HTTP Protocol을 사용한다.

2. URL로 요청하고 JSON Object를 반환한다.

3. 네트워크 처리를 다루는 부분은 Framework와 Library가 수행하는 처리에 의존한다.

기본적인 내용만을 언급했지만, 위 사실들로 GraphQL이 네트워크를 이용한 데이터 전송의 패러다임을 바꾼 것은 아니라는 것을 알았다.

<br/><br/>

### 변한 것은 무엇인가

Restfull은 resource가 API의 EndPoint URL에 메여있다.

```
- method: GET
- url: '/books/1'
- response: 
  {
    'title': 'Harry Potter',
    'author': 'J.K Rowling',
  }
```

위의 API는 요청에 대한 응답으로 책의 정보를 가져온다.

이미 배포된 API가 얼마나 많은 Client에 의해서 사용될 지 모르는 상황에서, 대부분의 경우에 하위호환은 쉽사리 포기하지 못하는 중요한 주제이다.

그러므로 만약 응답으로 오는 결과의 변화가 필요하다면, 새로운 API를 준비해야 할 것이다.

---

GraphQL은 resouce가 API의 EndPoint URL에 메여있지 않다.

어떤 resource를 반환할 것인지에 대한 결정은 요청할 때의 query에 의존한다. 

그러므로 필요한 response가 부족하거나 과도한 상황이 발생하지 않는다. Server는 Client가 API 요청 시점에 필요한 resource만을 제공할 수 있다.

<br/><br/>

### 개발자 경험

Restfull은 하나의 API를 추가하기 위해 꽤 많은 작업을 해야 한다. 반면에 GraphQL은 요청 시점의 Query를 기반으로 response를 결정하기 때문에 초기에 등록된 Schema 관리만으로 요구사항을 충족할 수 있다.

Restfull은 API가 무슨 역할을 하는지를 표현하는 방법으로 EndPoint의 URL을 사용한다. 

따라서 Restfull을 개발하는 개발자는 API가 수행하는 역할에 따라 적절한 EndPoint를 훌륭히 지어내야하는 책임이 따른다.

API를 직접 실행해보거나 별도의 문서를 통해 확인하지 않으면 도무지 무슨 일을 하는 API인지 확인할 길이 없을 수도 있다.

GraphQL로 작성된 API를 보면 코드 자체가 API 문서역할을 할 수 있다. Query와 Arguments가 직관적으로 구성이 되어있기 때문인데, Client가 어떤 데이터를 요구하고 결과로 어떤 값을 받을지 명확하게 표현이 된다. 

작성해야할 코드의 양이 줄어서 DX가 좋아졌다고 볼 수도 있겠다. 그러나 대게 이런 이유는 개인적 성향에 따르기 마련이다.

그보다는 API가 무슨 역할을 하는지 알아내기 위해서 API를 작성한 개발자의 URL 작명센스에 의존하는 것이 아니라, Architecture로 해결했다는 것에 더 주목할 필요가 있다.

<br/><br/>
