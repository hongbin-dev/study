# AOP(aspect-oriented programing)
![aspect-oriented programing-concept.png](aop.png)
AOP란? 관점지향 프로그래밍으로 로깅, 메서드 속도 측정 등 코드의 관점을 횡단으로 분리하여 코드를 작성한다는 의미다. (기존에 비즈니스코드를 작성하는걸 종단으로 생각할 수 있다)

객체지향과 AOP와 상반되는 개념이 아닌, 객체지향의 코드를 더 깔끔하게 작성할 수 있도록 도와준다.


## 용어 

AOP에서 들어볼 수 있는 용어가 있다.
- pointcut
  - advice가 적용될 위치. 어떤 메서드나 코드블럭에 AOP를 지정할지 결정한다.
  - 예를들어 `"@annotation(me.hongbin.generic.config.RedisLock)"` 위처럼 특정어노테이션을 적용한 코드에 AOP를 지정할 수 있다.
- advice
  - 관심사를 정의하고, 언제 어떻게 실행할지 나타내는 코드
  - 예를들어 메서드 시작 전, 시작 후 등 관심사가 언제 실행되는지 결정
- target
  - 부가기능을 추가할 대상이다.
  - ex)A메서드에 시간측정할 AOP를 추가한다. A가 target
- joinpoint
  - 관심사가 적용될 수 있는 특징 지점, 실행지점이다.
  - 예를 들어 생성자 호출, 메서드 실행, 필드 값 접근 등 프로그램 실행 중 하나의 흐름에 지정할 수 있다.
  - 스프링 AOP에서는 메서드호출만 지원되고 있다.
- weaving
  - aspect가 애플리케이션과 통합되는 과정이다. 통합되는건 컴파일 타임, 런타임 등 구현체마다 다르다.

## AOP 위빙
- 컴파일타임 위빙
  - 소스코드가 컴파일되는 시점에 위빙한다.
  - .java 코드를 bytecode로 만드는 시점에 .class에 aop 코드를 통합한다.
  - `javac main.java`
- 로드타임 위빙
  - 클래스로더에서 .class를 불러올때 위빙한다.
  - java agent를 추가하는 방식으로 사용되며, 바이트코드를 조작하는 방식으로 동작한다.
- 런타임 위빙
  - 애플리케이션이 시작한 후, 즉 런타임에 코드를 위빙한다.
  - 일반적으로 aspect를 적용하기 위해 프록시 객체를 두어 오버헤드가 발생한다. (런타임에 새 객체가 만들어져 호출되니, 아무래도 코드 자체가 변경된 것보다 오버헤드가 발생)
  - 스프링에서는 DynamicProxyJDK(인터페이스 구현 기반) 혹은 CGLIB(상속 기반)을 사용하여 프록시 객체를 만든다.

## 샘플 코드
[aop를 이용해서 retry 적용하기](./aop-retry.md)


## 출처
https://tecoble.techcourse.co.kr/post/2021-06-25-aop-transaction/