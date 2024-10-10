# 3주차 키워드 정리🎯

## REST API

### REST API 구성
1) 자원(RESOURCE) - URI
2) 행위(Verb) - HTTP METHOD 
3) 표현(Representations)

### REST API 특성
1) Uniform (유니폼 인터페이스)
   Uniform Interface는 URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일을 말한다.

2) Stateless (무상태성)
   REST는 무상태성 성격을 갖는다. 다시 말해 작업을 위한 상태정보를 따로 저장하고 관리하지 않는다.  
세션 정보나 쿠키정보를 별도로 저장하고 관리하지 않기 때문에 API 서버는 들어오는 요청만을 단순히 처리하면 된다.  
때문에 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해진다.

3) Cacheable (캐시 가능)
   REST의 가장 큰 특징 중 하나는 HTTP라는 기존 웹표준을 그대로 사용하기 때문에, 웹에서 사용하는 기존 인프라를 그대로 활용이 가능하다.  
따라서 HTTP가 가진 캐싱 기능이 적용 가능하다. HTTP 프로토콜 표준에서 사용하는 Last-Modified태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.

4) Self-descriptiveness (자체 표현 구조)
   REST의 또 다른 큰 특징 중 하나는 REST API 메시지만 보고도 이를 쉽게 이해 할 수 있는 자체 표현 구조로 되어 있다.

5) Client - Server 구조
   REST 서버는 API 제공, 클라이언트는 사용자 인증이나 컨텍스트(세션, 로그인 정보)등을 직접 관리하는 구조로 각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어들게 된다.

6) 계층형 구조
   REST 서버는 다중 계층으로 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고 PROXY, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

### ***REST API 디자인***

1) ***URI는 정보의 자원을 표현해야한다.***
2) ***자원에 대한 행위는 HTTP METHOD를 이용하여 표현한다.***

### API 설계 시, 고려사항

1. **path variable**
> 단 하나의 특정 대상을 지목할 때
>> GET /users/articles/{article-id}

2. **query string**
> 검색 조회 때, 주로 GET 요청 시 사용한다. 예를 들어 게시글을 1개 또한 그 이상 조회시에 사용한다.
>>GET /users/articles?name=umc&owner=ddol

❗ 주의점: 
**쿼리 스트링은 API 엔드 포인트에 포함이 되지 않기에**
**엔드 포인트 자체**는 **GET /users/articles** 이렇게 설계를 해야 한다.

3. **request body**
> POST 방식의 경우 사용된다. 주로 JSON 또는 form-data 형태로 보내게 된다.  
> Response Body와 차이점이 있다. ***Request***는 `클라이언트 -> 서버`의 요청이다.  
> ***Response***는 `서버 -> 클라이이언트`의 응답이다.
```json
{
  "name": "공윤서",
  "phoneNumber": 010-1234-5678,
  "nickName": "mori"
}
```

4. **request header**
> 서버와 전송시 meta-data (전송에 관련된 기타 정보들)이 담기는 부분이다.  
> 대표적으로 로그인이 되었다는 것을 알려주기 위한 토큰을 헤더에 담는다!

