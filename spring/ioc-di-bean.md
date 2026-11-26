# IoC, DI, Bean & Container

<sub>Spring · 2026.09.18 · 6주차</sub>

[Study Index](../README.md)

서비스가 저장소 구현체를 직접 생성하는 경우와 외부에서 전달받는 경우를 비교합니다. 객체를 누가 만들고 연결하는지 따라가면 IoC, DI, Bean의 역할이 구분됩니다.

**복습할 질문**

- [IoC란 무엇이고 왜 사용하나요?](#q1)
- [생성자 주입을 권장하는 이유는?](#q5)
- [Spring Bean이란 무엇이고 생명 주기는 어떻게 되나요?](#q10)

## 목차

- [IoC (제어의 역전)](#section-01)
- [DI (Dependency Injection)](#section-02)
- [Bean](#section-03)
- [Container](#section-04)
- [연관 종합 질문](#section-05)
- [복습 요약](#section-06)
- [핵심 요약](#section-07)
- [흐름으로 한 번에 외우기](#section-08)
- [구분해서 이해할 개념](#section-09)

---

<a name="section-01"></a>
## IoC (제어의 역전)

<a name="q1"></a>
### Q1. [대표 질문] IoC란 무엇이고 왜 사용하나요?

**연관 질문:** "객체 지향 설계에서 제어의 역전이란 구체적으로 무엇의 제어권이 누구에게로 넘어간 것인가요?"

> "IoC는 객체의 생성이나 의존 관계 설정 같은 제어권을 개발자가 직접 가지는 것이 아니라 프레임워크나 컨테이너에 맡기는 설계 원칙입니다.
>
> 예를 들어 개발자가 필요한 객체를 직접 `new`로 생성하는 대신, Spring 컨테이너가 객체를 생성하고 필요한 의존성을 연결해줍니다.
>
> 이를 통해 객체 간 결합도를 낮추고 변경과 테스트가 쉬운 구조를 만들 수 있습니다."

**핵심 요약**

**개발자가 가지고 있던 객체 생성·연결 제어권을 프레임워크나 Container로 넘기는 설계 원칙으로 객체 간 결합도를 낮춰 변경과 테스트가 쉬운 구조를 만듦.**

---

<a name="q1-1"></a>
#### ↳ Q1-1. IoC와 DI의 관계는?

> "IoC는 제어권을 외부에 넘긴다는 더 큰 개념이고, DI는 객체가 필요한 의존성을 외부에서 주입 받도록 해서 IoC를 구현하는 대표적인 방법입니다.
>
> 즉 IoC가 설계 원칙이라면 DI는 그 원칙을 실현하는 방법이라고 볼 수 있습니다."

**핵심 요약**

**IoC가 설계 원칙이라면, DI는 IoC를 구현하는 대표적인 방법**

---

<a name="q1-2"></a>
#### ↳ Q1-2. 프레임워크와 라이브러리의 차이는?

**연관 질문:** "제어의 주도권 관점에서 라이브러리와 프레임워크의 본질적 차이는?"

> "가장 쉽게 설명하면 제어 흐름의 주도권 차이입니다.
>
> 라이브러리는 개발자가 필요할 때 직접 호출해서 사용하지만, 프레임워크에서는 전체적인 실행 구조를 프레임워크가 제공하고 필요한 시점에 개발자가 작성한 코드를 호출합니다.
>
> 그래서 프레임워크에서는 흔히 '제어의 역전'이라는 특징이 나타납니다."

**핵심 요약**

**제어 흐름의 주도권의 차이가 있음. 라이브러리는 개발자가 필요할 때 직접 호출하고, 프레임워크는 전체적인 실행 구조를 프레임워크가 제공하고 필요한 시점에 개발자가 작성한 코드를 호출.**

---

<a name="q2"></a>
### Q2. IoC를 사용하면 왜 결합도가 낮아지나?

> "객체가 필요한 구현체를 내부에서 직접 생성하지 않고 외부에서 전달 받기 때문입니다.
예를 들어 Service가 `new MySQLRepository()`로 구현체를 직접 만들면 MySQLRepository에 강하게 결합됩니다.
반면 Repository 인터페이스에만 의존하고 실제 구현체는 외부에서 주입 받도록 하면 Service는 구체 구현을 몰라도 됩니다. 따라서 구현체를 변경해도 Service 코드에는 영향을 최소화할 수 있습니다."

**핵심 요약**

**객체가 필요한 구현체를 내부에서 직접 생성하지 않고 외부에서 전달 받기 때문**

---

**연관 질문:** "IoC를 실무 코드에 적용했을 때 낮은 결합도를 달성할 수 있는 원리"

"IoC를 적용하면 객체가 필요한 구현체를 내부에서 직접 생성하지 않고, 외부에서 주입 받도록 설계할 수 있습니다.

그래서 객체는 특정 구현체가 아니라 인터페이스나 추상화에 의존하게 되고, 실제 구현체 선택은 외부에서 결정할 수 있습니다.

그 결과 구현체를 변경해도 기존 비즈니스 로직을 수정할 필요가 줄어들어 객체 간 결합도가 낮아집니다."

**직접 `new` 하지 않고 추상화에 의존하게 해서, 구현체 교체가 기존 코드에 미치는 영향을 줄인다.**

---

<a name="q2-1"></a>
#### ↳ Q2-1. 구체적인 예시를 들어 설명해주세요.

> "예를 들어 OrderService 내부에서 `new MySQLRepository()`로 Repository를 직접 생성한다고 가정하겠습니다.
>
> 나중에 MySQL 대신 다른 저장소 구현으로 바꾸려면 OrderService 코드까지 수정해야 합니다.
>
> 반면 OrderService가 Repository 인터페이스에만 의존하고 구현체를 외부에서 주입받도록 하면, OrderService는 수정하지 않고 주입하는 구현체만 변경할 수 있습니다."

**핵심 요약**

**구현체 교체 시 사용하는 코드까지 수정하지 않아도 된다.**

---

<a name="q2-2"></a>
#### ↳ Q2-2. IoC가 OCP(Open-Closed Principle)에 어떻게 기여하나요?

> "OCP는 기존 코드는 수정에 닫혀 있고 기능 확장에는 열려 있어야 한다는 원칙입니다.
>
> IoC와 DI를 적용하면 객체가 특정 구현체를 직접 생성하지 않고 추상화에 의존하게 만들 수 있습니다. 따라서 새로운 구현체가 필요할 때 기존 Service 코드를 수정하는 대신 새로운 구현체를 추가하고 주입만 변경하면 됩니다.
>
> 즉 기존 코드를 수정하지 않고 기능을 확장하기 쉬워져 OCP를 적용하는 데 도움이 됩니다."

**핵심 요약**

**OCP란 기존 코드는 수정에 닫혀 있고 기능 확장에는 열려 있어야 한다는 원칙으로 IoC는 새 구현체를 추가하고 주입만 바꾸면 되므로 기존 코드를 덜 수정하기 때문에 OCP를 적용하는데 기여한다.**

---

<a name="q3"></a>
### Q3. IoC 컨테이너는 객체를 언제 생성하나요?

> "Spring의 ApplicationContext는 기본적으로 non-lazy singleton Bean을 컨테이너 초기화 시점에 미리 생성합니다.
>
> 이렇게 하면 의존성 누락이나 Bean 생성 실패 같은 설정 문제를 애플리케이션 시작 단계에서 발견할 수 있습니다.
>
> 다만 `@Lazy`가 적용된 Bean은 실제로 필요해질 때 생성될 수 있고, prototype처럼 Scope에 따라 생성 시점도 달라집니다."

**핵심 요약**

**기본 singleton은 시작할 때 생성, @Lazy는 필요할 때 생성한다.**

---

<a name="section-02"></a>
## DI (Dependency Injection)

<a name="q4"></a>
### Q4. [대표 질문] DI란 무엇이고 어떤 방법이 있나요?

**연관 질문:** "의존성 주입의 세 가지 방식의 개념적 차이"

> "DI는 객체가 필요한 의존 객체를 내부에서 직접 생성하지 않고 외부에서 주입 받는 방식입니다.
>
> Spring에서는 대표적으로 생성자 주입, Setter 주입, 필드 주입을 사용할 수 있습니다.
>
> 필수 의존성에는 일반적으로 생성자 주입을 사용하는 것이 권장됩니다."

**핵심 요약**

**생성자는 만들 때 / Setter는 만든 후 / Field는 필드에 직접 주입**

---

<a name="q5"></a>
### Q5. 생성자 주입을 권장하는 이유는?

**연관 질문:** "왜 필드 주입보다 생성자 주입을 절대적으로 권장하나요?"

> "첫째, 필수 의존성을 객체 생성 시점에 확실하게 전달할 수 있습니다.
>
> 둘째, 의존 필드를 final로 선언할 수 있어 객체 생성 후 의존성이 바뀌지 않도록 만들 수 있습니다.
>
> 셋째, Spring 컨테이너 없이도 생성자에 직접 Mock 객체를 넣어 테스트하기 쉽습니다.
>
> 또한 클래스가 어떤 의존성을 반드시 필요로 하는지 생성자만 봐도 명확하게 알 수 있습니다."

**핵심 요약**

**생성자 주입 = 필수 의존성 명확하고 + final 선언이 가능 + 테스트 쉬움**

---

<a name="q5-1"></a>
#### ↳ Q5-1. 필드 주입은 왜 비 권장인가요?

> "필드 주입은 의존성이 클래스 외부에서 잘 드러나지 않고, final 필드로 만들기 어렵습니다.
>
> 또한 Spring 컨테이너 없이 순수 Java 객체로 테스트하려면 의존성을 직접 넣기가 불편할 수 있습니다.
>
> 그래서 필수 의존성은 생성자 주입으로 명확하게 표현하는 것이 일반적으로 더 좋습니다."

**핵심 요약**

**필드 주입 = 의존성이 숨겨지고 테스트와 불변성에 불리하다**

---

<a name="q5-2"></a>
#### ↳ Q5-2. Setter 주입은 언제 사용하나요?

**연관 질문:** "수정자 주입은 주로 어떤 상황에 제한적으로 사용되며, 감수해야 하는 단점"

> "Setter 주입은 선택적인 의존성이나 객체 생성 후 변경 가능해야 하는 의존성에 사용할 수 있습니다.
>
> 하지만 대부분의 필수 의존성은 생성 후 변경되지 않는 것이 더 안전하기 때문에 보통 생성자 주입을 우선합니다."
>
> **한 줄로 외우면:**

**Setter = 선택적·변경 가능한 의존성에 사용할 수 있다. 대신 불변성을 잃기 때문에 보통 생성자 주입을 우선함.**

---

<a name="q6"></a>
### Q6. 순환 참조란 무엇인가요?

> "순환 참조는 A가 B를 필요로 하고 B도 다시 A를 필요로 하는 것처럼 의존 관계가 원형으로 연결된 상태입니다.
>
> 특히 서로가 생성자에서 반드시 상대 객체를 필요로 한다면 어느 객체도 먼저 완전히 생성할 수 없기 때문에 의존성을 해결할 수 없습니다.
>
> 이런 구조는 두 클래스의 책임이 지나치게 강하게 얽혀 있다는 신호일 수 있습니다."

**핵심 요약**

**순환 참조 = A가 B를 필요로 하고 B도 다시 A를 필요로 하는 상태**

---

<a name="q6-1"></a>
#### ↳ Q6-1. 순환 참조가 발생하면 어떻게 해결하나요?

> "가장 좋은 방법은 의존 관계 자체를 다시 설계하는 것입니다.
>
> 예를 들어 A와 B가 서로 필요로 한다면 공통 책임을 제3의 Service로 분리하거나, 한쪽 의존 방향을 제거할 수 있는지 검토합니다.
>
> `@Lazy` 등을 이용해 지연 주입으로 우회할 수도 있지만, 이는 근본적인 설계 문제를 숨길 수 있기 때문에 우선적으로 구조 개선을 고려하는 것이 좋습니다."

**핵심 요약**

**순환 참조는 우회보다 의존 구조를 다시 설계하는 것이 우선**

---

<a name="q7"></a>
### Q7. DI를 적용하면 결합도와 응집도는 어떻게 변하나요?

**연관 질문:** "객체지향 설계에서 의존성 주입을 적용했을 때 결합도와 응집도는 구체적으로 어떻게 변화하는지"

> "DI를 적용하면 객체가 구체 구현체를 직접 생성하지 않고 외부에서 전달받기 때문에 구현체에 대한 결합도가 낮아집니다.
>
> 또한 객체가 의존 객체의 생성이나 관리까지 담당하지 않고 자신의 핵심 비즈니스 책임에 집중할 수 있기 때문에 클래스의 책임이 명확해지고 응집도도 높아질 수 있습니다."

**핵심 요약**

**DI = 구현체와의 결합도는 낮추고, 자기 책임에 집중해 응집도는 높인다**

---

<a name="q7-1"></a>
#### ↳ Q7-1. DI와 DIP(의존성 역전 원칙)의 관계는?

> "DIP는 상위 수준 모듈과 하위 수준 모듈이 구체 구현에 직접 의존하기보다 추상화에 의존하도록 설계하라는 원칙입니다.
>
> DI를 사용하면 외부에서 구현체를 주입할 수 있기 때문에 DIP를 적용하기 쉬워집니다.
>
> 예를 들어 Service가 MySQLRepository라는 구체 클래스가 아니라 Repository 인터페이스에 의존하고, 실제 구현체는 외부에서 주입받는 구조입니다."

**핵심 요약**

**DIP는 추상화에 의존하는 원칙, DI는 이를 쉽게 구현하게 해주는 방법**

---

<a name="q7-2"></a>
#### ↳ Q7-2. DI를 사용하면 무조건 DIP가 지켜지나요?

> "아닙니다. 의존성을 외부에서 주입 받더라도 클래스가 특정 구체 구현체에 직접 의존한다면 DI는 사용했지만 DIP를 충분히 적용했다고 보기 어렵습니다.
>
> 변경 가능성이 있는 구현 세부 사항과 결합도를 낮추려면 적절한 추상화에 의존하도록 설계해야 합니다."

**핵심 요약**

**DI를 쓴다고 자동으로 DIP가 되는 것은 아니다**

---

<a name="q8"></a>
### Q8. @Autowired는 어떻게 동작하나요?

> "`@Autowired`는 Spring이 Bean의 의존성을 자동으로 찾아 주입하도록 하는 어노테이션입니다.
>
> Spring 컨테이너가 Bean을 생성하고 관리하는 과정에서 필요한 의존 객체를 컨테이너 안에서 찾아 연결합니다.
>
> 기본적으로 타입을 기준으로 후보를 찾고, 같은 타입의 Bean이 여러 개라면 `@Primary`나 `@Qualifier` 등을 이용해 대상을 결정할 수 있습니다."

**핵심 요약**

**@Autowired = Spring Container에서 필요한 타입의 Bean을 찾아 자동으로 연결**

<a name="q8-1"></a>
#### ↳ Q8-1. 의존성 주입은 언제 일어나나요?

> "주입 시점은 주입 방식에 따라 조금 다릅니다.
>
> 생성자 주입은 Bean 객체를 생성하는 과정에서 의존성이 전달됩니다.
>
> 필드 주입이나 Setter 주입은 객체가 생성된 뒤 Spring이 의존성을 주입합니다.
>
> 그 이후 `@PostConstruct` 같은 초기화 콜백이 실행됩니다."

**핵심 요약**

**생성자 주입은 생성하면서 / 필드·Setter는 생성 후 / 그다음 @PostConstruct**

<a name="q8-2"></a>
#### ↳ Q8-2. 생성자에 @Autowired를 반드시 붙여야 하나요?

> "아닙니다. Spring에서는 생성자가 하나뿐이라면 `@Autowired`를 생략해도 해당 생성자를 이용해 자동으로 의존성을 주입합니다."

**핵심 요약**

**생성자가 하나면 @Autowired 생략 가능**

<a name="q9"></a>
### Q9. 같은 타입의 Bean이 여러 개 있으면 어떤 Bean이 주입되나요?

> "하나의 타입에 여러 Bean이 등록되어 있는데 단일 Bean을 주입하려고 하면 어떤 Bean을 선택해야 하는지 모호해질 수 있습니다.
>
> 이 경우 `@Primary`로 기본 우선 Bean을 지정하거나 `@Qualifier`로 원하는 Bean을 명시해서 선택할 수 있습니다."

**핵심 요약**

**여러 Bean 중 기본 선택은 @Primary, 직접 지정은 @Qualifier**

---

<a name="section-03"></a>
## Bean

<a name="q10"></a>
### Q10. [대표 질문] Spring Bean이란 무엇이고 생명 주기는 어떻게 되나요?

> "Spring Bean은 Spring IoC 컨테이너가 생성하고 관리하는 객체입니다.
>
> 단순화하면 Bean의 생명 주기는 객체 생성 → 의존성 주입 → 초기화 → 사용 → 소멸 순서로 진행됩니다.
>
> 초기화 시점에는 `@PostConstruct` 같은 콜백을 사용할 수 있고, 컨테이너가 Bean을 소멸할 때는 `@PreDestroy` 같은 콜백을 사용할 수 있습니다."

**핵심 요약**

**Bean = Spring IoC가 만들고 관리하는 객체**

**생명 주기 한 줄:**

**객체 생성 → 의존성 주입 → 초기화 → 사용 → 소멸**

---

<a name="q11"></a>
### Q11. @Component와 @Bean의 차이는?

> "`@Component`는 클래스 자체에 붙여 컴포넌트 스캔을 통해 자동으로 Bean으로 등록할 때 사용합니다.
>
> 반면 `@Bean`은 보통 설정 클래스의 메서드에 붙이고, 해당 메서드가 반환하는 객체를 Bean으로 등록합니다.
>
> 직접 작성한 일반 애플리케이션 클래스는 `@Component`, `@Service`, `@Repository` 등을 많이 사용하고, 외부 라이브러리 객체나 생성 과정에 별도 설정이 필요한 객체는 `@Bean`을 사용하는 경우가 많습니다."

**핵심 요약**

**@Component = 클래스 자동 등록 / @Bean = 메서드 반환 객체 수동 등록**

---

<a name="q11-1"></a>
#### ↳ Q11-1. @Configuration과 @Component의 차이는?

> "`@Configuration`도 내부적으로 `@Component` 계열이기 때문에 컴포넌트 스캔을 통해 Bean으로 등록될 수 있습니다.
>
> 차이는 `@Configuration`이 여러 `@Bean` 정의를 구성하는 설정 클래스로 사용된다는 점입니다.
>
> 기본적인 full configuration mode에서는 Spring이 설정 클래스를 프록시해서 같은 `@Bean` 메서드를 직접 호출하더라도 컨테이너가 관리하는 Bean을 재사용할 수 있도록 처리합니다.
>
> 반면 일반 `@Component` 안의 `@Bean` 메서드를 코드에서 직접 호출하면 일반 Java 메서드 호출처럼 동작할 수 있기 때문에 새 객체가 만들어질 수 있습니다."

**핵심 요약**

**@Configuration = Bean 설정을 위한 특수한 @Component**

---

<a name="q11-2"></a>
#### ↳ Q11-2. @Controller, @Service, @Repository는 @Component와 어떻게 다른가요?

> "세 어노테이션 모두 컴포넌트 스캔을 통해 Spring Bean으로 등록되는 `@Component` 계열의 stereotype 어노테이션입니다.
>
> `@Controller`는 웹 요청 처리 계층, `@Service`는 비즈니스 로직 계층, `@Repository`는 데이터 접근 계층이라는 역할을 명확하게 표현합니다.
>
> 특히 `@Repository`는 Spring의 예외 변환 기능과 함께 사용되어 데이터 접근 기술의 예외를 Spring의 DataAccessException 계열로 변환하는 데 활용될 수 있습니다."

**핵심 요약**

**Controller·Service·Repository = 역할을 명확히 한 특수한 Component**

---

<a name="q12"></a>
### Q12. @PostConstruct는 왜 생성자 대신 사용하나요?

> "`@PostConstruct`는 Bean이 생성되고 의존성 주입이 완료된 뒤 수행해야 하는 초기화 작업에 사용할 수 있습니다.
>
> 생성자는 객체 자체를 생성하는 역할에 집중하고, 주입된 의존성을 이용한 추가 초기화가 필요하다면 `@PostConstruct` 같은 초기화 콜백을 사용할 수 있습니다."

**핵심 요약**

**생성 후 DI까지 끝난 다음 해야 할 초기화는 @PostConstruct**

---

<a name="q13"></a>
### Q13. singleton Bean이 왜 기본 Scope인가요?

> "대부분의 Service나 Repository 같은 Bean은 요청 별 데이터를 객체의 필드에 저장하지 않는 stateless 형태로 설계할 수 있습니다.
>
> 이런 객체를 매번 새로 만들 필요가 없기 때문에 하나의 인스턴스를 생성해서 여러 곳에서 공유하면 객체 생성과 메모리 사용을 줄일 수 있습니다.
>
> 그래서 Spring의 기본 Bean Scope는 singleton입니다."

**핵심 요약**

**상태 없는 객체는 하나 만들어 공유하는 것이 효율적이어서 singleton이 기본**

---

<a name="q13-1"></a>
#### ↳ Q13-1. singleton Bean에 상태를 두면 어떻게 되나요?

> "singleton Bean 하나를 여러 요청과 여러 스레드가 함께 사용할 수 있기 때문에, 공유되는 필드에 사용자 별 상태를 저장하면 Race Condition이나 데이터 오염이 발생할 수 있습니다.
>
> 예를 들어 한 사용자의 요청 데이터가 다른 사용자의 요청 처리 중 덮어 써질 수 있습니다.
>
> 따라서 singleton Bean은 가능하면 stateless하게 설계해야 합니다."

**핵심 요약**

**singleton은 여러 스레드가 공유하므로 사용자 상태를 필드에 저장하면 위험하다**

---

<a name="q13-2"></a>
#### ↳ Q13-2. 상태가 꼭 필요하면 어떻게 하나요?

> "요청 처리 중에만 필요한 값이라면 가능하면 메서드의 지역 변수나 파라미터로 관리하는 것이 좋습니다.
>
> 요청 단위로 객체 상태가 필요하다면 request scope, 세션 단위라면 session scope 같은 적절한 Scope를 사용할 수 있습니다.
>
> ThreadLocal도 특정 상황에서 사용할 수 있지만, 특히 스레드 풀 환경에서는 값 제거를 제대로 하지 않으면 문제가 생길 수 있으므로 주의해야 합니다."

**핵심 요약**

**상태는 지역 변수로 두고, 객체 상태가 필요하면 목적에 맞는 Scope 사용**

---

<a name="q14"></a>
### Q14. Bean Scope에는 어떤 것들이 있나요?

> "기본 Scope는 singleton으로, 하나의 Spring 컨테이너에서 해당 Bean 정의에 대해 하나의 인스턴스를 사용합니다.
>
> prototype은 Bean을 요청할 때마다 새로운 인스턴스를 생성합니다.
>
> 웹 환경에서는 HTTP 요청마다 생성되는 request, 세션마다 생성되는 session, ServletContext 단위의 application scope 등이 있습니다."

**핵심 요약**

**singleton = 컨테이너 기준 하나 / prototype = 요청할 때마다 / request = HTTP 요청마다 / session = 세션마다**

---

<a name="q14-1"></a>
#### ↳ Q14-1. prototype Bean을 singleton Bean이 주입 받으면 어떻게 되나요?

> "일반적인 직접 주입 방식에서는 singleton Bean이 생성될 때 prototype Bean도 한 번 주입 됩니다.
>
> 이후 singleton Bean은 이미 주입 받은 동일한 prototype 객체를 계속 참조하기 때문에 메서드를 호출할 때마다 새로운 prototype이 자동으로 생기는 것은 아닙니다.
>
> 매번 새로운 prototype Bean이 필요하면 ObjectProvider 같은 지연 조회 방법을 사용할 수 있습니다."

**핵심 요약**

**singleton에 prototype을 그냥 주입하면 처음 받은 객체를 계속 사용한다**

---

<a name="q14-2"></a>
#### ↳ Q14-2. prototype Bean의 생명 주기는 singleton과 어떻게 다른가요?

> "prototype Bean은 Spring 컨테이너가 객체 생성과 의존성 주입, 초기화까지는 담당하지만, 생성된 객체의 전체 생명 주기를 끝까지 관리하지는 않습니다.
>
> 따라서 prototype Bean의 소멸 시점을 컨테이너가 자동으로 관리해주는 것을 기대하면 안 되고, 필요한 자원 정리는 사용하는 쪽에서 직접 고려해야 합니다."

**핵심 요약**

**prototype은 생성·초기화까지만 Spring이 관리하고 이후 관리는 개발자 책임**

---

<a name="section-04"></a>
## Container

<a name="q15"></a>
### Q15. [대표 질문] Spring Container란 무엇인가요?

> "Spring Container는 Bean을 생성하고, 필요한 의존성을 연결하고, Bean의 설정과 생명 주기를 관리하는 Spring의 핵심 구성 요소입니다.
>
> 설정 정보를 바탕으로 어떤 Bean을 만들고 서로 어떻게 연결할지 관리하며, 대표적인 컨테이너 인터페이스로 BeanFactory와 ApplicationContext가 있습니다."

**핵심 요약**

**Container = Bean을 만들고 연결하고 관리하는 주체**

---

<a name="q16"></a>
### Q16. BeanFactory와 ApplicationContext의 차이는?

> "BeanFactory는 Spring IoC 컨테이너의 가장 기본적인 기능을 정의하는 인터페이스로 Bean 생성과 의존성 관리 기능을 제공합니다.
>
> ApplicationContext는 BeanFactory의 기능을 포함하면서 이벤트 처리, 국제화, 환경 설정, 리소스 접근 등 애플리케이션 개발에 필요한 추가 기능을 제공합니다.
>
> 일반적인 Spring 애플리케이션에서는 ApplicationContext를 주로 사용합니다."

**핵심 요약**

**BeanFactory = 기본 IoC 기능 / ApplicationContext = 기본 기능 + 여러 부가 기능**

---

<a name="q16-1"></a>
#### ↳ Q16-1. ApplicationContext는 왜 singleton Bean을 미리 생성하나요?

> "기본적으로 non-lazy singleton Bean을 시작 시 미리 생성하면 의존성 누락이나 Bean 생성 실패 같은 설정 문제를 애플리케이션 시작 단계에서 발견할 수 있기 때문입니다.
>
> 다만 모든 Bean이 반드시 즉시 생성되는 것은 아니며 `@Lazy`나 Scope 등의 설정에 따라 생성 시점은 달라질 수 있습니다."

**핵심 요약**

**미리 만들어 설정 오류를 시작할 때 발견한다**

---

<a name="q17"></a>
### Q17. Spring Container와 Servlet Container의 차이는?

> "Servlet Container는 HTTP 요청을 받아 Servlet의 실행과 웹 애플리케이션 환경을 관리합니다. Tomcat이 대표적인 Servlet Container입니다.
>
> 반면 Spring Container는 Controller, Service, Repository 같은 Spring Bean의 생성과 의존성, 생명주기를 관리합니다.
>
> 즉 Servlet Container는 웹 요청 실행 환경을 관리하고, Spring Container는 애플리케이션 객체를 관리한다고 볼 수 있습니다."

**핵심 요약**

**Servlet Container = HTTP·Servlet 관리 / Spring Container = Bean 관리**

---

<a name="q17-1"></a>
#### ↳ Q17-1. 둘은 실제로 어떻게 연결되나요?

> "Spring MVC에서는 HTTP 요청이 Servlet Container로 들어오고, Servlet Container가 DispatcherServlet을 실행합니다.
>
> DispatcherServlet은 Spring MVC의 Front Controller로서 요청에 맞는 Controller와 Handler 등을 찾아 실행합니다.
>
> 이때 Controller 같은 객체들은 Spring Container가 관리하는 Bean입니다."

**핵심 요약**

**Tomcat → DispatcherServlet → Spring Bean인 Controller**

---

<a name="q18"></a>
### Q18. Container 없이 DI를 할 수 있나요?

> "가능합니다. DI 자체는 Spring만의 기능이 아니기 때문입니다.
>
> 예를 들어 `new OrderService(new OrderRepository())`처럼 외부에서 의존 객체를 만들어 생성자에 전달해도 DI입니다.
>
> Spring Container는 이런 객체 생성과 의존 관계 연결을 자동화하고, 생명주기 관리나 AOP 같은 기능까지 함께 제공합니다."

**핵심 요약**

**DI는 Spring 없이도 가능하고, Spring은 이를 자동화해준다**

---

<a name="q18-1"></a>
#### ↳ Q18-1. 그럼 Container를 사용하는 실질적인 장점은 무엇인가요?

> "애플리케이션이 커지면 객체 수와 의존 관계가 많아져 개발자가 직접 모든 객체를 생성하고 연결하기 어려워집니다.
>
> Spring Container는 Bean 생성과 의존 관계를 자동으로 관리해 이런 복잡성을 줄여줍니다.
>
> 또한 Bean을 컨테이너가 관리하기 때문에 트랜잭션, 로깅, 보안 같은 공통 기능을 프록시나 AOP와 연결하기도 쉬워집니다."

**핵심 요약**

**Container는 복잡한 객체 조립을 자동화하고 공통 기능 적용도 쉽게 한다**

---

<a name="section-05"></a>
## 연관 종합 질문

<a name="q19"></a>
### Q19. IoC, DI, Bean, Container를 한 문장으로 연결한다면?

> "IoC는 객체 관리의 제어권을 외부에 넘기는 원칙이고, DI는 필요한 객체를 외부에서 주입하는 방식이며, Container는 이러한 객체를 생성하고 연결하는 주체이고, Bean은 그 Container가 관리하는 객체입니다."

**핵심 요약**

**IoC = 원칙 / DI = 방법 / Container = 관리 주체 / Bean = 관리 대상**

---

<a name="q20"></a>
### Q20. Spring이 이런 구조를 사용하는 이유는 무엇인가요?

> "객체 생성과 비즈니스 로직을 분리하고 객체 간 결합도를 낮춰 변경과 테스트가 쉬운 애플리케이션을 만들기 위해서입니다.
>
> 각 객체가 구체 구현을 직접 만들지 않고 필요한 역할에만 의존하도록 만들면 구현체를 교체하거나 테스트용 객체를 사용하는 것이 쉬워집니다."

**핵심 요약**

**Spring의 핵심 목적 = 결합도를 낮춰 변경과 테스트를 쉽게 만드는 것**

---

<a name="section-06"></a>
## 복습 요약

**IoC**

→ **객체 관리의 제어권을 Spring에 넘김**

**DI**

→ **필요한 객체를 외부에서 주입 받음**

**IoC vs DI**

→ **IoC는 원칙, DI는 구현 방법**

**Library**

→ **내가 호출**

**Framework**

→ **프레임워크가 내 코드를 호출**

**Constructor Injection**

→ **필수 의존성 명확 + final + 테스트 쉬움**

**Field Injection**

→ **의존성이 숨겨지고 테스트가 불편**

**Setter Injection**

→ **선택적·변경 가능한 의존성**

**Circular Dependency**

→ **A → B → A**

**DIP**

→ **구체 구현보다 추상화에 의존**

**@Primary**

→ **여러 Bean 중 우선 Bean**

**@Qualifier**

→ **원하는 Bean 직접 지정**

**Bean**

→ **Spring이 생성하고 관리하는 객체**

**Bean Lifecycle**

→ **생성 → DI → 초기화 → 사용 → 소멸**

**@Component**

→ **클래스 자동 Bean 등록**

**@Bean**

→ **메서드 반환 객체를 Bean 등록**

**@Configuration**

→ **Bean 설정용 클래스**

**@Controller**

→ **웹 계층**

**@Service**

→ **비즈니스 계층**

**@Repository**

→ **데이터 접근 계층**

**@PostConstruct**

→ **DI 후 초기화**

**@PreDestroy**

→ **Bean 소멸 전 처리**

**singleton**

→ **컨테이너에서 기본적으로 하나를 공유**

**prototype**

→ **요청할 때마다 새 객체**

**request scope**

→ **HTTP 요청마다**

**session scope**

→ **세션마다**

**singleton Bean의 원칙**

→ **가능하면 stateless**

**Container**

→ **Bean을 만들고 연결하고 관리**

**BeanFactory**

→ **기본 IoC 기능**

**ApplicationContext**

→ **BeanFactory 기능 + 애플리케이션 부가 기능**

**Servlet Container**

→ **HTTP와 Servlet 관리**

**Spring Container**

→ **Bean 관리**

**DispatcherServlet**

→ **Spring MVC 요청의 중앙 진입점**

---

<a name="section-07"></a>
## 핵심 요약

**IoC = 객체 관리의 제어권을 Spring에 넘긴다**

**DI = 필요한 객체를 외부에서 넣어준다**

**Container = 객체를 만들고 연결하고 관리한다**

**Bean = Container가 관리하는 객체**

---

<a name="section-08"></a>
## 흐름으로 한 번에 외우기

**Spring Container가 Bean을 만든다**

↓

**Bean끼리 필요한 의존성을 DI로 연결한다**

↓

**개발자가 직접 객체를 만들고 연결하던 제어권이 Spring으로 넘어간다**

↓

**이것이 IoC**

즉,

**Container → Bean 생성 → DI → IoC 구현**

---

<a name="section-09"></a>
## 구분해서 이해할 개념

**IoC = DI 그 자체는 아님**

→ DI는 IoC를 구현하는 대표적인 방법

**DI = Spring만 가능한 기능이 아님**

→ 순수 Java에서도 생성자로 객체를 넘기면 DI

**생성자 주입 = 순환 참조를 없애주는 기능은 아님**

→ 순환 구조가 있으면 오히려 명확하게 드러남

**필드 주입 = 순환 참조가 무조건 런타임까지 숨겨진다는 뜻은 아님**

**ApplicationContext = 모든 Bean을 무조건 시작 시 생성하는 것은 아님**

→ 기본 non-lazy singleton은 미리 생성하지만 `@Lazy`, prototype 등은 다름

**singleton = JVM 전체에 무조건 객체 하나라는 뜻이 아님**

→ 기본적으로 Spring Container의 해당 Bean 정의 기준으로 하나

**singleton Bean = Thread-safe라는 뜻이 아님**

→ 여러 스레드가 공유하므로 mutable state가 있으면 오히려 주의해야 함

**@Component와 @Bean = 둘 다 Bean 등록 방법이지만 등록 방식이 다름**

**@Configuration = 단순 @Component 이상의 설정 의미를 가짐**

**prototype = Spring이 소멸까지 전부 관리해주는 Scope가 아님**

**Servlet Container와 Spring Container는 서로 다른 역할**

**Tomcat = HTTP/Servlet 실행 환경**

**Spring Container = Controller·Service·Repository 같은 객체 관리**

---

## Related Topics

- [객체지향과 SOLID](../java/oop-solid.md)
- [Spring AOP](aop-proxy-transactional.md)
