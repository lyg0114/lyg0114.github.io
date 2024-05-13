---
title: "[JPA] 영속성 컨텍스트"
date: 2024-05-10 20:40:00 +09:00
categories: [ database ]
tags:
  [
    java, jpa, Hibernate, spring, spring data jpa, database, mysql
  ]
---

# JPA를 쓰는 이유

OOP 와 RDB 사이의 간극을 극복하기위해 사용.

# 영속성 컨텍스트 (Entity Manager)

<img src="/assets/img/entity-manager-1.png" alt="x509" width="500">

- 영속성 컨텍스트는 **EntityManager** 또는 **1차 캐시**를 포함하고 있다.
- **Entity Manager**는 사용자 요청마다 생성된다. 즉 **ThreadLocal**에 생성되어 쓰레드간 공유를 차단한다.

## 데이터 조회

JPA에서 조회를 하면 바로 DB에 조회를 하는것이 아닌 **1차캐시**에 데이터가 있는지 조회를 한다. 그리고 1차캐시에 데이터가 존재하지 않으면 DB에 조회 쿼리를 요청한다.

<img src="/assets/img/data-access.png" alt="x609" width="600">

1. 1차캐시 조회
2. 1차캐시에 데이터 존재할경우 바로 반환
3. 데이터 존재하지 않을경우 DB에 조회 요청
4. DB로 부터 가져온 데이터를 1차캐시에 업로드
5. 1차캐시에서 데이터 조회

## 데이터 INSERT

- DB에 네트워크를 사용하여 쿼리를 전송하는것은 상당한 비용이 발생한다. 따라서 DB에 쿼리를전송하기전 가장 최적의 데이터 상태를 유지하고 트랜잭션이 종료될때 쿼리를 DB에 전송한다.

```java
public class JpaBookMain {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
    EntityManager em = emf.createEntityManager();
    EntityTransaction tx = em.getTransaction();
    tx.begin();

    try {
      Member member = new Member();
      member.setUsername("Tom");
      em.persist(member);
      // 여기까지 1차 캐시에 보관만 하고 INSERT 쿼리를 DB에 전송하지 않는다. 
      
      tx.commit(); // 커밋 하는 순간 INSERT 쿼리를 DB에 전송한다.
    } catch (Exception e) {
      e.printStackTrace();
      tx.rollback();
    } finally {
      em.close();
      emf.close();
    }
  }
}
```

## 데이터 변경

### 변경감지 (Dirty Checking)

- 아래의 코드에서 findMember를 다시 persist 하거나, update하는 코드는 찾아볼 수 없다. 그 이유는 바로 트랜잭션 종료시점에 **EntityManager의 스냅샷(DB 에서 읽어온 최초의
  상태)**과 비교하여 변경이 일어났다면 하이버네이트가 **자동으로 update 쿼리를 생성**하여 DB에 전송한다.
  <img src="/assets/img/dirty-checking.png" alt="x609" width="600">

```java
public class Main {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("config");
    EntityManager em = emf.createEntityManager();
    EntityTransaction tx = em.getTransaction();

    tx.begin(); // 트랜잭션 시작 #########

    try {

      Member member = new Member();
      member.setUsername("Tom");
      em.persist(member);
          
      // EntityManager 초기화 (강제로 DB로 쿼리 전송)    
      em.flush();
      em.close();

      // DB로 부터 데이터 조회
      Member findMember = em.find(Member.class, member.getId()); 
			// 데이터 수정
      findMember.setUsername("Kyle");
      
      // 트랜잭션 종료 #########
      // EntityManager의 스냅샷과 비교하여 변경사항이 있으면 Update 쿼리 자동 생성
      tx.commit();
      
    } catch (Exception e) {
      e.printStackTrace();
      tx.rollback();
    } finally {
      em.close();
      emf.close();
    }
  }
}
```

### 병합 (merge)
  <img src="/assets/img/merge.png" alt="x609" width="600">
- jpa 에서 데이터를 수정하는 방법으로 merge가 있다. 이 방식은 객체를 영속화하기는 하지만 변경하고자하는 값으로 덮어씌우는 방식이라 사용할떄 주의가 필요하다. 

```java
@Entity
class Item {
  @Id @GeneratedValue
  private Long id;
  private String name;
  private int price;
  private int stockQuantity;
}

@Repository
@RequiredArgsConstructor
class ItemRepository {
  private final EntityManager em;
  
  public void saveWithMerge(Item item) {
    if (item.getId() == null) {
      em.persist(item);
    } else {
      em.merge(item);       // itemId 값이 null 이 아닐때(이미 insert된적 있는 객채) 
                            // DB에서 가져온 영속화된 객체에 item 객체의 필드를 덮어씌워서 영속화 진행
    }
  }
  
  public void saveWithDirtyChecking(Item item) {
    Item findItem = em.find(Item.class, id);    // 1차캐시 또는 DB로 부터 영속화된 객체 생성
    findItem.update(item);                      // 영속화된 객체에서 필요한 부분만 item을 통해 수정
                                                // transaction commit 시점에 DB로 update 쿼리 생성
  }
}
```

- 예를들어 위의 코드에서 price가 null이라면 영속화된 객체 item 의 필드값 price에 null로 덮어쓰게 된다. 따라서 이 방식 보다는 **변경감지(Dirty checking)** 방식을 사용하자


<br>
<br>
<br>
<br>

[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard) <br>
[hibernate.org](https://hibernate.org/)
