# Scaling & Load Balancing

<sub>Backend · 2026.09.04 · 4주차</sub>

[Study Index](../README.md)

접속자가 늘었을 때 서버 한 대의 성능을 높이는 선택과 서버 수를 늘리는 선택을 비교합니다. 서버를 늘린 뒤에는 요청 분배와 로그인 세션까지 따라가 보세요.

**복습할 질문**

- [Scale Up과 Scale Out, Load Balancer의 역할](#q1)
- [Scale Out을 하면 왜 세션 문제가 생기나요?](#q4)
- [L4와 L7 로드밸런서의 핵심 차이와 용도는?](#q8)

## 목차

- [대표 질문과 답변](#section-01)

---

<a name="section-01"></a>
## 대표 질문과 답변

<a name="q1"></a>
### Q1. Scale Up과 Scale Out, Load Balancer의 역할

**답변**

> "Scale Up(수직 확장)은 서버 한 대의 성능을 높이는 방식이고, Scale Out(수평 확장)은 서버 대수를 늘려 트래픽을 분산하는 방식입니다. Scale Up은 단순하지만 물리적 한계가 있고, Scale Out은 무한히 확장 가능하지만 분산 처리 복잡성이 생깁니다.
>
> Load Balancer는 Scale Out으로 늘어난 여러 서버에 트래픽을 분산시켜 가용성을 높임"

<a name="q2"></a>
### Q2. Scale Up의 한계에 다다르면 언제 Scale Out으로 전환해야 하나요?

> "비용 비 효율/최고 성능으로 한계 도달/가용성이 중요할 때 "

<a name="q3"></a>
### Q3. Scale Out이 무조건 좋은 것인가요?

> "아닙니다. Scale Out을 하면 데이터 동기화, 세션 관리, 네트워크 통신 비용, 분산 트랜잭션, 캐시 일관성 같은 새로운 문제들이 발생합니다. 트래픽이 그렇게 많지 않다면 오히려 Scale Up이 더 단순하고 효율적일 수 있습니다."

<a name="q4"></a>
### Q4. Scale Out을 하면 왜 세션 문제가 생기나요?

> "세션이 특정 서버 메모리에만 있기 때문에. Redis 같은 외부 저장소에 세션을 공유하거나, 같은 클라이언트를 항상 같은 서버로 보내는 Sticky Session으로 해결합니다."

<a name="q5"></a>
### Q5. Sticky Session이란 무엇인가요?

> "같은 유저는 같은 서버로 고정하는 것. → 세션 공유 문제를 간단히 해결할 수 있지만, 특정 서버에 특정 사용자들이 몰리면 부하가 균등하게 분산되지 않는 단점이 있습니다."

<a name="q6"></a>
### Q6. DB도 Scale Out이 가능한가요?

> "가능하지만 더 복잡합니다. 읽기 요청은 Read Replica로 분산하고, 쓰기까지 분산하려면 샤딩을 쓰는데, 샤딩은 JOIN과 트랜잭션이 복잡해지는 트레이드오프가 있습니다."

<a name="q7"></a>
### Q7. Load Balancer의 트래픽 분산 알고리즘에는 어떤 것들이 있나요?

> "Round Robin(순서대로 균등 분배), Least Connection(연결 수가 적은 서버 우선), IP Hash(서버 구성과 해시 조건이 유지될 때 같은 클라이언트 IP를 같은 서버에 연결)가 대표적입니다."

<a name="q8"></a>
### Q8. L4와 L7 로드밸런서의 핵심 차이와 용도는?

> L4 로드밸런서는 전송 계층의 연결 정보로 대상을 선택합니다. L7 로드밸런서는 HTTP 호스트·경로·쿠키 같은 애플리케이션 정보를 기준으로 라우팅할 수 있습니다. 실제 지연 시간은 TLS 종료, 설정, 구현과 부하에 따라 달라집니다.

<a name="q9"></a>
### Q9. Load Balancer 자체가 장애 지점(단일 장애점)이 될 수 있나요?

> "가능합니다. 그래서 실무에서는 Load Balancer 자체도 이중화(Active-Standby 등)해서 단일 장애점을 없앱니다."

---

## Related Topics

- [Servlet, Tomcat, WAS](servlet-tomcat-was.md)
