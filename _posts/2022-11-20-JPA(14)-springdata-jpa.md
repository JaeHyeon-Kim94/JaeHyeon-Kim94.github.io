---
title : JPA(14)-Spring Data JPA
categories : [jpa]
tags: [jpa, hibernate]
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


 - 기타
   - 환경 설정 : dependencies + javaConfig(@EnableJpaRepositories)
     - basePackages의 repository 인터페이스 구현 클래스 동적 생성
     - JpaRepository 계층 구조 ( Repository <- CrudRepository  <- PagingAndSortingRepository <-JpaRepository)
     - save()시 엔티티의 식별자 기준으로 없으면 새로운 엔티티로 판단해 persist(), 있으면 이미 있는 엔티티로 판단해 merge() 호출. 이런 신규 엔티티 판단 전략은 변경 가능.
     - SQL 힌트 기능 지원
       - @QueryHints
     - Lock
       - @Lock
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