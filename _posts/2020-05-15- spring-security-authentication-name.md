---
layout: post
title: React Native에서 단위 테스트 작성하기
comments: true
categories: Spring 
---

<br/><br/>

# Spring Security Authentication 용어 정리

1. `SecurityContextHolder` - 인증된 유저의 세부사항이 저장되는 장소

2. `SecurityContext` - SecurityContextHolder에서 얻어지며 현재 인증된 유저의 `Authentication`을 포함하고 있음

3. `Authentication` - 인증받을 유저나 `SecurityContext`안에 현재 유저에게 `credential`을 제공하기 위해 `AuthenticationManager`에 입력될 수 있음

4. `GrantedAuthority` - `Authentication`에서 인증받은 유저에게 부여되는 권한

5. `AuthenticationManager` - Spring Security의 Filter가 인증을 수행하는 방법이 정의된 API

6. `ProviderManager` - `AuthenticationManager`의 가장 일반적인 구현체

7. `AuthenticationProvider` - 특정 타입의 인증을 수행하기 위해 `ProviderManager`에 의해 사용되어짐

8. `Request Credentials with AuthenticationEntryPoint` - client에게 자격증명을 요청하는 응답을 보내기 위해 사용됨 (ex, 401)

9. `AbstactAuthenticationProcessingFilter` - 인증에 사용되는 기본 Filter

<br/><br/>

### References
  - https://docs.spring.io/spring-security/site/docs/current/reference/pdf/spring-security-reference.pdf#page=60&zoom=100,0,0

<br/><br/>
