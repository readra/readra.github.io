---
title: "Spring Boot + Maven 환경에서 커스텀 빌드하기 (4)"
date: "2024-08-05"
thumbnail: "/assets/img/thumbnail/custom3.webp"
---

# Spring Boot + Maven 환경에서 커스텀 빌드하기 (4)
---

## CUSTOM BUILD 예제 작성
공통 API 혹은 고객사 전용 API 를 포함하는 BUILD 예제를 작성한다.

이건 중요하다...

### pom.xml
```xml
<dependency>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>exec-maven-plugin</artifactId>
  <version>3.3.0</version>
</dependency>
```
빌드 시, 특정 클래스의 main() 함수를 실행시키고 싶을 때 사용한다.
```xml
<dependency>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>properties-maven-plugin</artifactId>
  <version>1.2.1</version>
</dependency>
```
빌드 시, 특정 파일 기준으로 properties 읽고 싶을 때 사용한다. 버전 표시용
```xml
<dependency>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>build-helper-maven-plugin</artifactId>
  <version>3.3.0</version>
</dependency>
```

### 일반 빌드

### 커스텀 빌드

### 결과

### 마치며