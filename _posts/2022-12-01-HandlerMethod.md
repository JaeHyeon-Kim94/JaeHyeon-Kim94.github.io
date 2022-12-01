---
categories: [Spring, Issue]
title: Casting error Handler Object to HandlerMethod
---

 - 문제

interceptor에 전달되는 Object 타입의 handler를 별다른 고려 없이 HandlerMethod 타입으로 형변환을 시도하면 에러가 발생한다.


 - 원인

static resource를 요청할 때에도 interceptor가 호출되기 때문에 스프링 컨테이너 입장에서는 Object 타입의 handler가 HandlerMethod가 아닐 수도 있기 때문에 ClassCastException이 발생한다. (ResourceHttpRequestHandler가 넘어온다.)


 - 해결

해당 interceptor의 pathPattern을 Controller의 path로 한정시킨다.


