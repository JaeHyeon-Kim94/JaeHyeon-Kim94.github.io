---
categories : [Spring]
tags : validation
---

# SpringBoot validation

요청 파라미터 값이 바인딩된 도메인 클래스의 입력값을 검증한다.

```groovy
    implementation 'org.springframework.boot:spring-boot-starter-validation'
```

## 예제

1. 입력값 검증을 할 메서드 파라미터의 도메인 클래스를 정의하고, @Validated 를 붙여준다.(입력값 검증 기능 활성화)
2. 입력값 검증 대상 도메인 클래스 **직후**에 BindingResult를 정의한다. 이 인자에 검증 오류 관련 데이터가 담김.
3. BindingResult가 제공하는 메서드를 통해 에러 정보 확인.


```java
    @PostMapping(path="", produces="application/json; charset=UTF-8")
    public ResponseEntity<String> register(@Validated @RequestBody Board board, BindingResult result){
       ResponseEntity<String> entity;
        if(result.hasErrors()){

            List<ObjectError> allErrors = result.getAllErrors();

            //객체 레벨의 에러 발생
            List<ObjectError> globalErrors = result.getGlobalErrors();

            //필드 레벨의 에러 발생 
            //(hasFieldErrors의 인자로 필드명을 넘겨주면 해당 필드에서 에러 발생시 true 반환)
            List<FieldError> fieldErrors = result.getFieldErrors();

            for( ObjectError oe : allErrors){
                log.info("all Error : " + oe);
            }

            for( ObjectError oe : globalErrors){
                log.info("global Error : " + oe);
            }

            for( FieldError fe : fieldErrors){
                log.info("all Error : " + fe);
                log.info("field error getDefaultMessage : " + fe.getDefaultMessage());
            }
            return entity = new ResponseEntity<>("FAIELD", HttpStatus.BAD_REQUEST);
        }
        boardService.register(board);
         entity = new ResponseEntity<>("SUCCESS", HttpStatus.OK);

        return entity;
    }


    @Data
    public class Board{
        private Long boardNo;
        @NotBlank
        private String title;
        private String content;
        private String writer;
        private LocalDateTime regDate;
        private LocalDateTime edtDate;
    }
```

## 입력값 검증 규칙

 - @NotNull : Null이 아니어야 한다.
 - @NotEmpty : Null이 아니며, 하나 이상의 공백 아닌 문자가 있어야 한다.
 - @NotEmpty : collection과 배열, map이 null이 아니며 비어있지 않아야 한다.
 - @Size : 글자 수나 컬렉션의 요소 개수를 검증
 - @Email : 이메일 주소 형식인지를 검증
 - @Past : 현재보다 과거인지를 검증(@PastOrPresent)
 - @Future : 현재보다 미래인지를 검증(@FutureOrPresent)
 - @Valid : 멤버 필드로 선언된 다른 객체에 대한 입력값 검증.
   - ex)
   ```java
    public class Team{
        ...
        @Valid
        private List<Member> members;

        @Valid
        private Manager manager;
    }
   ```
