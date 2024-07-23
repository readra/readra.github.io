---
title: "Spring Boot + Maven 환경에서 커스텀 빌드하기 (2)"
date: "2024-07-23"
thumbnail: "/assets/img/thumbnail/custom2.webp"
---

# Spring Boot + Maven 환경에서 커스텀 빌드하기 (2)
---

## REST API 예제 작성
예제는 최대한 간단하게 작성한다. (이게 중요한 게 아니니까) 

### Test Code
```java
package com.toy.springbootmavencustombuild.rest;

import com.toy.springbootmavencustombuild.service.HelloWorldService;
import org.junit.Before;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.restdocs.AutoConfigureRestDocs;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.restdocs.JUnitRestDocumentation;
import org.springframework.restdocs.mockmvc.RestDocumentationRequestBuilders;
import org.springframework.restdocs.payload.JsonFieldType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import static org.mockito.ArgumentMatchers.anyInt;
import static org.mockito.BDDMockito.given;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.document;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.*;
import static org.springframework.restdocs.payload.PayloadDocumentation.fieldWithPath;
import static org.springframework.restdocs.payload.PayloadDocumentation.responseFields;
import static org.springframework.restdocs.request.RequestDocumentation.parameterWithName;
import static org.springframework.restdocs.request.RequestDocumentation.pathParameters;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

/**
 * HelloWorldController 테스트 코드
 *
 * @author readra
 */
@AutoConfigureRestDocs
@WebMvcTest(HelloWorldController.class)
public class HelloWorldControllerTest {
	@Autowired
	private MockMvc mockMvc;
	@Autowired
	private WebApplicationContext webApplicationContext;
	@MockBean
	private HelloWorldService helloWorldService;

	@Before
	public void setUp() {
		this.mockMvc = MockMvcBuilders.webAppContextSetup(this.webApplicationContext)
				.apply(documentationConfiguration(new JUnitRestDocumentation()))
				.build();
	}

	@Test
	@DisplayName("Hello World")
	void helloWorldTest() throws Exception {
		final int code = 100;
		final String result = String.format(HelloWorldService.HELLO_WORLD, code);

		// given
		given(helloWorldService.helloWorld(anyInt())).willReturn(result);

		// when
		ResultActions resultActions = mockMvc.perform(
				RestDocumentationRequestBuilders.get("/v1/hello-world/{code}", code)
						.contentType(MediaType.APPLICATION_JSON)
		);

		// then
		resultActions.andExpect(status().isOk())
				.andDo(
						document(
								"Hello World",
								preprocessRequest(prettyPrint()),
								preprocessResponse(prettyPrint()),
								pathParameters(
										parameterWithName("code").description("코드")
								),
								responseFields(
										fieldWithPath("message").type(JsonFieldType.STRING).description("메시지"),
										fieldWithPath("code").type(JsonFieldType.NUMBER).description("코드")
								)
						)

				)
				.andDo(print());
	}
}

```

### Controller
```java
package com.toy.springbootmavencustombuild.rest;

import com.toy.springbootmavencustombuild.service.HelloWorldService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * 예제 Controller Layer
 *
 * @author readra
 */
@RestController
@RequestMapping("/v1")
public class HelloWorldController {
	private final HelloWorldService helloWorldService;

	@Autowired
	public HelloWorldController(final HelloWorldService helloWorldService) {
		this.helloWorldService = helloWorldService;
	}

	/**
	 * 안녕하세요!
	 *
	 * @param code
	 * 		코드
	 * @return
	 *      안녕하세요!
	 */
	@GetMapping("/hello-world/{code}")
	public String helloWorld(@PathVariable int code) {
		return helloWorldService.helloWorld(code);
	}
}
```

### Service
```java
package com.toy.springbootmavencustombuild.service;

import org.springframework.stereotype.Service;

/**
 * 예제 Service Layer
 *
 * @author readra
 */
@Service
public class HelloWorldService {
	public static final String HELLO_WORLD = "{ \"message\" : \"Hello World\", \"code\" : %d }";

	/**
	 * 안녕하세요!
	 *
	 * @param code
	 * 		코드
	 * @return
	 *      안녕하세요!
	 */
	public String helloWorld(int code) {
		return String.format(HELLO_WORLD, code);
	}
}
```