---
title: "Spring Security 다중 인증 방식 주의사항 (2)"
date: "2024-12-29"
thumbnail: "/assets/img/thumbnail/auth2.webp"
---

# Spring Security 다중 인증 방식 주의사항 (2)
---

1. 유효하지 않은 인증 정보로 JWT 발급이 가능한 현상
- 원인#1. UsernamePasswordAuthenticationToken 을 사용한 2가지의 인증 방식이 존재 (API KEY, JWT 방식)
- 해결 방안 : 
2. 잘못된 예외 처리로 인해 다중 인증 방식이 정상적으로 동작하지 않은 현상
- 원인 : 
- 해결 방안 : 