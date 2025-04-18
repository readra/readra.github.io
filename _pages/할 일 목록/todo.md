---
title: "정리해야 할 글 목록"
date: "2024-07-07"
thumbnail: "/assets/img/thumbnail/todo.webp"
---

# 정리해야 할 글 목록
---

**정리해두고 나중에 또 보자**

```
---
title: "정리해야 할 글 목록"
date: "2024-07-07"
---
```

1. Spring Boot + Maven 커스텀 빌드 (완료)
2. Netty Decoder 적용 (완료)
3. Datasource Router 적용 (진행중)
4. Spring Security 다중 Provider 사용할 경우, 주의점 (완료)
5. Tree 구조 REST Docs 적용
6. Request Body 단일 String + 배열 String 동시 적용
7. Netflix 부검 메일 작성
8. Rest API 검색 조건 1개 이상 설정 강제
9. Request Param LocalDateTime 다중 Pattern 적용
10. @Transactional 과 AOP 활용한 서비스 로직 개선

**궁금증**
- with / recursive with 문을 사용하지 말라고 권고가 내려왔다 이유가 무엇이라 생각하며 어떻게 해결할 것인가?
- 화면에서 parameter 변조로 권한이 없는 화면이 보이는 현상이 있다고 가정할 때, 추후에 이런 점을 어떤 방식으로 보완해서 설계를 할 것인가?
- 거래 내역을 조회하는 쿼리가 해시 조인으로 작성되어 있는데, 해시 조인 PGA 로 인한 CPU 과점유로 DBA 가 쿼리 튜닝을 요청했다. 어떻게 조치할 것인가?
- Oracle PGA(Program Global Area) 란?