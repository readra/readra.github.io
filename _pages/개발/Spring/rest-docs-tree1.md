---
title: "Tree 구조의 Response Field REST Docs 적용"
date: "2025-05-13"
thumbnail: "/assets/img/thumbnail/tree.webp"
---

# Tree 구조의 Response Field REST Docs 적용
---

```java
subsectionWithPath("results.[].children").description("자식").optional()
```

## subsectionWithPath
- API Request, Response Snippet을 구성하는 요소를 정의하는 function입니다.
- 요소의 하위를 선언하고 싶지 않거나, 가변적인 Request, Response가 오는 경우 사용하기 좋습니다.
- subSectionWithPath는 root key가 존재하는 지만 체크하고 하위 key에 대한 체크는 하지 않습니다.