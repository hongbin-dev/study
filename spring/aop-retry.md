# AOP로 기본 retry 어노테이션 만들기
AOP 실습목적으로 retry 어노테이션을 만들어보자.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Retry {

    int count() default 3;

    Class<? extends Exception>[] ignoreExceptions() default {};
}
```

어노테이션 선언입니다.
method에서만 사용할 수 있도록 했고, Retention을 Runtime까지 두어 어노테이션을 런타임에 읽을 수 있도록 설정.
주요 필드는 재시도 횟수와 예외를 무시하고 retry할 예외들을 입력받도록 했다.

```java

@Configuration
@Aspect
public class RetryAspect {

    @Around("@annotation(me.hongbin.generic.config.Retry)")
    public Object retry(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature)joinPoint.getSignature();
        var method = signature.getMethod();
        var retry = method.getDeclaredAnnotation(Retry.class);

        for (int i = 0; i < retry.count(); i++) {
            try {
                return joinPoint.proceed();
            } catch (Exception e) {
                var ignoreClasses = retry.ignoreExceptions();
                var canRetry = canRetry(e, ignoreClasses);

                if (!canRetry) {
                    throw e;
                }
            }
        }
        throw new RetryExceededException();
    }

    private static boolean canRetry(Exception exception, Class<? extends Exception>[] classes) {
        Class<? extends Exception> exceptionClass = exception.getClass();
        
        return Arrays.stream(classes)
            .anyMatch(clazz -> clazz.isAssignableFrom(exceptionClass));
    }
}
```

로직은 위와 같다
retry 수 만큼 N번 반복하고, joinPoint(메서드 실행시점)에서 허용된 예외가 발생하면 반복한다.
허용되지 않는 예외가 발생 시 그대로 예외를 전파시키는 비즈니스 로직이다.

예외 클래스는 isAssignableFrom()로 비교되기 때문에 동일한 타입만 retry 되는게 아닌, 해당 객체를 상속/구현한 하위 클래스도 retry할 수 있도록 설정.

```java
@Transactional
@Retry(ignoreExceptions = {IllegalArgumentException.class})
public UserCreateResponse save(String name) {
    var user = new User(name);
    var persistedUser = userRepository.save(user);

    return new UserCreateResponse(persistedUser.id(), persistedUser.getName());
}
```

이런식으로 서비스레이어에서 사용할 수 있는 AOP기반 retry를 적용해볼 수 있다.