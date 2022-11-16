---
title : JPA(3) - 매핑 어노테이션
categories : [jpa]
tags: [jpa, hibernate]
---

>[자바 ORM 표준 JPA 프로그래밍](http://www.yes24.com/Product/Goods/19040233)

# 매핑 어노테이션

1. 객체(Entity)와 테이블(Table) 매핑
2. 기본 키 매핑
3. 필드 - 컬럼 매핑
4. 연관 관계 매핑


## **@Entity**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Entity	| | 엔티티 클래스를 테이블과 매핑하겠다. | 
| name | 사용할 엔티티의 이름을 지정 <br> |  클래스명

 - Default Constructor 필수.
 - Modifier는 public or protected
 - 필드에 final X
<br>
<br>

## **@Table**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Table | | 엔티티와 매핑할 테이블 지정 |
|   name    | 매핑할 테이블 명  | 엔티티명
|   catalog | DB가 catalog 기능 있는 경우 매핑  | 
 |   schema  | DB가 schema 기능 있는 경우 매핑   |
|   uniqueConstraints(DDL)   | DDL 생성시 유니크 제약조건을 만든다. <br> 2개 이상의 복합 유니크 제약조건도 만들 수 있다. <br> 스키마 자동 생성시에만 사용.  |  

## **@Id**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Id | | 기본 키(primary key) 매핑 | | 

- 적용 가능한 자바 타입
     - primitive
     - Wrapper class
     - String
     - java.util.Date
     - java.sql.Date
     - java.math.BigDecimal
     - java.math.BigInteger


## **@Column**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Column || 컬럼 매핑 | 
| name  | 필드와 매핑할 테이블 컬럼 이름  | 객체의 필드 이름  |
| insertable  | SQL INSERT문에<br> 해당 필드를 포함할 것인지 여부 | true  |
| updatable | SQL UPDATE문에<br> 해당 필드를 포함할 것인지 여부 | true  |
| table | 한 엔티티를 두 개 이상의<br> 테이블에 매핑할 떄 사용  | 현재 매핑된 테이블  |
| nullable(DDL) | null값의 허용 여부 설정 | true  |
| unique(DDL)| @Table의 uniqueConstraints와 같음.<br>두 컬럼 이상을 조건을 걸어주려면 <br>uniqueConstraints 사용 | false |
| columnDefinition(DDL) | 데이터베이스 컬럼 정보를 직접 줄 수 있음. | 필드의 자바 타입과<br> dialect 이용해<br> 적절한 컬럼 타입 생성 | 
| length(DDL) | 문자 길이 제약 조건 String 타입. | 255 |
| precision, scale(DDL) | BigDecimal, BigInteger 타입에서 사용. <br>precision은 소수점 포함 전체 자릿수, <br> scale은 소수의 자릿수. <br> double, float 타입은 적용 X. | precision = 19, <br> scale = 2 |

- @Column 생략시 속성들은 대부분 기본값으로 설정되는데, int와 같은 primitive 타입은 null이 없기 때문에 not null로 설정된다. 

- DDL 생성 기능

1) &nbsp;@Column
```java
  @Column(name="user_name", nullable = false, length = 10)
  private String userName;
```


2) &nbsp;@TABLE
```java
  @Entity(name="MEMBER")
  @Table(name="MEMBER", uniqueConstraints = {@UniqueConstraint(name = "name_age_unique",
                                                               columnNames = {"name", "age"} )})
  public class Member{
    ...
  }
```
 - 이런건 그냥 DDL 작성해도 되지만, 이 기능을 사용함으로써 엔티티만 보고도 테이블 스키마 파악 가능하니 사용하는 것이 좋을 것 같다.


### DB 스키마 자동 생성

- JPA는 매핑 정보와 DB Dialect를 활용해 데이터베이스 스키마 생성함.
  - hibernate.hbm2ddl.auto 속성 설정 필요. 다음은 속성 값
    - create : DROP + CREATE
    - create-drop : DROP + CREATE + DROP (create 속성에 애플리케이션 종료시 생성한 DDL 제거.)
    - update : 데이터베이스 테이블과 엔티티 매핑 정보 비교하여 변경 사항만 수정.
    - validate : 데이터베이스 테이블과 엔티티 매핑 정보 비교하여 변경 사항 있으면 경고와 함께 애플리케이션 실행 X. DDL 건드리지 않음.
    - none : 자동 생성 기능 사용X.(이 값은 유효하지 않은 값으로, 속성 자체를 설정 파일에서 지우거나 유효하지 않은 값을 넣으면 기능 사용 X.)
- 다만, 운영 환경에서 사용할 정도로 완성도가 높지 않을 수 있음.
- hibernate.ejb.naming_strategy = org.hibernate.cfg.ImprovedNamingStrategy
  - 자바는 카멜 케이스, 데이터베이스는 언더스코어 표기법 사용하기 때문에 그 차이를 해결해주는 설정.
  - 설정에 위 속성을 추가하면 테이블 혹은 컬럼 명 생략시 언더스코어 표기법으로 DDL 구성.

## **@ManyToOne**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@ManyToOne | | 다대일 관계를 알려주는 매핑 정보 | 
| optional | false시 연관된 엔티티가 항상 있어야 한다. <br> true시에는 엔티티가 없는 경우도 조회한다(Outer Join) | true
| fetch | 글로벌 페치 전략 설정.    | @ManyToOne=FetchType.EAGER <br> @OneToMany=FetchType.LAZY
|   cascade |    엔티티의 상태 변화를 전파시키는 옵션   |
|   targetEntity    |   연관된 엔티티의 타입 정보를 설정    |


<br>

## **@OneToMany**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@OneToMany | | 일대다 관계를 알려주는 매핑 정보 | 
| fetch | 글로벌 페치 전략 설정.    | @ManyToOne=FetchType.EAGER <br> @OneToMany=FetchType.LAZY
| mappedBy | 연관관계의 주인 필드 지정 | 
|   cascade |    엔티티의 상태 변화를 전파시키는 옵션   |
|   targetEntity    |   연관된 엔티티의 타입 정보를 설정    |


- targetEntity
  - 제네릭으로 타입 정보를 알 수 있기 때문에 사용하지 않아도 됨.
  - ex)

    ```java
    @OneToMany
    private List<Memeber> members

    @OneToMany(targetEntity = Member.class)
    private List Members;
     ```


## **@JoinColumn**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@JoinColumn | | 외래 키를 매핑할 때 사용. 생략 가능. | 
|name  | 외래 키 이름  | 필드명 + "_" 참조하는 테이블의 <br>기본 키 컬럼 명
|referencedColumnName  | 외래 키가 참조하는 <br>대상 테이블에서의 컬럼명 | 참조하는 테이블의 <br>기본 키 컬럼명  
|foreignKey(DDL) | 외래 키 제약조건을 직접 지정할 수 있음. | 
|unique<br>nullable<br>insertable<br>updatable<br>columnDefinition<br>table | @Column의 속성과 같음 | 


- 생략하게 되면 외래 키를 찾을 때 기본 전략 사용.
  - 기본 전략 : 필드명+_+참조하는 테이블의 컬럼명

- name과 referencedColumnName의 차이를 설명한 글 : [name vs referencedColumnName](https://stackoverflow.com/questions/53287631/difference-between-name-and-referencedcolumnname-in-joincolumn-annotation)


## **@SequenceGenerator**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@SequenceGenerator | | 시퀀스 생성기 등록 | 
| name | 식별자 생성기 이름.  | 필수
|   sequenceName    |   데이터베이스에 등록되어있는 시퀀스의 이름.    |       
|   initialValue    |    시퀀스 DDL 생성시 처음 시작하는 수 지정.<br>DDL 생성시에만 사용됨.   |   1    
|   allocationSize    |   시퀀스 한 번 호출에 증가하는 수    |   50     
|   catalog, schema |   데이터베이스 catalog, schema 이름   |


 - allocationSize 기본값이 50인 이유
   - 50씩 증가하면 여러 JVM이 동작하더라도 기본 키 값이 충돌하지 않게 하려고 + 시퀀스 접근 횟수 줄이기 위해(메모리에서 식별자 할당)


## **@TableGenerator**

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@TableGenerator | | | 
|   name    | 식별자 생성기 이름. @GeneratedValue와 매핑시 사용 | (필수)
|   table   | DB에 생성한 식별자 테이블 명  | hibernate_sequences
|    pkColumnName   |    table primary key 컬럼명   |    sequence_name   
|    valueColumnName   |     마지막 generate값을 갖고있는 컬럼명 |  next_val
|   pkColumnValue   | Key로 사용할 이름 | 엔티티명
|   initialValue    | 초기 값.  | 0
|   catalog, schema | 데이터베이스 catalog, schema 이름 | 
|   uniqueConstraints(DDL)  | 유니크 제약 조건 지정.    |

<br><br>
<hr>
<br><br>

## @Enumerated

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Enumerated | | 자바 enum 매핑 | 
| value | - EnumType.ORDINAL : enum의 순서를 저장 <br> - EnumType.STRING : enum의 이름을 저장 | ORDINAL


- EnumType.ORDINAL : enum **순서**를 DB에 저장.
  - ex)1, 2, 3....
- EnumType.STRING : enum **이름**을 DB에 저장.
  - ex)USER, ADMIN

## @Temporal

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Temporal | | 자바 날짜 타입 매핑(Date, Calendar) |
| value | - TemporalType.DATE : 날짜, DB의 date 타입과 매핑  <br> - TemporalType.TIME : 시간, DB의 time 타입과 매핑  <br> - TemporalType.TIMESTAMP : 날짜와 시간, DB의 timestamp 타입과 매핑 | TemporalType은 <br>필수로 지정해야 함.

- @Temporal 생략시 dialect에 따라 MySQL은 datetime, H2, 오라클, PostegreSQL은 timestamp 타입으로 DDL 생성한다.


## @Lob

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Lob | | DB의 BLOB, CLOB 타입과 매핑 | 

- 속성 없음. 필드 타입이 문자면 CLOB, 나머지는 BLOB

## @Trnasient

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Transient | | 해당 필드는 DB 테이블의 컬럼과 매핑하지 않겠다는 의미. | 


## @Access

Annotation | attribute  | Description | DefaultValue 
--- | --- | --- | ---
@Access | | JPA가 엔티티 데이터에 접근하는 방식을 지정. | 


- 필드 접근 : AccessType.FIELD
  - 필드에 직접 접근
  - modifier가 private이어도 상관 없이 접근할 수 있다.

- 프로퍼티 접근 : AccessType.PROPERTY
  - 접근자(Getter) 사용.


- @Access 생략시 @Id의 위치를 기준으로 접근 방식을 결정한다
  - 필드 위에 있으면 필드 접근,
  - Getter 위에 있으면 프로퍼티 접근방법을 사용.




<!-- @Embedded	Specifies the properties of class or an entity whose value is an instance of an embeddable class.

@GeneratedValue	Specifies how the identity attribute can be initialized such as automatic, manual, or value taken from a sequence table.
@Transient	Specifies the property that is not persistent, i.e., the value is never stored in the database.
@Column	Specifies the column attribute for the persistence property.
@SequenceGenerator	Specifies the value for the property that is specified in the @GeneratedValue annotation. It creates a sequence.
@TableGenerator	Specifies the value generator for the property specified in the @GeneratedValue annotation. It creates a table for value generation.


@UniqueConstraint	Specifies the fields and the unique constraints for the primary or the secondary table.

@ColumnResult	References the name of a column in the SQL query using select clause.

@ManyToMany	Defines a many-to-many relationship between the join Tables.


@OneToOne	Defines a one-to-one relationship between the join Tables.
@NamedQueries	specifies list of named queries.
@NamedQuery	Specifies a Query using static name. -->