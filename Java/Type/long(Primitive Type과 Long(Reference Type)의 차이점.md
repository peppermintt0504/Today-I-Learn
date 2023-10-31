## long과 Long

long은 원시 타입(Primitive Type), Long은 참조 타입(Reference Type)이다

### 원시 타입(Primitive Type)
원시 타입은 정수, 실수, 문자, 논리 리터럴 등 실제 메모리에 데이터 값을 직접 저장하는 타입으로서
boolean, char, byte, short, int, long, float, double를 말한다.

### 참조 타입(Reference Type)
참조 타입은 객체의 주소를 저장하는 타입으로 메모리 주소 값을 통해 객체를 참조하는 타입으로서

원시 타입을 제외한 문자열, 배열, enum, 클래스, 인터페이스를 말한다.



## 원시 타입(Primitive Type)은 null 할당이 불가능하다
원시 타입과 참조 타입의 가장 큰 차이점 중 하나는 원시 타입은 null로 지정이 불가능하지만, 참조 타입은 가능하다는 점이다.
하지만 원시 타입이라고 해서 null이 필요없는 것은 아니다.
그래서 원시 타입을 참조 타입으로 변환하는 Boxed Primitive Type인 래퍼 클래스(Wrapper Class)를 사용하게 된다.
래퍼 클래스도 클래스이기 때문에 참조 타입이다.

 

## 하지만 원시 타입(Primitive Type)은 성능이 좋다
원시 타입은 직접 값을 할당한다는 점에서 참조 타입에 비해 성능이 좋다는 장점이 있다.
원시 타입은 'Stack' 영역에 값이 존재하고, 참조 타입의 경우 'Stack' 영역에는 참조 주소 정보만 있고 실제 데이터는 'Heap' 영역에 존재한다.
즉, 값을 가져오는 데에는 원시 타입이 속도 상 빠르다.
추가로 메모리 측면에서도 원시 타입을 사용하는 것이 좋다.

 

 

## 도메인에서 id 값에 Long을 사용하는 이유
도메인에서 id 값에 Long을 사용하는 이유는 도메인 영역의 id는 대체로 DB Table의 auto increment 값을 의미하는 경우가 많다.
즉, 데이터가 생성되는 시점에서 해당 값이 할당된다는 것이며, 도메인 객체의 id는 특정 시점에 존재할 수도 있고 존재하지 않을 수도 있다.
그렇기 때문에 long이 아닌 Long을 사용하며, 다만 이 변수가 not null 이 보장된다면 long을 사용하는 것이 더 좋다.

 

 

## 추가) 원시 타입과 참조 타입을 혼용해서 사용하지 말자
원시 타입을 참조 타입으로 변환하는 것을 Boxing, 참조 타입을 원시 타입으로 변환하는 것을 UnBoxing 이라고 한다.
자바에서는 이러한 변환을 명시적으로 선언하지 않아도 가능하도록 Auto Boxing과 Auto UnBoxing 기능을 제공한다.
하지만 이 경우 불필요한 객체 생성으로 인한 성능 저하를 일으킬 수 있다.
```
public class Sum {
    private static long sum() {
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) {
            sum += i;
        }
        return sum;
    }
}
```

다음과 같은 코드에서 long으로 선언된 i 값이 Long으로 선언된 sum 변수와 연산을 하기 위해 Auto Boxing을 하게 되는데,

long을 Long으로 변환하는 과정에서 불필요한 Long 인스턴스가 생성된다.

long으로 선언했을 때와 비교하면 약 3배의 실행 속도 차이(Java11, M1 기준)를 확인 가능하다.

그러므로 최대한 원시 타입을 사용하며, 원시 타입과 참조 타입이 혼용되지 않도록 주의해야 한다.

 
