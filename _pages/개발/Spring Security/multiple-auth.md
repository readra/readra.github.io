---
title: "Spring Security 다중 인증 방식 주의사항 (1)"
date: "2024-09-03"
thumbnail: "/assets/img/thumbnail/auth.webp"
---

# Spring Security 다중 인증 방식 주의사항 (1)
---

Spring Security 기반 API 인증 방식을 2가지 이상 지원 시, 발생했던 이슈를 정리한 글이다.   
1. JWT 기반 인증 방식 (내/외부 사용자)
2. API KEY 기반 인증 방식 (내부 사용자 전용)