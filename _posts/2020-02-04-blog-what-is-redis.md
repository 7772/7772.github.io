---
layout: post
title: Amazon ElastiCache for Redis
comments: true
categories: Blog
---

# Amazon ElastiCache for Redis

<br/><br/>

> 레디스는 데이터베이스, 캐시 그리고 메시지 브로커처럼 사용되는 **인-메모리 데이터 자료 저장소**이다. 
Amazon ElastiCache 는 Redis 를 이용한 아마존의 Cache 지원 서비스이다.

<br/><br/>

## 특징

1. 빠름
2. NoSQL Database 시스템임
3. 다양한 언어를 지원함 (JavaScript, Java, Go, C, C++, C#, Python, Objective-C, PHP, ...)
4. 오픈소스이며, 안정성을 갖춤
5. 다양한 자료구조 저장 가능 (String, List, Set, Sorted Set, Hashes, Bitmaps, HyperLogLogs, ...)

<br/>

## 어떻게 동작하는가?

데이터베이스가 디스크나 SSD 에 저장되는 것과는 반대로, 모든 레디스의 데이터는 인-메모리에 저장된다.
디스크에 접근할 필요성을 제거함으로써, 전통적 데이터베이스 시스템에서 발생하는 탐색 지연 시간을 피할 수 있어서, 수 밀리초 내에 데이터에 접근할 수 있다.

<br/><br/>

## 주로 언제 사용되는가?

- Caching

  > 쿼리 결과, 영구적 세견, 웹 페이지, 이미지, 파일 및 메타 데이터와 같이 자주 사용되는 객체의 캐싱은 모두 Redis 와 함께 주로 사용된다.

- Chat, messging and queues

  > 높은 성능을 요구하는 Chat rooms, 실시간 댓글, 노셜 미디어 피드 등을 지원할 수 있다.

- Gaming leaderboards

  > 게이밍에서 실시간 점수표와 같이 정렬된 데이터 집합 저장에 사용된다. 유저의 점수가 변화된 각 시점에 업데이트를 수행해서 실시간으로 처리된 리스트를 만들기 쉽다.

- Rich media stremaing

  > 레디스는 live streaming 을 가능하게 하는 속도를 제공하며, 유저 프로파일, 시청 기록, 수백만 유저의 정보 및 토큰 인증 그리고 동시에 수백만의 모바일과 데스크탑에 stream video 를 가능하게 하는 manifest 파일들을 저장하는데 사용될 수 있다.

- Machine Learning

  > 기계 학습 모델을 신속하게 구축, 교육 및 배포 할 수 있는 빠른 인-메모리 데이터 저장소를 제공한다. 게이밍이나 금융 서비스 분야의 사기 감지, 광고 기술의 실시간 입찰, 데이트 매치 메이킹 등 라이브 데이터를 수십 미리초 내에 결정해야 하는 분야에 주로 사용된다.

- Real-time ananlytics

  > Apache Kafka나 Amazon Kenesis와 같은 streaming solution 들고 함께 사용되어 수 밀리초 latency로 실시간 데이터를 분석하는데 사용될 수 있다. 레디스는 소셜 미디어 분석, 광고 타게팅, 개인화 그리고 IoT와 같은 분야에서 실시간 분석을 위한 이상적 방법이다.

<br/><br/>
