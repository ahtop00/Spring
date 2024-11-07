# 6주차 키워드 정리🎯

## 지연로딩과 즉시로딩의 차이
### 둘의 차이? 
- `지연 로딩 (Lazy Loading)`: 실제로 해당 엔티티를 사용할 때, 즉 필요한 순간에만 해당 엔티티를 조회하는 방식이다.
  - 장점: 처음에 불필요한 데이터를 안가져온다. ***메모리 사용을 줄이고 성능을 최적화***할 수 있다.
  - 단점: 실제로 데이터를 사용할 때마다 ***추가 쿼리***가 발생 할 수 있다. 즉 N+1 문제같은 이슈가 발생 할 수 있다.
  - `@OneToMany`, `@ManyToMany`의 설정 기본 값이다.
- `즉시 로딩 (Eager Loading)`: 엔티티를 조회할 때 관련된 엔티티도 함께 조회하는 방식이다.
  - 장점: 필요한 데이터를 한 번에 가져오기 때문에, 추가 쿼리 발생하지 않는다. (N+1 문제를 방지한다.)
  - 단점: 불필요한 데이터까지 모두 로드하기 때문에, 메모리 사용량이 증가하고 초기 조회시 성능 이슈가 생길 수 있다.
  - `@ManyToOne`, `@OneToOne`의 설정 기본 값이다.

### 그럼 각각의 로딩을 언제 사용해야 적절할까?
- `지연 로딩`의 사용
  - 연관된 엔티티가 반드시 필요한 경우가 아닐 때 사용한다.
  - 예를 들어, Order와 OrderItem의 관계에서 Order를 조회할 때 OrderItem이 항상 필요한 것은 아닐 수 있다. 이런 경우 지연 로딩으로 설정하여 필요할 때만 OrderItem을 조회하는 것이 좋다.
  - 연관된 데이터가 많은 OneToMany나 ManyToMany 관계는 기본적으로 지연 로딩을 사용하는 것이 좋다.

- `즉시 로딩`의 사용
  - 연관된 엔티티가 항상 필요한 경우에 사용한다.

- 꼭 필요한 경우에만 앞으로 서술할 `Fetch join`이나 `Entity Graph`를 사용하여 쿼리를 최적화하는 것이 좋다.

---

## Fetch Join
### 페치 조인 (Fetch Join)?
- 페치 조인은 JPQL(JPA Query Language)에서 연관된 엔티티나 컬렉션을 즉시 로딩하도록 지시하는 Join 방식이다.
- 일반적인 조인과 다르게, Fetch Join은 조회된 엔티티와 함께 연관된 엔티티 역시 가져오기 때문에 추가적인 쿼리가 발생하지 않는다.
- 페치 조인 사용시 `지연로딩`으로 설정된 엔티티나 컬렉션 또한 ***즉시 로딩*** 되므로, 여러 쿼리가 아닌 단일 쿼리로 조회된다.

### Fetch Join 사용 예시
> N+1의 문제가 발생하는 예시
```java
List<Region> regions = entityManager
    .createQuery("SELECT r FROM Region r", Region.class)
    .getResultList();

// 각 Region에 속한 MemberAddress 목록을 조회
for (Region region : regions) {
    System.out.println(region.getName() + " has " + region.getMemberAddresses().size() + " members. ");
}
```
- 5주차 Keyword에 있는 N+1문제이다. 지역이 10개라면 조회커리 1번이 11번의 쿼리가 실행된다.

>`Fetch Join`을 통한 문제 해결
```java
// QueryDSL 설정
QRegion region = QRegion.region;
QMemberAddress memberAddress = QMemberAddress.memberAddress;

// QueryDSL로 페치 조인 설정
List<Region> regions = queryFactory
        .selectFrom(region)
        .leftJoin(region.memberAddresses, memberAddress).fetchJoin()
        .fetch();
```
- 해당 코드는 `leftJoin().fetchJoin()`을 사용하여 지경과 멤버주소를 페치 조인으로 조회한다.
- 즉, ***단일 쿼리 `Region`과 그에 속한 모든 `MemberAddress`를 한번에 가져와 문제를 해결할 수 있다.***

### Fetch Join 주의사항
- 페이징 제한: 페치 조인을 사용하여 컬렉션(`OneToMany` 또는 `ManyToMany`)을 조회할 때는 페이징을 사용할 수 없다. 페이징이 필요한 경우, 별도의 쿼리를 통해 데이터를 분리해서 가져오거나 `Batch Size` 설정을 통해 최적화해야 한다.
  - `Batch Size`?: 연관된 엔티티를 한꺼번에 묶어서 조회하는 기능.
- 데이터 중복: 페치 조인을 사용하여 `다대일`이나 `일대일` 관계를 조회할 때는 문제가 없지만, `일대다` 관계에서 페치 조인을 사용할 경우 데이터 중복이 발생할 수 있다. SQL 조인 방식 때문에 `Region`이 여러 `MemberAddress`를 가지면, `Region` 데이터가 중복으로 조회될 수 있다.
- 너무 많은 데이터를 한 번에 로드하는 것은 유의사항: 페치 조인을 사용하여 너무 많은 데이터를 한 번에 로드하면 오히려 성능 문제가 발생할 수 있다. 필요한 경우에만 적절히 사용해야 하며, 필요한 연관 엔티티만 선택적으로 로드하는 것이 중요하다.

---

## @EntityGraph
### @EntityGraph?
- `@EntityGraph`는 특정 엔티티와 연관된 엔티티를 효율적으로 조회하기 위해 사용하는 어노테이션이다.
- `@EntityGraph` 사용 시에, `지연 로딩`이 설정된 연관 엔티티를 특정 조회 메서드에서 `즉시 로딩`으로 가져올 수 있다.
- 즉 `@EntityGraph`또한 Fetch Join처럼 N+1문제를 해결하는데 도움을 준다.


### @EntityGraph의 특징
- **N+1 문제 해결**: `@EntityGraph`는 연관된 엔티티를 함께 조회하므로, 지연 로딩으로 인해 발생할 수 있는 N+1 문제를 방지할 수 있다.
- **부분적인 즉시 로딩 설정 가능**: 특정 쿼리에서만 선택적으로 즉시 로딩을 적용하고 싶을 때 유용하다. 글로벌 설정을 변경하지 않고 필요한 곳에서만 즉시 로딩을 적용할 수 있다.
- **JPQL이나 QueryDSL 없이 사용 가능**: JPA의 기본 메서드나 Spring Data JPA의 메서드와 함께 사용할 수 있어, JPQL이나 QueryDSL을 사용하지 않고도 연관 엔티티를 함께 로딩할 수 있다.


### @EntityGraph 예시
> 역시 Region을 조회시 MemberAddress도 함께 가져올 수 있는 예시

```java
@Entity
@NamedEntityGraph(
    name = "Region.withMemberAddresses",
    attributeNodes = @NamedAttributeNode("memberAddresses")
)
public class Region extends BaseEntity {
    // 생략
}
```

### @EntityGraph의 동작 방식
- `@EntityGraph`는 `FETCH JOIN`과 비슷하게 작동한다. 하지만 JPQL 쿼리를 작성하지 않아도 연관 엔티티를 즉시 로딩할 수 있어 코드가 더 간결해진다.

### @EntityGraph의 장단점
- 장점
  - 선택적 즉시 로딩: 특정 메서드에만 즉시 로딩을 적용할 수 있어 전체 엔티티 설정을 변경할 필요가 없다.
  - 간편한 설정: JPQL이나 QueryDSL을 사용하지 않고, 연관 엔티티를 한 번에 로딩할 수 있다.
  - N+1 문제 해결: 지연 로딩으로 인해 발생할 수 있는 N+1 문제를 방지할 수 있다.
- 단점
  - 복잡한 엔티티 그래프 설정이 어려움: `@EntityGraph`는 복잡한 조건을 설정하기 어려우므로, 특정 조건에 따라 동적으로 페치 조인을 설정해야 하는 경우에는 `JPQL` 또는 `QueryDSL`이 필요할 수 있다.
  - 페이징과 함께 사용할 때 주의 필요: `@OneToMany` 관계에서 `@EntityGraph`를 사용할 때는 페이징이 정상적으로 동작하지 않을 수 있다. 이 경우 `Batch Size` 설정이 더 적합할 수 있다.

### @EntityGraph를 언제 사용할까?
- 특정 리포지토리 메서드에서만 연관 엔티티를 즉시 로딩해야 할 때: 예를 들어, `findAll` 메서드에서만 연관된 엔티티를 페치 조인으로 가져와야 할 때 유용하다.
  - 간단한 N+1 문제 해결: `JPQL 페치 조인`이나 `QueryDSL`을 사용하지 않고도 간단하게 N+1 문제를 해결할 수 있다.
- 복잡한 조건이 없는 경우: 복잡한 조건이 없고 단순히 연관된 엔티티를 즉시 로딩할 때 사용하면 좋다.

---

## JPQL
### JPQL?
- `Java Persistence Query Language`의 줄임말이다. `JPA`에서 사용하는 객체 지향 쿼리 언어이다.
- ***SQL과 유사하지만, JPQL은 데이터베이스 테이블이 아닌 엔티티 객체를 대상으로 쿼리를 작성한다.***

### JPQL의 특징
- 객체 지향 쿼리 언어: JPQL은 SQL과 유사한 구문을 사용하지만, SQL과 달리 테이블이나 컬럼이 아닌 엔티티와 그 속성을 대상으로 한다.
- 데이터베이스 독립적: JPQL은 데이터베이스에 독립적이므로, 특정 데이터베이스의 SQL 문법에 종속되지 않고 다양한 DBMS에서 동일하게 사용할 수 있다.
- JPA가 SQL로 변환: JPQL 쿼리는 실행될 때 JPA에 의해 데이터베이스에 맞는 SQL로 변환되어 실행된다.

### JPQL의 기본 문법
- JPQL의 기본 문법은 SQL과 유사하지만, 테이블과 컬럼 이름 대신 `엔티티와 엔티티의 필드`를 사용한다.
- `SELECT`: 데이터를 조회할 때 사용한다.
- `FROM`: 조회할 엔티티를 지정한다.
- `WHERE`: 조건을 지정하여 데이터를 필터링한다.
- `JOIN`: 연관된 엔티티를 조인하여 가져올 때 사용한다.
- `ORDER BY`: 데이터를 정렬할 때 사용한다.
- `GROUP BY / HAVING`: 그룹화할 때 사용하며, SQL과 동일한 역할을 한다.

### JPQL 주의사항
- 엔티티에 의존적: JPQL은 엔티티 객체에 의존하기 때문에, 엔티티 필드나 관계가 변경되면 JPQL 쿼리도 함께 수정해야 한다.
- 동적 쿼리의 어려움: JPQL은 정적인 쿼리 작성에 적합하지만, 조건에 따라 동적으로 쿼리를 변경해야 하는 경우에는 QueryDSL을 사용하는 것이 더 유리할 수 있다.
- 네임드 쿼리 사용 권장: 자주 사용되는 JPQL 쿼리는 `@NamedQuery`로 미리 정의해 두고 사용하는 것이 성능과 관리 측면에서 유리하다.
---

## QueryDSL
### QueryDSL?
- `QueryDSL`은 자바에서 타입 안전성을 보장하면서 동적 쿼리를 객체 지향적으로 작성할 수 있도록 도와주는 프레임워크이다.
- 주로 JPA와 함께 사용되어 ***동적 쿼리 작성 및 타입 안정성***을 보장하는 데 유용하다.
- `JPA`만으로는 조건에 따라 동적으로 쿼리를 생성하는 것이 어렵기 때문에, 복잡한 동적 쿼리가 필요할 때 `QueryDSL`을 많이 사용한다.

### QueryDSL 특징
- 타입 안전성: 쿼리를 작성할 때 컴파일 시점에 필드의 타입을 체크할 수 있어 런타임 에러를 줄일 수 있다.
- 동적 쿼리 작성: 조건에 따라 동적으로 쿼리를 작성하기가 매우 편리하다. 메서드 체이닝을 통해 복잡한 조건을 유연하게 구성할 수 있다.
  - `메서드 체이닝`?: 객체의 메서드 호출이 연속적으로 이어지도록 작성하는 프로그래밍 기법이다. 메서드 체이닝을 사용하면 하나의 문장에서 여러 개의 메서드를 호출할 수 있어 코드를 더 간결하고 읽기 쉽게 만든다.
- 객체 지향적 문법: SQL이나 JPQL과는 달리, 메서드 체이닝을 활용한 객체 지향적 문법을 사용한다. 코드가 더 깔끔하고 가독성이 좋다.

### QueryDSL의 장단점
- 장점
  - 타입 안전성: 컴파일 시점에 쿼리에서 사용하는 필드와 조건을 체크할 수 있어 오류를 줄일 수 있다.
  - 동적 쿼리 작성 용이: 조건에 따라 쿼리를 동적으로 구성할 수 있으며, `BooleanBuilder`를 통해 복잡한 조건도 쉽게 구현할 수 있다.
    - `BooleanBuilder`?: QueryDSL에서 동적 쿼리를 작성할 때 조건을 유연하게 추가하거나 제거할 수 있도록 도와주는 클래스다. 특정 조건이 있을 때만 해당 조건을 쿼리에 추가하는 식으로, 조건을 동적으로 구성하는 데 유용하다.
  - 코드 가독성 향상: 메서드 체이닝과 객체 지향적인 문법을 사용하여 코드가 깔끔하고 가독성이 높아진다.

- 단점
  - 설정 및 초기 학습 코스트: `QueryDSL`을 사용하려면 프로젝트에 별도의 설정이 필요하며, `Q 클래스`를 생성해야 한다.
  - `JPQL` 대비 코드가 길어질 수 있다: 간단한 쿼리의 경우, `QueryDSL`이 오히려 `JPQL`보다 코드가 길어질 수 있다.

### QueryDSL의 예시
- https://github.com/ahtop00/Spring_Practice_Mission/tree/master/src/main/java/javalab/umc7th_mission/repository
- 해당 리포지토리에서 QueryDSL을 통해 구현하였습니다.

---

## N+1문제의 해결책
> 5주차 Keyword의 연장선입니다.

### 1. 페치 조인(Fetch Join) 사용
- 페치 조인(Fetch Join)을 사용하면, 연관된 엔티티를 단일 쿼리로 함께 조회할 수 있다. 
- `JPA`의 `JPQL`에서 `JOIN FETCH` 구문을 사용하면 연관된 엔티티를 즉시 로딩할 수 있다.
```java
// JPQL을 사용한 페치 조인
List<Region> regions = entityManager.createQuery(
    "SELECT r FROM Region r JOIN FETCH r.memberAddresses", Region.class)
    .getResultList();
```
- 해당 코드에서 `JOIN FETCH`를 사용하여, `Region`과 `MemberAdress`를 한번에 조회하여 추가 쿼리를 방지한다.

### 2. 엔티티 그래프(Entity Graph) 사용
- JPA의 `@EntityGraph` 어노테이션을 사용하여 필요한 연관 관계를 미리 정의하고, 특정 쿼리에서 이를 적용할 수 있다.
- 이를 통해 원하는 쿼리에서만 연관 엔티티를 함께 조회할 수 있다.
```java
@Entity
@NamedEntityGraph(name = "Region.withMemberAddresses",
                 attributeNodes = @NamedAttributeNode("memberAddresses"))
public class Region {
    //생략
}

// Repository에서 @EntityGraph 사용
public interface RegionRepository extends JpaRepository<Region, Long> {

    @EntityGraph(value = "Region.withMemberAddresses")
    List<Region> findAll();
}
```
- 해당 코드에서 `@NamedEntityGraph`를 사용, `Region` 엔티티와 연관된 `MemberAddress`엔티티를 함께 조회하는 엔티티 그래프를 정의한다.

### 3. Batch Size 설정하기
- 한 번에 로딩할 엔티티 개수를 설정할 수 있다.
- 이를 통해서 한 번에 여러 엔티티를 가져올 수 있기 때문에, 호출 횟수를 줄일 수 있다.
```java
@Entity
public class Region {

    @OneToMany(mappedBy = "region")
    @BatchSize(size = 10)
    private List<MemberAddress> memberAddresses;
}
```
- 해당 코드에서 `BatchSize = 10`을 통해 최대 10개의 `MemberAddress`를 한번에 가져온다.
- 따라서 `Region`이 10개라면 1 + 1 = 2개의 쿼리로 해결된다.

### 4. QueryDSL의 페치 조인(Fetch Join) 사용
- `QueryDSL`을 사용하는 경우, 페치 조인을 사용해 단일 쿼리로 연관 엔티티를 함께 조회할 수 있다.
```java
QRegion region = QRegion.region;
QMemberAddress memberAddress = QMemberAddress.memberAddress;

List<Region> regions = queryFactory
    .selectFrom(region)
    .leftJoin(region.memberAddresses, memberAddress).fetchJoin()
    .fetch();

```
- `QueryDSL`에서 `fetchJoin()` 메서드를 사용하면, 페치 조인을 통해 연관된 엔티티를 한 번에 가져올 수 있다.