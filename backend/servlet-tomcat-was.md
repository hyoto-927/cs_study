# Servlet, Tomcat & WAS

<sub>Backend · 2026.10.07 · 9주차</sub>

[Study Index](../README.md)

HTTP 요청 하나가 서버에 도착해 Java 코드로 전달되는 경로를 따라갑니다. Tomcat이 관리하는 객체와 스레드를 구분하면 동시 요청을 어떻게 처리하는지 보입니다.

**복습할 질문**

- [Servlet이란 무엇인가요?](#q1)
- [Servlet의 생명 주기를 설명해주세요.](#q4)
- [Tomcat Thread Pool과 DB Connection Pool의 차이는 무엇인가요?](#q18)

## 목차

- [Servlet](#section-01)
- [Servlet과 동시성](#section-02)
- [Spring MVC · DispatcherServlet](#section-03)
- [Tomcat](#section-04)
- [Tomcat 내부 구조](#section-05)
- [Spring과 Tomcat](#section-06)
- [Web Server · WAS](#section-07)
- [Nginx · Reverse Proxy](#section-08)
- [Load Balancing](#section-09)
- [WAS · DB](#section-10)
- [최종 요청 흐름](#section-11)
- [대표 질문 정리](#section-12)

---

<a name="section-01"></a>
## Servlet

<a name="q1"></a>
### Q1. [대표 질문] Servlet이란 무엇인가요?

> "Servlet은 Java 웹 환경에서 HTTP 요청을 받아 애플리케이션 로직을 수행하고, HTTP 응답을 생성하기 위한 표준 기술입니다.
>
> Servlet 자체가 서버 역할을 하는 것은 아니고, Tomcat과 같은 Servlet Container 안에서 실행됩니다."

**핵심 요약**

**Servlet은 HTTP 요청을 Java 코드로 처리하고 응답을 생성하는 Java 웹 표준 기술**

---

<a name="q2"></a>
### Q2. [대표 질문] Servlet과 Servlet Container의 차이는 무엇인가요?

> "Servlet은 실제 요청을 처리하는 객체이고, Servlet Container는 Servlet을 생성하고 실행하며 생명주기와 HTTP 요청 연결을 관리하는 실행 환경입니다.
>
> Servlet이 직접 네트워크 연결을 받거나 자신의 생명주기를 관리하는 것이 아니라 Tomcat 같은 Servlet Container가 이를 담당합니다."

**핵심 요약**

**Servlet은 요청 처리 객체, Servlet Container는 Servlet의 실행과 생명 주기를 관리하는 환경**

---

<a name="q3"></a>
### Q3. Servlet은 HTTP 요청이 들어올 때마다 새로운 객체가 생성되나요?

> "아닙니다. 일반적으로 Servlet Container가 Servlet 객체를 한 번 생성하고 여러 요청에서 재사용합니다.
>
> 따라서 Servlet 객체의 생명주기는 하나의 HTTP 요청보다 길고, 여러 요청이 동일한 Servlet 인스턴스를 사용할 수 있습니다."

**핵심 요약**

**Servlet은 요청마다 생성되는 것이 아니라 일반적으로 하나의 객체를 여러 요청에서 재사용한다.**

---

<a name="q3-1"></a>
#### ↳ Q3-1. 그렇다면 Servlet을 싱글톤이라고 해도 되나요?

> "하나의 인스턴스를 여러 요청에서 재사용한다는 점에서는 싱글톤과 비슷합니다.
>
> 하지만 개발자가 Singleton Pattern을 직접 구현하는 것이 아니라 Servlet Container가 객체의 생성과 재사용을 관리하기 때문에 정확히 같은 개념이라고 보기는 어렵습니다."

**핵심 요약**

**하나의 객체를 재사용한다는 점에서는 싱글톤과 비슷하지만 객체 관리는 Container가 한다.**

---

<a name="q4"></a>
### Q4. Servlet의 생명 주기를 설명해주세요.

> "Servlet Container가 Servlet 객체를 생성한 뒤 `init()`을 호출하여 초기화합니다.
>
> 이후 요청이 들어올 때마다 `service()`가 호출되고, `HttpServlet`에서는 HTTP Method에 따라 `doGet()`이나 `doPost()` 등의 메서드로 분기합니다.
>
> 마지막으로 Servlet이 제거되거나 애플리케이션이 종료될 때 `destroy()`가 호출됩니다."

**핵심 요약**

**생성 → init() → 요청마다 service() → 종료 시 destroy()**

---

<a name="q4-1"></a>
#### ↳ Q4-1. service(), doGet(), doPost()의 관계는 무엇인가요?

> "Servlet Container가 요청을 전달할 때 기본적으로 `service()`가 호출됩니다.
>
> `HttpServlet`의 `service()`가 HTTP Method를 확인한 뒤 GET 요청이면 `doGet()`, POST 요청이면 `doPost()`를 호출합니다."

**핵심 요약**

**service()가 HTTP Method를 확인해서 doGet(), doPost()로 분기한다.**

---

<a name="q5"></a>
### Q5. HttpServletRequest와 HttpServletResponse의 차이는 무엇인가요?

> "`HttpServletRequest`는 클라이언트가 보낸 HTTP 요청 정보를 담는 객체이고, `HttpServletResponse`는 클라이언트에게 반환할 HTTP 응답을 구성하는 객체입니다.
>
> Request에서는 URI, Parameter, Header, Cookie, Body 등의 정보를 읽을 수 있고, Response에서는 Status Code, Header, Content-Type, Body 등을 설정할 수 있습니다."

**핵심 요약**

**Request는 들어온 요청 정보, Response는 나갈 응답 정보를 담당한다.**

---

<a name="q5-1"></a>
#### ↳ Q5-1. Request와 Response 객체는 개발자가 직접 생성하나요?

> "아닙니다. Tomcat과 같은 Servlet Container가 실제 HTTP 메시지를 처리한 뒤 Request와 Response 객체를 생성하여 Servlet에 전달합니다."

**핵심 요약**

**Request와 Response는 개발자가 만드는 것이 아니라 Servlet Container가 생성한다.**

---

<a name="section-02"></a>
## Servlet과 동시성

<a name="q6"></a>
### Q6. [대표 질문] Servlet 객체가 하나라면 여러 요청을 어떻게 동시에 처리할 수 있나요?

> "Servlet 객체가 하나인 것과 요청을 하나씩 처리하는 것은 별개의 개념입니다.
>
> Tomcat은 여러 작업 Thread를 이용해 하나의 Servlet 객체를 동시에 호출할 수 있습니다.
>
> 따라서 Servlet 인스턴스 하나를 여러 Thread가 동시에 사용하는 구조가 될 수 있습니다."

**핵심 요약**

**Servlet 하나를 여러 Thread가 동시에 호출하기 때문에 여러 요청을 동시에 처리할 수 있다.**

<a name="q7"></a>
### Q7. Servlet 객체가 하나인데 어떻게 여러 요청을 동시에 처리할 수 있나요?

> "Servlet 객체는 일반적으로 하나를 생성해서 재사용하지만, 하나의 Servlet 객체를 여러 Thread가 동시에 실행할 수 있습니다.
>
> Tomcat은 요청마다 작업 Thread를 배정하고, 각각의 Thread가 같은 Servlet의 `service()` 메서드를 호출합니다.
>
> 따라서 Servlet 객체를 요청마다 새로 만들지 않아도 여러 요청을 동시에 처리할 수 있습니다."

**핵심 요약**

> **Servlet 객체는 하나지만 여러 Thread가 같은 Servlet의 `service()`를 동시에 실행할 수 있다.**

---

<a name="q8"></a>
### Q8. Servlet은 Thread Safe(여러 Thread가 동시에 사용해도 데이터가 꼬이지 않고 안전한 상태)한가요?

> "자동으로 Thread Safe한 것은 아닙니다.
>
> 하나의 Servlet 객체를 여러 Thread가 동시에 사용할 수 있기 때문에 변경 가능한 인스턴스 변수를 사용하면 여러 요청이 같은 데이터를 공유하게 되고 동시성 문제가 발생할 수 있습니다."

**핵심 요약**

**Servlet 하나를 여러 Thread가 공유하므로 인스턴스 변수 사용 시 동시성 문제가 발생할 수 있다.**

<a name="q8-1"></a>
#### ↳ Q8-1. Servlet을 Thread Safe하게 사용하려면 어떻게 해야 하나요?

> "가장 기본적인 방법은 여러 Thread가 공유하는 변경 가능한 상태를 만들지 않는 것입니다.
>
> Servlet의 인스턴스 변수는 여러 요청이 공유할 수 있기 때문에 요청별 데이터를 인스턴스 변수에 저장하지 않고 지역 변수나 Request 범위에 두는 것이 좋습니다.
>
> 만약 공유 데이터를 변경해야 한다면 동기화나 Thread-safe한 자료구조 등의 방법을 사용해야 합니다."

**핵심 요약**

**요청별 데이터는 지역 변수에 두고, 여러 Thread가 공유하는 변경 가능한 상태를 최소화한다.**

---

<a name="q8-2"></a>
#### ↳ Q8-2. Servlet에서 인스턴스 변수를 사용하면 왜 위험한가요?

> "인스턴스 변수는 Servlet 객체에 포함되기 때문에 여러 요청이 같은 값을 공유합니다.
>
> 여러 Thread가 동시에 값을 읽고 수정하면 요청 간 데이터가 섞이거나 실행 순서에 따라 결과가 달라지는 Race Condition이 발생할 수 있습니다."

**핵심 요약**

**인스턴스 변수는 모든 요청이 공유하기 때문에 동시에 수정하면 Race Condition이 발생할 수 있다.**

---

<a name="q8-3"></a>
#### ↳ Q8-3. 지역 변수는 왜 상대적으로 안전한가요?

> "지역 변수는 메서드가 호출될 때마다 각각 별도로 생성됩니다.
>
> 따라서 서로 다른 요청을 처리하는 Thread가 같은 Servlet을 사용하더라도 각 메서드 호출의 지역 변수는 공유되지 않습니다."

**핵심 요약**

**지역 변수는 메서드 호출마다 별도로 생성되므로 요청 간 공유되지 않는다.**

---

<a name="section-03"></a>
## Spring MVC · DispatcherServlet

<a name="q9"></a>
### Q9. [대표 질문] Spring MVC는 Servlet을 대체하는 건가요?

> "아닙니다. Spring MVC는 Servlet 기반 위에서 동작합니다.
>
> Servlet의 저수준 HTTP 요청 처리 위에 Controller Mapping, 요청 데이터 변환, 반환값 처리 같은 기능을 제공하여 개발자가 Servlet을 직접 다루는 부분을 줄여주는 것입니다."

"아닙니다. Spring MVC는 Servlet 기반 위에서 동작합니다.
Servlet을 직접 사용하면 개발자가 HTTP 요청과 응답을 비교적 직접 처리해야 하지만, Spring MVC는 요청에 맞는 Controller를 찾아주고, 요청 데이터를 Java 객체로 변환하며, Controller의 반환값을 HTTP 응답으로 변환하는 기능을 제공합니다.
따라서 Servlet을 없애는 것이 아니라 Servlet 기반의 웹 처리를 더 편리하게 사용할 수 있도록 추상화한 것입니다."

**핵심 요약**

**Spring MVC는 Servlet 위에서 Controller 연결, 요청 데이터 변환, 응답 변환 등을 대신 처리해 개발자가 HTTP 세부 처리를 직접 하는 부분을 줄여준다.**

---

<a name="q10"></a>
### Q10. [대표 질문] DispatcherServlet이 무엇인가요?

> "DispatcherServlet은 Spring MVC에서 Front Controller 역할을 하는 Servlet입니다.
>
> 클라이언트 요청을 먼저 받은 뒤 어떤 Controller가 해당 요청을 처리할지 찾고, Controller 실행부터 응답 반환까지 Spring MVC의 전체 요청 흐름을 조정합니다."

**핵심 요약**

**DispatcherServlet은 모든 주요 요청을 먼저 받아 적절한 Controller로 연결하는 Spring MVC의 Front Controller**

---

<a name="q11"></a>
### Q11. Front Controller Pattern이 무엇인가요?

> "여러 요청을 각각의 Controller가 직접 받는 것이 아니라 하나의 중앙 Controller가 먼저 받은 뒤 적절한 처리 대상으로 전달하는 패턴입니다.
>
> Spring MVC에서는 DispatcherServlet이 Front Controller 역할을 수행합니다."

**핵심 요약**

**모든 요청을 하나의 중앙 Controller가 먼저 받고 실제 처리 Controller로 분배하는 패턴**

---

<a name="q12"></a>
### Q12. Controller는 어떻게 HTTP 요청과 연결되나요?

> "DispatcherServlet이 HTTP 요청을 받은 뒤 HandlerMapping을 이용하여 요청 URL과 HTTP Method에 맞는 Controller 메서드를 찾습니다.
>
> 예를 들어 `GET /users` 요청이 들어오면 `@GetMapping("/users")`이 선언된 Controller 메서드를 찾아 실행할 수 있습니다."

**핵심 요약**

**DispatcherServlet이 HandlerMapping을 이용해 요청과 Controller 메서드를 연결한다.**

---

<a name="q12-1"></a>
#### ↳ Q12-1. HandlerMapping은 무엇인가요?

> "HandlerMapping은 현재 HTTP 요청을 처리할 Handler, 즉 일반적인 Spring MVC에서는 Controller 메서드를 찾는 역할을 합니다.
>
> URL과 HTTP Method 등의 Mapping 정보를 이용하여 어떤 Controller를 실행할지 결정합니다."

**핵심 요약**

**HandlerMapping은 요청을 처리할 Controller를 찾는다.**

---

<a name="q12-2"></a>
#### ↳ Q12-2. HandlerAdapter는 왜 필요한가요?

> "DispatcherServlet이 여러 형태의 Handler를 직접 실행하지 않고 공통된 방식으로 처리할 수 있도록 실행 방식을 추상화하기 위해 사용합니다.
>
> DispatcherServlet은 Handler를 찾은 뒤 적절한 HandlerAdapter에게 실제 실행을 위임합니다."

**핵심 요약**

**HandlerMapping은 Controller를 찾고, HandlerAdapter는 찾은 Controller를 실행한다.**

---

<a name="q13"></a>
### Q13. Controller는 Servlet인가요?

> "아닙니다. 일반적인 Spring MVC Controller는 Servlet이 아니라 Spring Container가 관리하는 Bean입니다.
>
> 실제 Servlet은 DispatcherServlet이고, DispatcherServlet이 적절한 Controller를 찾아 호출합니다."

**핵심 요약**

**Controller는 Spring Bean이고, Servlet은 DispatcherServlet이다.**

---

<a name="q14"></a>
### Q14. Servlet Mapping과 Spring MVC Mapping의 차이는 무엇인가요?

> "Servlet Mapping은 Servlet Container가 HTTP 요청을 어떤 Servlet으로 전달할지 결정하는 것이고, Spring MVC Mapping은 DispatcherServlet에 들어온 요청을 어떤 Controller 메서드가 처리할지 결정하는 것입니다."

**핵심 요약**

**Servlet Mapping은 Servlet을 찾고, Spring Mapping은 Controller를 찾는다.**

---

<a name="section-04"></a>
## Tomcat

<a name="q15"></a>
### Q15. [대표 질문] Tomcat이란 무엇인가요?

> "Tomcat은 Servlet 명세를 구현하여 Servlet을 실행하고 관리할 수 있도록 하는 Servlet Container입니다.
>
> 또한 HTTP 요청을 직접 받을 수 있는 서버 기능도 제공하기 때문에 Java 웹 애플리케이션을 실행할 수 있습니다."

**핵심 요약**

**Tomcat은 Servlet을 실행하고 관리하며 HTTP 요청도 받을 수 있는 Servlet Container**

---

<a name="q16"></a>
### Q16. [대표 질문] Tomcat은 WAS인가요, Servlet Container인가요?

> "엄밀히 말하면 Tomcat은 Servlet Container에 초점을 둔 서버입니다.
>
> 다만 Java 웹 애플리케이션을 실행하고 동적인 요청을 처리할 수 있기 때문에 실무에서는 WAS라고 넓게 표현하기도 합니다."

**핵심 요약**

**엄밀하게는 Servlet Container, 실무에서는 WAS라고 부르기도 한다.**

---

<a name="q17"></a>
### Q17. Tomcat은 요청을 어떻게 동시에 처리하나요?

> "Tomcat은 Thread Pool에 미리 준비된 여러 작업 Thread를 이용하여 요청을 동시에 처리합니다.
>
> 요청이 들어오면 사용 가능한 Thread가 요청을 처리하고, 처리가 끝나면 해당 Thread는 제거되지 않고 Pool로 돌아가 다음 요청에서 재사용됩니다."

**핵심 요약**

**Tomcat은 Thread Pool의 여러 Thread로 요청을 동시에 처리하고 Thread를 재사용한다.**

---

<a name="q17-1"></a>
#### ↳ Q17-1. 요청마다 Thread를 새로 만들지 않고 Thread Pool을 사용하는 이유는 무엇인가요?

> "Thread를 생성하고 제거하는 작업에도 비용이 들기 때문입니다.
>
> Thread Pool을 사용하면 기존 Thread를 재사용할 수 있고, 동시에 실행할 수 있는 Thread 수를 제한하여 서버 자원도 제어할 수 있습니다."

**핵심 요약**

**Thread 생성·제거 비용을 줄이고 동시 요청 수를 제어하기 위해 Thread Pool을 사용한다.**

---

<a name="q17-2"></a>
#### ↳ Q17-2. Thread Pool 크기를 크게 하면 처리 성능도 계속 좋아지나요?

> "그렇지 않습니다.
>
> Thread가 많아지면 Thread마다 필요한 메모리가 증가하고 Context Switching 비용도 커질 수 있습니다.
>
> 또한 DB Connection이나 CPU 같은 다른 자원이 먼저 병목이 된다면 Thread 수만 늘려도 실제 처리량은 증가하지 않습니다."

**핵심 요약**

**Thread가 너무 많으면 메모리와 Context Switching 비용이 증가하고 다른 자원이 병목이 될 수 있다.**

---

<a name="q18"></a>
### Q18. Tomcat Thread Pool과 DB Connection Pool의 차이는 무엇인가요?

> "Tomcat Thread Pool은 HTTP 요청을 처리하는 작업 Thread를 관리하고, DB Connection Pool은 데이터베이스와 연결하기 위한 Connection을 관리합니다.
>
> 즉 Thread는 요청을 실행하는 작업자이고, Connection은 DB와 통신하기 위한 연결 자원입니다."

**핵심 요약**

**Thread Pool은 요청 처리 Thread 관리, Connection Pool은 DB 연결 관리**

---

<a name="q18-1"></a>
#### ↳ Q18-1. Tomcat Thread가 200개이고 DB Connection이 30개라면 어떻게 되나요?

> "동시에 200개의 요청을 처리할 수 있는 Thread가 있더라도 DB Connection이 필요한 요청은 Connection Pool의 영향을 받습니다.
>
> 30개의 Connection이 모두 사용 중이라면 나머지 요청은 Thread를 가지고 있는 상태에서 Connection이 반환되기를 기다릴 수 있습니다.
>
> 따라서 DB Connection Pool이 병목이 될 수 있습니다."

**핵심 요약**

**Thread가 많아도 DB Connection이 부족하면 Connection을 기다리면서 병목이 발생한다.**

---

<a name="q18-2"></a>
#### ↳ Q18-2. CPU 사용률이 낮은데도 요청 처리가 밀릴 수 있나요?

> "가능합니다.
>
> 요청이 DB나 외부 API 같은 I/O 응답을 기다리고 있으면 CPU는 거의 사용하지 않지만 Thread는 해당 요청에 계속 점유될 수 있습니다.
>
> 이런 요청이 많아지면 CPU 사용률은 낮더라도 Thread Pool이 고갈되어 새로운 요청이 대기할 수 있습니다."

**핵심 요약**

**I/O를 기다리느라 CPU는 놀아도 Thread는 점유될 수 있어서 요청이 밀릴 수 있다.**

---

<a name="section-05"></a>
## Tomcat 내부 구조

<a name="q19"></a>
### Q19. Tomcat의 Connector와 Container는 각각 어떤 역할을 하나요?

> "Connector는 외부 클라이언트의 네트워크 연결을 받고 HTTP 프로토콜을 처리하는 역할을 합니다.
>
> Container는 Connector가 전달한 요청을 어떤 웹 애플리케이션과 Servlet이 처리할지 결정하고 Servlet 실행 환경을 관리합니다."

**핵심 요약**

**Connector는 네트워크·HTTP 처리, Container는 Servlet 실행과 관리**

---

<a name="q19-1"></a>
#### ↳ Q19-1. Coyote와 Catalina는 무엇인가요?

> "Coyote는 Tomcat에서 HTTP와 같은 프로토콜 및 네트워크 처리를 담당하는 영역이고, Catalina는 Servlet Container의 핵심 구현 영역입니다."

**핵심 요약**

**Coyote는 네트워크, Catalina는 Servlet Container**

---

<a name="q20"></a>
### Q20. Tomcat의 Engine → Host → Context → Wrapper(래퍼) 구조는 무엇인가요?

> "Tomcat Container는 관리 범위에 따라 Engine, Host, Context, Wrapper 계층으로 구성됩니다.
>
> Engine은 Tomcat의 전체 요청 처리 영역, Host는 가상 호스트, Context는 하나의 웹 애플리케이션, Wrapper는 하나의 Servlet에 대응합니다."

**핵심 요약**

**Engine → Host → Context → Wrapper 순으로 내려갈수록 관리 범위가 구체화된다.**

---

<a name="section-06"></a>
## Spring과 Tomcat

<a name="q21"></a>
### Q21. Servlet Container와 Spring Container의 차이는 무엇인가요?

> "Servlet Container는 HTTP 요청과 Servlet의 실행 환경을 관리하고, Spring Container는 Controller, Service, Repository와 같은 Spring Bean의 생성과 의존관계를 관리합니다."

**핵심 요약**

**Servlet Container는 Servlet과 웹 실행 환경 관리, Spring Container는 Bean과 의존관계 관리**

---

<a name="q22"></a>
### Q22. Tomcat이 죽으면 Spring도 같이 죽나요?

> "Spring Boot에서 Embedded Tomcat을 사용하는 경우 Spring과 Tomcat은 일반적으로 하나의 JVM 프로세스 안에서 함께 실행됩니다.
>
> 따라서 애플리케이션 프로세스가 종료되면 Spring Container와 Embedded Tomcat도 함께 종료됩니다.
>
> 외부 Tomcat에 Spring 애플리케이션을 배포한 경우에도 Tomcat이 종료되면 그 안의 웹 애플리케이션은 더 이상 요청을 처리할 수 없습니다."

**핵심 요약**

**Embedded Tomcat을 사용하는 Spring Boot에서는 같은 프로세스에서 실행되므로 애플리케이션이 죽으면 둘 다 종료된다.**

---

<a name="q23"></a>
### Q23. Tomcat 없이 Spring을 실행할 수 있나요?

> "네. 가능합니다.
>
> Spring 자체는 웹 전용 프레임워크가 아니기 때문에 Batch나 Command Line Application처럼 웹 서버가 필요하지 않은 애플리케이션에서는 Tomcat 없이도 사용할 수 있습니다.
>
> 다만 Servlet 기반의 Spring MVC 웹 애플리케이션을 실행하려면 Tomcat, Jetty 같은 Servlet Container가 필요합니다."

**핵심 요약**

**Spring 자체는 Tomcat 없이 가능하지만 Servlet 기반 Spring MVC에는 Servlet Container가 필요하다.**

---

<a name="q24"></a>
### Q24. Embedded Tomcat이란 무엇인가요?

> "Tomcat을 서버에 별도로 설치하는 대신 애플리케이션의 라이브러리로 포함하여 Spring Boot와 함께 실행하는 방식입니다.
>
> Spring Boot 애플리케이션을 실행하면 Embedded Tomcat도 함께 시작됩니다."

**핵심 요약**

**Embedded Tomcat은 Tomcat을 애플리케이션 안에 포함해 함께 실행하는 방식**

---

<a name="section-07"></a>
## Web Server · WAS

<a name="q25"></a>
### Q25. [대표 질문] Web Server와 WAS의 가장 큰 차이는 무엇인가요?

> "Web Server는 HTTP 요청을 받아 정적 리소스를 제공하거나 요청을 다른 서버로 전달하는 역할을 주로 담당합니다.
>
> WAS는 여기에 더해 웹 애플리케이션을 직접 실행할 수 있는 환경을 제공합니다.
>
> 따라서 단순히 Web Server는 정적, WAS는 동적이라고만 구분하기보다 애플리케이션 실행 환경을 제공하는지가 핵심적인 차이입니다."

**핵심 요약**

**Web Server는 HTTP·정적 자원·요청 전달 중심, WAS는 웹 애플리케이션 실행 환경까지 제공**

---

<a name="q25-1"></a>
#### ↳ Q25-1. Web Server는 정적, WAS는 동적이라고 외우면 안 되나요?

> "기본적인 이해에는 도움이 되지만 엄밀한 구분은 아닙니다.
>
> WAS도 정적 파일을 반환할 수 있고 Web Server도 다른 프로그램과 연동하여 동적인 처리를 할 수 있습니다.
>
> 더 중요한 기준은 웹 애플리케이션 자체를 실행할 수 있는 환경을 제공하는지 여부입니다."

**핵심 요약**

**정적/동적은 쉬운 구분이고, 정확한 기준은 애플리케이션 실행 환경 제공 여부**

---

<a name="q26"></a>
### Q26. Web Server 없이 WAS만 사용해도 되나요?

> "네. 가능합니다.
>
> Tomcat 자체에도 HTTP Server 기능이 있기 때문에 클라이언트가 Tomcat에 직접 요청하도록 구성할 수 있습니다.
>
> 따라서 규모가 작거나 단순한 서비스에서는 별도의 Web Server 없이 Tomcat만 사용할 수도 있습니다."

**핵심 요약**

**Tomcat도 HTTP 요청을 받을 수 있기 때문에 WAS만으로 서비스할 수 있다.**

---

<a name="section-08"></a>
## Nginx · Reverse Proxy

<a name="q27"></a>
### Q27. [대표 질문] WAS가 있는데 왜 Nginx(엔진엑스) 같은 Web Server를 앞에 두나요?

> "Tomcat만으로도 서비스는 가능하지만 운영 환경에서는 역할을 분리하기 위해 Nginx를 앞에 둘 수 있습니다.
>
> Nginx가 TLS 처리, Reverse Proxy, Load Balancing, 정적 리소스 제공, 요청 Routing 등을 담당하면 WAS는 비즈니스 로직 실행에 더 집중할 수 있습니다."

**핵심 요약**

**Nginx가 TLS·Proxy·Load Balancing 등 앞단 처리를 담당하고 WAS는 애플리케이션 로직에 집중하게 한다.**

---

<a name="q28"></a>
### Q28. [대표 질문] Reverse Proxy란 무엇인가요?

> "Reverse Proxy는 클라이언트의 요청을 먼저 받은 뒤 실제 요청을 처리하는 내부 서버로 전달하는 서버 측 Proxy입니다.
>
> 클라이언트는 실제 WAS의 주소나 서버 개수를 알 필요 없이 Reverse Proxy에만 요청합니다."

**핵심 요약**

**Reverse Proxy는 클라이언트 요청을 대신 받아 내부 서버로 전달한다.**

---

<a name="q29"></a>
### Q29. Reverse Proxy를 WAS 앞에 두면 보안 측면에서 어떤 장점이 있나요?

> "클라이언트가 내부 WAS에 직접 접근하지 않고 Reverse Proxy만 외부에 노출하도록 구성할 수 있습니다.
>
> 따라서 내부 서버의 주소와 구조를 외부에서 분리할 수 있고, Proxy에서 TLS 처리, 접근 제어, 요청 제한 등의 정책도 적용할 수 있습니다."

**핵심 요약**

**내부 WAS를 직접 노출하지 않고 Proxy에서 접근 제어와 TLS 같은 보안 처리를 할 수 있다.**

---

<a name="q29-1"></a>
#### ↳ Q29-1. Forward Proxy와 Reverse Proxy의 차이는 무엇인가요?

> "Forward Proxy는 클라이언트를 대신해 외부 서버에 접근하고, Reverse Proxy는 서버를 대신해 클라이언트 요청을 받습니다.
>
> 따라서 Forward Proxy는 실제 클라이언트를 숨기고, Reverse Proxy는 실제 내부 서버를 숨긴다고 이해할 수 있습니다."

**핵심 요약**

**Forward Proxy는 Client를 대신하고, Reverse Proxy는 Server를 대신한다.**

---

<a name="section-09"></a>
## Load Balancing

<a name="q30"></a>
### Q30. 트래픽이 증가했을 때 여러 WAS 인스턴스를 두는 이유는 무엇인가요?

> "하나의 WAS가 처리할 수 있는 요청량에는 한계가 있기 때문에 여러 WAS 인스턴스를 두고 요청을 분산하여 처리량과 가용성을 높이기 위해서입니다.
>
> 앞단의 Nginx나 Load Balancer가 요청을 여러 WAS에 분배할 수 있습니다."

**핵심 요약**

**여러 WAS에 요청을 분산하여 처리량과 가용성을 높이기 위해서**

---

<a name="q30-1"></a>
#### ↳ Q30-1. Load Balancing이 무엇인가요?

> "Load Balancing은 들어오는 요청을 여러 서버에 분산하여 특정 서버에 부하가 집중되지 않도록 하는 기술입니다.
>
> 이를 통해 트래픽을 분산하고 서버를 수평 확장할 수 있으며, 일부 서버에 문제가 발생했을 때 다른 서버가 요청을 처리하도록 구성할 수도 있습니다."

**핵심 요약**

**Load Balancing은 여러 서버에 요청을 분산하여 부하를 나누는 것**

---

<a name="section-10"></a>
## WAS · DB

<a name="q31"></a>
### Q31. WAS는 DB에 직접 접근하나요?

> "정확히 말하면 WAS라는 서버 프로그램 자체가 비즈니스 데이터를 직접 조회하는 것은 아닙니다.
>
> WAS에서 실행되는 애플리케이션 코드가 DB Connection을 이용하여 DB에 접근합니다.
>
> Spring에서는 일반적으로 Controller → Service → Repository 구조를 거쳐 Repository 같은 데이터 접근 계층이 DB에 접근합니다."

**핵심 요약**

**WAS 자체가 DB에 접근한다기보다 WAS에서 실행되는 애플리케이션의 데이터 접근 코드가 DB에 접근한다.**

---

<a name="section-11"></a>
## 최종 요청 흐름

<a name="q32"></a>
### Q32. [대표 질문] 클라이언트의 요청이 Spring Controller까지 도달하는 과정을 설명해주세요.

> "Nginx를 사용하는 구조라면 먼저 클라이언트의 요청이 Nginx에 도착하고, 애플리케이션 요청이 Tomcat으로 전달됩니다.
>
> Tomcat은 요청을 처리할 Thread를 할당하고 DispatcherServlet으로 요청을 전달합니다.
>
> DispatcherServlet은 HandlerMapping을 이용해 적절한 Controller를 찾고 HandlerAdapter를 통해 Controller를 실행합니다.
>
> 이후 Controller가 Service를 호출하고 필요한 경우 Repository를 통해 DB에 접근한 뒤 결과가 다시 HTTP 응답으로 반환됩니다."

```
Client
  ↓
Nginx
  ↓
Tomcat
  ↓
Thread 할당
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
HandlerAdapter
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
DB
```

**핵심 요약**

**Nginx → Tomcat → DispatcherServlet → HandlerMapping → Controller → Service → Repository → DB**

---

<a name="section-12"></a>
## 대표 질문 정리

실제로 질문자를 정해서 하나씩 질문하는 방식이라면 **대표 질문은 이 정도**로 뽑고, 나머지를 꼬리질문으로 사용하는 게 좋습니다.

**Servlet**

**대표-1. Servlet이란 무엇인가요?**

→ Servlet Container란?

→ Request / Response란?

**대표-2. Servlet의 생명주기를 설명해주세요.**

→ 요청마다 생성되나?

→ Singleton인가?

**대표-3. Servlet 객체 하나로 여러 요청을 어떻게 동시에 처리하나요?**

→ Thread Safe한가?

→ 인스턴스 변수가 왜 위험한가?

**Spring MVC**

**대표-4. Spring MVC는 Servlet과 어떤 관계인가요?**

→ Spring MVC가 Servlet을 대체하나?

**대표-5. DispatcherServlet이 무엇인가요?**

→ Front Controller란?

→ Controller는 어떻게 찾나?

→ HandlerMapping / HandlerAdapter 차이?

**Tomcat**

**대표-6. Tomcat이란 무엇인가요?**

**대표-7. Tomcat은 WAS인가요, Servlet Container인가요?**

→ Web Server 기능도 있는가?

**대표-8. Tomcat은 여러 요청을 어떻게 동시에 처리하나요?**

→ Thread Pool을 사용하는 이유?

→ Thread Pool을 크게 하면?

→ DB Connection Pool과 차이?

**WAS / Web Server**

**대표-9. Web Server와 WAS의 차이는 무엇인가요?**

→ WAS만 사용해도 되는가?

**대표-10. WAS가 있는데 Nginx를 왜 앞에 두나요?**

→ Reverse Proxy란?

→ 보안상 장점은?

→ Load Balancing이란?

**전체 구조**

**대표-11. 클라이언트의 HTTP 요청이 Spring Controller까지 도달하는 흐름을 설명해주세요.**

---

## Related Topics

- [Spring MVC](../spring/spring-mvc.md)
- [동시성](../operating-system/concurrency-and-synchronization.md)
- [로드밸런싱](scaling-and-load-balancing.md)

[더 읽기 · servlet-tomcat-was](https://jakarta.ee/specifications/servlet/6.0/jakarta-servlet-spec-6.0)
