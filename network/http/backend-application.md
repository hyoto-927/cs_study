# HTTP Backend Application

<sub>Network · 2026.11.06 · 12주차</sub>


[Study Index](../../README.md)

## Contents

- [실제 백엔드 프로젝트와 연결하는 HTTP 질문](#section-01)

---

<a name="section-01"></a>
## 실제 백엔드 프로젝트와 연결하는 HTTP 질문


특히 프로젝트 경험을 묻는 질문은 실제로 수행하지 않은 경험을 답변에 넣으면 안 되므로, 아래에는 개념을 설명할 수 있는 답변과 상황별 예시를 구분했습니다.

<a name="q1"></a>
### Q1. REST API에서 HTTP 상태 코드는 어떻게 관리해야 하나요?

> "REST API에서는 요청의 처리 결과에 맞는 HTTP 상태 코드를 반환하도록 설계합니다.
>
> 예를 들어 입력값 검증 실패는 400, 인증이 필요한 상황은 401, 권한이 부족한 상황은 403, 존재하지 않는 자원은 404, 예상하지 못한 서버 내부 오류는 500 등으로 구분할 수 있습니다.
>
> Spring에서는 `@RestControllerAdvice`와 `@ExceptionHandler` 등을 활용하여 예외를 일관된 응답 형식으로 처리할 수 있습니다."

**핵심 요약**

REST API에서는 오류 원인에 맞는 HTTP 상태 코드를 반환하고 예외 처리를 일관되게 관리해야 한다.

<a name="q1-1"></a>
#### ↳ Q1-1. 모든 오류를 500으로 처리하면 어떤 문제가 있나요?

> "잘못된 입력값으로 인한 오류와 실제 서버 내부 장애를 구분하기 어렵습니다.
>
> 클라이언트가 재시도해도 해결되지 않는 요청을 반복할 수 있고, 서버 모니터링에서 실제 장애를 파악하기도 어려워집니다."

**핵심 요약**

모든 오류를 500으로 처리하면 클라이언트 오류와 서버 장애를 구분하기 어렵다.

<a name="q1-2"></a>
#### ↳ Q1-2. [핵심 질문] 400과 422는 어떻게 구분하나요?

> "400 Bad Request는 서버가 요청을 처리할 수 없는 잘못된 요청을 나타냅니다.
>
> 422 Unprocessable Content는 요청의 콘텐츠 타입과 문법은 이해했지만 내용의 지시를 처리할 수 없는 상황에 사용할 수 있습니다.
>
> 실제 유효성 검증 실패를 400과 422 중 어느 코드로 처리할지는 API의 오류 응답 정책에 따라 일관되게 설계해야 합니다."

**핵심 요약**

400은 잘못된 요청 전반에 사용하고 422는 이해한 요청 내용을 의미적으로 처리할 수 없는 경우에 사용할 수 있다.

<a name="q2"></a>
### Q2. Spring Boot에서 클라이언트 요청은 어떤 순서로 처리되나요?

> "일반적인 Spring MVC에서는 Tomcat 같은 서블릿 컨테이너가 요청을 받고 서블릿 필터 체인을 거쳐 DispatcherServlet에 전달합니다.
>
> DispatcherServlet은 HandlerMapping과 HandlerAdapter를 활용해 Controller를 실행하고, 필요하면 Interceptor가 Controller 실행 전후에 개입합니다.
>
> 이후 반환값은 HttpMessageConverter 등을 통해 JSON으로 변환되어 HTTP 응답으로 전달될 수 있습니다."

```
Client
   ↓
Tomcat
   ↓
Filter Chain
   ↓
DispatcherServlet
   ↓
HandlerMapping / HandlerAdapter
   ↓
Interceptor (설정된 경우)
   ↓
Controller
   ↓
Service → Repository
   ↓
응답 변환
   ↓
Client
```

**핵심 요약**

Spring MVC 요청은 Filter와 DispatcherServlet을 거쳐 Controller에서 처리되고 HTTP 응답으로 변환된다.

<a name="q2-1"></a>
#### ↳ Q2-1. 인증 처리를 왜 Controller가 아니라 Filter에서 수행하나요?

> "인증은 여러 Controller에서 공통으로 필요한 기능이기 때문입니다.
>
> Filter에서 처리하면 요청이 Controller에 도달하기 전에 인증 여부를 확인하고 필요한 경우 차단할 수 있습니다.
>
> Spring Security도 Security Filter Chain을 기반으로 인증과 인가 기능을 제공하므로 Controller마다 인증 로직을 반복 작성하지 않아도 됩니다."

**핵심 요약**

인증은 공통 기능이므로 Controller 이전의 Filter에서 일관되게 처리하는 것이 유리하다.

<a name="q3"></a>
### Q3. 외부 API 호출에 Timeout을 설정해야 하는 이유는 무엇인가요?

> "외부 API가 응답하지 않거나 지나치게 느릴 때 요청 처리가 장시간 대기하는 문제를 방지하기 위해서입니다.
>
> 특히 Spring MVC의 동기식 요청에서 외부 API를 기다리는 동안 처리 스레드와 연결 자원이 점유될 수 있습니다.
>
> 이러한 요청이 누적되면 서버의 처리량이 감소하므로 적절한 연결 및 응답 시간 제한이 필요합니다."

**핵심 요약**

외부 API의 Timeout은 장시간 대기로 인한 스레드·연결 자원 고갈을 방지하는 데 중요하다.

<a name="q3-1"></a>
#### ↳ Q3-1. Connection Timeout과 Read Timeout의 차이는 무엇인가요?

> "Connection Timeout(커넥션 타임아웃)은 서버와 네트워크 연결을 수립하는 데 허용하는 대기 시간을 제한합니다.
>
> Read Timeout(리드 타임아웃)은 연결 후 데이터를 읽는 과정에서 허용하는 대기 시간을 제한합니다.
>
> 다만 Read Timeout의 정확한 측정 방식과 전체 응답 시간 제한 여부는 HTTP 클라이언트 구현에 따라 달라집니다."

| 구분 | 의미 |
| --- | --- |
| Connection Timeout | 연결 수립 대기 제한 |
| Read Timeout | 데이터 읽기 대기 제한 |
| Overall Timeout | 구현체가 지원하는 경우 전체 요청 시간 제한 |

**핵심 요약**

Connection Timeout은 연결 대기, Read Timeout은 데이터 읽기 대기를 제한한다.

<a name="q3-2"></a>
#### ↳ Q3-2. Timeout이 발생하면 무조건 재시도해야 하나요?

> "아닙니다. 서버가 실제 작업을 완료했지만 응답만 전달되지 않았을 수 있기 때문입니다.
>
> 특히 주문 생성이나 결제처럼 일반적으로 멱등성이 보장되지 않는 POST 요청을 무조건 재시도하면 중복 처리가 발생할 수 있습니다.
>
> 따라서 요청의 멱등성, 실패 원인, 멱등성 키와 같은 중복 방지 기능 등을 고려해 재시도해야 합니다."

**핵심 요약**

Timeout 후 재시도는 요청의 멱등성과 중복 처리 위험을 고려해야 한다.

<a name="q3-3"></a>
#### ↳ Q3-3. [핵심 질문] HTTP 요청을 재시도할 때 어떤 전략을 사용할 수 있나요?

> "일시적인 장애에 대해서는 제한된 재시도 횟수와 Exponential Backoff 같은 대기 전략을 사용할 수 있습니다.
>
> 여러 클라이언트가 동시에 재시도하지 않도록 Jitter를 추가할 수도 있습니다.
>
> 다만 결제처럼 부작용이 있는 요청에서는 멱등성 보장과 중복 처리 방지가 우선입니다."

**핵심 요약**

재시도는 횟수와 간격을 제한하고 멱등성과 중복 처리 방지를 고려해야 한다.
