---
categories : [Spring]
tags : [security]
published : false
---

# 스프링 시큐리티

 - 인증(Authentication) : 사용자의 신원을 증명하는 과정으로, 인증 과정에서 신원 증명 정보(credential, 주로 비밀번호) 제시.
 - 인가(Authorization) : **인증을 마친 유저**에게 권한을 부여하여 애플리케이션의 특정 리소스에 접근할 수 있도록 허가하는 과정. ROLE_USER 등 롤 형태로 권한을 부여함.

- 스프링 시큐리티는 HTTP 요청에 서블릿 필터를 적용해 보안 처리. (AbstractSecurityWebApplicationInitializer라는 베이스 클래스를 상속하여 필터 등록 및 구성 내용 자동 감지)

- WebSecurityConfigurerAdatper를 상속받아 configure() 메서드 오버라이드를 통해 보안요소 구성.
  - 기본적으로 매 요청마다 반드시 인증을 받도록 강제함.
  - HTTP 기본 인증(httpBasic()), 폼 기반 로그인(formLogin()) 기능도 기본적으로 구현되어 있음. 따라서 로그인 페이지를 만들어 지정하지 않는다면 기본 로그인 페이지를 보여줌.
- CSRF : 사이트 간 요청 위조 방어용 토큰을 생성해 HTTpSession에 삽입.
- 스프링 시큐리티는 개발자가 골라 쓸 수 있게 다양한 인증 공급자(authentication provider=> 유저를 인증하고 사전에 부여된 권한을 반환) 제공.