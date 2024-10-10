# 3주차 미션: API 설계
![week3_erd(ver2).png](image%2Fweek3_erd%28ver2%29.png)
## 1) 홈 화면 API 설계
![홈 화면](image%2Fweek3_home.png)
1. URL: `/api/home`  
2. METHOD: `GET`
3. Query String: `?id={user-id}`
3. Request Header: `Authorization : Bearer {YOUR-APP-KEY}`, `Content-Type:application/json`
4. ***Response*** Body:

```json
{
  "region-name": "안암동",
  "point": 999999,
  "missions": [
    {
      "id": 1,
      "store-name": "일곱평",
      "category": "중식당",
      "content": "12,000원 이상의 식사를 하세요!",
      "success": 1,
      "mission-deadline": 7,
      "mission-point": 500
    },
    {
      "id": 2,
      "store-name": "백소정",
      "category": "일식당",
      "content": "12,000원 이상의 식사를 하세요!",
      "success": 0,
      "mission-deadline": 7,
      "mission-point": 500
    }
  ]
}
```

---

## 2. 마이페이지 리뷰 작성
![마이페이지 리뷰 작성](image%2Fweek3_reviews.png)

1. URL: `/api/users/reviews`
2. METHOD: `POST`
3. Request Header: `Authorization : Bearer {YOUR-APP-KEY}`, `Content-Type:application/json`

4. ***Request*** Body:
```json
{
  "reviews-id": 1,
  "nickname": "mori",
  "content": "음 너무 맛있어요! 포인트도 얻고 맛있는 맛집도 알게 된거 같아 너무나도 행복한 식사였어요!",
  "rating": 5,
  "images": ["example1.jpg", "example2.jpg"]
}
```

5. ***Response*** Body:
```json
{
  "success" : 0,
  "content" : "성공적으로 리뷰가 작성되었습니다!"
}
```

---

## 3.미션 목록 조회(진행중, 진행완료)
![week3_missions.png](image%2Fweek3_missions.png)
1. URL: `/api/users/missions`
2. METHOD: `GET`
3. Query String `successed: ?success=1, progressing: ?success=0`
4. Request Header: `Authorization : Bearer {YOUR-APP-KEY}`, `Content-Type:application/json`
5. ***Response*** Body:

```json
{
  "missions": [
    {
      "id": 1,
      "store-name": "일곱평",
      "category": "중식당",
      "content": "12,000원 이상의 식사를 하세요!",
      "success": 1,
      "mission-deadline": 7,
      "mission-point": 500
    },
    {
      "id": 2,
      "store-name": "백소정",
      "category": "일식당",
      "content": "12,000원 이상의 식사를 하세요!",
      "success": 0,
      "mission-deadline": 7,
      "mission-point": 500
    },
    {
      "id": 3,
      "store-name": "번 크러쉬",
      "category": "패스트푸드",
      "content": "12,000원 이상의 식사를 하세요!",
      "success": 1,
      "mission-deadline": 0,
      "mission-point": 500
    }
  ]
}
```

---

## 4.미션 성공 누르기
1. URL: `/api/stores/requests/{mission-id}`
2. METHOD: `PATCH`
3. Request Header: `Authorization : Bearer {YOUR-APP-KEY}`, `Content-Type:application/json`
4. ***Request*** Body:
```json
{
  "success": 1
}
```
---

## 5.회원가입
1. URL: `api/users/sign-up`
2. METHOD: `POST`
3. Request Header: `Content-Type:application/json`
4. ***Request*** Body:
```json
{
  "email" : "example123@gmail.com",
  "name" : "홍길동",
  "nickname" : "mori",
  "password" : "*******",
  "gender": "male",
  "birth": "2024-10-01",
  "address": "서울특별시 강남구 신사동 압구정로12길",
  "etc-address:" : "XX아파트 101동 201호",
  "liked-food-category" : ["일식", "한식", "패스트푸드"],
  "contract": [1, 1, 1, 0]
}
```







