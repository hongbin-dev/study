# 람다의 local variable

메서드에 선언한 로컬변수를 람다에서 사용하지 못하는케이스가 있다.

과거에는 final, 이후에는 expectedly final인 로컬변수만 사용할 수 있었다고 한다.

```java
public void test() {
    var localVariable = 1;

    Runnable runnable = () -> {
    System.out.println(localVariable);
    };

    localVariable++;
}
```

위와 같은 코드에서 localVariable이 가변이기 때문에, 컴파일이 되지 않는다.
람다를 사용하려면 localVariable++ 코드를 제거하거나, final을 선언해서 참조해야한다.


왜 그럴까?

어느정도 메모리 구조를 이해해야한다. 로컬변수는 메서드와 함께 stack 쌓이고, 객체들은 힙 영역에 저장된다.
