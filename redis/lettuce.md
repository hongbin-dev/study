### 셋팅
lettuce 사용으로 RedisConnectionFactory를 Bean으로 등록하였음.

```java
@Bean
public RedisConnectionFactory redisConnectionFactory() {
    return new LettuceConnectionFactory(redisProperties.getHost(),
        redisProperties.getPort());
}
```

정상적으로 시스템이 동작했지만, 레디스가 정상 연결되지 않았음에도 애플리케이션이 정상으로 동작했다.

테스트 커넥션이 필요하다고 느꼈다. 왜냐하면 애플리케이션이 배포 후에 request를 받은 후 에러가 발생하면 늦었다고 생각했다.

애플리케이션 배포를 실패할 수 있도록 빠른실패를 하는 설정을 추가하면 빠른실패를 할 수 있어 좋겠다.

```java
@Bean
public RedisConnectionFactory redisConnectionFactory() {
    var lettuceConnectionFactory = new LettuceConnectionFactory(redisProperties.getHost(),
        redisProperties.getPort());

    lettuceConnectionFactory.initConnection();
    return lettuceConnectionFactory;
}
```