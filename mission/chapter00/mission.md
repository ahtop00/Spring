0주차 미션 제출합니다  
1. 너더너리 홈페이지에 접속하는 과정
   1) 브라우저를 키고 너더너리 홈페이지를 입력합니다.
   2) 브라우저에 해당 URL의 IP 확인한다.
      (보통 캐시에서 DNS 기록 확인 또는 ISP의 DNS서버에서 해당 URL을 호스팅하는 서버 주소를 찾음)
   3) 그 후 브라우저가 해당 서버와 TCP 연결을 시작한다.
      (3 way-handshake를 통해 서로 서버가 켜졌음을 인식하고, 데이터 전달 및 순서를 보장한다.)
   4) 해당 IP주소를 패킷에 담고 network core로 보낸다. 그후 패킷을 받은 라우터는
     더 URL에 올바른 라우터로 옮겨지고 옮겨지다 도착지에 도착한다.
   5) 브라우저가 웹 서버에 HTTP 요청을 보내고 서버는 요청을 처리함 그후 HTTP 응답을 보낸다.
   6) 브라우저가 해당 HTML 컨텐츠를 보여준다.
2. 깃허브 clone 받아서 실행하고 나온 페이지 스크린 샷 찍기 v