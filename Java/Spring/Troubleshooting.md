## jakarta.validation.UnexpectedTypeException: HV000030
- 문제 원인

@Valid 어노테이션에서 제공하는 @NotBlank / @NotEmpty 을 잘못 사용하는 경우 생기는 에러이다.

@Valid 기능들 중에 @NotBlank / @NotEmpty / @NotNull 이용하여 파라미터로 넘어오는 데이터에 설정을 해주면 편리하게 컨트롤 할 수 있는 장점이 있다. 하지만 해당 어노테이션의 특성을 잘 이해하지 못하고 사용하는 경우 다음의 에러를 보게 된다.

해당 에러의 경우는 어노테이션으로는 @NotEmpty를 Integer 타입의 데이터에 사용한 경우 나오는 에러 문구이다. 먼저 @NotEmpty에 경우 널 또는 "", " " 값을 허용하지 않겠다는 어노테이션인데 Integer 타입에서 공백 또는 ""가 올 일이 없기 때문에 에러가 나는 것이다.

- 해결 방법

@NOTNULL 어노테이션을 사용한다.
