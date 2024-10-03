# 2주차 미션: 쿼리 작성
![week2_erd(ver2).png](image%2Fweek2_erd%28ver2%29.png)
- 1주차에서 약간의 보완을 하였습니다. 

---

## 1번 예시
![week2_m1.png](image%2Fweek2_m1.png)

- 내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함)
- 필요한 내용: 가게이름, 해당 미션의 포인트, 해당 미션의 성공 여부, 해당 미션의 내용
- 기본 테이블 mission에서 각종 테이블을 조인하여 필요한 값을 얻어낸다.

```mysql
SELECT m.reward, s.name, m.content, mm.mission_sucess, mm.updated_at
FROM mission as m
    JOIN member_mission as mm ON m.mission_id = mm.mission_id
    JOIN member as mem ON mm.user_id = mem.user_id
    JOIN stores as s ON s.stores_id = m.stores_id
WHERE mm.mission_sucess = "0" OR mm.mission_sucess = "1" // '-1' = 시작전 '0' = 진행중 '1' = 완료
LIMIT 20 OFFSET (n-1) * 20; //실제로는 n-1 대신 실제 값이 쿼리문에 들어가야함
```

---

## 2번 예시
![week2_m2.png](image%2Fweek2_m2.png)
- 리뷰 작성하는 쿼리, 사진의 경우는 일단 배제
- 필요한 내용: 해당 가게의 아이디, 리뷰의 글쓴이, 리뷰 평점, 리뷰 내용

```mysql
INSERT INTO reviews VALUES ('{store_id}', '{user_id}', '{review_name}', '{review_content}', '{rating}');
```

---

## 3번 예시
![week2_m3.png](image%2Fweek2_m3.png)
- 홈 화면 쿼리 (현재 선택 된 지역에서 도전이 가능한 미션 목록, 페이징 포함)
- 필요한 내용: 지역명, 해당 미션 인덱스, 미션 기한, 해당지역의 수행가능한 미션
- 
```mysql
SELECT r.name, s.name, s.food_category, 
       m.content, m.point, m.mission_deadline
FROM mission as m
    JOIN stores as s ON s.store_id = m.store_id
    JOIN region as r ON r.region_id = m.region_id
    JOIN member_mission as mm ON mm.mission_id = m.mission_id
WHERE r.name = '안암동'
    AND mm.member_id = '{current user_id}'
    AND mm.status = '-1'
    AND m.mission_deadline > NOW();
ORDER BY  m.mission_deadline
LIMIT 10 OFFSET (n-1) * 10;
```

### +) 해당 지역의 미션을 몇 번하였는지 알려주는 쿼리문
```mysql
SELECT count(*)
from member_mission as mm
JOIN mission as m ON m.mission_id = mm.mission_id
JOIN region as r ON r.region_id = m.region_id
JOIN member as mem ON mem.user_id = mm.user_id
WHERE mm.member_id = '{current user_id}'
    AND mm.status = '1'
    AND r.region_name = '안암동'
```

---

## 4번 예시
![week2_m4.png](image%2Fweek2_m4.png)
- 필요한 내용: 닉네임, 이메일, 휴대폰 번호, 내 현재 포인트

```mysql
SELECT nickname, email, phone_number, point
FROM member
WHERE user_id = '{current user_id}'
```