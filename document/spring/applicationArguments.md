## Application Arguments

Spring Application에서는 실행시 인자를 받는 두가지 방법이 있다. VM Argument와 Program Argument.

### VM Argument

`-D${name}={value}` 방식: 자바 클래스에서 사용할 수 있는 시스템 속성 정의
해당 값은 application.yml에 선언한것처럼 @Value 어노테이션으로 값을 받을 수 있다.


`-X${name}={value` 방식: JVM에서 사용할 수 있는 메모리양 수정 등으로 사용되는 값을 받을 수 있음

### Program Argument

main함수의 인자인 args로 받는 값들이라고 생각하면 된다.
Spring을 사용중이라면 ApplicationArguments를 사용하여 빈으로 값을 받아 낼 수 있음.

option: `--${name}={value}`
nonOption: `{name}={value}`
