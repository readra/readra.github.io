---
title: "Spring Security 다중 인증 방식 주의사항 (1)"
date: "2024-11-01"
thumbnail: "/assets/img/thumbnail/auth.webp"
---

# Spring Security 다중 인증 방식 주의사항 (1)
---

Spring Security 기반 API 인증 방식을 2가지 이상 지원 시, 발생했던 이슈를 정리한 글이다.   
1. JWT 기반 인증 방식 (내/외부 사용자)
2. API KEY 기반 인증 방식 (내부 사용자 전용)

유효하지 않은 인증 정보로 JWT 발급이 가능한 이슈가 발생했다. 
이슈 해결하는 과정에서 잘못된 예외 처리로 인해 다중 인증 방식이 정상적으로 동작하지 않는 현상도 발생했다.

1. 유효하지 않은 인증 정보로 JWT 발급이 가능했던 원인
2. 잘못된 예외 처리로 인해 다중 인증 방식이 정상적으로 동작하지 않은 원인

# ProviderManager Class 해체 분석