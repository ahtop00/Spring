# 7주차 키워드 정리🎯
# @RestControllerAdvice
## @ControllerAdvice?
- API 동작 중, 예외가 발생했을 때, 예외를 바로 사용자에게 반환하는 것은 사용자에게 있어 불필요한 정보이다.
- 이때 스프링의 `@ControllerAdvice`를 이용하면 API 동작 중에 발생한 예외를 적절히 처리할 수 있다.
- `@ControllAdvice`는 애플리케이션 내 모든 컨트롤러에서 발생하는 예외를 처리할 수 있다.
- `@RequestMapping`이 달린 메서드 실행 중 발생한 예외를 가로채는 exceptions interceptor라 생각하면 간단하다.

## @RestControllerAdvice?
- `@RestControllerAdvice`는 `@ControllerAdvice`와 유사하지만, ***REST API 전용 전역 예외 처리를 위해 사용된다.***
- `@RestController`와 주로 함께 사용되며, `@RestController`처럼 JSON 또는 XML과 같은 데이터 형식의 응답을 반환한다.
- `@RestControllerAdvice`는 내부적으로 `@ControllerAdvice`와 `@ResponBody`가 결합된 어노테이션이다. -> ***모든 응답을 JSON 형식으로 반환한다.***

## @RestControllerAdvice 장점
1. 전역적인 예외 처리가 가능하다.
   - `@RestControllerAdvice`는 모든 `@RestController`에서 발생하는 예외를 한 곳에서 처리할 수 있다.
   - 이를 통해 컨트롤러별로 예외 처리를 할 필요가 없고, 전역적으로 발생한 에러에 대한 응답을 제공할 수 있다.
2. 일관된 에러 응답 제공한다.
   - 클라이언트에게 일관된 형식으로 에러 응답을 주는 것이 가능하다.
3. 예외에 따라 다른 처리 로직을 적용할 수 있다.
   - `@ExceptionHandler`을 사용하여 특정 예외에 대한 `Handler Method`를 정의할 수 있다.
   - 예시로, `NullPointerException`이 발생시 `error 400`과 함께 에러 메세지를 반환하는 로직을 구현 가능하다.

## @RestControllerAdvice 단점
1. 예외 처리가 한 곳에 집중시엔 복잡할 수 있다.
    - 처리할 예외의 종류가 많아지면 코드가 복잡해질 수 있다.
    - 예외 처리를 세분화하지 않고 모두 한 클래스에서 처리시, 코드가 길어지고 관리가 어려워질 수 있다.
    - 해당 문제의 해결책: `@RestControllerAdvice`를 여러 개 정의하고, 특정 패키지나 컨트롤러를 대상으로 분리하여 관리하는 것이 좋다.
2. 예상치 못한 예외 처리 누락의 위험이 있다.
    - `@RestControllerAdvice`에서 처리하지 않은 예외가 발생했을 때, 이를 놓칠 가능성이 있다.
    - 모든 예외를 일일이 핸들링하긴 어렵기 때문에, 별도의 `Exception`핸들러 추가 혹은 최상위 부모 예외를 핸들링 하는 메서드의 추가가 필요하다.

## @RestControllerAdvice 사용 시 권장 사항
1. 예외 처리 클래스를 적절히 분리한다.
   - `@RestControllerAdvice`를 여러 개로 나누어, 특정 컨트롤러나 패키지에 대해서만 적용되도록 설정한다.
   - 복잡성을 줄이고, 예외 처리 로직을 더 세밀하게 관리할 수 있다.
2. 예외 응답 형식을 통일한다.
   - API 에러 응답에 공통 필드를 포함시켜, 이를 통해 일관성 있는 에러 응답을 제공하도록 한다.
3. 최상위 예외 핸들러를 설정한다.
   - 예상치 못한 예외를 처리할 수 있도록 `Exception.class`를 처리하는 핸들러를 하나 추가해 두어 모든 예외가 적어도 오류메세지로 응답되도록 보장할 수 있다.

---
# Lombok
## Lombok?
- `어노테이션` 기반으로 코드를 자동완성 해주는 라이브러리다.
- `lombok`을 이용시, `Getter`, `Setter`, `Equals`, `ToString`등과 다양한 방면의 코드를 자동완성하여 코드 다이어트에 일조할 수 있다.

## Lombok 장점
1. 코드의 간결성이 향상된다.
   - 반복적인 `getter`, `setter`, `toString`, `equals`, `hashCode`등을 작성하지 않아도 되므로 코드가 간결해진다.
2. 가독성이 향상된다.
   - 실제로 필요한 비지니스 로직에만 집중할 수 있게 되어 코드 가독성이 높아진다.
3. 생산성이 증가한다.
   - 반복적인 코드를 줄여주기 때문에 개발 속도가 빨라지고 생산성이 높아진다.

## Lombok 단점
1. 디버깅시 어려움이 발생할 수 있다.
   - Lombok이 자동 생성한 메서드들이 코드에 보이지 않기 때문에, 디버깅시에 혼란을 줄 수 있다.
2. 컴파일러 의존성을 가지고 있다.
   - Lombok은 컴파일 타임에 코드에 추가되므로, 프로젝트의 빌드 도구와의 호환성 문제를 일으킬 수 있다.

## Lombok 기능 및 예제
### @Getter @Setter
- lombok에서 가장 자주 활용하는 어노테이션이다.
- 클래스 이름 위에 적용시 모든 변수들이 적용 가능하고, 변수 이름 위에 적용시키면 **해당 변수들**만 적용 가능하다.
```java
@Getter
public class Region extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Setter
    @Column(nullable = false, length = 50)
    private String name;
    
}
// 해당 코드에선 Region 엔티티에서 id와 name을 변수로 선언하였다.
// id와 name 둘다 Getter를 사용할 수 있지만, Setter의 경우 name에 대해서만 생긴다.
```
### @AllArgsConstructor
- `@AllArgsConstructor`는 모든 변수를 사용하는 생성자를 자동완성 시켜주는 어노테이션이다.
```java
@Getter
@AllArgsConstructor
public class Region extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 50)
    private String name;
    
}
/*
        AllArgsConstructor의 효과는 아래의 생성자를 자동으로 생성할 수 있다.
        
        public Region(Long id, String name) {
            this.id = id;
            this.name = name;
        }
 */
```

### @NoArgsConstructor
- `@NoArgsConstructor`는 `@AllArgsConstructor`처럼 생성자를 자동 완성 시켜주는 어노테이션이다.
- 다만 차이점이 있는데, `@NoArgsConstructor`는 어떤 변수도 사용하지 않는 기본 생성자를 자동 완성시켜준다.
```java
@Getter
@NoArgsConstructor
public class Region extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 50)
    private String name;
    
}
/*
        NoArgsConstructor의 효과는 아래의 생성자를 자동으로 생성할 수 있다.
        
        public Region() {}
 */
```

### @RequiredArgsConstructor
- `@RequiredArgsConstructor`는 특정 변수만을 활용하는 생성자를 자동완성 시켜주는 어노테이션이다.
- 생성자의 인자로 추가할 변수에 `@NonNull` 어노테이션을 붙여서 해당 변수를 생성자의 인자로 추가 가능하다.
- `@NonNull`을 쓰지 않아도 해당 변수를 `final`로 선언시 의존성이 주입된다.

### @ToString
- `@ToString`은 클래스의 `toString()` 메서드를 자동으로 생성한다.
- `toString()` 메서드는 객체의 정보를 문자열로 출력할 때 사용한다.

### @EqualsAndHashCode
- `@EqualsAndHashCode` 어노테이션은 해당 클래스에 대한 `equals()`와 `hashCode()` 메서드를 자동으로 생성해준다.
- `callSuper=true(false)` 옵션을 통해서 상위 클래스에 적용 여부를 설정할 수 있다.

### @Builder
- `@Builder` 어노테이션을 활용하면 해당 클래스의 객체 생성에 Builder 패턴을 적용시켜준다.
- `Builder Pattern`?: 복잡한 객체를 생성하기 위한 별도의 `Builder` 클래스를 ***만들어서 단계별로 설정값을 지정후***, ***마지막에 객체를  생성하는 것***이다.
  - 해당 패턴은 메서드 체인 형식으로 설정할 수 있게하여 코드를 쉽게 읽게 해주고, 유지보수 측면에서 장점이 있다.
- 그렇지만, 불변성 보장이 어렵고, 성능 오버헤드 문제가 발생 할 수 있다.
  - 보통의 경우엔 직접 ***생성자 주입***을 통해 해당 문제를 해결하는 편이 좋다.
```java
@Builder
@ToString
public class Member {
    private String name;
    private int age;
    private String email;
    private String address;
    private String phoneNumber;
}

Member member = member.budiler()
        .name("Mori")
        .age(25)
        .email("ehcl1027@gmail.com")
        .address("inha")
        .phoneNumber("010-1234-xxxx")
        .build();

System.out.println(member);
//User(name=Mori, age=25, email=ehcl1027@gmail.com, address=inha, phonNumber=010-1234-xxxx)
```

### @Data
- `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, `@RequiredArgsConstructor`가 결합된 종합 어노테이션이다.
- 주로 데이터를 저장하는 DTO에 사용된다. 그렇지만 실무에선 프로그램이 무거워질 수 있기 때문에 지나친 사용을 지양해야한다.