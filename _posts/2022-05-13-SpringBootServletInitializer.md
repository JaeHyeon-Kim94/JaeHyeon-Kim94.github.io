---
categories: [Spring, tmp]
tags: [SpringBootServletInitializer]
published : false
---

war 패키징 방식으로 spring boot 프로젝트를 initialize 하게 되면

SpringBootServletInitializer를 상속받고, configure 메서드를 오버라이드 하고있는 ServletInitializer 클래스가 추가적으로 생성됨.

> The first step in producing a deployable war file is to provide a SpringBootServletInitializer
> subclass and override its configure method. Doing so makes use of Spring Framework’s servlet 3.0
> support and lets you configure your application when it is launched by the servlet container.
> Typically, you should update your application’s main class to extend SpringBootServletInitializer

#추후보완

<!-- - 이 SpringbootServletInitializer를 왜 상속받고 있는가?-->
