---
categories: [Spring, annotation]
tags: [spring, springboot, annotation]
published : false
---

## Spring, Springboot Annotations

- `@Components`
  <br> 해당 클래스가 스프링에서 객체로 만들어서 관리하는 대상임을 명시.
  <br> @ComponentScan을 통해 스프링이 알게할 클래스 지정.

- `@Autowired`
  <br>스프링 내부에서 자신이 특정 객체에 의존적이므로 자신에게 해당 타입의 빈을 주입해달라는 어노테이션. 스프링 4.3 이후에는 묵시적 생성자 주입이 가능함.

- `@InitBinder`
- `@DateTimeFormat`

- `@Model`
  <br>파라미터로 전달된 데이터중 화면에서 필요한 데이터가 없을 때 해당 데이터를 전달하기 위해 사용. 기본적으로 Spring MVC Controller는 Java Beans 규칙에 맞는 객체는 다시 화면으로 객체를 전달. 여기서 규칙은 기본 생성자가 존재해야 하며, 멤버변수에 접근 가능한 getter, setter 존재해야 하고... [자바빈 규약 참고](https://dololak.tistory.com/133)
  <br>반면 기본 자료형의 경우에는 파라미터로 선언되더라도 화면까지 전달되지 않음. 이때 필요할 수 있는 어노테이션이 @ModelAttribute

- `@ModelAttribute`
  <br>전달받은 파라미터를 Model에 담아서 전달하도록 할 때 필요. 타입과 관계없이 무조건 Model에 담아서 전달되므로 파라미터로 전달된 데이터를 다시 화면에서 사용해야할 경우에 씀. 인자 선언부 앞에
  ex) @ModelAttribute("uri파라미터명") 타입 변수명

- `@SpringBootApplication`
  <br>이 어노테이션은 다음의 3가지 기능을 가지고 있다.
  첫째, `@EnableAutoConfiguration` : 스프링부트의 auto-config 가능하게 한다.

  > This annotation tells Spring Boot to “guess” how you want to configure Spring, based on the jar dependencies that you have added. Since spring-boot-starter-web added Tomcat and Spring MVC, the auto-configuration assumes that you are developing a web application and sets up Spring accordingly.

  > > EnableAutoConfiguration에 관한 [공식문서](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started.first-application.code.enable-auto-configuration)의 내용인데,
  > > jar 의존성을 기반으로 스프링부트가 추측하여 적절하게 설정을 해준단다. 그 예로 spring-boot-starter-web 의존성이 추가된 것을 보고 web 개발에 적절한 tomcat과 spring mvc 설정을 해준다고 한다.

  둘째, `@ComponentScan` : application이 위치한 패키지로부터 @Component를 스캔하여 사용가능하도록 함.
  셋째, `@SpringBootConfiguration` : 추가적인 bean들,config 클래스들을 등록

<br>스프링

## Spring Test Annotation

- `@ContextConfiguration`
  <br>스프링이 실행되면서 어떤 설정 정보를 읽어 들여야 하는지를 명시.
  <br>xml의 경우에는 locations 속성을, java bean configuration인 경우에는 classes 속성 사용

- `@Runwith`
  <br>테스트시 필요한 클래스 지정. 스프링에선 SpringJUnit4ClassRunner가 대상.

- `@Test`
  <br>해당 메서드가 jUnit상에서 단위 테스트의 대상인지 알려주는 역할.
