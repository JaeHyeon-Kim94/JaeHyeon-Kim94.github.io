---
categories: [Spring, annotation]
tags: [annotation]
---

## Annotations

- `@Components`
  <br> 해당 클래스가 스프링에서 객체로 만들어서 관리하는 대상임을 명시.
  <br> @ComponentScan을 통해 스프링이 알게할 클래스 지정.

- `@Autowired`
  <br>스프링 내부에서 자신이 특정 객체에 의존적이므로 자신에게 해당 타입의 빈을 주입해달라는 어노테이션. 스프링 4.3 이후에는 묵시적 생성자 주입이 가능함.

## Spring Test Annotation

- `@ContextConfiguration`
  <br>스프링이 실행되면서 어떤 설정 정보를 읽어 들여야 하는지를 명시.
  <br>xml의 경우에는 locations 속성을, java bean configuration인 경우에는 classes 속성 사용

- `@Runwith`
  <br>테스트시 필요한 클래스 지정. 스프링에선 SpringJUnit4ClassRunner가 대상.

- `@Test`
  <br>해당 메서드가 jUnit상에서 단위 테스트의 대상인지 알려주는 역할.
