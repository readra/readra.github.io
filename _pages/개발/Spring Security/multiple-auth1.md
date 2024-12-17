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

```java
public class ProviderManager implements AuthenticationManager, MessageSourceAware, InitializingBean { 
	/* 중략 */
	public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		Class<? extends Authentication> toTest = authentication.getClass();
		AuthenticationException lastException = null;
		AuthenticationException parentException = null;
		Authentication result = null;
		Authentication parentResult = null;
		int currentPosition = 0;
		int size = this.providers.size();
		Iterator var9 = this.getProviders().iterator();

		while(var9.hasNext()) { // 1번
			AuthenticationProvider provider = (AuthenticationProvider)var9.next();
			if (provider.supports(toTest)) {
				if (logger.isTraceEnabled()) {
					Log var10000 = logger;
					String var10002 = provider.getClass().getSimpleName();
					++currentPosition;
					var10000.trace(LogMessage.format("Authenticating request with %s (%d/%d)", var10002, currentPosition, size));
				}

				try { // 2번
					result = provider.authenticate(authentication);
					if (result != null) {
						this.copyDetails(authentication, result);
						break;
					}
				} catch (InternalAuthenticationServiceException | AccountStatusException var14) { // 3번
					this.prepareException(var14, authentication);
					throw var14;
				} catch (AuthenticationException var15) { // 4번
					lastException = var15;
				}
			}
		}

		if (result == null && this.parent != null) { // 5번
			try {
				parentResult = this.parent.authenticate(authentication);
				result = parentResult;
			} catch (ProviderNotFoundException var12) {
			} catch (AuthenticationException var13) { // 6번
				parentException = var13;
				lastException = var13;
			}
		}

		if (result != null) { // 7번
			if (this.eraseCredentialsAfterAuthentication && result instanceof CredentialsContainer) {
				((CredentialsContainer)result).eraseCredentials();
			}

			if (parentResult == null) {
				this.eventPublisher.publishAuthenticationSuccess(result);
			}

			return result;
		} else {
			if (lastException == null) { // 8번
				lastException = new ProviderNotFoundException(this.messages.getMessage("ProviderManager.providerNotFound", new Object[]{toTest.getName()}, "No AuthenticationProvider found for {0}"));
			}

			if (parentException == null) {
				this.prepareException((AuthenticationException)lastException, authentication);
			}

			throw lastException;
		}
	}
	/* 중략 */
}
```