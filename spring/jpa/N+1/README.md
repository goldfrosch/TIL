# 1대다, 다대1 이슈란

JPA 사용 시 데이터 테이블이 하나의 column에 여러가지의 데이터를 가지고 있는 테이블 구조가 만들어지곤 한다.

```
user-table
| id | name                  |
| 1  | GoldFrosch            |
| 2  | Frog                  |

bank-table
| id | user_id   | bank_name       |
| 1  | 1         | Toss Bank       |
| 2  | 1         | Shinhan bank    |
```

위처럼 user-table에 id 기반으로 여러개의 컬럼을 저장하는 bank-table이 생길 경우 다음과 같이 객체를 생성한다.

```
@Entity
@Table(name = "user-table")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id; // int의 최대값이 Long보다 낮기 때문에 보통 많은 데이터를 수용하기 위해 기본적으로 id는 Long을 사용한다. 일종의 국룰

    @NotNull
    @Size(min=4, max=25)
    private String name;

    @OneToMany(fetch = FetchType.EAGER)
    @JoinColumn(name = "user_id", referencedColumnName = "id")
    private List<Bank> banks; // Bank라는 도메인 객체는 임의로 있다고 가정하자.
}
```

이런식으로 구현해 일대다, 다대일 관계를 ORM을 통해 매핑시켜줄 수 있다.

## FetchType 이란?

연관된 엔티티를 가져오는 시기를 결정하는 설정 값

### Eager

Eager 옵션은 데이터를 **즉시** 가져와야한다는 가정하에 동작하는 옵션이다.

즉시라는 단어는 늘 위험하다. 일반적으로 findById에 대한 메소드는 EntityManager에서 PK 값을 찍어 사용하기 때문에 JPA에서는 내부적으로 join문을 사용한다.

내부적으로 inner join문 하나가 날아가 User와 동시에 bank를 조회해주는 역할을 수행하는데 즉시 로딩도 문제가 없어보이지만 JPQL에서 문제가 발생할 수 있다.

직접 Jpql문을 짜거나, findBy~~같은 쿼리 메소드에서도 Jpql이 만들어져서 나가게 되는데 jpql은 Sql로 그대로 번역이 되어 날아가게 된다.

User의 findAll을 요청한다면 select u from User u; 형식으로 진행되지만, Articles column에 즉시로딩을 걸음으로써 User 정보 query 하나를 날리고나서

User가 가진 Banks정보 table을 모두 검색하는 (bank 하나당 하나의 쿼리를 검색하게 되는) N+1문제가 발생하게 된다.

여기서 N은 User가 가진 Bank의 갯수를 의미하게 된다.

만약 일대다 관계에서 이 N의 갯수가 몇백만개까지 올라가게 되면 한번에 백만 쿼리를 조회하는 것이니 DB가 울부짖을 수 있다...

### Lazy

Lazy 옵션은 데이터를 느리게 가져올 수 있다. 지연 로딩이라고 하는데 연관된 객체를 사용하는 시점에 로딩을 해줌으로써 객체를 사용할 때 마다 조회시켜 주지 않는다라는 이점이 존재한다.

하지만 이 옵션도 N+1의 함정을 피해가지는 않는다. 조회 타이밍을 늦춰줄 뿐...

### fetch join

JPA가 자동으로 먼저 생성해주는 Jpql을 통해서 우선적으로 쿼리를 만들다보니 연관관계가 걸려있어도 join이 걸리지 않는 것이 핵심이다.

```
@Query("select distinct u from User u left join u.banks")
List<User> findAllJPQL();
```

위 쿼리로 진행하게 된다면 N+1 문제가 발생한다.

```
@Query("select distinct u from User u left join fetch u.banks")
List<User> findAllJPQLFetch();
```

join 문에 fetch를 걸게되면 지연 로딩이 걸려있는 연관관계에 대해 한번에 같이 즉시로딩을 해주는 역할을 해준다.
이 작업을 함으로써 단 한번의 쿼리로 해결하게 될 수 있다.

```
@EntityGraph(attributePaths = {"banks"}, type = EntityGraphType.FETCH)
@Query("select distinct u from User u left join u.banks")
List<User> findAllEntityGraph();
```

대신 단순하게 fetch join을 사용하게 되면 하드코딩이 되는 문제가 있어 그것을 최소화하기 위해 EntityGraph라는 annotation을 사용해 join 시키게 되면 동일한 효과를 얻을 수 있다.
