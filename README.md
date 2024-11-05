# 2024-11-05

## Enum 타입 Role의 캡슐화

```java
public enum UserRoleEnum {

    USER(Authority.USER),
    ADMIN(AUthority.ADMIN);

    private final String authority;

    UserRole(String authority) {
        this.authority = authority;
    }

    public String getAuthority () {
        return this.authority;
    }

    public static class Authority {
        public static final String USER = "ROLE_USER";
        public static final String ADMIN = "ROLE_ADMIN";
    }
}
```

> ### 위의 소스처럼 enum에서 내부 클래스를 사용하는 이유?

위의 소스는 유저의 Authority를 정의하기 위한 Enum 클래스이다. Enum 클래스에서 내부 class를 사용하는 것을 보고 여기에서 왜 내부 클래스가 사용되었는지 궁금했다.

Eum 클래스에서 내부 클래스를 사용한 이유는 바로 **_캡슐화_** 때문이었다.

> ### 캡슐화는 무엇인가?

캡슐화는 데이터를 보호하고 오용을 방지하기 위한 목적으로 주로 쓰이는데 실생활로 비유를 하자면 약국에서 약을 각각의 투약 회분마다 봉지에 담아서 주는 것을 비유하면 좋을 것 같다. 일단 약국에서 약에서 봉지에 담아주는 것은 약을 안전하게 보관하고 약 정보를 알려주고 다른 약과 섞이지 않게 하지 위해서이다. 이것을 캡슐화에 대입을 해보자.

> -   약이 봉지에 안전하게 보관됨 - **_데이터 보호(은닉)_**
> -   약 봉투에 약 정보가 명확히 적혀있음 - **_명확한 사용법_**
> -   다른 약과 섞이지 않음 - **_오용 방지_**

### **캡슐화가 왜 쓰이는지 이해가 조금이라도 갔다면 이번에는 위의 코드를 보면서 확실하게 이해를 해보자.**

```java
public enum UserRoleEnum {

    USER(Authority.USER),
    ADMIN(Authority.ADMIN);

    private final String authority;

    private static class Authoirty {
        public static final String USER = "ROLE_USER";
        public static final String ADMIN = "ROLE_ADMIN;
    }
}
```

```java
private final String authority; // 진짜 값 숨겨둠
```

```java
// 실제 문자열을 안전하게 보관
private static class Authority {
    public static final String USER = "ROLE_USER";
    public static final String ADMIN = "ROLE_ADMIN";
}
```

```java
// 외부에서는 아래의 깔끔한 값만 보임 - 실제 문자열은 내부클래스에...
USER(Authority.USER),
ADMIN(Authority.ADMIN);
```

### **그렇다면 캡슐화 하지 않는다면 어떤 문제가 생기는지 알아보자.**

```java
// 캡슐화 하지 않은 경우
String userRole = "ROLE_USER";  // 여기서도 사용
String adminROLE = "ROLE_ADMIN";    // 저기서도 사용

if(user.getRole().equals("ROLE_ADMIN")) // 또 다른 곳에서 사용
```

캡슐화를 하지 않는 경우에 어떤 문제가 발생할 수 있냐면

-   오타가 날 수 있음 ("ROLE_ADMIN" -> "ROLE_ADIMN")
-   권한 이름을 변경하려면 모든 파일을 찾아다니며 수정해야 함
-   누군가 실수로 "ADMIN_ROLE" 처럼 잘못된 형식을 사용할 수 있음

캡슐화는 위의 문제들을 모두 상쇄시켜준다.

### 캡슐화 사용했을 때의 장점

```java
// 캡슐화된 Enum 사용
if (user.getRile() == UserRoleEnum.ADMIN) { // 깔끔하고 실제 데이터 수정 불가

    // 관리자 로직...
}

// IDE에서 자동완성 지원
UserRoleEnum role = UserRoleEnum.   // . 찍으면 USER, ADMIN만 보임

// 컴파일러가 오류 체크 가능
UserRoleEnum role = UserRoleEnum.ADIMN; // 오타가 있으면 즉시 에러 표시
```

> ### 캡슐화
>
> ### 1. 복잡한 내용을 감춰준다. (은닉)
>
> ### 2. 사용하기 쉬운 인터페이스만 제공한다.
>
> ### 3. 실수를 방지할 수 있다.
>
> ### 4. 내부 구현 변경을 쉽게 할 수 있는 방법

## ObjectMapper

> ### Object Mapper란?

Json 데이터와 Java 객체 간의 변환(직렬화/역직열화)을 담당하는 Jackson 라이브러리의 핵심 클래스

> ### 사용하는 이유?

-   Request Body의 JSON 데이터를 Java 객체로 자동 변환
-   수동으로 파싱하는 복잡한 과정을 피할 수 있음
-   타입 안정성 보장
-   코드의 가독성과 유지 보수성 향상

> ### Object Mapper를 사용하지 않았다면?

```java
// JSON을 수동으로 파싱
String body = new String(reqeust.getInputStream(). readAllBytes());
JSONObject json = new JSONObject(body);
String username = json.getString("username");
String password = json.getString("password");

LoginReqeustDto dto = new LoginRequestDto();
dto.setUsername(username);
dto.setPassword(password);
```

```java
// ObjectMapper 사용시
LoginRequestDto requestDto = new ObjectMapper().readValue(request.InputStream, LoginRequestDto.class)
```

> ### 결론
>
> ObjectMapping 클래스는 Request Body의 JSON 데이터를 역직렬화(Java 객체화) 혹은 직렬화할 때 사용.

<장점>

-   코드 간소화
-   오류 가능성 감소
-   자동 타입 변환
-   유연한 데이터 처리

> ## SecurityConfig Method
>
> SecurityConfig 클래스에서 @Bean으로 등록해야하는 메서드들이 왜 선언이 되어야하는지 궁금했다.

### 1. AuthenticationManager

```java
@Bean
public AuthenticationMnager authenticationManager(AuthenticationConfiguration configuration) throws Exception {
    return configuration.getAuthenticationManager();
}
```

[역할]

-   실제 인증(authentication) 프로세스를 처리하는 핵심 컴포넌트
-   사용자의 credential(username/password)을 검증
-   UserDetailsService 와 PasswordEncoder를 상용하여 인증 수행

[Bean으로 등록하는 이유]

-   JwtAuthenticationFilter에서 사용자 인증 시 필요
-   다른 컴포넌트에서 인증 로직 필요할 때 주입받아 사용 가능
-   Spring Security의 인증 매커니즘을 커스터마이징 할 때 필요

### 2. JwtAuthenticationFilter

```java
@Bean
public JwtAuthenticationFilter jwtAuthenticationFilter() throws Exception {
    JwtAuthenticationFilter filter = new JwtAuthenticationFilter(jwtUtil);
    filter.setAuthenticationManager(authenticationMnager(authenticationConfiguration));

    return filter
}
```

[역할]

-   로그인 요청을 처리하는 필터
-   사용자 인증 성공 시 JWT 토큰 생성
-   인증 성공/실패에 대한 핸들링

[Bean으로 등록하는 이유]

-   SecurityFilterChain에 필터로 등록하기 위해
-   AuthenticationManager를 주입받아야 함
-   다른 컴포넌트에서 필터에 접근해야 할 때 사용

### 3. JwtAuthorizationFilter

```java
@Bean
public JwtAuthorizationFilter jwtAuthorizationFilter(){
    return new JwtAuthorizationFilter(jwtUtil, userDetailsService);
}
```

[역할]

-   요청에 포함된 JWT 토큰의 유효성 검사
-   요효한 토큰이면 SecurityContext에 인증 정보 설정
-   사용자의 권한 확인

[Bean으로 등록하는 이유]

-   SecurityFilterChain에 필터로 등록하기 위해
-   토큰 검증에 필요한 의존성(jwtUtil, userDetailsService) 주입
-   다른 컴포넌트에서 재사용 가능

### Bean 등록의 장점

1. 의존성 주입 가능
2. 싱글톤 객체로 관리되어 메모리 효율적
3. 스프링의 AOP 기능 사용 가능
4. 객체 생성과 생명주기를 스프링이 관리
5. 컴포넌트 간 결합도를 납춤
6. 테스트가 용이
