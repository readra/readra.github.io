---
title: "Spring Boot + Maven 환경에서 커스텀 빌드하기 (4)"
date: "2024-08-05"
thumbnail: "/assets/img/thumbnail/custom4.webp"
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
빌드 시, 특정 클래스의 main() 함수를 실행시키기 위해 사용한다.
```xml
<dependency>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>properties-maven-plugin</artifactId>
  <version>1.2.1</version>
</dependency>
```
빌드 시, 특정 파일 기준으로 properties 읽기 위해 사용한다. 버전 표시용
```xml
<dependency>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>build-helper-maven-plugin</artifactId>
  <version>3.3.0</version>
</dependency>
```
빌드 시, 커스터마이징 코드를 추가적으로 빌드에 포함시키기 위해 사용한다.

그 외, 나머지 빌드 스크립트는 GitHub 소스를 참고하세요.

URL : https://github.com/readra/spring-boot-maven-custom-build

### 일반 빌드
mvn clean package

### 커스텀 빌드
mvn clean package -Pcustom -Dsite.name=customer

### 결과
![일반 빌드 결과](/assets/img/pages/maven-custom-build/common-build.png)

spring-boot-maven-custom-build-0.0.1-SNAPSHOT-v1.tar.gz : 일반 모듈 패키지

spring-boot-maven-custom-build-0.0.1-SNAPSHOT-docs-v1.tar.gz : 일반 API 문서 패키지

![커스텀 빌드 결과](/assets/img/pages/maven-custom-build/custom-build.png)

spring-boot-maven-custom-build-0.0.1-SNAPSHOT-v1.1.tar.gz : 일반 모듈 패키지

spring-boot-maven-custom-build-0.0.1-SNAPSHOT-docs-v1.1.tar.gz : 일반 API 문서 패키지

spring-boot-maven-custom-build-0.0.1-SNAPSHOT-customer-docs-v1.1.tar.gz : 커스텀 API 문서 패키지

### 마치며