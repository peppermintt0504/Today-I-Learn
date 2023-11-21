Java에서는 불변성을 확보할 수 있도록 final 키워드를 제공하고 있다.

클래스나 변수에 final을 붙이면 처음 정의된 상태가 변하지 않는 것을 보장한다는 의미이다.

Java에서 변수들은 기본적으로 가변적인데, 변수에 final 키워드를 붙여 참조값을 변경 못하도록 해 불변성을 확보할 수 있다.

이 Final 키워드가 사용되는 4가지 경우를 정리해보자면 다음과 같다.

 <br/>

### 1. final 변수
다음과 같이 변수를 선언할 때 클래스 앞에 final 키워드
```
final String hello = "Hello world";
final 키워드가 붙은 변수는 초기화 후 변경할 수 없다. 다음과 같이 변경하려고 하면 컴파일 에러가 발생한다.

final String hello = "Hello world";

hello = "See you around" // compile error!
```

 

### 2. final arguments (인자) 
final로 선언된 인자는 메소드 내에서 변경이 불가능하다. 따라서 다음과 같이 final int로 선언한 number는 읽을 수 있지만, number = 2처럼 값을 변경하려고 하면 컴파일 에러가 발생한다.

```
public void func(final int number) {
    System.out.println(number);

    // number = 2;  compile error!
}
```

### 3. final 클래스
```
final class MJ {
    final String hello;
    MJ() {
        hello = "hello world";
    }
}
```
클래스에 final을 붙이면 다른 클래스가 상속할 수 없는 클래스가 된다. 다음과 같이 final 클래스를 상속하려고 하면 컴파일 에러 발생.

```
final class MJ {
    final String hello;
    MJ() {
        hello = "hello world";
    }
}

class MMJ extends MJ() { // compile error!
}
```

### 4. final 메소드
```
class MJ {
    final String hello = "hello world";

    final String getHello() {
        return hello;
    }
}
```

final 메소드는 Override가 안되도록 한다. 예를 들어 다음과 같이 MJ 클래스를 상속하는 MMJ 클래스에서는 getHello()를 재정의할 수 없다. Override하려고 하면 컴파일 에러가 발생한다.

```
class MJ extends MMJ {

    @Override
    String getHello() { // compile error !
        return "See you MJ";
    }
}
```

## 정리
- final variables, arguments : 값이 변경되지 않도록 만듬

- final class : 클래스를 상속하지 못하도록 만듬

- final method : 메소드가 오버라이드되지 못하도록 만듬

 

### 주의할 점
final 변수는 초기화 이후 값 변경이 발생하지 않도록 만든다.

```
final List<String> list = new ArrayList<>();
list.add("Hello");
list.add("World");
```

다음과 같이 List에 final을 선언하여 list 변수의 변경은 불가능하다.

하지만 list 내부에 있는 변수들은 변경이 가능하여 문자열을 계속 추가할 수 있다.

 

 

## + Effective final keyword

Effective final은 Java8에서 추가된 기능으로, final이 붙지 않은 변수의 값이 변경되지 않는다면 그 변수를 Effectively final이라고 한다. final을 붙이지 않았지만 컴파일러가 final로 취급하는 것이다.

```
int num = 1;

Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("number: " + num);
    }
};
runnable.run();
```

위 코드에서 변수 num은 Effectively final이다.

num은 선언과 동시에 1으로 할당되었고, 객체가 소멸될 때까지 값이 변경되지 않았기 때문이다.

 

Effective final이 없었던 Java8 이전에는 컴파일 에러가 발생하는 코드였다.

run()안에서 변경이 가능한 num 변수에 접근하기 때문이다. 하지만 Java8은 num 변수가 내부에서 변경되지 않기 때문에 final로 취급하여 컴파일 에러가 발생하지 않는다.
