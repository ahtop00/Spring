# Chapter 1. JAVA 추가 미션  
### ‼️ 추가과제) 해당 키워드에 대해서 공부한 후 간단정리후 제출 & 면접질문 최소 한개 만들어서 제출 후 스터디 시간에 스터디원들과 질문하기!

## 1. Java의 접근제어자  
### 1) 자바 접근제어자의 존재 이유 : 객체 지향의 4대 특성 中 캡슐화
자바에서 접근 제어자(Access Modifiers)는 클래스, 변수, 메소드 및 생성자에 대한 접근 수준을 설정하는 데 사용됩니다. 이를 통해 객체 지향 프로그래밍의 캡슐화 원칙을 구현하며, 클래스 내부의 데이터를 보호하고 외부에서의 무분별한 접근을 제한합니다.

왜냐하면 캡슐화는 객체의 상태를 외부에서 직접 접근하는 것을 막고, 객체가 제공하는 메소드를 통해서만 상태를 변경할 수 있도록 함으로써 프로그램의 안정성을 높이기 때문입니다. 접근 제어자는 이러한 캡슐화를 실현하는 핵심 요소입니다.
### 2) 클래스 멤버의 접근 제어자 종류
1. **Private**
- *private* 접근 제어자는 가장 제한적인 접근 수준을 제공한다.
  - *private*으로 선언된 멤버는 해당 클래스 내에서만 접근 가능하고, **외부 클래스** or **상속받은 클래스**에서는 사용이 불가하다.
  - *private*로 선언된 멤버는 getter-setter를 이용하여 접근하는 방식이 일반적이다.
    - 이를 통해 해당 private 변수의 접근을 제어하고, 데이터의 무단 변경을 억제할 수 있다.
```java
public class Person {
    private String name;
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
}
```

---

2. **Default**
- *default* 접근 제어자는 같은 패키지  같은 패키지 내의 클래스들만 접근할 수 있도록 한다. 다른 패키지의 클래스에서는 접근할 수 없다.
  - 같은 패키지 내의 클래스들 사이에서만 데이터 공유를 허용하고, 패키지 외부로는 데이터를 숨기기 위해 존재하기 때문이다. (패키지 단위의 캡슐화를 제공)
- 패키지 내부에서의 데이터 공유에 유용하게 작용한다.
  - 패키지 내부의 클래스가 서로 밀접할 확률이 높고, 이를 공유해야 할 확률이 높기 때문이다. **허나 무분별한 default 접근 제어자의 사용은 지양해야 한다.**
```java
//Korea.java
package world;

public class Korea {
    String fullname = "Repulic of Korea"; 
     //앞에 접근제어자가 없기 때문에 default 접근제어자로 인식한다.
}
```

```java
//PrintWorld.java
package world; //Korea.java와 같은 패키지

public class PrintWorld {
    String str = "Hello, ";
    
    public static void main(String[] args) {
        Korea korea = new Korea();
        System.out.println(str + korea.fullname + "!");
        //Korea 클래스의 fullname 변수를 사용할 수 있다.
        //Hello, Republic of Korea!
    }
}
```
- 이처럼 같은 패키지로 묶여있으면 default 접근 제어자로 설정된 변수에 접근 가능하다.

---

3. **Protect**
- *protect* 접근 제어자는 같은 패키지 내의 클래스 또는 다른 패키지의 상속받은 클래스에서 접근할 수 있도록 한다. 
- 이는 상속 관계에 있는 클래스들 사이의 데이터 공유를 가능하게 한다.
- *default* 접근 제어자와 공통된 부분은 상속관계가 아닌 객체멤버를 호출할 때, 같은 패키지 일때만 가능하다.
- 그러나 차이점은 자식 클래스에서 부모 멤버를 호출할 때 발생한다. 자식 클래스일때 부모 멤버를 호출하면 protect의 경우 문제없이 작동한다.
```java
///Parents.java
///부모 클래스의 선언
package Package1;

public class Parents {
   void defaultMethod {
       System.out.println("default!");
  } 
  
  protected void protectedMethod() {
       System.out.println("protected!");
  }
}
```
```java
///Child.java
package Package2;
import Package1.Parents;

public class Child() extends Parents {
    defaultMethod(); //에러 발생! 패키지가 다르기 때문이다.
    protectedMethod(); //문제 없이 정상 작동한다.
}

```

---

4. **Public**
- 접근 제어자가 public으로 설정되었다면 public 접근 제어자가 붙은 변수나 메서드는 어떤 클래스에서도 접근이 가능하다.
```java
//Korea.java
package world;

public class Korea {
    public String fullname = "Repulic of Korea"; 
     //앞에 접근제어자가 없기 때문에 default 접근제어자로 인식한다.
}
```

```java
//PrintWorld.java
package house; //Korea.java와 다른 패키지임에도 불가하고 public은 문제가 없다. 
import world.Korea;

public class PrintWorld {
    String str = "Hello, ";
    
    public static void main(String[] args) {
        Korea korea = new Korea();
        System.out.println(str + korea.fullname + "!");
        //public이기 때문에 import를 한다면 접근이 어디서든 가능하다.
        //Korea 클래스의 fullname 변수를 사용할 수 있다.
        //Hello, Republic of Korea!
    }
}
```

---


## 2. 클래스와 인터페이스
### 1) 클래스 (*Class*)
- 클래스는 여러 가지 멤버 변수와 메서드를 포함하며, 이들을 적절히 구성하여 객체의 동작을 정의한다.
  - 멤버 변수 : 클래스 내에서 사용되는 변수, 클래스의 속성을 나타낸다. 인스턴스 변수라고도 불린다.
  - 메세드 : 클래스 내에서 사용되는 함수, 클래스의 동작을 나타낸다. 인스턴스 메서드라고도 불린다.
  - 생성자 : 클래스로부터 객체를 생성할 때 호출되는 특별한 메서드. 객체의 초기화를 수행한다.

```java
public class Dog {
    //인스턴스 변수
    String name;
    int age;

    //생성자
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    //메서드
    public void bow() {
        System.out.println("멍멍");
    }
}
```
### 2) 인터페이스 (*Interface*)
- 자바에서는 다중 상속을 지원하지 않지만, 인터페이스를 통해 이점을 제공한다. 
- 인터페이스는 클래스 간의 중간 매개 역할을 하며, 추상 메소드와 상수만 포함할 수 있다. 
- 반면, 추상 클래스는 추상 메소드 외에도 생성자, 필드, 일반 메소드를 가질 수 있다.
  - 추상 클래스?: 하나 이상의 추상 메소드를 포함할 수 있으며, 일반 메소드, 필드, 생성자도 가질 수 있는 클래스이다.
- 이로 인해 자바는 다중 상속의 이점을 살리면서도 메소드 출처의 모호성을 피할 수 있다.

```java
interface Animal {public abstract void cry();}
///인터페이스의 구현, 인터페이스 안에 있는 메소드를 반드시 구현해야 한다.

class Dog implements Animal {
    public void cry() {
        System.out.println("멍멍!");
    }
}

class Chick implements Animal {
    public void cry() {
        System.out.println("삐약삐약!");
    }
}

public class Example {
    public static void main(String[] args) {
        Chick chick = new Chick();
        Dog dog = new Dog();
        
        chick.cry();
        dog.cry();
        //삐약삐약!
        //멍멍!
    }
}
```

---

## 3. 상속

### 1) 상속 (*inheritance*)?
- 상속이란 기존의 클래스에 기능을 추가하거나 재정의하여 새로운 클래스를 정의하는 것을 의미한다.
- 역시 객체 지향 4대 특성 중에 하나를 차지하고 있다.
- 상속을 이용하면 기존에 정의되어 있는 클래스의 모든 필드와 메소드를 물려받아, 새로운 클래스를 생성할 수 있다.
- 이때 기존에 정의되어 있던 클래스를 부모 클래스(parent class) 또는 상위 클래스(super class), 기초 클래스(base class)라고도 한다.
- 그리고 상속을 통해 새롭게 작성되는 클래스를 자식 클래스(child class) 또는 하위 클래스(sub class), 파생 클래스(derived class)라고도 한다.

### 2) 상속의 장점
- 기존에 작성된 클래스를 재활용할 수 있다.
- 자식 클래스 설계 시 중복되는 멤버를 미리 부모 클래스에 작성해 놓으면, 자식 클래스에서는 해당 멤버를 작성하지 않아도 된다.
- 클래스 간의 계층적 관계를 구성함으로써 다형성의 문법적 토대를 마련한다.

### 3) 자식 클래스 (*child class*)
- 자식 클래스(child class)란 부모 클래스의 모든 특성을 물려받아 새롭게 작성된 클래스를 의미한다.
- class 자식클래스이름 extend 부모클래스이름 { ... }

```java
class Parent {
    private int a = 10;
    public int b = 20;
}

class Child extends Parent {
    public int c = 30;
    void display() {
        System.out.println(a); //실행 오류: private이기 때문이다.
        System.out.println(b); //정상 출력
        System.out.println(c); //정상 출력
    }
}

```
---

## 4. 면접 질문
### 상속과 인터페이스의 차이가 무엇일까요?
1) 상속 : 부모-자식 관계를 나타내며, 부모 클래스의 코드를 재사용하고 확장하는 방법
2) 인터페이스 : 클래스가 따라야 할 규약을 정의하여 다형성을 구현하는 방법

**상속과 인터페이스의 차이**
- 상속의 경우 하위 클래스에서 부모 클래스의 메서드를 전부 오버라이드할 필요는 없지만 인터페이스의 경우 반드시 구현해야 한다.
- 상속의 경우 단일 상속만이 가능하지만, 인터페이스는 다중 상속이 가능하다