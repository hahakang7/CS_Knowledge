# AOP ( Aspect Oriented Programming )

AOP는 Aspect Oriented Programming의 약자로 관점 지향 프로그래밍이라고 불린다. 관점 지향은 쉽게 말해 <u>어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것이다.</u> 여기서 모듈화란 어떤 공통된 로직이나 기능을 하나의 단위로 묶는 것을 말한다. 

AOP에서 각 관점을 기준으로 로직을 모듈화한다는 것은 코드들을 부분적으로 나누어서 모듈화하겠다는 의미다. 이때, 소스 코드상에서 다른 부분에 계속 반복해서 쓰는 코드들을 발견할 수 있는 데 이것을 **흩어진 관심사 (Crosscutting Concerns)** 라 부른다. 

## AOP 주요 용어

| 용어             | 설명                                         |
| -------------- | ------------------------------------------ |
| **Aspect**     | 공통 기능을 담은 모듈 (예: 로깅 모듈)                    |
| **Join Point** | Aspect가 적용될 수 있는 지점 (메서드 호출 등)             |
| **Advice**     | 언제, 어떤 방식으로 Aspect를 적용할지 (before, after 등) |
| **Pointcut**   | 어떤 Join Point에 적용할지를 결정하는 조건               |
| **Weaving**    | 실제로 Aspect를 비즈니스 코드에 끼워 넣는 과정              |

## 주요 어노테이션

| 메서드           | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| `@Aspect`        | 해당 클래스를 Aspect로 사용하겠다는 것을 명시합니다.               |
| `@Before`        | 대상 “메서드”가 실행되기 전에 Advice를 실행합니다.                |
| `@AfterReturning`| 대상 “메서드”가 정상적으로 실행되고 반환된 후에 Advice를 실행합니다. |
| `@AfterThrowing` | 대상 “메서드에서 예외가 발생”했을 때 Advice를 실행합니다.         |
| `@After`         | 대상 “메서드”가 실행된 후에 Advice를 실행합니다.                  |
| `@Around`        | 대상 “메서드” 실행 전, 후 또는 예외 발생 시에 Advice를 실행합니다.|


## 스프링 AOP 특징

- 프록시 패턴 기반의 AOP 구현체, 프록시 객체를 쓰는 이유는 접근 제어 및 부가기능을 추가하기 위해서임
- 스프링 빈에만 AOP를 적용 가능
- 모든 AOP 기능을 제공하는 것이 아닌 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제(중복코드, 프록시 클래스 작성의 번거로움, 객체들 간 관계 복잡도 증가 ...)에 대한 해결책을 지원하는 것이 목적

## AOP 적용 방법

위빙은 대상 객체의 생애 중 수행되는 시점에 따라 아래 3가지로 나뉠 수 있다.
**1. 컴파일 타임 위빙(Compile time weaving = CTW)**
타겟 클래스가 컴파일될 때 aspect가 위빙되며 별도의 컴파일러가 필요하다. AspectJ의 위빙 컴파일러는 이러한 목적으로 사용된다.
**2. 로드 타임 위빙(Load time weaving = LTW)**
클래스가 JVM에 적재될 때 aspect가 위빙된다. 이를 위해서는 애플리케이션에서 사용되기 전에 타켓 클래스의 바이트코드를 enhance하는 특별한 ClassLoader가 필요하다. AspectJ의 로드시간 위빙 기능을 사용하면 클래스로드시간에 위빙된다.
**3. 실행 시간 위빙(Runtime Weaving = RTW)**
애플리케이션 실행 중에 aspect가 위빙된다. 보통 타겟 객체에 호출을 위임하는 구조의 프록시 객체를, 위빙 중에 AOP 컨테이너가 동적으로 만들어낸다. 스프링 자체 AOP의 aspect는 실시간 위빙을 사용한다.


## 스프링 AOP : @AOP

스프링 @AOP를 사용하기 위해서는 다음과 같은 의존성을 추가해야 한다. 
``` java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

다음에는 아래와 같이 @Aspect 어노테이션을 붙여 이 클래스가 Aspect를 나타내는 클래스라는 것을 명시하고 @Component를 붙여 스프링 빈으로 등록한다.

``` java
@Component
@Aspect
public class PerfAspect {

    @Around("execution(* com.saelobi..*.EventService.*(..))")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();

        Object retVal = pjp.proceed(); // 메서드 호출 자체를 감쌈

        System.out.println(System.currentTimeMillis() - begin);

        return retVal;
    }
}

```
`@Around` 어노테이션은 타겟 메서드를 감싸서 특정 Advice를 실행한다는 의미이다. 위 코드의 Advice는 타겟 메서드가 실행된 시간을 측정하기 위한 로직을 구현하였다. 추가적으로 `execution(* com.saelobi..*.EventService.*(..))`가 의미하는 바는 `com.saelobi` 아래의 패키지 경로의 `EventService` 객체의 모든 메서드에 이 Aspect를 적용하겠다는 의미다.

이외에도 특정 애노테이션이 붙은 포인트에 Aspect 실행, 


## 스프링 AOP vs AspectJ AOP 차이

| 구분         | **스프링 AOP**                               | **AspectJ AOP**                 |
| ---------- | ----------------------------------------- | ------------------------------- |
| **기반**     | 프록시 기반 (Proxy)                            | 바이트코드 조작 기반 (Weaving)           |
| **적용 대상**  | 메서드 실행(join point)                        | 메서드, 생성자, 필드 접근 등 모든 join point |
| **적용 범위**  | 스프링 컨테이너 내 빈(bean)만                       | 스프링 외부 객체까지 가능                  |
| **실행 시점**  | 런타임(동적)                                   | 컴파일 타임, 클래스 로딩 타임 또는 런타임        |
| **설정/사용성** | 간단하고 스프링 부트와 잘 통합됨                        | 설정이 복잡하고 별도 툴 필요 (ajc 등)        |
| **주 용도**   | 대부분의 일반 AOP 작업                            | 고성능, 빈이 아닌 객체에도 적용 필요할 때        |
| **사용 예**   | 로깅, 트랜잭션, 보안 등 일반적인 cross-cutting concern | 성능 측정, 타 시스템 로깅 등 복잡한 AOP 작업    |

### 어떤걸 써야 할까?

| 상황                          | 추천 방식                        |
| --------------------------- | ---------------------------- |
| 스프링 프로젝트에서 일반적인 AOP 적용      | ✅ **스프링 AOP**                |
| 빈이 아닌 객체에 AOP를 적용해야 함       | ⚠️ **AspectJ** (그러나 신중하게 선택) |
| 성능이 매우 중요한 곳에서 바이트코드 최적화 필요 | ✅ AspectJ 고려                 |
| 학습용 또는 빠른 개발                | ✅ 스프링 AOP 추천                 |




