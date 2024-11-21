# 8주차 키워드 정리🎯
## Java Exception
### Exception(예외) 와 Error(오류)의 차이
> 먼저 흔히 혼동되는 예외와 오류의 구별을 해보자!
- `Exception`: 예외(`Exception`)는 개발자가 구현한 로직에서 발생한 실수나 사용자의 영향에 의해 발생한다. 오류와 달리 개발자가 미리 예측하여 방지할 수 있기에 상황에 맞는 예외처리(`Exception Handle`)를 해야 한다.
- `Error`: 오류(`Error`)는 시스템이 종료되어야 할 수준의 상황과 같이 ***수습할 수 없는 심각한 문제***를 의미한다. 개발자가 미리 예측하여 방지할 수 밖에 없다.
- `Exception`이 발생시, 프로그램이 종료되는건 `Error`와 마찬가지이나, `Error`와 다르게 `Exception`은 `Exception Handle`을 통해 프로그램이 종료되지 않고, ***정상적으로 작동되게 만들 수 있다.***

### Java Exception의 분류
- `Checked Exception`: `Checked Exception`은 컴파일 타임에 체크되는 예외이다. 즉, 컴파일러가 예외 처리를 강제하는 유형이다. ***개발자는 이러한 예외를 반드시 try-catch 블록으로 처리하거나, 메서드 시그니처에 throws를 선언***해야 합니다.
- `UnChecked Exception`: `Unchecked Exception`은 런타임에 발생하며, 컴파일 타임에는 체크되지 않는 예외이다. 예외 처리가 필수가 아니며, 주로 ***프로그래밍 오류***에서 발생한다.
  - `RuntimeException`을 상속받은 예외가 보통 이에 속한다.

### Checked Execption의 종류
#### IOException
- `IOException`은 입출력 작업 중에 발생하는 예외이다.
- 파일이 존재하지 않거나 파일에 접근할 수 없는 경우 등의 입출력 관련 오류 시 발생한다.
- 파일을 열거나 읽거나 쓸 때, 네트워크와의 통신 시, 입출력 작업을 수행하는 도중에 오류가 발생하는 경우 등에 발생한다.
- `IOException`의 계층구조는 다음과 같다.

```php
Throwable
  └── Exception
       └── IOException
            ├── FileNotFoundException
            ├── EOFException
            ├── SocketException
            ├── MalformedURLException
            └── 기타 입출력 관련 예외 클래스
 
```
- `FileNotFoundException`: 파일이 없거나 접근 권한이 없을 때 발생한다.
- `EOFException`: 예상보다 일찍 데이터의 끝(End Of File)에 도달했을 때 발생한다.
- `SocketException`: 네트워크 소켓 작업 중 문제가 발생할 때 던져진다.
- `MalformedURLException`: 잘못된 URL 형식일 때 발생한다.

```java
//예외 예시 
import java.io.*;

public class IOExceptionExample {
    public static void main(String[] args) {
        try {
            // 쓰기 권한이 없는 디렉토리에 파일 생성 시도
            FileWriter writer = new FileWriter("/restricted_directory/output.txt");
            writer.write("Hello, World!");
            writer.close();
        } catch (IOException e) {
            System.out.println("입출력 오류 발생: " + e.getMessage());
        }
    }
}
```

#### SQLException
- `SQLException`은 데이터베이스와 관련된 작업 중에 발생하는 예외이다.
- 데이터베이스에 접속할 수 없거나 쿼리를 실행하는 도중에 오류가 발생하는 경우 등에 발생한다.
- 데이터베이스 연결에 실패하거나, 잘못된 SQL 문을 실행하거나, 데이터베이스 서버와의 통신에 문제가 발생하는 경우 등에 발생한다.
- `SQLException`의 계층구조는 다음과 같다.

```php
Throwable
├── Exception
│   ├── SQLException
│   │   ├── SQLIntegrityConstraintViolationException
│   │   ├── SQLSyntaxErrorException
│   │   ├── SQLTimeoutException
│   │   └── 기타 SQL 관련 하위 클래스들
```

- `SQLIntegrityConstraintViolationException`: 무결성 조건(`Primary Key`, `Foreign Key`, `Unique Key`)를 위반했을 때 발생한다.
- `SQLSyntaxErrorException`: SQL 구문 오류가 있을 때 발생한다. (테이블이나 열 이름이 잘못되었거나, 쿼리가 잘못된 형식을 따를 때 발생한다.)
- `SQLTimeoutException`: 데이터베이스 연결 또는 쿼리 실행이 제한된 시간 내에 완료되지 않을 때 발생한다.

```java
//예외 예시
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class SQLExceptionExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/nonexistentDB";
        String username = "invalidUser";
        String password = "invalidPassword";

        try {
            // 데이터베이스 연결 시도
            Connection connection = DriverManager.getConnection(url, username, password);
        } catch (SQLException e) {
            // SQLException 처리
            System.out.println("데이터베이스 연결 실패: " + e.getMessage());
            System.out.println("SQL 상태 코드: " + e.getSQLState());
            System.out.println("벤더 에러 코드: " + e.getErrorCode());
            //DB마다 정의한 에러 코드가 다르니 유의
        }
    }
}
```

#### InterruptedException
- `InterruptedException`은 쓰레드가 일시 중지(sleep, wait 등)되어 있는 도중에 다른 쓰레드에 의해 인터럽트(interrupt)를 받았을 때 발생하는 예외이다.
- 주로 멀티스레드 환경에서 스레드 동작을 제어하거나 중단할 때 유용하게 사용된다.
- 해당 예외의 하위 클래스는 없기 때문에 계층 구조는 생략한다.

```java
//예외 예시
public class InterruptedExceptionExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            try {
                System.out.println("스레드가 5초 동안 대기...");
                Thread.sleep(5000); // 5초 동안 대기
                System.out.println("대기가 끝났습니다!");
            } catch (InterruptedException e) {
                System.out.println("스레드가 인터럽트되었습니다: " + e.getMessage());
            }
        });

        thread.start(); // 스레드 시작

        // 2초 후 스레드에 인터럽트 발생
        try {
            Thread.sleep(2000); // 메인 스레드 대기
            thread.interrupt(); // 스레드 중단 요청
        } catch (InterruptedException e) {
            System.out.println("메인 스레드가 인터럽트되었습니다.");
        }
    }
}
```

#### ClassNotFoundException
- `ClassNotFoundException`는 주로 JVM이 특정 클래스를 로드하려고 시도했지만, 해당 클래스가 클래스패스(classpath)에 존재하지 않을 때 발생한다.
- 해당 예외는  Java Reflection, ClassLoader를 이용한 동적 클래스 로딩, 또는 JDBC 드라이버 로드와 같은 작업 중에 자주 발생한다.
- 이 예외도 `InterruptedException`처럼 하위 클래스가 없다.

```java
public class ClassNotFoundExample {
    public static void main(String[] args) {
        try {
            // 존재하지 않는 클래스 로드 시도
            Class.forName("com.example.NonExistentClass");
        } catch (ClassNotFoundException e) {
            System.out.println("클래스를 찾을 수 없습니다: " + e.getMessage());
        }
    }
}
```

#### 이외에도 Spring에서 접할 수 있는 Chceked Exception
- `URISyntaxException` (java.net): URI를 생성하거나, 잘못된 URI 형식을 사용했을 때 발생한다.
- `TimeoutException` (java.util.concurrent): 비동기 작업 또는 스레드 실행 중 타임아웃이 발생했을 때 발생한다.
- `NoSuchMethodException` (java.lang): Spring `리플렉션(Reflection)`을 사용해 메서드를 호출할 때 메서드 이름이나 매개변수 시그니처가 일치하지 않는 경우 발생한다.
  - `Reflection API`?: `Reflection API`는 `Java`에서 클래스, 메서드, 필드, 생성자 등의 메타데이터를 런타임에 조사하거나, 런타임에 동적으로 조작할 수 있는 기능을 제공한다. 주로 동적 프로그래밍을 구현하거나, 런타임 시점에 컴파일된 코드의 구조를 이해하고 활용하는 데 사용된다.
- `PropertyVetoException` (java.bean): `Java Beans` 설정 중 잘못된 속성 값이 적용되었을 때 발생한다.

### UnChecked Execption의 종류
- 모든 `UnChecked Execption`은 `RuntimeException` 클래스를 상속받는다.
- `RuntimeException`의 계층 구조는 다음과 같다.

```php
Throwable
├── Exception
│   └── RuntimeException
│       ├── NullPointerException
│       ├── IndexOutOfBoundsException
│       │   ├──ArrayIndexOutOfBoundsException 
│       ├── ArithmeticException
│       ├── ClassCastException
│       ├── IllegalArgumentException
│       │   ├── NumberFormatException
│       ├── IllegalStateException
│       ├── UnsupportedOperationException
│       ├── ConcurrentModificationException
│       ├── SecurityException
│       ├── MissingResourceException
│       └── 기타 RuntimeException 하위 클래스들...
```
> 물론 이렇게 수많은(...) 예외들이 있지만 대표적인 예외만이라도 알아보자

#### NullPointerException
- `null` 값을 참조하려고 할 때 발생한다.
- 객체가 초기화되지 않았거나 `null`인 상태에서 해당 객체의 메서드 호출, 필드 접근, 배열 길이 확인 등을 시도할 때 던져진다.

```java
public class NullPointerExample {
    public static void main(String[] args) {
        String str = null;
        System.out.println(str.length()); // NullPointerException 발생
    }
}
```
#### ArrayIndexOutOfBoundsException
- `Java`에서 **배열(Array)** 에 잘못된 인덱스로 접근하려고 할 때 발생하는 예외이다.
- 배열의 유효한 범위(0부터 length-1까지)를 벗어난 인덱스로 접근하려고 할 때 예외가 던져진다.

```java
public class ArrayIndexOutOfBoundsExample {
    public static void main(String[] args) {
        int[] numbers = {10, 20, 30};
        
        // 유효하지 않은 인덱스로 접근
        System.out.println(numbers[3]); // ArrayIndexOutOfBoundsException 발생
    }
}
```
#### ArithmeticException
- `Java` 에서 산술 연산이 잘못되었을 때 발생하는 예외이다.
- 정수를 0으로 나누는 경우나, 기타 잘못된 산술 계산이 이루어질 때 발생한다.
- `오버플로우`는 해당 예외가 발생하지 않는다 -> ***수동적인 검증이 반드시 필요하다.***

```java
public class ArithmeticExample {
    public static void main(String[] args) {
        int a = 10;
        int b = 0;
        System.out.println(a % b); // ArithmeticException 발생
    }
}
```

#### IllegalArgumentException
- `Java`에서 메서드에 전달된 인자가 유효하지 않을 때 발생하는 예외이다.
- **메서드** 호출 시 전달된 값이 메서드에서 요구하는 조건에 부합하지 않는 경우, 개발자가 명시적으로 이 예외를 던질 수 있다.

```java
public class ArgumentExample {
    public static void printName(String name) {
        if (name == null) {
            throw new IllegalArgumentException("이름은 null일 수 없습니다."); //명시적으로 해당 예외를 던짐
        }
        System.out.println("이름: " + name);
    }

    public static void main(String[] args) {
        printName(null); // IllegalArgumentException 발생
    }
}

```

#### ClassCastException
- Java에서 잘못된 형변환(casting)을 시도할 때 발생하는 예외이다.
- 객체를 잘못된 타입으로 변환하려고 할 때 발생한다. 예시로, 부모 클래스 타입의 객체를 자식 클래스 타입으로 변환하려 할 때, 객체가 실제로 그 타입을 구현하지 않으면 이 예외가 발생한다.

```java
public class ClassCastExample {
    public static void main(String[] args) {
        Object obj = "Hello, World!";
        Integer number = (Integer) obj;  // ClassCastException 발생 잘못된 클래스 타입이기 때문이다.
    }
}
```

---

## @Valid
### @Valid?
- @Valid는 JSR-303 표준 스펙(자바 진영 스펙)으로써 빈 검증기(Bean Validator)를 이용해 객체의 제약 조건을 검증하도록 지시하는 어노테이션이다. JSR 표준의 빈 검증 기술의 특징은 객체의 필드에 달린 어노테이션으로 편리하게 검증을 한다는 것이다.

### @Valid 특징
- `@Valid`는 `Java Bean Validation API`를 기반으로 동작하며, 이와 함께 사용하는 `Hibernate Validator`는 기본 구현체로 제공된다.
- 객체의 필드 값들이 유효성 조건(예: `@NotNull`, `@Size`, `@Email` 등)에 부합하는지 검증한다.
- 중첩된 객체의 유효성 검증도 수행한다. (`@Valid`를 중첩된 객체에도 붙일 수 있다)
- `Spring MVC`에서는 `@RequestBody`, `@ModelAttribute`, `@PathVariable` 등의 입력 매개변수와 함께 사용하여 클라이언트 요청 데이터를 자동으로 검증할 수 있다.
- 개발자가 필요에 따라 커스텀 검증 로직을 정의할 수도 있다.

### @Valid 사용 위치
- **Controller 매개변수** : HTTP 요청 데이터의 유효성을 검증한다.
- **Service 계층 메서드 매개변수** : 비즈니스 로직에서 유효성 검증을 수행한다.
- **Bean Validation** : 스프링 컨테이너에서 관리되는 빈의 Property 유효성을 검증한다.
- **JPA Entity Validation** : 데이터베이스로 저장하기 전에 엔티티의 유효성을 검증한다.

### @Valid 예시 (Controller에서의 상황)

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/stores")
public class StoreRestController {
    private final StoreCommandService storeCommandService;

    @PostMapping("/")
    public ApiResponse<StoreResponseDTO.AddResultDTO> join(@RequestBody @Valid StoreRequestDTO.AddDto request){

        Store store = storeCommandService.addStore(request);
        return ApiResponse.onSuccess(StoreConverter.toAddResultDTO(store));
    }
}
```

- 해당 코드에서 `@Valid` 어노테이션은 `@RequestBody`와 함께 사용된다.
- HTTP 요청의 본문에서 전달된 데이터를 `StoreRequestDTO.AddDto` 객체로 맵핑후, ***유효성 검증(Bean Validation)*** 을 수행한다.
- 유효성 검증에 실패시, `Spring`이 `Exception`(`MethodArgumentNotValidException`)을 던지고, 컨트롤러는 해당 예외를 처리하도록 구성해야한다.
  - 해당 예외를 처리하지 않으면, `Spring`이 기본적으로 `400 Bad Request` 응답을 클라이언트에 반환한다.
- 유효성 검증에 성공시, `StoreRequestDTO.AddDto` 객체가 `storeCommandService.addStore(request)` 메서드로 전달되어 처리된다.