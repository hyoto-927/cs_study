# Class Loading

<sub>Java · 2026.11.13 · 13주차</sub>

[Study Index](../../README.md)

클래스 파일을 찾는 일, JVM에 연결하는 일, 초기화 코드를 실행하는 일은 서로 다른 단계입니다. static 필드에 처음 접근하는 상황을 떠올리며 각 단계의 역할을 확인해 보세요.

**복습할 질문**

- [클래스 로딩이란 무엇이며 왜 필요한가요?](#q1)
- [Preparation과 Initialization의 차이는 무엇인가요?](#q2-2)
- [ClassNotFoundException과 NoClassDefFoundError의 차이는 무엇인가요?](#q6)

## 목차

- [Class Loading](#section-01)

---

<a name="section-01"></a>
## Class Loading

<a name="q1"></a>
### Q1. 클래스 로딩이란 무엇이며 왜 필요한가요?

> 클래스 로딩은 JVM이 실행에 필요한 클래스의 바이트코드를 읽어 런타임에서 사용할 수 있도록 준비하는 과정입니다.
>
> Java는 필요한 시점에 클래스를 동적으로 로딩할 수 있으며, 로딩된 클래스는 연결과 초기화 과정을 거쳐 사용됩니다.

**핵심 요약:** 클래스 로딩은 바이트코드를 JVM이 사용할 수 있는 클래스 정보로 준비하는 과정이다.

<a name="q1-1"></a>
#### ↳ Q1-1. JVM은 모든 클래스를 프로그램 시작 시 한꺼번에 로딩하나요?

> 아닙니다.
>
> JVM은 필요한 시점에 클래스를 동적으로 로딩할 수 있으므로 모든 클래스를 시작할 때 한꺼번에 로딩할 필요는 없습니다.
>
> 다만 JVM 구현과 실행 환경에 따라 일부 클래스는 미리 로딩될 수 있습니다.

**핵심 요약:** JVM은 필요에 따라 클래스를 동적으로 로딩할 수 있다.

<a name="q1-2"></a>
#### ↳ Q1-2. [심화 질문] 클래스 로딩과 객체 생성은 같은 과정인가요?

> 아닙니다.
>
> 클래스 로딩은 클래스의 정의를 JVM에 준비하는 과정이고, 객체 생성은 해당 클래스를 기반으로 인스턴스를 생성하는 과정입니다.
>
> 하나의 클래스 정보로 여러 객체를 생성할 수 있으므로 객체를 만들 때마다 클래스를 새로 로딩할 필요는 없습니다.

**핵심 요약:** 클래스 로딩은 클래스 정보 준비이고 객체 생성은 인스턴스를 만드는 과정이다.

---

<a name="q2"></a>
### Q2. 클래스 로딩 과정에 대해 설명해 주세요.

> 클래스 로딩과 준비 과정은 크게 Loading, Linking, Initialization의 세 단계로 설명할 수 있습니다.
>
> Loading에서는 클래스 바이트코드를 읽어 클래스 정보를 생성합니다.
>
> Linking에서는 바이트코드 검증, 정적 필드 준비, 심볼릭 참조 해석 등을 수행합니다.
>
> Initialization에서는 정적 필드의 명시적인 초기화 코드와 static 초기화 블록을 실행합니다.
>
> 다만 Resolution은 필요한 시점까지 지연될 수 있습니다.

**핵심 요약:** 클래스는 Loading → Linking → Initialization을 거쳐 실행에 필요한 상태로 준비된다.

<a name="q2-1"></a>
#### ↳ Q2-1. Linking 단계는 어떻게 구성되나요?

> Linking은 Verification, Preparation, Resolution으로 구성됩니다.
>
> Verification은 클래스 파일과 바이트코드가 JVM 규칙에 맞는지 검증합니다.
>
> Preparation은 정적 필드를 위한 공간을 준비하고 기본값을 설정합니다.
>
> Resolution은 심볼릭 참조를 실제 참조로 해석합니다.
>
> 단, Resolution은 JVM 구현과 실행 상황에 따라 지연될 수 있습니다.

**핵심 요약:** Linking은 Verification, Preparation, Resolution으로 구성된다.

<a name="q2-2"></a>
#### ↳ Q2-2. Preparation과 Initialization의 차이는 무엇인가요?

> Preparation은 정적 필드에 기본값을 설정하는 단계이고, Initialization은 개발자가 작성한 클래스 초기화 코드를 실행하는 단계입니다.
>
> 예를 들어 static int count = 10이라는 필드가 있다면 일반적으로 Preparation에서는 0이 설정되고 Initialization에서 10이 할당됩니다.
>
> 다만 컴파일 타임 상수인 static final 필드에는 예외적인 처리가 적용될 수 있습니다.

**핵심 요약:** Preparation은 기본값 준비, Initialization은 명시적 초기화 코드 실행이다.

<a name="q2-3"></a>
#### ↳ Q2-3. Loading과 Initialization의 차이는 무엇인가요?

> Loading은 클래스의 바이트코드를 읽어 JVM 내부에 클래스 정보를 생성하는 과정입니다.
>
> Initialization은 정적 필드의 초기화 표현식과 static 초기화 블록 등 클래스 초기화 코드를 실행하는 과정입니다.
>
> 따라서 클래스가 로딩되었다고 반드시 초기화까지 완료된 것은 아닙니다.

**핵심 요약:** Loading은 클래스 정보를 읽어오는 과정이고 Initialization은 초기화 코드를 실행하는 과정이다.

<a name="q2-4"></a>
#### ↳ Q2-4. static 변수는 언제 초기화되나요?

> 일반적인 static 필드는 Preparation 단계에서 기본값으로 준비되고 Initialization 단계에서 명시한 초기값이 적용됩니다.
>
> 다만 컴파일 타임 상수인 static final 필드는 Preparation 단계에서 상수 값이 설정될 수 있습니다.

**핵심 요약:** 일반 static 필드는 Preparation에서 기본값을 받고 Initialization에서 명시적 초기값이 적용된다.

<a name="q2-5"></a>
#### ↳ Q2-5. [심화 질문] static 초기화 블록은 언제 실행되나요?

> static 초기화 블록은 클래스의 초기화 과정에서 실행됩니다.
>
> 정적 필드 초기화 표현식과 static 초기화 블록은 소스 코드에 작성된 순서에 따라 실행됩니다.
>
> 동일한 정의 ClassLoader가 관리하는 클래스는 일반적으로 한 번만 초기화됩니다.

**핵심 요약:** static 초기화 블록은 클래스 초기화 시 일반적으로 한 번 실행된다.

---

<a name="q3"></a>
### Q3. ClassLoader의 종류와 역할을 설명해 주세요.

> ClassLoader는 JVM이 클래스의 바이트코드를 찾아 로딩하도록 하는 역할을 합니다.
>
> 대표적으로 Bootstrap ClassLoader, Platform ClassLoader, Application ClassLoader가 있습니다.
>
> Bootstrap은 Java 핵심 클래스를, Platform은 플랫폼 관련 클래스를, Application은 애플리케이션 클래스패스의 클래스를 주로 로딩합니다.
>
> 필요에 따라 Custom ClassLoader도 구현할 수 있습니다.

**핵심 요약:** ClassLoader는 클래스를 JVM에 로딩하며 대표적으로 Bootstrap, Platform, Application 로더가 있다.

<a name="q3-1"></a>
#### ↳ Q3-1. Bootstrap ClassLoader는 Java로 구현되어 있나요?

> HotSpot JVM의 Bootstrap ClassLoader는 JVM 내부에서 구현되며 일반적인 Java ClassLoader 객체와는 다릅니다.
>
> Bootstrap ClassLoader가 정의한 클래스에서 getClassLoader()를 호출하면 일반적으로 null이 반환됩니다.

**핵심 요약:** HotSpot의 Bootstrap ClassLoader는 JVM 내부에서 구현되며 일반적인 Java ClassLoader 객체와 다르다.

<a name="q3-2"></a>
#### ↳ Q3-2. Custom ClassLoader는 언제 사용하나요?

> 동적으로 클래스를 로딩하거나 클래스 간 격리가 필요할 때 사용합니다.
>
> 예를 들어 애플리케이션 서버나 플러그인 시스템에서는 별도 ClassLoader를 사용하여 라이브러리 버전을 분리하거나 클래스를 격리할 수 있습니다.

**핵심 요약:** Custom ClassLoader는 동적 클래스 로딩이나 클래스 격리 등에 사용한다.

<a name="q3-3"></a>
#### ↳ Q3-3. [심화 질문] 서로 다른 ClassLoader가 같은 이름의 클래스를 로딩하면 같은 클래스인가요?

> 반드시 그렇지는 않습니다.
>
> JVM에서 클래스의 정체성은 클래스의 이름과 해당 클래스를 정의한 ClassLoader의 조합으로 구분됩니다.
>
> 따라서 서로 다른 ClassLoader가 같은 이름의 클래스를 각각 정의하면 서로 다른 타입으로 취급될 수 있습니다.

**핵심 요약:** JVM에서 클래스의 정체성은 클래스 이름과 정의한 ClassLoader에 의해 결정된다.

---

<a name="q4"></a>
### Q4. Parent Delegation Model(부모 위임 모델)이란 무엇인가요?

> 부모 위임 모델은 클래스 로딩 요청을 받았을 때 자식 ClassLoader가 먼저 부모 ClassLoader에 로딩을 위임하는 방식입니다.
>
> 부모가 해당 클래스를 로딩하지 못하면 자식 ClassLoader가 자신의 탐색 범위에서 클래스를 찾습니다.
>
> 이를 통해 핵심 Java 클래스의 일관성을 유지하고 공통 클래스를 재사용할 수 있도록 합니다.

**핵심 요약:** 부모 위임 모델은 클래스 로딩을 부모에게 먼저 위임하고 부모가 처리하지 못하면 자식이 처리하는 방식이다.

<a name="q4-1"></a>
#### ↳ Q4-1. 부모 위임 모델을 사용하는 이유는 무엇인가요?

> Java 핵심 라이브러리의 일관성과 안전성을 유지하기 위해서입니다.
>
> 예를 들어 애플리케이션이 Java 핵심 클래스와 동일한 이름의 클래스를 정의하더라도 기본적인 부모 위임 모델에서는 상위 ClassLoader가 제공하는 핵심 클래스가 우선 사용됩니다.
>
> 또한 여러 ClassLoader가 공통 클래스를 재사용하도록 돕습니다.

**핵심 요약:** 부모 위임 모델은 핵심 클래스 보호와 클래스 로딩의 일관성을 유지하는 데 도움이 된다.

<a name="q4-2"></a>
#### ↳ Q4-2. 부모 위임 모델은 반드시 지켜야 하나요?

> 아닙니다.
>
> 사용자 정의 ClassLoader는 필요한 경우 다른 클래스 로딩 전략을 구현할 수 있습니다.
>
> 예를 들어 애플리케이션 서버나 플러그인 시스템에서는 특정 라이브러리를 격리하기 위해 Child-First 전략을 사용하기도 합니다.
>
> 다만 Java 핵심 클래스의 정의에는 JVM의 별도 제약이 적용됩니다.

**핵심 요약:** 부모 위임 모델은 일반적인 방식이지만 사용자 정의 ClassLoader에서는 다른 전략을 사용할 수 있다.

---

<a name="q5"></a>
### Q5. 클래스 로딩과 클래스 초기화는 언제 발생하나요?

> 클래스 로딩은 JVM이 클래스 정의를 필요로 할 때 발생하며, 클래스 초기화는 JVM이 정한 특정 사용 시점에 수행됩니다.
>
> 예를 들어 클래스의 인스턴스를 생성하거나 static 메서드를 호출하는 경우 해당 클래스의 초기화가 필요할 수 있습니다.
>
> 다만 클래스가 로딩되었다고 반드시 즉시 초기화되는 것은 아닙니다.

**핵심 요약:** 클래스 로딩과 초기화는 서로 다른 과정이며 발생 시점도 다를 수 있다.

<a name="q5-1"></a>
#### ↳ Q5-1. static 변수에 접근하면 항상 클래스가 초기화되나요?

> 항상 그렇지는 않습니다.
>
> 일반적인 static 필드를 적극적으로 사용하면 해당 필드를 선언한 클래스의 초기화가 필요할 수 있습니다.
>
> 하지만 컴파일 타임 상수인 static final 필드는 값이 컴파일 과정에서 인라인될 수 있으므로 해당 상수를 사용하는 것만으로 클래스 초기화가 발생하지 않을 수 있습니다.

**핵심 요약:** 일반 static 필드 사용은 초기화를 유발할 수 있지만 컴파일 타임 상수는 예외가 될 수 있다.

<a name="q5-2"></a>
#### ↳ Q5-2. 클래스가 한 번 초기화되면 다시 초기화되나요?

> 동일한 ClassLoader가 정의한 클래스는 일반적으로 한 번만 초기화됩니다.
>
> 하지만 서로 다른 ClassLoader가 같은 이름의 클래스를 각각 정의한다면 서로 다른 클래스로 취급되므로 각각 별도로 초기화될 수 있습니다.

**핵심 요약:** 같은 정의 ClassLoader의 클래스는 일반적으로 한 번 초기화되지만 서로 다른 ClassLoader에서는 별도 초기화될 수 있다.

<a name="q5-3"></a>
#### ↳ Q5-3. [심화 질문] 클래스 초기화에 실패하면 어떻게 되나요?

> 클래스 초기화 과정에서 예외가 발생하면 초기화가 실패할 수 있습니다.
>
> 최초 실패에서는 ExceptionInInitializerError 등이 발생할 수 있으며, 이후 해당 클래스를 다시 사용하려고 하면 NoClassDefFoundError가 발생할 수 있습니다.

**핵심 요약:** 클래스 초기화가 실패하면 이후 사용 시 NoClassDefFoundError가 발생할 수 있다.

---

<a name="q6"></a>
### Q6. ClassNotFoundException과 NoClassDefFoundError의 차이는 무엇인가요?

> ClassNotFoundException은 Class.forName() 등으로 클래스를 명시적으로 로딩하려 했지만 찾지 못했을 때 발생하는 체크 예외입니다.
>
> NoClassDefFoundError는 JVM이 실행 중 필요한 클래스 정의를 사용할 수 없을 때 발생하는 Error입니다.
>
> 예를 들어 실행 환경에서 필요한 클래스가 누락되었거나 클래스 초기화에 실패한 경우 발생할 수 있습니다.

**핵심 요약:** ClassNotFoundException은 명시적 로딩 실패이고 NoClassDefFoundError는 실행 중 필요한 클래스 정의를 사용할 수 없는 오류이다.

<a name="q6-1"></a>
#### ↳ Q6-1. ClassNotFoundException과 NoClassDefFoundError는 각각 Exception인가요, Error인가요?

> ClassNotFoundException은 체크 예외인 Exception 계열에 속합니다.
>
> NoClassDefFoundError는 LinkageError의 하위 타입인 Error입니다.
>
> 전자는 예외 처리를 고려할 수 있지만 후자는 일반적으로 클래스패스나 배포 환경 등 근본적인 문제를 해결해야 합니다.

**핵심 요약:** ClassNotFoundException은 체크 예외이고 NoClassDefFoundError는 Error이다.

<a name="q6-2"></a>
#### ↳ Q6-2. [심화 질문] 컴파일은 성공했는데 실행 시 클래스 로딩 오류가 발생하는 이유는 무엇인가요?

> 컴파일 환경에 있던 클래스나 라이브러리가 실행 환경에도 존재한다고 보장할 수 없기 때문입니다.
>
> 실행 환경에서 필요한 라이브러리가 누락되거나 의존성 버전이 충돌하면 클래스 로딩 문제가 발생할 수 있습니다.
>
> 따라서 실행 환경의 클래스패스와 의존성 구성을 확인해야 합니다.

**핵심 요약:** 컴파일 환경과 실행 환경의 클래스패스 또는 의존성 차이로 클래스 로딩 오류가 발생할 수 있다.

---


---

## Related Topics

- [JVM 기본](jvm-fundamentals.md)

[더 읽기 · class-loading](https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-5.html)
