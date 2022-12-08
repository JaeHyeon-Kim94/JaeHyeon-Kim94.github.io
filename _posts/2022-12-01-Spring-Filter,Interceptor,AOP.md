---
categories: Spring
tags: interceptor
title: Filter와 Spring Interceptor, AOP
---

## 공통 처리를 위해 어떤 것을 사용해야 하나?

<p>
애플리케이션을 구축할 때 권한 처리, 로깅 등 필요하지만 비즈니스 로직과는 거리가 먼 요소들이 있다.<br>

공통적인 것들을 비롯한 이러한 요소들은 비즈니스 로직에서 분리하여 관리할 필요가 있는데, Filter와 Interceptor, AOP를 사용하여 문제를 해결할 수 있다.<br>

이 세 기능들의 차이점을 알아보고, 효과적인 방법을 선택하도록 한다.
</p>


## 들어가며

<p>
차이점을 살펴보기 앞서 요청이 처리되는 과정에 대해 살펴볼 필요가 있다.
</p>


![](../../assets/img/request-flow.png)
[출처:갓대희 님의 [spring]filter vs interceptor vs AOP](https://goddaehee.tistory.com/154)



## **Filter**

<p>
필터는 doFilter의 메서드 매개변수로 ServletRequest, SevletResponse, FilterChain을 받아 요청과 응답을 재가공하여 FilterChain의 흐름에 싣는 일을 한다.<br>

이미지에서 볼 수 있는 것처럼 필터는 스프링 영역 외에 존재한다. 필터를 직접 구현할 때 implements하는 Filter 클래스를 보면 spring이 아닌 javax.servlet이다. (다만 DelegatingFilterProxy를 사용하거나 서블릿 컨테이너까지 관리하는 스프링 부트의 경우에는 스프링 컨테이너의 관리를 받게 되지만.)

즉, 필터는 웹 컨테이너에 의해 관리되며, 스프링 컨텍스트 밖에서 동작한다. (추가로 스프링의 최 앞단에는 DispatcherServlet이라는 프론트 컨트롤러가 존재한다.)

</p>



## **Interceptor**


인터셉터는 **DispatcherServlet**이 컨트롤러를 호출하기 전 요청과 응답을 참조하고 재가공할 수 있는 기능을 제공한다. 즉, 스프링 컨텍스트 내에서 DispatcherServlet과 Controller의 사이에 존재한다.<br>

DispatcherSerlvet은 HandlerMapping을 통해 컨트롤러를 찾고 HandlerExecutionChain을 return한다. HandlerExecutionChain은 등록된 인터셉터가 있다면 순차적으로 거친 후 컨트롤러가 실행되도록 한다. 단, 필터와 달리 request, response 객체를 임의로 조작할 순 없다. 그 내부의 값을 참조하고, 변경할 순 있지만.

interceptor의 preHandle 메서드의 경우 리턴 타입이 boolean으로서 리턴값에 따라 다음 인터셉터 혹은 컨트롤러의 작업을 지속할지, 중단할지를 결정할 수 있다. 

postHandle 메서드의 경우 컨트롤러 메서드의 동작 후에 필요한 후처리작업을 정의할 수 있으며 컨트롤러에서 예외 발생시 postHandle은 호출되지 않는다.

afterCompletion 메서드의 경우 view 관련 작업 전에 실행되는 postHandle 메서드와 달리 view 관련 작업을 비롯한 모든 작업이 완료된 후 호출되며 예외 발생 여부와 상관 없이 반드시 호출된다. 요청 처리 중 사용한 리소스 반환에 적합하다.



## AOP


객체지향적인 프로그래밍으로는 줄일 수 없는 중복을 줄이기 위해 사용할 수 있다. JoinPoint와 Pointcut(Before, After, Around)를 이용해 메서드를 세밀하게 조정할 수 있다.

인자로 ProceedingJoinPoint를 받아 처리하는데, 리플렉션을 통해 공통적인 부분을 조정한다. Request객체를 RequestContextHolder에서 받아 처리할 수 있지만, 성격상 컨트롤러 호출 과정에서 적용해야 하는 부가 기능을 적용하기는 상대적으로 적절하지 않아 보이며 메서드 자체의 기능과 관련된 작업에 사용하기 좋을 것 같다.<br>


 + 어노테이션
 - @Around, @Before, @AfterReturning, @After ,@AfterThrowing
   - 동작
     - AfterReturing : 비즈니스 메서드가 성공적으로 결과를 return했을시 동작.
     - After : 성공 실패와 상관 없이 동작.
     - Around : 프록시 객체를 통해 비즈니스 메서드 실행. 다른 것과 달리 ProceedingJoinPoint를 인자로 받음.
   - 실행 순서 : Around -> Before -> AfterReturing -> After -> Around
 - 



## Filter와 Interceptor


알아본 바와 같이 필터는 스프링 밖, 인터셉터는 스프링 안에서 동작한다.

따라서 **필터는** 보안 공통 작업(비정상적 요청 차단)등 스프링과 무관하게(스프링과 분리될 필요가 있는)웹 애플리케이션 전역에서 공통적으로 처리해야 할 작업들을 규정하기 적절해 보인다.

반면 **인터셉터**는 스프링 컨텍스트 내에서 요청과 관련한 전역적인 작업들을 처리하기 적절해 보인다. 

필터의 경우 filterChain.doFilter를 통해 request, response를 임의로 넘겨줄 수 있어 해당 객체들과 관련한 강력한 기능을 가지고 있는 반면, 

인터셉터의 경우 request, response를 제공받긴 하지만 객체 자체는 변경이 불가능하고 객체 내부의 값만 변경할 수 있다. 따라서 컨트롤러로 넘겨주기 위한 정보를 가공하기에는 용이하다.

스프링부트나 DelegatingFilterProxy를 이용하는 경우에도 이 부분은 크게 달라지지 않을 것이라고 생각한다.


## Reference

>정말 설명이 잘되어있다.<br>
[[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)