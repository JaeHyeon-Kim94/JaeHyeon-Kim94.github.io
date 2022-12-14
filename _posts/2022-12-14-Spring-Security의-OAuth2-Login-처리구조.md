---
categories: [Spring]
tags: [oauth2, security]
title: Spring Security의 OAuth2 Login 처리 구조
---

## OAuth2 Login Process


[![](../../assets/img/OAuth2-Authorization.png)](https://hudi.blog/oauth-2.0/)


이번 포스트는 Spring Security 내부 동작과정을 살펴보고 이해하고자 합니다. 위 이미지에서 1~8의 프로세스에 해당합니다.


### 1. 로그인 요청 from Resource Owner to Client

![](../../assets/img/oauth2-process-1-login-request.PNG)

Spring Security를 통해 OAuth2 로그인을 구현하고자 하는 경우 uri를 해당 이미지와 같이 지정하면, 우리가 구현할 서버에서는 각종 정보(client id, redirect uri...)가 담긴 쿼리스트링을 구성하여 Authentication Server에게 로그인 요청을 합니다. 

### 2. 로그인 요청 from Client to Authentication Server

/oauth2/authorization/{registrationId} 형식의 uri를 통해 어떻게 쿼리 스트링이 구성되어 Resource Owner에게 요청을 보내는지 살펴보겠습니다.

![](../../assets/img/DefaultSecurityFilterChain_OAuth2AuthorizationRequestRedirectFilter.PNG)

스프링 Configuration Bean에서 HttpSecurity.build()를 통해 Filter Chain에 등록한 DefaultSecurityFilterChain입니다. 해당 Filter Chain의 6번째 필터로 OAuth2AuthorizationRequestRedirectFilter가 등록되어있는 것을 볼 수 있는데, 

내부를 들여다 보면 다음과 같습니다.

![](../../assets/img/OAuth2AuthorizationRequestRedirectFilter.PNG)

우선 설정 파일(yml,properties)이나 bean을 통해 등록한 provider에 대한 메타데이터를 이용해 ClientRegistration 객체를 만들고, 이 ClientRegistration 객체들을 Map에 담아 InMemoryClientRegistrationRepository 저장합니다. (키 값은 naver, kakao등의 registrationId)

컴포넌트 스캔을 통해 InMemoryClientRegistrationRepository이 빈으로 등록되고, 로그인 요청시마다 provider에 대한 정보를 가져와 사용하게 됩니다.

이 덕분에 우리가 직접 쿼리 스트링을 구성하지 않아도 href=/oauth2/authorization/{registeration}만으로 Authentication Server에 적절한 요청을 보낼 수 있게 됩니다.


![](../../assets/img/DefaultSecurityFilterChain_OAuth2AuthorizationRequestRedirectFilter-doFilterInternal.PNG)

/oauth2/authorization/{registrationId}가 requestMatcher로 등록되어있어 해당 uri로 온 요청에 대해 resolve()메서드를 호출하고, 해당 메서드에서는 registrationId를 추출하여 적합한 provider의 정보를 Build해 redirect 요청을 보내게 됩니다.


### 3 ~ 6 Resource Owner의 로그인과 Authrization Code

Resource Owner는 네이버, 구글 등 Provider가 만들어놓은 로그인 페이지로 이동하게 됩니다. 그 후 
Resource Owner가 자신의 id와 password를 통해 로그인을 하게 되면 우리 서버(Client)측에서는 Authorization Code를 발급받게 됩니다. 이 코드는 Access Token를 얻기 위해 사용하는 일회성 코드이며, 수명이 매우 짧습니다.


### 7 ~ 8 Authorization Code로 Access Token 얻기

Authentication Server에서 받은 코드는 AbstractAuthenticationProcessingFilter를 상속받고있는 OAuth2LoginAuthenticationFilter(7번째 필터)의 attempAuthentication()를 통해 Accss Token으로 교환됩니다. 

![](../../assets/img/DefaultSecurityFilterChain_OAuth2AuthorizationRequestRedirectFilter_2.PNG)


![](../../assets/img/DefaultSecurityFilterChain_OAuth2LoginAuthenticationFilter_2.PNG)

1에서getAuthenticationManager()는 AuthenticationManager 구현체인 ProviderManager를 리턴하고,

![](../../assets/img/DefaultSecurityFilterChain_OAuth2LoginAuthenticationFilter_2_1.PNG)

내부 로직을 거쳐 Token을 받아오는 것을 확인할 수 있습니다.

![](../../assets/img/loadUser.PNG)

이후 우리가 구현할 OAuth2UserService 구현체의 loadUser()를 호출하게 됩니다.

## Reference

[OAuth 2.0 개념과 동작원리](https://hudi.blog/oauth-2.0/)

[로그인을 어떻게 처리할까? 스프링 시큐리티 구조](https://velog.io/@max9106/OAuth3)