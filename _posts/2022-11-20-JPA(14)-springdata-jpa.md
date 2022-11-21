---
title : JPA(14)-Spring Data JPA
categories : [jpa]
tags: [jpa, hibernate]
published : false
---


 - 핵심
   - JpaRepository 인터페이스. (구현체 X)
   - 쿼리메소드
     - [메소드 이름으로 쿼리 생성](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repository-query-keywords)
       - ex) findByEmailName
       - findBy~시 뒤에 들어갈 메서드 이름은 필드명과 같아야 함.
     - 메소드 이름으로 JPA NamedQuery 호출
       - @NamedQuery(name="쿼리이름", query="쿼리" )
       - 혹은 XML <named-query name="쿼리이름"><query><CDATA[쿼리]</CDATA></query></named-query>
       - 호출
         - 기존 방법 : em.createNamedQuery("쿼리이름", 엔티티.class)...
         - 스프링 JPA : List<엔티티.class> 쿼리이름(@Param("파라미터명") String name);
     - @Query 어노테이션을 사용해 레포지토리 인터페이스에 쿼리 직접 정의
       - @Query(value="쿼리", nativeQuery = true"
         - JPQL과 달리 Native SQL은 위치기반 파라미터 0부터 시작.
         - 스프링 데이터 JPA는 위치 기반 + 이름 기반 모두 지원
         - 벌크성 쿼리는 @Query와 함께 @Modifying 같이 사용.
           - 벌크 쿼리 실행 후 영속성 컨텍스트 초기화 위해선 Modifying.clearAutomatically=true



     - 페이징과 정렬
       - PageRequest implements Pageable 사용.
       - ex)
        ```java
            public interface MemberRepository extends Repository<Meber, Long>{
                Page<Member> findByNameStartingWith(String name, Pageable pageable);
            }

            PageRequest request = 
                    new PageRequest(0, 10, new Sort(Direction.DESC, "name"));

            Page<Member> result = 
                    repoisotry.findByNameStartingWith("김", pageRequest); 554
        ```

     - Spring Data 제공 Web 확장 기능
       - 설정
         - SpringDataWebConfiguration(XML) or @EnableSpringDataWebSupport 설정.
         - DomainClassConverter(도메인 클래스 컨버터) & HandlerMethodArgumentResolver(페이징, 정렬)가 스프링 빈으로 등록됨.
       - DomainClassConverter
         - HTTP 파라미터로 넘어온 엔티티의 ID로 엔티티 객체를 찾아 바인딩.
         - public String memberUpdateForm(@RequestParam("id") Member member, Model model) : 넘어온 파라미터는 id이지만, 도메인 클래스 컨버터가 알맞은 엔티티 객체로 변환.
           - 이 도메인 클래스를 컨트롤러에서 수정하더라도 이는 데이터베이스에 반영되지 않음.
           - 영속성 컨텍스트의 동작 방식과 OSIV와 관련.
             - OSIV 사용X : 조회한 엔티티는 준영속 상태로서 더티 체킹 동작하지 않음. 반영 위해선 merge 필요.
             - OSIV 사용O : 조회한 엔티티는 영속 상태. But 컨트롤러, 뷰에서는 영속성 컨텍스트를 플러시하지 않아 DB 반영 안됨. 반영을 위해선 트랜잭션을 시작하고 커밋하는 서비스 계층 호출 필요.
       - 페이징 & 정렬
         - 페이징 : PageableHandlerMethodArgumentResolver
         - 정렬 : SortHandlerMethodArgumentResolver
         - 도메인클래스 컨버터 기능을 이용하면 페이징, 정렬을 위한 인스턴스가 생성되고 컨트롤러 메서드의 인자로 사용할 수 있음.
         ```java
            @RequestMapping
            String showUsers(Model model, Pageable pageable) {

                model.addAttribute("users", repository.findAll(pageable));
                return "users";
            }
         ```
          - 여러 페이징, 정렬 인스턴스가 필요하다면 @Qualifier 어노테이션을 사용.
           ```java
            public String getUserList(
                @Qualifier("user") Pageable userPageable,
                @Qualifier("order") Pageable orderPageable
            )
           ```
            - 요청 파라미터는 qualifier_page이어야 한다.
              - ex) /...?user_page=0&order_page=0
            



 - 기타
   - 환경 설정 : dependencies + javaConfig(@EnableJpaRepositories)
     - basePackages의 repository 인터페이스 구현 클래스 동적 생성
     - JpaRepository 계층 구조 ( Repository <- CrudRepository  <- PagingAndSortingRepository <-JpaRepository)
     - save()시 엔티티의 식별자 기준으로 없으면 새로운 엔티티로 판단해 persist(), 있으면 이미 있는 엔티티로 판단해 merge() 호출. 이런 신규 엔티티 판단 전략은 변경 가능.
    - SQL 힌트 기능 지원
      - @QueryHints
    - Lock : @Lock
    - 명세(Specification)기능
      - JpaSpecificationExecutor 인터페이스 상속받아 사용.
        ```java
            public interface JpaSpecificationExecutor<T>{
                T findOne(Specification<T> spec);
                List<T> findAll(Specification<T> spec);
                Page<T> findAll(Specification<T> spec, Pageable pageable);
                List<T> findAll(Specification<T> spec, Sort sort);
                long count(Specification<T> spec);
            }


            public class ex{
                import static org.springframework.data.jpa.domain.Specifications.*;
                import static com.example.myCustomSpec.*

                public List<MyClass> findMyClasses(String name){
                    List<MyClass> result = repository.findAll(
                        where(memberName(name)).and(isOrderStatus())
                    )
                }
            }

            public class MyCustomSpec{
                public static Specification<MyClass> memberName(final String memberName){
                    return new Specification<MyClass>() {
                        public Predicate toPredicate(Root<MyClass> root, CriteriaQuery<?> query, CriteriaBuilder builder){
                            if(StringUtils.isEmpty(memberName)) return null;

                            Join<MyClass, MyClass2> m = root.join("member", JoinType.INNER);

                            return builder.equal(m.get("name"), memberName);
                        }
                    };
                }

                public static Specification<MyClass> isOrderStatus(){
                    return new Specification<MyClass>() {
                        public Predicate toPredicate(Root<MyClass> root, CriteriaQUery<?> query, CriteriaBuilder builder){
                            return builder.equal(root.get("status"), OrderStatus.ORDER);
                        }
                    };
                }
            }
        ```

     - 사용자 정의 repository 구현
       - repository 인터페이스 구현시 필요한 메서드만 구현할 수 있도록 지원함. repository 인터페이스에서 JpaRepository 인터페이스와 사용자 정의 인터페이스를 상속받고, repository 인터페이스를 구현하는 ~Impl 클래스에서 메서드 구현.
       - 사용자 정의 구현 클래스 이름은 ~Impl이어야 하는데, @EnableJpaRepositories.repositoryImplementationPostfix 속성을 통해 변경할 수 있음.
    

     - 스프링 데이터 JPA + QueryDSL 통합 두 가지 방법
       - repository에서 QueryDslPredicateExecutor 상속.
       - ex)
        ```java
        public interface ExampleRepository extends JpaRepository<Item, Long>, QueryDslPredicateExecutor<Item>{
            //이제 QueryDSL 사용 가능
        }

        main(){
            QExample ex = QExample.example;
            Iterable<Example> result = repository.findAll(
                example.name.contains("ex").and(~~)
            )
        }
        ```
       - QueryDslPredicateExecutor는 join, fetch 불가.
       - 따라서 QueryDSL 자유롭게 사용 위해선 JPAQuery 직접 사용 혹은 QueryDslRepositorySupport 사용해야 함.
       - QueryDslRepository
         - 매번 JPAQuery 직접 생성하기보다 QueryDslRepositorySupport 상속 받아 사용하는 것이 편리.
           - 다만 공통 인터페이스는 직접 구현 불가하므로 custom repository 필요.
            ```java
                public class OrderRepositoryImpl extends QueryDslRepositorySupport implements CustomOrderRepository{
                    public OrderRepositoryImpl(){
                        //생성자로 QueryDslRepositorySupport에 엔티티 정보 넘겨주어야 함.
                        super(Order.class);
                    }
                }
                ...
            ```


            ```java
                @Repository
                public abstract class QueryDslRepositorySupport{
                    //엔티티 매니저
                    protected EntityManager getEntityManager(){
                        return entityManager;
                    }

                    //from절
                    protected JPQLQuery from(EntityPath<?>... paths){
                        return querydsl.createQuery(paths);
                    }

                    //QueryDSL delete절
                    protected DeleteClause<JPADeleteClause> delete(EntityPath<?> path){
                        return new JPADeleteClause(entityManager, path);
                    }

                    //QueryDSL update절
                    protected UpdateClause<JPAUpdateClause> update(EntityPath<?> path){
                        return new JPAUpdateClause(entityManager, path);
                    }

                    //Querydsl을 편하게 사용할 수 있도록 돕는 헬퍼 객체
                    protected Querydsl getQuerydsl(){
                        return this.querydsl;
                    }

                }

            ```