# CS Study

![Study Archive](assets/study-archive.svg)

컴퓨터공학의 기초부터 Java와 Spring 백엔드 기술까지, 기술의 동작 원리와 설계 이유를 학습하고 정리하는 기록입니다.

기본 개념을 이해하고, 질문과 답변 및 꼬리질문으로 지식을 확장합니다.

## 첫 복습
 
웹 서비스에 요청이 들어오면 **네트워크**가 데이터를 운반하고, **서버**가 요청을 처리하며, **데이터베이스**가 필요한 상태를 저장합니다. 처음이라면 아래 세 질문에 먼저 답해 보세요. 문서에서 대표 답변을 읽고, 이유가 궁금한 부분의 꼬리질문으로 내려가면 됩니다.
 
1. [TCP는 데이터를 어떤 형태로 전달할까?](network/tcp-udp-handshake.md#q1)
2. [프로세스와 스레드는 어떻게 다를까?](operating-system/process-thread.md#q7)
3. [계좌이체에서 왜 트랜잭션이 필요할까?](database/transaction-acid-isolation.md#q1)
 
## 처음부터 읽는 순서
 
1. **자료구조** — [연산 비용과 자료구조 선택](data-structure/complexity-and-data-structures.md)에서 시작해 [해시의 내부 동작](data-structure/hash.md)으로 들어갑니다.
2. **실행과 자원 공유** — [프로세스와 스레드](operating-system/process-thread.md)를 읽은 뒤 [동시성과 동기화](operating-system/concurrency-and-synchronization.md)를 연결합니다.
3. **Java 실행 환경** — [객체지향 설계](java/oop-solid.md)와 [JVM](java/jvm/README.md)에서 객체의 역할과 실행 중의 메모리를 살펴봅니다.
4. **통신** — [TCP와 UDP](network/tcp-udp-handshake.md) → [HTTP](network/http/README.md) → [REST API 설계](network/rest-api.md) 순서로 전송에서 요청의 의미까지 올라갑니다.
5. **데이터 저장** — [인덱스](database/index-btree.md)로 행을 찾는 과정을 이해한 뒤 [트랜잭션과 격리](database/transaction-acid-isolation.md)를 읽습니다.
6. **서버와 Spring** — [Servlet과 Tomcat](backend/servlet-tomcat-was.md) → [IoC와 DI](spring/ioc-di-bean.md) → [Spring MVC](spring/spring-mvc.md) → [프록시와 트랜잭션](spring/aop-proxy-transactional.md) 순서로 연결합니다.
7. **서비스 운영** — [테스트](backend/testing.md), [메시지 큐](backend/message-queue.md), [서버 확장](backend/scaling-and-load-balancing.md), [HTTP 실무 질문](network/http/backend-application.md)으로 복습합니다.
 
## 질문으로 찾아가기
 
| 궁금한 점 | 읽는 순서 |
| --- | --- |
| 브라우저 요청은 어떻게 Java 메서드까지 도착할까? | [TCP 연결](network/tcp-udp-handshake.md) → [HTTP 메시지](network/http/fundamentals.md) → [Tomcat과 Servlet](backend/servlet-tomcat-was.md) → [Spring MVC](spring/spring-mvc.md) |
| 여러 요청이 같은 데이터를 바꾸면 어떻게 될까? | [프로세스와 스레드](operating-system/process-thread.md) → [경쟁 상태와 동기화](operating-system/concurrency-and-synchronization.md) → [DB 트랜잭션과 격리](database/transaction-acid-isolation.md) → [Spring 트랜잭션](spring/aop-proxy-transactional.md) |
| Java 객체는 어디에 생기고 언제 회수될까? | [JVM 실행 과정](java/jvm/jvm-fundamentals.md) → [메모리와 참조](java/jvm/memory.md) → [GC](java/jvm/garbage-collection.md) → [서버 메모리 분석](java/jvm/runtime-application.md) |
 
다시 공부할 때는 각 문서 첫머리의 **복습할 질문**에 먼저 답해 보세요. 질문을 누르면 상세 답변으로 이동하고, 그 아래에서 같은 개념의 예외와 꼬리질문을 확인할 수 있습니다.
 
## Study Topics

| Data Structure | Operating System |
| --- | --- |
| [Hash, Hashtable & HashMap](data-structure/hash.md)<br>[Complexity & Data Structure Selection](data-structure/complexity-and-data-structures.md) | [Process, Thread & Context Switching](operating-system/process-thread.md)<br>[Concurrency & Synchronization](operating-system/concurrency-and-synchronization.md) |
| **Network** | **Database** |
| [TCP, UDP & Handshake](network/tcp-udp-handshake.md)<br>[REST API](network/rest-api.md)<br>[HTTP](network/http/README.md) | [Transaction, ACID & Isolation](database/transaction-acid-isolation.md)<br>[Index, B+Tree & Clustered Index](database/index-btree.md) |
| **Java** | **Spring Framework** |
| [OOP, Polymorphism & SOLID](java/oop-solid.md)<br>[JVM, Memory, GC & Class Loading](java/jvm/README.md) | [IoC, DI, Bean & Container](spring/ioc-di-bean.md)<br>[Spring MVC & DispatcherServlet](spring/spring-mvc.md)<br>[AOP, Proxy & @Transactional](spring/aop-proxy-transactional.md) |
| **Backend Engineering** | |
| [Unit & Integration Testing](backend/testing.md)<br>[Message Queue](backend/message-queue.md)<br>[Scaling & Load Balancing](backend/scaling-and-load-balancing.md)<br>[Servlet, Tomcat & WAS](backend/servlet-tomcat-was.md) | |

## Learning Approach

기본 개념 → 동작 원리 → 비교와 예외 → 꼬리질문 → 실무 응용

- **Q1, Q2:** 기본 및 개별 학습 질문
- **↳ Q1-1:** 바로 앞 질문에서 확장되는 꼬리질문
- **핵심 질문 / 심화 질문:** 원본에서 중요도가 표시되었던 질문
- **핵심 요약:** 복습을 위한 한 줄 정리

## 회차별 기록 · 날짜순

| Date | Topic |
| --- | --- |
| 2026.09.04 | [Testing](backend/testing.md) · [Message Queue](backend/message-queue.md) · [Scaling](backend/scaling-and-load-balancing.md) |
| 2026.09.08 | [Hash / Hashtable / HashMap](data-structure/hash.md) |
| 2026.09.11 | [Process / Thread / Context Switching](operating-system/process-thread.md) |
| 2026.09.18 | [IoC / DI / Bean / Container](spring/ioc-di-bean.md) |
| 2026.09.22 | [TCP / UDP / Handshake](network/tcp-udp-handshake.md) |
| 2026.09.29 | [Transaction / ACID / Isolation](database/transaction-acid-isolation.md) |
| 2026.10.02 | [OOP / Polymorphism / SOLID](java/oop-solid.md) |
| 2026.10.07 | [Servlet / Tomcat / WAS](backend/servlet-tomcat-was.md) |
| 2026.10.13 | [Spring MVC / DispatcherServlet](spring/spring-mvc.md) |
| 2026.10.16 | [REST API / HTTP Methods / Idempotency](network/rest-api.md) |
| 2026.10.27 | [Complexity / Data Structure Selection](data-structure/complexity-and-data-structures.md) |
| 2026.10.30 | [Concurrency / Race Condition / Mutex / Semaphore](operating-system/concurrency-and-synchronization.md) |
| 2026.11.03 | [AOP / Proxy / @Transactional](spring/aop-proxy-transactional.md) |
| 2026.11.06 | [HTTP](network/http/README.md) |
| 2026.11.10 | [Index / B+Tree / Clustered Index](database/index-btree.md) |
| 2026.11.13 | [JVM / Memory / Class Loading](java/jvm/README.md) |

## Next Topics

원본에서 제목만 등록된 학습 주제입니다.

- Spring Transaction / Propagation
- Database Lock / MVCC / Deadlock

이 저장소는 학습 기록입니다. 상세 답변을 임의로 축약하지 않으며, 새로운 학습 내용은 지속적으로 보완합니다.
