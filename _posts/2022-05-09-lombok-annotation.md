---
categories: [Spring, annotation]
tags: [annotation, lombok]
---

## Annotations

- `@Data` <br>@ToString, @EqualsAndHashCode, @Getter/@Setter, @RequiredArgsConstructor를 모두 포함하는 어노테이션. 세부적인 설정 필요 없는 경우 사용

- `@RequiredArgsConstructor`
  <br> @NonNull이나 final이 붙은 인스턴스 변수에 대한 생성자 만듬.

- `@Log4j`
  <br>로그 객체 생성.

  ```java
  @Log4j
  public class LogExample{}
  //===============================================================
  //변환된 클래스
  //===============================================================
  import org.apache.logging.log4j.Logger
  public class LogExample{
  private static final Logger log = Logger.getLogger(클래스명.class);
  }
  ```

### @Setter의 속성

| 속성명     | 의미                                                                                                                          |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `value`    | 접근 제한 속성. <br> 기본 값은 lombok.AccessLevel.PUBLIC                                                                      |
| `onMethod` | setter 메서드 생성시 메서드에 추가할 어노테이션을 지정. <br> JDK8 이후의 syntax ex : @Setter(onMethod\_={@AnnotationsGohere}) |
| `onParam`  | setter 메서드의 파라미터에 어노테이션을 사용하는 경우 적용                                                                    |
