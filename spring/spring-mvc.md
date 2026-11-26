# Spring MVC & DispatcherServlet

<sub>Spring · 2026.10.13 · 10주차</sub>

[Study Index](../README.md)

요청 URL만 보고 적절한 Controller 메서드를 찾아 실행한 뒤 JSON 응답을 만드는 과정을 살펴봅니다. 각 단계에서 담당하는 객체의 이름과 역할을 짝지어 보세요.

**복습할 질문**

- [Spring MVC에서 요청이 처리되고 응답되는 흐름을 설명해주세요.](#q1)
- [HandlerMapping과 HandlerAdapter의 역할은 무엇인가요?](#q21)
- [Spring MVC에서 Filter와 Interceptor는 어떤 차이가 있으며 각각 언제 사용하나요?](#q30)

## 목차

- [Spring MVC / DispatcherServlet 통합 정리](#section-01)
- [Spring MVC 기본](#section-02)
- [DispatcherServlet](#section-03)
- [HandlerMapping / HandlerAdapter](#section-04)
- [Filter / Interceptor](#section-05)
- [응답 처리](#section-06)
- [예외 처리](#section-07)
- [동시성 / Thread](#section-08)
- [전체 요청 흐름 한 번에 보기](#section-09)

---

<a name="section-01"></a>
## Spring MVC / DispatcherServlet 통합 정리

<a name="q1"></a>
### Q1. [대표 질문] Spring MVC에서 요청이 처리되고 응답되는 흐름을 설명해주세요.

> "클라이언트 요청이 들어오면 먼저 Filter Chain을 거쳐 DispatcherServlet에 도달합니다.
>
> DispatcherServlet은 HandlerMapping을 통해 요청을 처리할 Handler와 Interceptor를 찾습니다. 이후 Interceptor의 `preHandle()`을 실행하고, HandlerAdapter를 통해 Controller 메서드를 실행합니다.
>
> Controller는 필요한 경우 Service와 Repository를 호출하고 결과를 반환합니다. 반환값은 HandlerAdapter 안에서 처리됩니다.
>
> 뷰 이름을 반환하는 동기 요청에서는 postHandle 후 뷰를 찾아 렌더링합니다. @ResponseBody를 사용하는 동기 요청에서는 메시지 변환기가 본문을 쓴 뒤 postHandle이 호출됩니다.
>
> 마지막으로 `afterCompletion()`이 호출되고 Filter Chain을 다시 거쳐 클라이언트에게 응답이 반환됩니다."

**핵심 요약**

Filter → DispatcherServlet → HandlerMapping → preHandle → HandlerAdapter → Controller → 본문 작성 → postHandle → afterCompletion → Filter

---

<a name="section-02"></a>
## Spring MVC 기본

<a name="q2"></a>
### Q2. Spring MVC는 왜 필요하며, Servlet만 사용하는 것과 어떤 차이가 있나요?

> "Servlet만 직접 사용하면 URL 매핑, 요청 파라미터 처리, 타입 변환, 응답 작성 같은 반복적인 HTTP 처리 작업을 개발자가 직접 구현해야 합니다.
>
> Spring MVC는 DispatcherServlet을 중심으로 이러한 공통 웹 처리 과정을 추상화하여 Controller 기반으로 개발할 수 있게 해줍니다."

**핵심 요약**

**Spring MVC는 Servlet 기반의 반복적인 HTTP 처리를 추상화하여 Controller 중심으로 개발할 수 있게 해준다.**

---

<a name="q3"></a>
### Q3. Spring MVC에서 MVC 패턴을 사용하는 이유는 무엇인가요?

> "MVC는 Model, View, Controller로 역할을 분리하여 관심사를 나누기 위해 사용합니다.
>
> Controller는 요청을 받아 필요한 로직을 호출하고, Model은 화면이나 응답에 필요한 데이터와 애플리케이션 상태를 표현하며, View는 결과를 사용자에게 보여주는 역할을 합니다.
>
> 이렇게 역할을 분리하면 한 부분이 변경되어도 다른 부분에 미치는 영향을 줄일 수 있습니다."

**핵심 요약**

**MVC는 Model, View, Controller의 책임을 분리해 관심사를 나누고 유지보수성을 높인다.**

---

<a name="q4"></a>
### Q4. Spring MVC와 Spring Boot의 차이는 무엇인가요?

> "Spring MVC는 HTTP 요청을 Controller에 연결하고 응답을 생성하는 웹 프레임 워크입니다.
>
> Spring Boot는 Spring 애플리케이션의 설정, 실행, 배포를 편리하게 해주는 프로젝트로 자동 설정과 내장 서버 등을 제공합니다.
>
> 따라서 Spring MVC는 웹 요청 처리에 초점이 있고, Spring Boot는 Spring 애플리케이션을 쉽게 구성하고 실행하는 데 초점이 있습니다."

**핵심 요약**

**Spring MVC는 웹 요청 처리 프레임워크, Spring Boot는 Spring 애플리케이션의 설정과 실행을 간소화한다.**

---

<a name="q5"></a>
### Q5. Spring MVC는 멀티스레드 환경에서 동작하나요?

> "네. 전통적인 Servlet 기반 Spring MVC에서는 Tomcat의 여러 작업 Thread가 동시에 HTTP 요청을 처리합니다.
>
> 따라서 하나의 DispatcherServlet이나 기본적으로 Singleton인 Controller 인스턴스에 여러 Thread가 동시에 접근할 수 있습니다.
>
> 이런 이유로 Controller나 Servlet에 요청별 변경 가능한 상태를 인스턴스 필드로 저장하면 동시성 문제가 발생할 수 있습니다."

**핵심 요약**

**Spring MVC에서는 여러 Tomcat Thread가 같은 Singleton Controller나 DispatcherServlet에 동시에 접근할 수 있다.**

---

<a name="section-03"></a>
## DispatcherServlet

<a name="q6"></a>
### Q6. DispatcherServlet은 무엇인가요?

> "DispatcherServlet은 Spring MVC의 Front Controller입니다.
>
> 요청을 중앙에서 먼저 받고 HandlerMapping을 통해 처리할 Handler를 찾은 뒤 HandlerAdapter를 통해 실행하며, 이후 응답 처리와 예외 처리까지 Spring MVC의 전체 요청 흐름을 조정합니다."

**핵심 요약**

**DispatcherServlet은 Spring MVC 요청 처리 전체를 조정하는 Front Controller**

---

<a name="q7"></a>
### Q7. Front Controller Pattern의 장점은 무엇인가요?

> "모든 요청이 공통 진입점을 통과하기 때문에 요청 매핑, 예외 처리, 데이터 변환 같은 Spring MVC의 공통 처리 흐름을 중앙화할 수 있습니다.
>
> 또한 URL마다 Servlet을 직접 작성하지 않고 Controller 메서드만 추가하면 되므로 중복을 줄일 수 있습니다."

**핵심 요약**

**공통 요청 처리를 중앙화하고 URL마다 Servlet을 직접 만들 필요를 줄인다.**

---

<a name="q8"></a>
### Q8. DispatcherServlet은 일반 Servlet과 어떤 차이가 있나요?

> "일반 Servlet은 개발자가 직접 HTTP 요청 처리 로직을 구현하는 데 사용합니다.
>
> DispatcherServlet도 Servlet이지만 Spring MVC의 Front Controller 역할을 하도록 이미 구현된 Servlet입니다.
>
> 직접 비즈니스 로직을 처리하기보다 적절한 Controller를 찾아 요청 처리를 위임합니다."

**핵심 요약**

**DispatcherServlet은 Spring MVC의 요청 흐름을 조정하도록 구현된 Servlet**

---

<a name="q9"></a>
### Q9. Servlet과 DispatcherServlet은 어떤 관계인가요?

> "Servlet은 Java 웹 환경에서 HTTP 요청과 응답을 처리하기 위한 표준 기술입니다.
>
> DispatcherServlet은 Spring MVC가 제공하는 Servlet 구현체로, Spring MVC의 Front Controller 역할을 합니다.
>
> Tomcat 같은 Servlet Container가 DispatcherServlet을 실행하고 생명주기를 관리하며, DispatcherServlet은 Spring MVC 구성 요소를 사용해 요청을 Controller로 연결합니다."

**핵심 요약**

**DispatcherServlet은 Servlet의 한 종류이며 Spring MVC의 Front Controller 역할을 한다.**

---

<a name="q10"></a>
### Q10. DispatcherServlet도 일반 Servlet과 생명주기가 같나요?

> "네. DispatcherServlet도 Servlet이기 때문에 기본적인 Servlet 생명주기를 따르며 Servlet Container가 생명주기를 관리합니다.
>
> 객체가 생성되고 초기화된 뒤 여러 HTTP 요청을 반복해서 처리하며, 애플리케이션이 종료되거나 Servlet이 제거될 때 종료됩니다.
>
> 요청이 들어올 때마다 새로운 DispatcherServlet 객체를 만드는 것은 아닙니다."

**핵심 요약**

**DispatcherServlet도 Servlet이므로 Container가 생성·초기화·요청 처리·종료의 생명주기를 관리한다.**

생명 주기

Container가 생성하고 `init()`으로 초기화한 뒤 요청마다 `service()`를 호출하고, 종료 시 `destroy()`를 호출

---

<a name="q11"></a>
### Q11. DispatcherServlet과 Tomcat의 관계는 무엇인가요?

> "Tomcat은 Servlet Container로서 HTTP 연결을 관리하고 Servlet을 실행합니다.
>
> DispatcherServlet은 Tomcat 위에서 실행되는 하나의 Servlet이며, Spring MVC 내부에서 요청을 적절한 Controller로 연결합니다."

**핵심 요약**

**Tomcat은 DispatcherServlet을 실행하고, DispatcherServlet은 Controller를 실행한다.**

---

<a name="q12"></a>
### Q12. Spring Boot에서는 DispatcherServlet을 직접 등록하지 않아도 HTTP 요청을 처리할 수 있는 이유는 무엇인가요?

> "Spring Boot의 자동 설정이 DispatcherServlet을 Bean으로 등록하고 Servlet Container에 매핑해줍니다.
>
> 따라서 개발자가 `web.xml`에 직접 DispatcherServlet을 등록하지 않아도 기본 설정으로 동작합니다."

**핵심 요약**

**Spring Boot가 DispatcherServlet 등록과 매핑을 자동 설정해준다.**

---

<a name="q13"></a>
### Q13. DispatcherServlet은 몇 개인가요?

> "일반적인 Spring Boot 애플리케이션에서는 하나의 DispatcherServlet을 사용하는 경우가 많습니다.
>
> 필요하다면 서로 다른 URL 패턴에 여러 DispatcherServlet을 등록할 수도 있지만 일반적인 구조는 하나입니다."

**핵심 요약**

**보통 DispatcherServlet 하나가 Spring MVC 요청을 처리한다.**

---

<a name="q14"></a>
### Q14. DispatcherServlet에서 요청 분배를 담당하는 핵심 메서드는 무엇인가요?

> "DispatcherServlet의 핵심 요청 분배 메서드는 `doDispatch()`입니다.
>
> `doDispatch()`는 HandlerMapping을 통해 요청을 처리할 Handler를 찾고, 적절한 HandlerAdapter를 선택해 Handler를 실행하며 결과와 예외 처리 과정도 조정합니다."

**핵심 요약**

**doDispatch()는 Handler를 찾고 실행하며 Spring MVC의 핵심 요청 흐름을 조정한다.**

---

<a name="q15"></a>
### Q15. 요청이 들어오면 무조건 doDispatch()가 호출되나요?

> "아닙니다.
>
> 요청이 정상적으로 DispatcherServlet까지 전달되어 Spring MVC 처리가 진행될 때 `doDispatch()`가 호출됩니다.
>
> DispatcherServlet에 도달하기 전에 Filter에서 요청이 차단되거나 앞단에서 응답이 완료되면 `doDispatch()`까지 도달하지 않을 수 있습니다."

**핵심 요약**

**DispatcherServlet까지 정상적으로 도달한 요청이 Spring MVC 처리를 진행할 때 doDispatch()가 호출된다.**

---

<a name="q16"></a>
### Q16. doService()와 doDispatch()의 차이는 무엇인가요?

> "`doService()`는 Spring MVC 요청 처리를 위한 공통 환경을 준비하는 역할을 합니다.
>
> `doDispatch()`는 실제 Handler를 찾고 실행하며 결과와 예외를 처리하는 핵심 Dispatch 로직을 수행합니다."

**핵심 요약**

**doService는 요청 환경 준비, doDispatch는 Handler 탐색과 실행**

---

<a name="q17"></a>
### Q17. 정적 리소스 요청은 어떻게 처리되나요?

> "Spring Boot에서는 정적 리소스 처리를 위한 ResourceHandler가 자동 구성됩니다.
>
> 정적 리소스 요청과 매핑되면 `ResourceHttpRequestHandler`가 해당 파일을 찾아 응답합니다.
>
> 예를 들어 `src/main/resources/static/css/main.css`에 있는 파일은 일반적으로 `/css/main.css` 같은 경로로 접근할 수 있습니다."

**핵심 요약**

**정적 리소스는 ResourceHttpRequestHandler가 파일을 찾아 응답한다.**

---

<a name="q18"></a>
### Q18. DispatcherServlet은 multipart/form-data 형식의 파일 업로드 요청을 어떻게 처리하나요?

> "multipart 요청이 들어오면 Spring이 MultipartResolver를 이용해 multipart 요청을 처리합니다.
>
> Controller에서는 `MultipartFile` 등을 이용해 업로드된 파일에 접근할 수 있습니다."

**핵심 요약**

**MultipartResolver가 파일 업로드 요청을 처리하고 Controller는 MultipartFile로 접근한다.**

---

<a name="q19"></a>
### Q19. DispatcherServlet은 비동기 요청을 어떻게 처리하며, 동기 요청과 어떤 차이가 있나요?

> "일반적인 동기 요청에서는 요청을 처리하는 Tomcat Thread가 응답이 끝날 때까지 해당 요청에 점유됩니다.
>
> Servlet 비동기 처리를 사용하면 장시간 작업이 진행되는 동안 요청 Thread를 계속 점유하지 않고 Container에 반환할 수 있으며, 작업이 완료된 뒤 다시 Dispatch하여 응답 처리를 이어갈 수 있습니다."

**핵심 요약**

**동기는 요청 Thread를 끝까지 점유하고, Servlet 비동기는 기다리는 동안 Thread를 반환할 수 있다.**

---

<a name="q20"></a>
### Q20. DispatcherServlet과 ApplicationContext의 관계는 무엇인가요?

> "전통적인 Spring MVC에서는 Root ApplicationContext와 DispatcherServlet 전용 WebApplicationContext를 계층적으로 구성할 수 있습니다.
>
> 일반적으로 Root Context에는 Service, Repository 같은 공통 Bean을 두고, DispatcherServlet 전용 Context에는 Controller와 MVC 관련 Bean을 둘 수 있습니다.
>
> 자식 Context는 부모 Context의 Bean을 참조할 수 있지만 반대 방향은 불가능합니다."

**핵심 요약**

**DispatcherServlet은 전용 WebApplicationContext를 가질 수 있고, 자식 Context는 부모 Bean을 참조할 수 있다.**

---

<a name="section-04"></a>
## HandlerMapping / HandlerAdapter

<a name="q21"></a>
### Q21. HandlerMapping과 HandlerAdapter의 역할은 무엇인가요?

> "HandlerMapping은 요청을 처리할 Handler를 찾는 역할을 하고, HandlerAdapter는 찾아낸 Handler를 실제로 실행하는 역할을 합니다.
>
> 일반적인 Spring MVC에서는 Handler가 Controller 메서드인 경우가 많습니다."

**핵심 요약**

**HandlerMapping은 찾고, HandlerAdapter는 실행한다.**

---

<a name="q22"></a>
### Q22. HandlerMapping과 HandlerAdapter를 분리한 이유는 무엇인가요?

> "DispatcherServlet이 특정 Controller나 Handler의 구체적인 실행 방식에 직접 의존하지 않도록 하기 위해서입니다.
>
> 찾는 역할과 실행하는 역할을 분리하면 새로운 형태의 Handler가 추가되어도 적절한 HandlerAdapter를 추가하는 방식으로 확장할 수 있습니다.
>
> 따라서 확장에 유리하고 OCP에 부합하는 구조를 만들 수 있습니다."

**핵심 요약**

**찾는 책임과 실행 책임을 분리해 DispatcherServlet이 Handler의 구체적인 실행 방식에 의존하지 않게 한다.**

---

<a name="q23"></a>
### Q23. DispatcherServlet이 Controller를 직접 실행하지 않는 이유는 무엇인가요?

> "DispatcherServlet이 모든 형태의 Controller와 Handler 실행 방식을 직접 알게 되면 특정 구현 방식에 강하게 의존하게 됩니다.
>
> 그래서 HandlerMapping이 Handler를 찾고 HandlerAdapter가 실행을 담당하도록 책임을 분리합니다."

**핵심 요약**

**DispatcherServlet이 Handler 구현 방식에 직접 의존하지 않기 위해 실행을 HandlerAdapter에 위임한다.**

---

<a name="q24"></a>
### Q24. HandlerMapping은 Controller를 어떻게 찾나요?

> "`@RequestMapping`, `@GetMapping`, `@PostMapping` 같은 매핑 정보를 애플리케이션 시작 시 등록해두고, 요청이 들어오면 URL과 HTTP Method 등의 조건을 기준으로 처리할 Controller 메서드를 찾습니다."

**핵심 요약**

**URL과 HTTP Method 매핑 정보를 기준으로 Controller 메서드를 찾는다.**

---

<a name="q25"></a>
### Q25. Controller 메서드의 파라미터는 어떻게 채워지나요?

> "HandlerAdapter가 ArgumentResolver를 이용해 Controller 메서드의 각 파라미터 값을 만들어줍니다.
>
> `@RequestParam`은 요청 파라미터에서 값을 가져오고, `@PathVariable`은 URL 경로에서 값을 가져오며, `@RequestBody`는 HttpMessageConverter를 통해 Request Body를 Java 객체로 변환합니다."

**핵심 요약**

**HandlerAdapter가 ArgumentResolver를 이용해 Controller 파라미터 값을 채운다.**

---

<a name="q26"></a>
### Q26. @RequestParam과 @RequestBody의 차이는 무엇인가요?

> "`@RequestParam`은 Query Parameter나 Form Parameter 같은 요청 파라미터의 값을 가져와 필요한 타입으로 변환합니다.
>
> `@RequestBody`는 HTTP Request Body를 읽어 HttpMessageConverter를 통해 Java 객체 등으로 변환합니다."

**핵심 요약**

**RequestParam은 요청 파라미터, RequestBody는 HTTP Body를 읽는다.**

---

<a name="q27"></a>
### Q27. @ModelAttribute와 @RequestBody의 차이는 무엇인가요?

> "`@ModelAttribute`는 요청 파라미터를 객체의 프로퍼티에 바인딩하는 방식이고, `@RequestBody`는 HTTP Body를 HttpMessageConverter를 이용해 객체로 변환하는 방식입니다.
>
> 일반적으로 Form이나 Query Parameter 기반 요청은 `@ModelAttribute`, JSON Body 요청은 `@RequestBody`를 많이 사용합니다."

**핵심 요약**

**ModelAttribute는 요청 파라미터를 객체에 바인딩하고, RequestBody는 HTTP Body를 객체로 변환한다.**

---

<a name="q28"></a>
### Q28. JSON 요청에서 @RequestBody를 생략하면 어떻게 되나요?

> "`@RequestBody`를 생략하면 Spring이 HTTP Body의 JSON 데이터를 읽어서 DTO로 변환하라는 의미로 처리하지 않습니다. 따라서 JSON Body가 DTO에 정상적으로 매핑되지 않을 수 있습니다. `@RequestBody`를 붙이면 HttpMessageConverter가 JSON Body를 Java 객체로 변환합니다."

**핵심 요약**

**JSON Body를 Java 객체로 받으려면 일반적으로 @RequestBody를 명시해야 한다.**

---

<a name="q29"></a>
### Q29. 커스텀 ArgumentResolver는 언제 사용하나요?

> "여러 Controller에서 반복적으로 수행하는 파라미터 추출 로직이 있을 때 사용할 수 있습니다.
>
> 예를 들어 JWT에서 로그인 사용자 정보를 매번 직접 꺼내는 대신 `@LoginUser` 같은 어노테이션과 커스텀 ArgumentResolver를 만들어 Controller 파라미터로 바로 전달할 수 있습니다."

**핵심 요약**

**반복되는 Controller 파라미터 생성 로직을 공통화할 때 커스텀 ArgumentResolver를 사용한다.**

---

<a name="section-05"></a>
## Filter / Interceptor

<a name="q30"></a>
### Q30. Spring MVC에서 Filter와 Interceptor는 어떤 차이가 있으며 각각 언제 사용하나요?

> **"Filter와 Interceptor의 가장 큰 차이는 동작 위치입니다.**
>
> **Filter는 Servlet Container 레벨에서 동작해서 DispatcherServlet에 들어가기 전과 나온 후의 요청·응답을 처리합니다.** 그래서 인코딩, CORS, Spring Security처럼 애플리케이션 전체에 공통으로 적용되는 처리에 적합합니다.
>
> 반면 **Interceptor는 Spring MVC 내부에서 Controller가 실행되기 전후에 동작합니다.** 그래서 특정 Controller 접근 제어, 로그인 여부 확인, 요청 로깅처럼 Controller와 관련된 공통 처리에 적합합니다.
>
> 둘 다 인증이나 로깅 같은 기능을 구현할 수는 있지만, **Filter는 Servlet 계층의 전역 공통 처리, Interceptor는 Spring MVC의 Controller 전후 처리에 더 적합합니다.**"

**핵심 요약**

**Filter는 Servlet 계층의 공통 처리, Interceptor는 Spring MVC Controller 전후의 공통 처리에 적합하다.**

Filter는 CORS·보안처럼 전체 요청에 적용할 때, Interceptor는 특정 Controller의 접근 검사나 로깅 같은 MVC 처리가 필요할 때 사용합니다.

---

<a name="q31"></a>
### Q31. 인증 처리는 Filter와 Interceptor 중 어디서 수행하나요?

> "둘 다 인증 관련 로직을 구현할 수 있습니다.
>
> 다만 Spring Security는 기본적으로 Servlet Filter Chain 기반으로 동작하기 때문에 Spring Security를 사용하는 애플리케이션에서는 인증·인가 처리가 주로 Filter 계층에서 이루어집니다.
>
> Interceptor에서도 로그인 여부 검사 같은 Spring MVC 수준의 공통 처리를 구현할 수 있습니다."

**핵심 요약**

**둘 다 가능하지만 Spring Security의 인증·인가는 주로 Filter Chain에서 처리한다.**

---

<a name="q32"></a>
### Q32. Filter → Interceptor → AOP의 실행 위치는 어떻게 다른가요?

> "Filter는 Servlet Container 레벨에서 동작하고, Interceptor는 Spring MVC의 Handler 실행 전후에서 동작합니다.
>
> AOP는 Spring Bean의 메서드 호출 시점에 적용됩니다."

**핵심 요약**

**Filter는 Servlet 계층, Interceptor는 MVC Handler 계층, AOP는 Spring Bean 메서드 호출 계층**

---

<a name="q33"></a>
### Q33. Interceptor의 주요 메서드 3가지는 무엇인가요?

> "`preHandle()`은 Handler 실행 전에 호출됩니다.
>
> `postHandle()`은 Handler가 정상적으로 실행된 뒤 View 렌더링 전에 호출됩니다.
>
> `afterCompletion()`은 요청 처리가 완료된 뒤 호출되어 로깅이나 자원 정리 등에 사용할 수 있습니다.
>
> 예외나 비동기 요청 등에 따라 구체적인 호출 여부와 시점에는 차이가 있을 수 있습니다."

**핵심 요약**

**preHandle은 실행 전, postHandle은 정상 실행 후, afterCompletion은 요청 완료 후**

---

<a name="q34"></a>
### Q34. Interceptor가 여러 개면 실행 순서는 어떻게 되나요?

> "`preHandle()`은 등록된 순서대로 실행되고, `postHandle()`과 `afterCompletion()`은 역순으로 실행됩니다.
>
> 스택처럼 들어갈 때는 순서대로, 나올 때는 역순이라고 이해하면 됩니다."

**핵심 요약**

**preHandle은 순서대로, postHandle과 afterCompletion은 역순**

---

<a name="q35"></a>
### Q35. preHandle()에서 false를 반환하면 어떻게 되나요?

> "해당 Interceptor의 `preHandle()`이 false를 반환하면 이후 Interceptor와 Controller는 실행되지 않습니다.
>
> 다만 그 이전까지 `preHandle()`을 성공적으로 통과한 Interceptor의 `afterCompletion()`은 역순으로 호출될 수 있습니다."

**핵심 요약**

**preHandle이 false면 이후 흐름은 중단되지만, 이전에 통과한 Interceptor의 afterCompletion은 실행될 수 있다.**

---

<a name="q36"></a>
### Q36. @RestController에서 postHandle()로 응답 Body를 수정할 수 있나요?

> "`@ResponseBody`나 `@RestController`의 응답은 HandlerAdapter의 반환값 처리 과정에서 HttpMessageConverter가 응답 Body를 작성합니다.
>
> 따라서 `postHandle()`은 Response Body 수정 용도로 적합하지 않습니다.
>
> 공통적으로 응답 Body를 가공하려면 `ResponseBodyAdvice`를 사용하는 것이 적절합니다."

**핵심 요약**

**REST 응답 Body 가공은 postHandle보다 ResponseBodyAdvice가 적합하다.**

---

<a name="q37"></a>
### Q37. @RestControllerAdvice는 Filter와 Interceptor 중 어디에 속하나요?

> "`@RestControllerAdvice`는 Filter도 Interceptor도 아닙니다.
>
> Spring MVC의 전역 예외 처리 기능으로, `@ControllerAdvice`와 `@ResponseBody`가 결합된 형태입니다.
>
> HandlerExceptionResolver 체계를 통해 Spring MVC 처리 과정에서 발생한 예외를 공통적으로 처리할 수 있습니다."

**핵심 요약**

**RestControllerAdvice는 Filter나 Interceptor가 아니라 Spring MVC의 전역 예외 처리 기능이다.**

---

<a name="section-06"></a>
## 응답 처리

<a name="q38"></a>
### Q38. @Controller와 @RestController의 차이는 무엇인가요?

> "`@Controller`는 기본적으로 반환값을 View 이름으로 해석합니다. 다만 `@ResponseBody`를 사용하면 HTTP Response Body에 직접 값을 작성할 수도 있습니다.
>
> `@RestController`는 `@Controller + @ResponseBody` 형태이므로 기본적으로 반환값을 HTTP 응답 Body에 작성합니다."

**핵심 요약**

**Controller는 기본적으로 View, RestController는 기본적으로 Response Body를 반환한다.**

---

**ViewResolver**

<a name="q39"></a>
### Q39. ViewResolver란 무엇인가요?

> "ViewResolver는 Controller가 반환한 논리적인 View 이름을 실제 화면을 렌더링할 View 객체와 연결하는 역할을 합니다.
>
> 예를 들어 Controller가 `"home"`을 반환하면 설정에 따라 JSP나 Thymeleaf 등의 실제 View를 찾아 렌더링할 수 있도록 합니다."

**핵심 요약**

**ViewResolver는 Controller가 반환한 View 이름을 실제 View와 연결한다.**

---

<a name="q40"></a>
### Q40. ModelAndView란 무엇인가요?

> "ModelAndView는 View에 전달할 데이터인 Model과 렌더링할 View 정보를 함께 담는 객체입니다.
>
> 주로 서버에서 HTML 화면을 렌더링하는 Spring MVC 구조에서 사용할 수 있습니다."

**핵심 요약**

**ModelAndView는 화면에 전달할 데이터와 View 정보를 함께 담는 객체**

---

<a name="q41"></a>
### Q41. 모든 Controller가 ModelAndView를 반환해야 하나요?

> "아닙니다.
>
> Controller는 `String`, `ModelAndView`, Java 객체, `ResponseEntity` 등 다양한 타입을 반환할 수 있습니다.
>
> Spring MVC가 반환 타입에 맞는 처리 방식을 선택해 응답을 생성합니다."

**핵심 요약**

**Controller는 ModelAndView뿐 아니라 String, 객체, ResponseEntity 등 다양한 타입을 반환할 수 있다.**

---

**HttpMessageConverter**

<a name="q42"></a>
### Q42. HttpMessageConverter란 무엇인가요?

> "HttpMessageConverter는 HTTP 요청·응답 Body와 Java 객체 사이의 변환을 담당하는 Spring MVC 구성 요소입니다.
>
> 예를 들어 JSON 요청 Body를 Java 객체로 역직렬화하거나, Java 객체를 JSON으로 직렬화하여 HTTP 응답 Body에 작성할 수 있습니다."

**핵심 요약**

**HttpMessageConverter는 HTTP Body와 Java 객체 사이를 변환한다.**

---

<a name="q43"></a>
### Q43. @RequestBody와 @ResponseBody의 차이는 무엇인가요?

> "`@RequestBody`는 HTTP 요청 Body를 Java 객체로 변환할 때 사용합니다.
>
> `@ResponseBody`는 Controller의 반환값을 HTTP 응답 Body에 작성할 때 사용합니다.
>
> 이 과정에서 적절한 HttpMessageConverter가 사용될 수 있습니다."

**핵심 요약**

**RequestBody는 요청 Body → Java 객체, ResponseBody는 Java 객체 → 응답 Body**

---

<a name="q44"></a>
### Q44. ViewResolver와 HttpMessageConverter의 차이는 무엇인가요?

> "ViewResolver는 Controller가 반환한 View 이름을 실제 View와 연결하여 HTML 화면을 렌더링하는 데 사용합니다.
>
> HttpMessageConverter는 Java 객체를 JSON이나 XML 같은 HTTP Response Body 형태로 변환하거나, 반대로 Request Body를 Java 객체로 변환합니다."

**핵심 요약**

**ViewResolver는 화면 렌더링, HttpMessageConverter는 HTTP Body와 Java 객체 변환**

---

<a name="q45"></a>
### Q45. SSR과 CSR의 차이는 무엇인가요?

> "SSR은 서버가 완성된 HTML을 만들어 클라이언트에게 전달하는 방식입니다.
>
> CSR은 서버가 주로 JSON 같은 데이터를 전달하고 브라우저의 JavaScript가 화면을 구성하는 방식입니다.
>
> Spring에서는 `@Controller + Thymeleaf`가 SSR에 자주 사용되고, `@RestController + React/Vue` 구조가 CSR에 자주 사용됩니다."

**핵심 요약**

**SSR은 서버가 HTML 생성, CSR은 브라우저가 JavaScript로 화면 생성**

---

<a name="section-07"></a>
## 예외 처리

<a name="q46"></a>
### Q46. Controller에서 예외가 발생하면 어떻게 처리하나요?

> "Controller 실행 중 예외가 발생하면 DispatcherServlet이 예외를 감지하고 HandlerExceptionResolver 체계에 처리를 위임합니다.
>
> `@ExceptionHandler`, `@ControllerAdvice`, `@RestControllerAdvice` 등을 이용하여 예외를 적절한 HTTP 상태 코드와 응답 형태로 변환할 수 있습니다."

**핵심 요약**

**Controller 예외는 HandlerExceptionResolver 체계를 통해 처리한다.**

---

<a name="q47"></a>
### Q47. @ExceptionHandler와 @ControllerAdvice의 차이는 무엇인가요?

> "`@ExceptionHandler`는 특정 Controller 내부에서 발생한 예외를 처리할 수 있습니다.
>
> `@ControllerAdvice`는 여러 Controller에 공통으로 적용되는 전역 예외 처리를 구성할 수 있습니다.
>
> REST API에서는 `@RestControllerAdvice`를 이용해 예외 결과를 Response Body로 반환하기도 합니다."

**핵심 요약**

**ExceptionHandler는 개별 Controller, ControllerAdvice는 전역 예외 처리**

---

<a name="q48"></a>
### Q48. Filter에서 발생한 예외도 @ControllerAdvice가 처리하나요?

> "일반적으로 자동으로 처리되지 않습니다.
>
> Filter는 DispatcherServlet보다 앞단에서 실행되기 때문에 Spring MVC의 `@ControllerAdvice` 예외 처리 범위에 들어오지 않습니다.
>
> 따라서 Filter 자체에서 처리하거나 HandlerExceptionResolver 등에 별도로 위임하는 방식이 필요할 수 있습니다."

**핵심 요약**

**Filter 예외는 DispatcherServlet 이전에 발생하므로 일반적으로 ControllerAdvice가 자동 처리하지 않는다.**

---

<a name="section-08"></a>
## 동시성 / Thread

<a name="q49"></a>
### Q49. Spring MVC는 요청마다 Controller 객체를 새로 만드나요?

> "아닙니다.
>
> Spring Bean의 기본 Scope는 Singleton이기 때문에 일반적인 Controller도 하나의 인스턴스를 여러 요청이 공유합니다.
>
> 따라서 Controller에 요청별 상태를 인스턴스 필드로 저장하면 여러 Thread가 동시에 접근하면서 데이터가 섞일 수 있으므로 무상태로 설계하는 것이 좋습니다."

**핵심 요약**

**Controller는 기본적으로 Singleton Bean이므로 요청별 상태를 인스턴스 변수에 두지 않는 것이 좋다.**

---

<a name="q50"></a>
### Q50. 요청마다 Thread는 어떻게 할당되나요?

> "일반적인 동기식 Spring MVC 요청에서는 Tomcat이 Thread Pool에서 작업 Thread 하나를 할당하여 요청 처리를 담당하게 합니다.
>
> 응답 처리가 끝나면 해당 Thread는 Thread Pool로 반환되어 다른 요청을 처리할 수 있습니다.
>
> 비동기 처리에서는 하나의 요청이 처음부터 끝까지 반드시 같은 Thread에서 처리된다고 볼 수는 없습니다."

**핵심 요약**

**동기 요청은 Tomcat Thread가 처리하고 완료 후 Pool로 반환되며, 비동기 요청에서는 Thread가 바뀔 수 있다.**

---

<a name="section-09"></a>
## 전체 요청 흐름 한 번에 보기

```
Client
  ↓
Filter
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
Interceptor preHandle
  ↓
HandlerAdapter
  ↓
ArgumentResolver
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
DB
  ↓
Controller 반환
  ↓
ViewResolver
또는
HttpMessageConverter
  ↓
Interceptor postHandle / afterCompletion
  ↓
Filter
  ↓
Client
```

**최종 한 줄로 외우면 :**

**Filter → DispatcherServlet → HandlerMapping → Interceptor → HandlerAdapter → Controller → Service → Repository → 응답 변환 → Client**

---

## Related Topics

- [Servlet, Tomcat, WAS](../backend/servlet-tomcat-was.md)
- [HTTP 기본](../network/http/fundamentals.md)

## 참고 자료

- [공식 문서 또는 구현](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/handlermapping-interceptor.html)
