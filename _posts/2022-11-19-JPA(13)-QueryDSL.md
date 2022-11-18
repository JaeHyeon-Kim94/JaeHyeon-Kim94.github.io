---
title : JPA(13)-QueryDSL
categories : [jpa]
tags: [jpa, hibernate]
---
- 세부 내용 누락되어있음.

# QueryDSL

 - Criteria처럼 JPQL을 편하게 작성하도록 도와주는 빌더클래스 모음. 비표준 오픈소스 프레임워크.
 - 장황하고 복잡한 Criteria에 비해 단순하고 사용하기 쉽다.

## Settings

 - 필요 라이브러리
   -  querydsl-jpa : QueryDSL JPA 라이브러리
   -  querydsl-apt : 쿼리타입 생성시 필요한 라이브러리
 - 플러그인 설정
    ```xml
        <project>
        <build>
        <plugins>
            ...
            <plugin>
            <groupId>com.mysema.maven</groupId>
            <artifactId>apt-maven-plugin</artifactId>
            <version>1.1.3</version>
            <executions>
                <execution>
                <goals>
                    <goal>process</goal>
                </goals>
                <configuration>
                    <outputDirectory>target/generated-sources/java</outputDirectory>
                    <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                </configuration>
                </execution>
            </executions>
            </plugin>
            ...
        </plugins>
        </build>
        </project>
   ```


## 기능들

QueryDSL이 제공하는 메서드를 비롯한 기능들 중심으로 정리하도록 한다.

```java
main(){
    EntityManager em = emf.createEntitymanager();

    JPAQuery query = new JPAQuery(em);

    //m : JPQL의 별칭
    QMember qMember = new QMember("m");
    List<Member> members =
                query.from(qMember)
                    .where(qMember.name.contains("김")
                        .and(qMember.name.height.gt("180")))
                        .list(qMember);
}
```

 - 우선 1) JPAQuery객체와 2) 쿼리객체가 필요함.

 - from(), where()등은 직관적이므로 설명 생략. 

 - 결과 조회
    - uniqueResult() : 하나일 때만
    - singleResult() : 하나 이상이면 처음 것만
    - lst() : 하나 이상일 때

 - 페이징
    - asc()/desc() + offset() + limit() 조합 => SearchResult 반환하면 활용하여 구현
    ex)
    ```java
    // 1)
        QPost qPost = new QPost("p");
        JPAQuery query = new JPAQuery(em);

        SearchResults<qPost> result = 
                    query.from(qPost)
                        .where(qPost.regDate.gt(anyDate))
                        .orderBy(qPost.regDate.desc(), qPost.id.asc())
                        .offset(10).limit(20)
                        .listResults(qPost);
    
    // 2)
        QueryModifiers mod = new QueryModifiers(20L, 10L) //limit, offset
        SearchResults<qPost> result = 
                query.from(qPost)
                    .where(...)
                    .restrict(mod)
                    .listResults(qPost);


        long total = result.getTotal();
        long limit = result.getLimit();
        long offset = result.getOffset();
        List<qPost> results = result.getResults();
    ```

 - 서브쿼리(new JPASubQuery().from()...)

    ```java
        QItem item = new QItem("i");
        QItem itemSub = new QItem("isub");
        JPAQuery query = new JPAQuery(em);

        query.from(item)
            .where(item.price.eq(
                new JPASubQuery().from(itemSub).unique(itemSub.price.max())
            ))
            .list(item);
    ```


 - 프로젝션과 결과 반환
    - 프로젝션 대상이 하나일 경우
    - 여러 컬럼 반환과 튜플( Tuple 객체 사용. )
    - 빈 생성 기능
        - 프로퍼티 접근(Setter)
            Projections.bean(type, ~.as("프로퍼티명") ...)
        - 필드 직접 접근(private이어도 동작함)
            Projections.fields(type, .as("필드명"))
        - 생성자 사용
            Projections.constructor (생성자 파라미터 순서 동일해야함.)

 - 수정 배치
    - 영속성 컨텍스트를 무시하고 직접 데이터베이스에 쿼리한다
    - JPAUpdateClause

 - 삭제 배치
    - 영속성 컨텍스트를 무시하고 직접 데이터베이스에 쿼리한다
    - JPADeleteClause


 - 동적 쿼리
    - BooleanBuilder 사용
    - booleanBuilder.and(item.name.contains(param.getName()));


 - 메서드 위임
    static 메서드를 만들고 @QueryDelegate(적용할엔티티)
    static 메서드의 파라미터 : (대상 엔티티의 쿼리타입, 필요한 파라미터 ...)

 