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


### Final을 사용하는 이유
#### 1. 개발 의도
- final 변수 : 해당 변수가 생성자나 대입 연산자를 통해 한번만 초기화 가능함을 의미합니다. 상수를 만들 때 주로 응용합니다.

- final 메소드 : 해당 메소드를 오버라이드 하지 못하게 하거나 숨길 수 없음을 의미합니다.
  - 상속 되면 절대 안 되는 경우에 사용합니다.
  - ISP(Interface Segregation Principle)에 따라 인터페이스를 선언한 경우 final을 사용하는 것이 한정적입니다.
    - Inteface에서 정의한 변수는 final 선언과 같이 재정의가 불가능하다.
    - Inteface에서 Method를 final로 선언할 수 없다.
    - 따라서 Interface에서 상속받은 구현체의 메소드를 final로 선언한여 재정의를 막을 수는 있습니다.
   
- final 클래스 : 해당 클래스를 상속할 수 없음을 의미합니다.
  - 대표적인 자바 표준 라이브러리 클래스로 java.lang.System, java.lang.String 등이 있습니다.
 

##### 클래스 및 생성자 의존성에 대한 final
```
public class Position {
  private final int value;

  Position(final int value) {
          this.value = value;
      }

  Position move() {
        return new Position(value + 1);        //  위치를 1칸 이동 하는 경우
    }
}
```

위와 같은 코드는 Position의 value가 필요한 경우 move() 메서드를 통해 새로운 인스턴스를 반환합니다. 이는 SRP(Single Responsibility Principle)를 준수하여 생성자를 통해 멤버 변수 값을 설정하고, 생성자를 호출하는 일관성 있는 메소드를 제공하게 됩니다.

#### 2. 코드 가독성
```
final int MAX_COUNT = 100;

for (int i = 0; i < MAX_COUNT; i++) { ... }
```

자바의 final은 컴파일러에 의해 타입 검사와 이름의 범위 처리 및 프로그램 전체에서 이름으로 대상을 가리킬 수 있고 접근자(private이나 public이냐)도 구분할 수 있다. 이러한 상수 이름을 컴파일 시간 상수(compile time constant)라고 한다.


final은 변경할 수 없는 변수 또는 컴파일 시간 상수 두 가지 경우로 사용된다. 그리고 두 경우 모두 코드의 가독성과 효율성이 동시에 좋아진다. 프로그래밍 언어에서 가독성과 효율성은 서로 트레이드오프 관계인 경우가 많은데 두 가지가 동시에 좋아지는 것은 두 마리 토끼를 잡는 것과 같다. 그러므로 프로그래밍에서 아주 바람직한 기능이라고 할 수 있고 가능한 한 자주 써주어야 한다.



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
