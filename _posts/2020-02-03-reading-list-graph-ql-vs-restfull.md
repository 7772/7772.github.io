---
layout: post
title: [Reading List] GraphQL vs. Restfull
comments: true
categories: ReadingList 
---

# GraphQL vs Restfull

<br/>

> 이 글은 [Sashko Stubailo 의 GraphQL vs. Rest](https://blog.apollographql.com/graphql-vs-rest-5d425123e34b) 를 읽은 후, 개인적 생각 및 개념을 정리한 글이다.

<br/><br/>

### GraphQL 을 익히기 전에

GraphQL 이 Server 와 Client 사이의 데이터를 주고받는 하나의 방식이라는 점을 잊지말자.

근본적으로 클라이언트가 서버로 부터 필요로 하는 데이터를 획득하는 방법은 요청과 응답을 하나의 쌍으로 묶어 이를 *약속*해 두고, 다음부터 그 데이터가 필요할 땐 이 *약속*을 활용하는 것이다.

이 낯선 새로운 개념안에 친숙한 것들에 집중해보자.

<br/><br/>

### 변하지 않은 것들

<br/><br/>

