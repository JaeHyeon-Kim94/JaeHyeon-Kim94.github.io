---
categories: [HTTP]
title: HTTP Header
---

## 컨텍스트에 따른 헤더 분류

 - Message Body
   - Content-Type
     - 리소스의 미디어 타입
     - ex) text/html, application/json, multipart/form-data
   - Content-Encoding
     - 표현 데이터 압축을 위한 헤더. 전달하는 쪽에선 압축 후 인코딩 헤더 추가, 읽는 쪽에선 헤더로 압축 해제
     - ex) gzip, deflate, identity(압축 X를 의미)
   - Content-Language
     - 사용자 선호 언어 구분 위한 헤더.
     - ex) ko, en ...
   - Content-Length
     - 데이터 길이



 - 협상 (only request)
   - Accept : 서버에게 돌려줄 데이터 타입 명시.
   - Accept-Charset : 클라이언트가 이해할 수 있는 문자 인코딩
   - Acept-Encoding : 돌려줄 리소스에 사용할 압축 알고리즘에 대해 서버에게 알려줌
   - Accept-Language : 서버가 돌려줄 언어를 알려줌.
     - ex) Accept-Language: ko-KR, ko;q=0.9,en-US;q=0.8
       - q는 우선순의 표현. 0부터 1까지의 range. 높을 수록 우선순위 높음.
 - 전송 방식
   - 단순 전송 : Content-Length
   - 압축 전송 : Content-Encoding
   - 분할 전송 : Transfer-Encoding (Content-Length 미포함)
   - 범위 전송 : Request시 Range: bytes=100-200, response시 Content-Range: bytes 100-200 / 1000

 - 요청 컨텍스트
   - Host : 도메인명 혹은 서버에서 리스닝중인 TCP:PORT. 하나의 IP에 여러 도메인이 있을 경우 특정 도메인 식별
   - Referer : 이전 웹 페이지 주소. 유입 경로 분석에 사용
   - User-Agent : 클라이언트 애플리케이션 정보. 브라우저 등.

 - 응답 컨텍스트
   - Allow : 허용 가능한 HTTP 메서드. 405(Method Not Allowed)에서 응답에 포함해야 한다.
   - Server : 오리진 서버의 소프트웨어 정보


 - 리다이렉트
   - Location : 페이지를 리다이렉트할 URL. 201 혹은 3xx 응답 헤더.

 - 인증
   - WWW-Authenticate : 인증 방법에 대한 정의
   - Authorization : 인증을 위한 자격 증명. ex) Authorization: Berer xxxxx(credentials)

 - 쿠키
   - Set-Cookie : 서버에서 클라이언트로 쿠키 전달
     - expires, Max-Age, Domain, Path, Secure, HttpOnly, SameSite 등의 options
   - Cookie : 서버로부터 받은 쿠키를 저장하고, HTTP 요청에 포함하여 서버로 전달.

 - 캐시, 조건부
   - Cache-Control : 캐싱 메커니즘 위한 옵션 정의
     - public, private, no-cache, max-age, must-revaidate, no-store 등
       - no-cache : 데이터 캐시 가능. 변경 여부를 항상 Origin Server에 검증하고 사용.
       - no-store : 민감한 정보가 있으므로 저장하지 말 것.
       - must-revalidate : 캐시 만료 후 최초 조회시 원 서버에 검증 필요.
   - Last-Modified : 리소스의 마지막 수정 날짜로, 동일한 리소스의 여러 버전 비교하는데 사용.
     - 변경되지 않은 경우 304 코드와 헤더만 응답(바디 X). 클라이언트는 저장된 캐시로 데이터 재활용.
     - 변경된 경우 200 코드와 모든 데이터(바디 O) 응답.
     - If-Modified-Since, If-Unmodified-Since
   - ETag : 리소스 버전 식별하는 고유한 문자열. 
     - If-Match, If-None-Match 

## References

[https://developer.mozilla.org/ko/docs/Web/HTTP/Headers](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)