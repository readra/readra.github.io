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
생각보다 블로그에 정리하는 것이 힘든 작업인 것을 알게 됐다.   
가끔 구글링하다보면 똑같은 예제 코드에 똑같은 설명 복붙한 글들이 넘치는 것을 보고 저렇게 할거면 하지를 말아야지 라고 생각한 적이 있다.   
업무에서 개발한 코드를 그대로 사용하지 않고, 다른 Repository 로 따로 정리하는 것은 생각보다 쉽지 않은 작업이었다.   
장점과 단점으로 정리하고 마무리하겠다.   
(장점)   
1. 업무상 개발했던 코드를 다시 한 번 정리하며 학습하는 효과

(단점)   
1. 쌓여있는 개발 노하우나 코드들을 빠르게 정리하고 싶었지만, 생각보다 공수가 많이 들어가는 작업임을 깨달음