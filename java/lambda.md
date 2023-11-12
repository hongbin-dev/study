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

자바 메모리 구조를 이해해야한다. 로컬변수는 stack 쌓이고, 객체들은 힙 영역에 저장된다.

localVariable은 `test()` 레벨의 스택에 저장된다. runnable의 람다가 실행되면 별도의 스택에 실행된다.

그래서 람다 영역에서 localVariable을 수정하면, `test()` 스택에서 localVariable를 반영할 수 없다.

```java
// 예시 1
public void test() {
    var localVariable = 1;

    Runnable runnable = () -> {
        localVariable++;
    };

    runnable.run();
    assertThat(localVariable).isNotEqual(2);
}
```

예시 1코드는 컴파일이 되지 않지만... 람다에서 값을 update하는 경우이다. 

만약 localVariable을 업데이트 했을때, runnable의 람다에서 변수를 값을 바꿔서 test() 스택 로컬변수의 값을 반영해줄 수 없다.


```java
// 예시 2
public void test() {
    var flag = true;

    Runnable runnable = () -> {
        while(flag) {
            System.out.println("...");
        }
    };
    
    new Thread(runnable).start();
    flag = false;
}
```

예시 2의 코드도 컴파일이 되지 않는 케이스다.

람다에서 로컬변수를 참조하고 있고, 람다를 멀티쓰레드로 실행하고 flag를 false로 바꿔 루프를 중지하려는 코드다.

문제의 이유는 다른 스택에 있는 flag 값의 변경을 람다에서 추적할 수 없기 때문이다.