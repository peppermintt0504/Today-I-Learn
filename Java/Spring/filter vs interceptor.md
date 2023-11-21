Spring은 공통적으로 여러 작업을 처리함으로써 중복된 코드를 제거할 수 있도록 많은 기능들을 지원하고 있다.

이번에는 그 중에서 필터(Filter) vs 인터셉터(Interceptor)의 차이에 대해 알아보고자 한다. 또한 간략하게 인터셉터(Interceptor)와 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)에 대해서도 비교해보고자 한다.
 


 
## 필터(Filter)

### 필터(Filter)란?
필터(Filter)는 J2EE 표준 스펙 기능으로 디스패처 서블릿(Dispatcher Servlet)에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가작업을 처리할 수 있는 기능을 제공한다. 디스패처 서블릿은 스프링의 가장 앞단에 존재하는 프론트 컨트롤러이므로, 필터는 스프링 범위 밖에서 처리가 되는 것이다.

<img src="https://blog.kakaocdn.net/dn/bZQx9K/btq9zEBsJ75/dEAKj1HEymcKyZGZNOiA80/img.png"/>

즉, 스프링 컨테이너가 아닌 톰캣과 같은 웹 컨테이너(서블릿 컨테이너)에 의해 관리가 되는 것이고(스프링 빈으로 등록은 된다), 디스패처 서블릿 전/후에 처리하는 것이다. 이러한 과정을 그림으로 표현하면 다음과 같다.
 
 
### 필터(Filter)의 메소드
필터를 추가하기 위해서는 javax.servlet의 Filter 인터페이스를 구현(implements)해야 하며 이는 다음의 3가지 메소드를 가지고 있다.


- init 메소드
- doFilter 메소드
- destroy 메소드


```
public interface Filter {

    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
```
 
#### init 메소드
init 메소드는 필터 객체를 초기화하고 서비스에 추가하기 위한 메소드이다. 웹 컨테이너가 1회 init 메소드를 호출하여 필터 객체를 초기화하면 이후의 요청들은 doFilter를 통해 처리된다.
 

#### doFilter 메소드
doFilter 메소드는 url-pattern에 맞는 모든 HTTP 요청이 디스패처 서블릿으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메소드이다. doFilter의 파라미터로는 FilterChain이 있는데, FilterChain의 doFilter 통해 다음 대상으로 요청을 전달하게 된다. chain.doFilter() 전/후에 우리가 필요한 처리 과정을 넣어줌으로써 원하는 처리를 진행할 수 있다.
 
 
#### destroy 메소드
destroy 메소드는 필터 객체를 서비스에서 제거하고 사용하는 자원을 반환하기 위한 메소드이다. 이는 웹 컨테이너에 의해 1번 호출되며 이후에는 이제 doFilter에 의해 처리되지 않는다.
 


## 인터셉터(Interceptor)

### 인터셉터(Interceptor)란?
인터셉터(Interceptor)는 J2EE 표준 스펙인 필터(Filter)와 달리 Spring이 제공하는 기술로써, 디스패처 서블릿(Dispatcher Servlet)이 컨트롤러를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공한다. 즉, 웹 컨테이너(서블릿 컨테이너)에서 동작하는 필터와 달리 인터셉터는 스프링 컨텍스트에서 동작을 하는 것이다.


디스패처 서블릿은 핸들러 매핑을 통해 적절한 컨트롤러를 찾도록 요청하는데, 그 결과로 실행 체인(HandlerExecutionChain)을 돌려준다. 그래서 이 실행 체인은 1개 이상의 인터셉터가 등록되어 있다면 순차적으로 인터셉터들을 거쳐 컨트롤러가 실행되도록 하고, 인터셉터가 없다면 바로 컨트롤러를 실행한다.


인터셉터는 스프링 컨테이너 내에서 동작하므로 필터를 거쳐 프론트 컨트롤러인 디스패처 서블릿이 요청을 받은 이후에 동작하게 되는데, 이러한 호출 순서를 그림으로 표현하면 다음과 같다. (실제로는 Interceptor가 Controller로 요청을 위임하지는 않는다. 아래의 그림은 처리 순서를 도식화한 것으로만 이해하면 된다.)


<img src ="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSz6DV%2Fbtq9zjRpUGv%2F68Fw4fZtDwaNCZiCFx57oK%2Fimg.png"/>
 

### 인터셉터(Interceptor)의 메소드
인터셉터를 추가하기 위해서는 org.springframework.web.servlet의 HandlerInterceptor 인터페이스를 구현(implements)해야 하며, 이는 다음의 3가지 메소드를 가지고 있다.

- preHandle 메소드
- postHandle 메소드
- afterCompletion 메소드


```
public interface HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
        
        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable Exception ex) throws Exception {
    }
}
```


#### preHandle 메소드

preHandle 메소드는 컨트롤러가 호출되기 전에 실행된다. 그렇기 때문에 컨트롤러 이전에 처리해야 하는 전처리 작업이나 요청 정보를 가공하거나 추가하는 경우에 사용할 수 있다.
preHandle의 3번째 파라미터인 handler 파라미터는 핸들러 매핑이 찾아준 컨트롤러 빈에 매핑되는 HandlerMethod라는 새로운 타입의 객체로써, @RequestMapping이 붙은 메소드의 정보를 추상화한 객체이다.
또한 preHandle의 반환 타입은 boolean인데 반환값이 true이면 다음 단계로 진행이 되지만, false라면 작업을 중단하여 이후의 작업(다음 인터셉터 또는 컨트롤러)은 진행되지 않는다.
 
 
#### postHandle 메소드
postHandle 메소드는 컨트롤러를 호출된 후에 실행된다. 그렇기 때문에 컨트롤러 이후에 처리해야 하는 후처리 작업이 있을 때 사용할 수 있다. 이 메소드에는 컨트롤러가 반환하는 ModelAndView 타입의 정보가 제공되는데, 최근에는 Json 형태로 데이터를 제공하는 RestAPI 기반의 컨트롤러(@RestController)를 만들면서 자주 사용되지는 않는다.
또한 컨트롤러 하위 계층에서 작업을 진행하다가 중간에 예외가 발생하면 postHandle은 호출되지 않는다.
 
 
#### afterCompletion 메소드
afterCompletion 메소드는 이름 그대로 모든 뷰에서 최종 결과를 생성하는 일을 포함해 모든 작업이 완료된 후에 실행된다. 요청 처리 중에 사용한 리소스를 반환할 때 사용하기에 적합하다. postHandler과 달리 컨트롤러 하위 계층에서 작업을 진행하다가 중간에 예외가 발생하더라도 afterCompletion은 반드시 호출된다.
 
 
 
### 인터셉터(Interceptor)와 AOP의 비교
인터셉터 대신에 컨트롤러들에 적용할 부가기능을 어드바이스로 만들어 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)를 적용할 수도 있다. 하지만 다음과 같은 이유들로 컨트롤러의 호출 과정에 적용되는 부가기능들은 인터셉터를 사용하는 편이 낫다.

1. 컨트롤러는 타입과 실행 메소드가 모두 제각각이라 포인트컷(적용할 메소드 선별)의 작성이 어렵다.
2. 컨트롤러는 파라미터나 리턴 값이 일정하지 않다.
3. AOP에서는 HttpServletRequest/Response를 객체를 얻기 어렵지만 인터셉터에서는 파라미터로 넘어온다.

 
 
 

## 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도

### 필터(Filter) vs 인터셉터(Interceptor) 차이 정리 및 요약

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbFtyZ3%2Fbtr5QpmFU4r%2FlG0gSXUaO4hKlM5JvpLOG1%2Fimg.png"/>

#### 관리되는 컨테이너
앞서 살펴본 그림에서 보이듯이 필터와 인터셉터는 관리되는 영역이 다르다. 필터는 스프링 이전의 서블릿 영역에서 관리되지만, 인터셉터는 스프링 영역에서 관리되는 영역이기 때문에 필터는 스프링이 처리해주는 내용들을 적용 받을 수 없다. 이로 인한 차이로 발생하는 대표적인 예시가 스프링에 의한 예외처리가 되지 않는다는 것이다.
참고로 일부 포스팅 또는 자료에서 필터(Filter)가 스프링 빈으로 등록되지 못하며, 빈을 주입 받을 수도 없다고 하는데, 이는 잘못된 설명이다. 이는 매우 옛날의 이야기이며, 필터는 현재 스프링 빈으로 등록이 가능하며, 다른 곳에 주입되거나 다른 빈을 주입받을 수도 있다. 이와 관련해서는 다음 포스팅에서 자세히 살펴보도록 하자.
 
 
 
#### 스프링의 예외 처리 여부
일반적으로 스프링을 사용한다면 ControllerAdvice와 ExceptionHandler를 이용한 예외처리 기능을 주로 사용한다. 예를 들어 원하는 멤버를 찾지 못하여 로직에서 MemberNotFoundException을 던졌다면 404 Status로 응답을 반환하길 원할 것이다. 그리고 이를 위해 우리는 다음과 같은 예외 처리기를 구현하여 활용할 것이다. 이를 통해 예외가 서블릿까지 전달되지 않고 처리된다.
```
@RestControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(MemberNotFoundException.class)
    public ResponseEntity<Object> handleMyException(MemberNotFoundException e) {
        return ResponseEntity.notFound()
            .build();
    }
    
    ...
}
```

 
하지만 앞서 설명하였듯 필터는 스프링 앞의 서블릿 영역에서 관리되기 때문에 스프링의 지원을 받을 수 없다. 그래서 만약 필터에서  MemberNotFoundException이 던져졌다면, 에러가 처리되지 않고 서블릿까지 전달된다. 서블릿은 예외가 핸들링 되기를 기대했지만, 예외가 그대로 올라와서 예상치 못한 Exception을 만난 상황이다. 따라서 내부에 문제가 있다고 판단하여 500 Status로 응답을 반환한다. 이를 해결하려면 필터에서 다음과 같이 응답(Response) 객체에 예외 처리가 필요하다.

```
public class MyFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse servletResponse = (HttpServletResponse) response;
        servletResponse.setStatus(HttpServletResponse.SC_NOT_FOUND);
        servletResponse.getWriter().print("Member Not Found");
    }
}
```
 
 
 
#### Request/Response 객체 조작 가능 여부
필터는 Request와 Response를 조작할 수 있지만 인터셉터는 조작할 수 없다. 여기서 조작한다는 것은 내부 상태를 변경한다는 것이 아니라 다른 객체로 바꿔친다는 의미이다. 이는 필터와 인터셉터의 코드를 보면 바로 알 수 있다.
필터가 다음 필터를 호출하기 위해서는 필터 체이닝(다음 필터 호출)을 해주어야 한다. 그리고 이때 Request/Response 객체를 넘겨주므로 우리가 원하는 Request/Response 객체를 넣어줄 수 있다. NPE가 나겠지만 null로도 넣어줄 수 있는 것이다.
public MyFilter implements Filter {

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
        // 개발자가 다른 request와 response를 넣어줄 수 있음
        chain.doFilter(new MockHttpServletRequest(), new MockHttpServletResponse());       
    }
    
}
 
 
하지만 인터셉터는 처리 과정이 필터와 다르다. 디스패처 서블릿이 여러 인터셉터 목록을 가지고 있고, for문으로 순차적으로 실행시킨다. 그리고 true를 반환하면 다음 인터셉터가 실행되거나 컨트롤러로 요청이 전달되며, false가 반환되면 요청이 중단된다. 그러므로 우리가 다른 Request/Response 객체를 넘겨줄 수 없다. 그리고 이러한 부분이 필터와 확실히 다른 점이다.
```
public class MyInterceptor implements HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        // Request/Response를 교체할 수 없고 boolean 값만 반환할 수 있다.
        return true;
    }

}
```
 
 
### 필터(Filter)와 인터셉터(Interceptor)의 용도 및 예시
#### 필터(Filter)의 용도 및 예시

- 공통된 보안 및 인증/인가 관련 작업
- 모든 요청에 대한 로깅 또는 감사
- 이미지/데이터 압축 및 문자열 인코딩
- Spring과 분리되어야 하는 기능

필터에서는 기본적으로 스프링과 무관하게 전역적으로 처리해야 하는 작업들을 처리할 수 있다.
대표적으로 보안 공통 작업이 있다. 필터는 인터셉터보다 앞단에서 동작하므로 전역적으로 해야하는 보안 검사(XSS 방어 등)를 하여 올바른 요청이 아닐 경우 차단을 할 수 있다. 그러면 스프링 컨테이너까지 요청이 전달되지 못하고 차단되므로 안정성을 더욱 높일 수 있다.
또한 필터는 이미지나 데이터의 압축이나 문자열 인코딩과 같이 웹 애플리케이션에 전반적으로 사용되는 기능을 구현하기에 적당하다. Filter는 다음 체인으로 넘기는 ServletRequest/ServletResponse 객체를 조작할 수 있다는 점에서 Interceptor보다 훨씬 강력한 기술이다.
 
 
 
#### 인터셉터(Interceptor)의 용도 및 예시

- 세부적인 보안 및 인증/인가 공통 작업
- API 호출에 대한 로깅 또는 감사
- Controller로 넘겨주는 정보(데이터)의 가공

인터셉터에서는 클라이언트의 요청과 관련되어 전역적으로 처리해야 하는 작업들을 처리할 수 있다.
대표적으로 세부적으로 적용해야 하는 인증이나 인가와 같이 클라이언트 요청과 관련된 작업 등이 있다. 예를 들어 특정 그룹의 사용자는 어떤 기능을 사용하지 못하는 경우가 있는데, 이러한 작업들은 컨트롤러로 넘어가기 전에 검사해야 하므로 인터셉터가 처리하기에 적합하다.

또한 인터셉터는 필터와 다르게 HttpServletRequest나 HttpServletResponse 등과 같은 객체를 제공받으므로 객체 자체를 조작할 수는 없다. 대신 해당 객체가 내부적으로 갖는 값은 조작할 수 있으므로 컨트롤러로 넘겨주기 위한 정보를 가공하기에 용이하다. 예를 들어 사용자의 ID를 기반으로 조회한 사용자 정보를 HttpServletRequest에 넣어줄 수 있다.
그 외에도 우리는 다양한 목적으로 API 호출에 대한 정보들을 기록해야 할 수 있다. 이러한 경우에 HttpServletRequest나 HttpServletResponse를 제공해주는 인터셉터는 클라이언트의 IP나 요청 정보들을 포함해 기록하기에 용이하다.

출처: https://mangkyu.tistory.com/173 [MangKyu's Diary:티스토리]
