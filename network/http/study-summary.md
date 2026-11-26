# HTTP Review Summary

<sub>Network · 2026.11.06 · 12주차</sub>

[Study Index](../../README.md)

## Contents

- [최종 학습 우선순위](#section-01)

---

<a name="section-01"></a>
## 최종 학습 우선순위

1순위 — 반드시 답할 수 있어야 할 내용

HTTP 정의·특징, 메시지 구조, GET·POST·PUT·PATCH·DELETE, Safe·Idempotent, Stateless, 상태 코드, Content-Type과 Accept, 쿠키·세션, HTTP와 HTTPS

2순위 — 자주 나오는 꼬리질문

Keep-Alive와 Connection Pool, 401·403, 502·503·504, JWT와 Stateless, 쿠키 보안, Cache-Control과 ETag, HTTP/2 멀티플렉싱, CORS, TLS 인증서

3순위 — 실무·심화 질문

HTTP/3 QUIC, HOL Blocking, 대칭키·비대칭키, HTTP 캐시와 Redis, Spring Security의 상태 코드 처리, 외부 API 타임아웃, 중복 요청과 재시도

**전체 통합본에서 꼭 연결해야 할 다섯 가지**

① HTTP와 TCP

HTTP는 요청과 응답의 의미를 정의하고, TCP나 QUIC은 데이터를 전송합니다. HTTP의 Stateless와 TCP의 연결 상태 관리는 서로 다른 개념입니다.
