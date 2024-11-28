# 9주차 키워드 정리🎯

## Spring Data JPA의 Paging

### Page? 그게 왜 필요한데?🤔
> E-mart나 올리브영 같은 쇼핑몰에서는 해당 검색어에 대한 상품을 리스트로 보여준다.   
> 하지만 리스트를 모두 보여주기엔 ***서버에 대한 과부하가 당연히 걸리게 된다.***  
> 따라서 리스트를 나누어 페이지의 형태로 해당 상품들을 보여준다.

![Paging의 예시](img1.png)
- 올리브영의 실제 상품 페이지이다. 각종 분류 기준이 있고, 몇 개씩 상품을 노출 시킬건지 설정 또한 가능하다.
- 이는 페이징 기법을 통해 상품을 분류 후, 상품 리스트를 설정에 맞개 개수를 정해 잘라서 ***페이지***로 보여준다.

### Spring Data JPA의 Paging - Page
- `Spring Data JPA`의 `Paging` 기능은 대량의 데이터를 효율적으로 처리하고 표시하기 위해 제공한다.
- 해당 기능은 `Page` 및 `Pageable` 인터페이스를 통해 구현되고, 데이터를 부분적으로 쿼리하여 가져오는 데 사용된다.

#### Page 객체
- `Page<T>` : 페이징된 결과를 담는 객체로, 조회된 데이터뿐만 아니라 추가적인 메타정보도 포함한다.
  - 주요 메타 정보:
    - `getTotalElements()`: 전체 데이터 개수
    - `getTotalPages()`: 전체 페이지 수
    - `getNumber()`: 현재 페이지 번호 (0부터 시작)
    - `getSize()`: 한 페이지의 크기
    - `hasNext()` / `hasPrevious()`: 다음/이전 페이지 존재 여부
  - 데이터 접근 관련 메소드:
    - `getContent()`: 실제 데이터 리스트 반환
    - `isEmpty()`: 페이지가 비어 있는지 여부

- `Pageable` : 페이징 정보를 담는 인터페이스로, 다음과 같은 정보가 포함된다.
  - `page` : 페이지 번호
  - `size` : 페이지의 크기
  - `sort` : 정렬 조건

- `PageRequest` : `Pageable` 인터페이스를 구현하고, 페이징 처리와 정렬에 관한 정보를 캡슐화한다.
  - `Pageable`과 동일한 정보가 포함된다.
  - 정적 메서드 `of()`를 통해 생성 가능하다.
  - Pageable은 페이징 기능을 위한 추상적인 인터페이스이고, `PageRequest`는 이를 구현한 객체이다.
  - 실질적으로 페이징을 설정할 때는 `PageRequest`를 만들어 사용하며, 이를 통해 `Pageable` 인터페이스의 역할을 수행한다.

#### Page 사용 예시

```java
    @Override
    public MemberMissionResponseDTO.ChallengingMissionPreviewListDTO getChallengingMemberMissions(Long memberId, Integer page) {
        //PageRequest를 통해 객체를 생성한다. 인자로 각각, 페이지 번호, 페이지의 크기, 그리고 *최신순*으로 정렬한다.
        PageRequest pageRequest = PageRequest.of(page, 10, Sort.by("createdAt").descending());
        
        //Page<MemberMission> 객체로 리포지토리에서 보낸 정보들을 받는다.
        Page<MemberMission> memberMissions = memberMissionRepository.findChallengingMissionByMemberId(memberId, pageRequest);

        //컨버터를 통해 Page로 받은 정보를 변환하여 컨트롤러에게 보낸다.
        return MemberMissionConverter.challengingMissionPreviewListDTO(memberMissions);
    }
```

### Spring Data JPA의 Paging - Slice
> 가끔 우리는 SNS에서 무한으로 스크롤을 밑으로 땡기는 경우가 있다. 이때 과연 모든 정보가 필요할까?

![Slice의 예시](img2.png)
- 구글에 반달곰 이미지를 검색해보았다. 해당 이미지들의 전체 데이터 개수와 총 페이지 수가 나오지 않는다.

#### Slice?
- `Slice`는 `Page`와 비슷하지만 약간 다른 방식으로 페이징 데이터를 처리한다.
- `Slice`는 페이지의 끝 여부와 다음 데이터의 존재 여부만 확인하며, 전체 데이터 개수나 페이지 수를 알 필요가 없을 때 사용한다.
- 이를 통해 ***불필요한 쿼리 오버헤드를 줄이고 더 효율적인 페이징이 가능하다.***

#### Slice의 주요 메서드
- `getContent()`: 현재 페이지의 데이터 리스트를 반환한다.
- `getNumber()`: 현재 페이지 번호를 반환한다.
- `getSize()`: 한 페이지의 크기를 반환한다.
- `hasNext()`: 다음 페이지가 있는지 여부를 확인한다.
- `isLast()`: 현재 페이지가 마지막 페이지인지 여부를 확인한다.

#### 왜 Slice도 사용해야 할까?🤔
- 전체 데이터 개수를 알아야 하는 `Page`와 달리, `Slice`는 ***다음 페이지가 있는지 여부만 확인***한다.
  - 전체 데이터 개수를 조회하지 않아 성능에 이점이 있다.
- 데이터 개수를 계산하는 쿼리가 비싼 비용을 발생시키는 경우 `Slice`는 더 효율적이다.
- 무한 스크롤이나 더 보기 기능에 사용된다.
  - `Slice`는 다음 페이지가 있는지 확인하는 데만 초점을 맞추므로, 페이징된 데이터를 간단히 불러오는 UI에 적합하다.

#### Slice의 사용 예시

```java
PageRequest pageRequest = PageRequest.of(0, 10, Sort.by("createdAt").descending());
//이번엔 Slice<Member> 의 형태로 리포지토리에서 활성화 멤버를 받는다.
Slice<Member> members = memberRepository.findByStatus("ACTIVE", pageRequest);

//활성화 멤버를 출력한다.
for (Member member : members.getContent()) {
    System.out.println(member.getnickName());
}

//모든 조회 필요없이, 다음페이지가 있는지만 확인한다.
if (members.hasNext()) {
    System.out.println("다음 페이지가 있습니다.");
} else {
    System.out.println("마지막 페이지입니다.");
}
```

---

## 객체 그래프 탐색
> Spring에서 객체 그래프 탐색은 DI를 기반으로 한 빈의 생성 및 의존 관계를 분석 및 이해하는 과정이다.  
> 의존성을 관리하고 최적화하는 데 중요한 탐색 기법이다.

### Spring 객체 그래프?
- Spring 애플리케이션에서 관리되는 빈들 간의 관계를 나타낸 구조이다.
- 하나의 빈을 `Node`로 생각하고, 빈 간의 의존성을 `Edge`로 생각하면 그래프 구조가 나온다.

### Spring에서 객체 그래프의 탐색의 사용 이유?
- 의존성 순환 문제 해결
  - 객체 간 의존성이 서로 순환시 애플리케이션이 무한 참조의 늪에 빠질 수 있다...
  - 객체 그래프를 탐색하면 `순환 의존성`을 발견하고 이를 해결할 수 있다.
- 빈 관리 최적화
  - 불필요한 빈이 생성되거나, 의존성이 과도하게 복잡할 때 이를 탐색해 최적화한다.
- 성능 문제 디버깅
  - 초기화 시에 의존성이 과도하거나, 지연 로딩(Lazy Loading)이 필요한 경우 객체 그래프 분석을 통해 문제점을 발견한다.
- 대규모 애플리케이션 제작시의 구조 파악
  - 객체 그래프 탐색은 전체 애플리케이션 구조를 이해하고 설계를 개선하는데 도움을 준다.

### JPA 객체 그래프?
- JPA 객체 그래프는 엔티티와 그 연관된 엔티티 간의 관계를 표현한 구조다.
- 엔티티 간의 연관 관계는 DB에서 외래 키(Foreign Key)를 사용해 연결되며, JPA는 이를 객체 참조로 매핑한다. (주로 1:1, N:M, 1:N...)

```java

public class MemberMission extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id", referencedColumnName = "id")
    private Member member;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "mission_id", referencedColumnName = "id")
    private Mission mission;
    
    //...
}

public class Member extends BaseEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    //...
    
    @OneToMany(mappedBy = "member", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<MemberMission> memberMissionList = new ArrayList<>();
    
    //...
}
```

- 해당 예시에서 `Member`와 `MemberMission`의 관계를 보여주고 있다.
  - `Member`는 `MemberMission`을 `@OneToMany`로 참조
  - `MemberMission`은 `Member`을 `@ManyToOne`으로 참조
- 해당 구조는 객체 그래프의 노드와 엣지를 형성한다.

### JPA의 객체 그래프 탐색?
- 객체 그래프 탐색은 연관된 엔티티를 가져오고 활용하는 작업을 의미한다. 
- JPA에서 객체 그래프 탐색은 다음을 통해 수행됩니다:
  
 1. 연관 관계 필드를 직접 접근:
    - 서로서로 `Member`와 `MemberMission` 객체를 참조.
 2. JPQL로 쿼리 작성:
    - 연관된 엔티티를 함께 가져오는 쿼리를 실행한다.
 3. 객체 그래프를 부분적으로 로드(`FetchType.LAZY/EAGER`):
    - 필요한 엔티티만 가져오거나, 전체를 로드한다.


### JPA 객체 그래프 탐색 예시

```java
public static MemberMissionResponseDTO.CompleteMissionResponseDTO toCompleteMissionResponseDTO(MemberMission memberMission) {
    return MemberMissionResponseDTO.CompleteMissionResponseDTO.builder()
            .memberMissionId(memberMission.getId())
            .missionName(memberMission.getMission().getName())
            .missionStatus(memberMission.getStatus())
            .build();
}
```

- 해당 예시는 MemberMission을 받아 완료되었다는 응답DTO로 바꾸는 컨버터이다. (이번 주차 미션 4)
- `memberMission`에서는 미션 이름이 있지 않다. 하지만 `Mission`이 연관관계로 설정 되어있다.
- `memberMission`에서 `@Getter`를 이용해 `Mission` 객체를 받아오고 해당 객체에서 메서드 체이닝을 이용해 `name`을 가져온다.