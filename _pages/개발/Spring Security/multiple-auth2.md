---
title: "Spring Security 다중 인증 방식 주의사항 (2)"
date: "2024-12-29"
thumbnail: "/assets/img/thumbnail/auth2.webp"
---

# Spring Security 다중 인증 방식 주의사항 (2)
---

1. 유효하지 않은 인증 정보로 JWT 발급이 가능한 현상
- 원인#1. UsernamePasswordAuthenticationToken 을 사용한 2가지의 인증 방식이 존재 (API KEY, JWT 방식)
- 해결 방안#1. 각 인증 방식에서 사용하는 Token 클래스를 분리 (JWT = UsernamePasswordAuthenticationToken, API KEY = AbstractAuthenticationToken 상속 사용자 정의 클래스)
- 원인#2. Provider 루프에 의해 JWT, API KEY Provider 의 authenticate 함수가 호출되는데, JWT Provider 처리 시, Password 불일치하더라도 다음 API KEY Provider 처리 시, ID는 일치하여 인증이 성공하는 현상
- 해결 방안#2. 각 인증 방식 Provider 에서 취급하는 Token 클래스가 아닌 경우, authenticate 함수 return 처리하여 인증 로직을 수행하지 않고 Pass 처리
2. 잘못된 예외 처리로 인해 다중 인증 방식이 정상적으로 동작하지 않은 현상
- 원인#1. ProviderManager 에서 throw 처리하는 Exception 을 발생시키고 있어, 다음 Provider 로 넘어가지 않고 즉시 인증이 실패하는 현상
- 원인#2. 커스텀 인증 방식 중에 실제로 존재하지 않는 ID를 기반으로 한 익명 계정으로 인증할 수 있도록 기능 제공한 사례가 있다. 이 때, 존재하지 않는 익명의 계정 정보로 인증 요청이 온 경우, InternalAuthenticationServiceException 이 발생하여 익명 계정을 기반으로 인증을 수행하는 Provider 까지 넘어가지 않고 인증이 실패하는 현상
- 해결 방안#1. NPE 처럼 크리티컬한 오류 상태가 아닌 경우에는 InternalAuthenticationServiceException 혹은 AccountStatusException 사용을 지양