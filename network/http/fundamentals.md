# HTTP Fundamentals

<sub>Network · 2026.11.06 · 12주차</sub>

[Study Index](../../README.md)

브라우저의 요청 한 건을 예로 들어 HTTP 메시지와 그 메시지를 운반하는 연결을 구분합니다. 요청이 끝나는 시점과 연결이 닫히는 시점이 같은지도 생각해 보세요.

**복습할 질문**

- [HTTP란 무엇이고 왜 필요한가요?](#q1)
- [HTTP Request와 Response는 어떻게 구성되나요?](#q6)
- [Keep-Alive란 무엇이고 왜 사용하나요?](#q13)

## 목차

- [HTTP의 기본 개념](#section-01)
- [HTTP 요청과 응답 메시지](#section-02)
- [Connectionless와 HTTP 연결 관리](#section-03)

---

<a name="section-01"></a>
## HTTP의 기본 개념

<a name="q1"></a>
### Q1. [대표 질문] HTTP란 무엇이고 왜 필요한가요?

> "HTTP(HyperText Transfer Protocol)는 클라이언트와 서버가 웹에서 데이터를 주고받기 위해 사용하는 응용 계층 통신 프로토콜입니다.
>
> 서로 다른 프로그래밍 언어나 운영체제를 사용하는 프로그램도 HTTP라는 공통 규약에 따라 요청과 응답을 주고받을 수 있습니다."

**핵심 요약**

HTTP는 클라이언트와 서버가 데이터를 주고받기 위한 응용 계층 통신 규약이다.

<a name="q2"></a>
### Q2. HTTP의 핵심 특징은 무엇인가요?

> "HTTP는 클라이언트가 요청하고 서버가 응답하는 요청-응답 구조의 응용 계층 프로토콜입니다.
>
> 기본적으로 Stateless한 특성을 가지며, HTTP/1.x는 텍스트 형식의 메시지를 사용하고 HTTP/2와 HTTP/3는 바이너리 프레이밍을 사용합니다.
>
> 일반적인 기본 포트는 HTTP 80, HTTPS 443입니다."

**핵심 요약**

HTTP는 요청-응답 구조, Stateless, 응용 계층 프로토콜이라는 특징을 가진다.

<a name="q2-1"></a>
#### ↳ Q2-1. HTTP는 어떤 종류의 데이터를 전달할 수 있나요?

> "HTTP는 HTML 문서뿐 아니라 JSON, XML, 이미지, 동영상, 파일 등 다양한 형태의 데이터를 전달할 수 있습니다.
>
> 데이터의 미디어 타입은 Content-Type 헤더를 통해 표현할 수 있으며, 일반적인 REST API에서는 JSON을 자주 사용합니다."

**핵심 요약**

HTTP는 HTML뿐 아니라 JSON, 이미지, 동영상 등 다양한 데이터를 전달할 수 있다.

<a name="q3"></a>
### Q3. HTTP와 TCP는 어떤 관계인가요?

> "HTTP는 요청과 응답의 의미를 정의하는 응용 계층 프로토콜이고, TCP는 데이터를 신뢰성 있게 전달하는 전송 계층 프로토콜입니다.
>
> HTTP/1.1과 HTTP/2는 일반적으로 TCP를 사용하며, HTTP/3는 UDP 기반 QUIC을 사용합니다."

**핵심 요약**

HTTP는 통신의 의미를 정의하고 TCP나 QUIC은 데이터를 전달한다.

<a name="q3-1"></a>
#### ↳ Q3-1. TCP가 데이터를 전달한다면 HTTP는 무엇을 담당하나요?

> "TCP는 순서 있는 바이트 스트림의 신뢰성 있는 전달을 담당합니다.
>
> HTTP는 메서드, URI, 헤더, 상태 코드, 본문 등을 통해 어떤 자원을 요청하고 어떤 결과를 반환할지 정의합니다."

**핵심 요약**

TCP는 데이터 전달, HTTP는 요청과 응답의 의미를 담당한다.

<a name="q3-2"></a>
#### ↳ Q3-2. HTTP는 Stateless인데 TCP는 Stateful인가요?

> "그렇습니다. TCP는 연결 수립 후 연결 상태, 순서 번호, 송수신 버퍼 등의 정보를 관리하는 연결 지향 프로토콜입니다.
>
> 반면 HTTP의 Stateless는 애플리케이션 요청 사이의 대화 상태를 HTTP 자체가 자동으로 유지하지 않는다는 의미입니다.
>
> 따라서 TCP 연결 상태와 HTTP 애플리케이션 상태는 서로 다른 개념입니다."

**핵심 요약**

TCP는 연결 상태를 관리하지만 HTTP의 Stateless는 요청 간 애플리케이션 상태에 관한 개념이다.

<a name="q4"></a>
### Q4. HTTP에서는 서버가 먼저 데이터를 보낼 수 없나요?

> "일반적인 HTTP는 클라이언트가 요청을 시작하고 서버가 응답하는 구조입니다.
>
> 실시간 통신에서는 Polling, Long Polling, SSE, WebSocket 등의 방식을 활용할 수 있으며, 특히 SSE는 이미 시작된 HTTP 요청의 응답을 지속적으로 전송할 수 있습니다."

**핵심 요약**

HTTP는 기본적으로 요청-응답 구조이며, 실시간 통신에는 SSE와 WebSocket 등을 활용할 수 있다.

<a name="q4-1"></a>
#### ↳ Q4-1. Polling, Long Polling, SSE, WebSocket의 차이는 무엇인가요?

> "Polling은 클라이언트가 주기적으로 요청하고, Long Polling은 서버가 새 데이터가 생길 때까지 응답을 지연하는 방식입니다.
>
> SSE는 HTTP 응답 스트림으로 서버가 이벤트를 지속적으로 전송하고, WebSocket은 연결 수립 후 양방향 메시지 통신을 지원합니다."

| 방식 | 발음 | 특징 |
| --- | --- | --- |
| Polling | 폴링 | 주기적으로 요청 |
| Long Polling | 롱 폴링 | 새 데이터까지 응답 대기 |
| SSE | 에스에스이 | 서버 → 클라이언트 이벤트 전송 |
| WebSocket | 웹소켓 | 양방향 메시지 통신 |

**핵심 요약**

Polling은 반복 요청, SSE는 서버 단방향 스트림, WebSocket은 양방향 통신이다.

<a name="q5"></a>
### Q5. [핵심 질문] HTTP와 REST는 같은 개념인가요?

> "아닙니다. HTTP는 통신 프로토콜이고 REST는 네트워크 애플리케이션을 설계하는 아키텍처 스타일입니다.
>
> RESTful API는 일반적으로 HTTP의 URI, 메서드, 상태 코드, 캐싱 등의 기능을 활용합니다."

**핵심 요약**

HTTP는 통신 규약이고 REST는 시스템 설계 원칙이다.

<a name="section-02"></a>
## HTTP 요청과 응답 메시지

<a name="q6"></a>
### Q6. [대표 질문] HTTP Request와 Response는 어떻게 구성되나요?

> "HTTP/1.1의 Request와 Response는 모두 시작 줄(Start Line), 헤더(Header), 빈 줄, 본문(Body) 으로 구성됩니다.
>
> Request의 시작 줄에는 HTTP 메서드, 요청 경로, HTTP 버전이 포함되고, Response의 시작 줄에는 HTTP 버전, 상태 코드, 상태 메시지가 포함됩니다.
>
> 헤더에는 요청이나 응답에 관한 부가 정보가 들어가고, 본문에는 실제 주고받는 데이터가 담깁니다. 본문은 생략할 수도 있습니다."

요청 예시:

```
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json

{"name":"Kim"}
```

응답 예시:

```
HTTP/1.1 201 Created
Content-Type: application/json
Location: /users/1

{"id":1,"name":"Kim"}
```

**핵심 요약**

HTTP/1.1 메시지는 시작 줄, 헤더, 빈 줄, 선택적인 본문으로 구성된다.

<a name="q7"></a>
### Q7. Header와 Body의 차이는 무엇인가요?

> "Header는 요청이나 응답의 형식, 인증, 캐시 등 부가 정보를 전달합니다.
>
> Body는 회원가입 데이터, 게시글 내용, JSON 응답처럼 실제로 전송하려는 데이터를 담는 영역입니다."

**핵심 요약**

Header는 부가 정보, Body는 실제 전송 데이터를 담는다.

<a name="q7-1"></a>
#### ↳ Q7-1. HTTP 요청에 Body가 항상 존재하나요?

> "아닙니다. HTTP 요청 본문은 필수가 아닙니다.
>
> GET 요청은 일반적으로 본문 없이 경로나 쿼리 파라미터를 사용하며, POST와 PUT 요청에서는 JSON 등의 데이터를 본문에 포함하는 경우가 많습니다."

**핵심 요약**

HTTP 요청의 Body는 선택적이며 모든 요청에 필요한 것은 아니다.

<a name="q8"></a>
### Q8. Path Variable, Query String, Body는 어떻게 구분하나요?

> "Path Variable은 URI 경로에 포함된 자원 식별값이고, Query String은 검색·필터·정렬·페이징 등에 활용할 수 있는 쿼리 파라미터입니다.
>
> Body는 요청 본문에 데이터를 전달하는 영역입니다. Spring에서는 각각 `@PathVariable`, `@RequestParam`, `@RequestBody` 등을 활용합니다."

| 방법 | 예시 | Spring |
| --- | --- | --- |
| Path Variable | `/users/10` | `@PathVariable` |
| Query String | `/users?page=2` | `@RequestParam` |
| Body | `{"name":"Kim"}` | `@RequestBody` |

**핵심 요약**

경로는 자원 식별, 쿼리는 조회 조건, 본문은 요청 데이터 전달에 주로 사용한다.

<a name="q8-1"></a>
#### ↳ Q8-1. Path Variable과 Query String은 언제 각각 사용하나요?

> "특정 자원을 식별할 때는 경로를 사용하고, 목록 검색이나 정렬, 페이징처럼 조회 조건을 표현할 때는 쿼리 파라미터를 사용하는 것이 일반적인 설계 관례입니다."

**핵심 요약**

자원의 정체성은 경로에, 조회 조건은 쿼리에 표현한다.

<a name="q9"></a>
### Q9. Spring Boot에서는 HTTP 요청을 어떻게 처리하나요?

> "일반적인 Spring MVC에서는 Tomcat 같은 서블릿 컨테이너가 요청을 받고, DispatcherServlet이 적절한 Controller 메서드를 찾아 실행합니다.
>
> `@PathVariable`, `@RequestParam`, `@RequestBody` 등을 통해 요청 데이터를 처리하고, 반환 객체는 HttpMessageConverter 등을 통해 JSON으로 직렬화하여 응답할 수 있습니다."

**핵심 요약**

Spring MVC는 DispatcherServlet을 중심으로 요청을 Controller에 연결하고 처리 결과를 HTTP 응답으로 변환한다.

<a name="q10"></a>
### Q10. Content-Type이 잘못되면 어떻게 되나요?

> "서버가 요청 본문의 미디어 타입을 지원하지 않으면 415 Unsupported Media Type이 발생할 수 있습니다.
>
> 실제 결과는 Spring의 메시지 컨버터와 요청 매핑 설정 등에 따라 달라집니다."

**핵심 요약**

지원하지 않는 Content-Type은 415 오류를 발생시킬 수 있다.

<a name="q11"></a>
### Q11. [핵심 질문] Content-Length와 Transfer-Encoding은 무엇인가요?

> "`Content-Length`와 `Transfer-Encoding`은 HTTP 메시지의 본문(Body)을 어떻게 구분하고 전달할지 알려주는 헤더입니다.
>
> `Content-Length`는 본문의 크기를 바이트 단위로 나타냅니다.
>
> `Transfer-Encoding`은 메시지 본문을 전송할 때 적용하는 인코딩 방식을 나타내며, HTTP/1.1의 `chunked` 방식에서는 데이터를 여러 청크로 나누어 전송하고 마지막 청크로 종료를 알립니다.
>
> 따라서 `Content-Length`는 본문의 크기를 명시하고, `Transfer-Encoding: chunked`는 전체 크기를 미리 알리지 않고도 본문을 전송할 수 있게 합니다."

**핵심 요약**

Content-Length는 본문 길이, chunked는 HTTP/1.1에서 본문을 나누어 전송하는 방식이다.

<a name="section-03"></a>
## Connectionless와 HTTP 연결 관리

<a name="q12"></a>
### Q12. [대표 질문] Connectionless란 무엇인가요?

> "Connectionless(커넥션리스, 비연결성)는 요청과 응답을 처리한 뒤 연결을 계속 유지하지 않는 통신 방식의 특성입니다.
>
> 초기 HTTP에서는 요청마다 연결을 종료하는 방식이 일반적이었지만, HTTP/1.1부터 지속 연결이 기본이 되었으며 HTTP/2와 HTTP/3도 연결 재사용을 지원합니다."

**핵심 요약**

초기 HTTP는 요청마다 연결을 종료했지만 현대 HTTP는 지속 연결을 적극적으로 활용한다.

<a name="q13"></a>
### Q13. Keep-Alive란 무엇이고 왜 사용하나요?

> "Keep-Alive(킵 얼라이브)는 요청과 응답이 끝나더라도 연결을 일정 기간 유지해 후속 요청에서 재사용하는 방식입니다.
>
> 매 요청마다 TCP 연결을 새로 생성할 때 발생하는 핸드셰이크와 자원 사용 비용을 줄여 통신 효율을 높입니다.
>
> HTTP/1.1에서는 지속 연결이 기본 동작입니다."

**핵심 요약**

Keep-Alive는 기존 연결을 재사용하여 반복적인 연결 수립 비용을 줄인다.

<a name="q13-1"></a>
#### ↳ Q13-1. Keep-Alive를 사용하면 무조건 좋은가요?

> "아닙니다. 연결 재사용은 효율적이지만 사용하지 않는 연결까지 오래 유지하면 소켓과 메모리 등의 자원이 사용됩니다.
>
> 따라서 유휴 연결 시간과 최대 연결 수 등을 서버 환경에 맞게 설정해야 합니다."

**핵심 요약**

Keep-Alive는 연결 비용을 줄이지만 유휴 연결 자원 관리가 필요하다.

<a name="q13-2"></a>
#### ↳ Q13-2. Keep-Alive와 Connection Pool의 차이는 무엇인가요?

> "Keep-Alive는 개별 연결을 종료하지 않고 재사용할 수 있도록 하는 방식입니다.
>
> Connection Pool(커넥션 풀)은 여러 연결을 생성·보관·관리하여 필요한 작업에 재사용하도록 하는 구조입니다.
>
> HTTP 클라이언트의 연결 풀은 지속 연결을 활용하여 반복적인 연결 수립 비용을 줄일 수 있습니다."

**핵심 요약**

Keep-Alive는 연결 하나의 재사용이고 Connection Pool은 여러 연결의 재사용과 관리를 담당한다.

<a name="q13-3"></a>
#### ↳ Q13-3. [핵심 질문] HTTP Connection Pool과 DB Connection Pool은 같은가요?

> "둘 다 연결을 재사용해 비용을 줄이는 구조이지만 관리 대상이 다릅니다.
>
> HTTP Connection Pool은 외부 웹 서버로 향하는 HTTP 연결을 관리하고, DB Connection Pool은 데이터베이스와의 연결을 관리합니다."

**핵심 요약**

두 커넥션 풀은 목적은 비슷하지만 각각 HTTP 연결과 DB 연결을 관리한다.

<a name="q14"></a>
### Q14. [핵심 질문] Stateless와 Connectionless의 차이는 무엇인가요?

> 즉, Stateless는 기억의 문제이고, Connectionless는 연결의 문제

"Stateless는 서버가 이전 HTTP 요청의 사용자 상태를 자동으로 유지하지 않는다는 의미이고, Connectionless는 요청과 응답 이후 네트워크 연결을 계속 유지하지 않는다는 의미입니다.

두 개념은 각각 애플리케이션 상태와 네트워크 연결에 관한 것이므로 서로 독립적입니다. 따라서 HTTP/1.1의 Keep-Alive로 TCP 연결을 재사용하면서도 HTTP 요청은 Stateless하게 처리할 수 있습니다."

**핵심 요약**

Stateless는 요청 상태, Connectionless는 네트워크 연결 유지에 관한 개념이다.
