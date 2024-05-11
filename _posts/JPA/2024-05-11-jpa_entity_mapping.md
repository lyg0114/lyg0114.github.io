---
title: "[JPA] 연관관계 매핑"
date: 2024-05-11 20:40:00 +09:00
categories: [ database ]
tags:
  [
    java, jpa, Hibernate, spring, spring data jpa, database, mysql
  ]
---

# 단방향 연관관계

- 객체에서 한쪽 방향으로 의존관계가 성립하는 관계

  <img src="/assets/img/entity-mapping-1.png" alt="x609" width="600">


```java
@Getter
@Setter
@Entity
@Table(name = "member")
public class Member {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "member_id")
  private Long id;

  private String username;

  private int age;

  @ManyToOne
  @JoinColumn(name = "team_id")
  private Team team; //Team 쪽에서 Member 쪽으로의 의존관계는 없음.
}
```

# 양방향 연관관계

- 객체에서 양쪽 방향으로 의존관계가 성립하는 관계

## 연관관계의 주인

- 관계를 맺음에 있어 객체와, 테이블의 차이
  - 객체의 양방향 관계는 서로다른 두개의 단방향 관계

    ```java
    class A { B b; }
    class B { A a; }
    ```

  - 테이블은 외래키를 기반으로 JOIN을 하여 양쪽 어느곳이든 접근할 수 있다.

    ```sql
    select * from member m join team t on m.team_id = t.team_id
    select * from team t join member m on t.team_id = m.team_id
    ```

- 두개의 테이블중 한쪽에서 **외래 키** 를 관리해야 한다.
- 데이터의 등록, 수정, 삭제는 **연관관계의 주인** 클래스에서 관리.
- 주인이 아닌쪽(**mappedBy**)은 읽기만 가능

<img src="/assets/img/entity-mapping-2.png" alt="x609" width="600">

```java
@Getter
@Setter
@Entity
@Table(name = "member")
public class Member {
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "member_id")
  private Long id;
  private String username;
  private int age;

  @ManyToOne
  @JoinColumn(name = "team_id")
  private Team team; // 연관관계의 주인
}

@Getter
@Setter
@Entity
@Table(name = "team")
public class Team {
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "team_id")
  private Long id;
  private String name;

  @OneToMany(mappedBy = "team") 
  private List<Member> members = new ArrayList<>();
  // 참조만 가능하고, 읽기 전용 
  // 컬렉션에 데이터를 넣고 뺀다고 커밋 시점에 쿼리가 날아가진 않는다.
}
```

## 연관관계 편의 메소드

사실 연관과계의 주인 쪽에만 CRUD를 할 수 있지만, **순수 객체 상태**를 고려 해서 항상 양쪽에 값을 설정하자!!

```java
@Getter
@Setter
@Entity
@Table(name = "member")
public class Member {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "member_id")
  private Long id;
  private String username;
  private int age;

  @ManyToOne
  @JoinColumn(name = "team_id")
  private Team team;

	//연관관계 편의 메소드 (양쪽 모두의 값을 수정해 준다.)
  public void changeTeam(Team team) { 
    this.team.getMembers().remove(this);
    team.getMembers().add(this);
    this.team = team;
  }
}

```

# 다양한 연관관계 매핑

## 다중성

- 다대일 : @ManyToOne
- 일대다 : @OneToMany
- 일대일 : @OneToOne
- 다대다 : @ManyToManay

### 다대일 : @ManyToOne [N : 1]

- 단방향
  - N 이 연관관계의 주인
  - 가장 일반적이고 많이 사용하는 연관관계
- 양방향
  - N 이 연관관계의 주인
  - 양쪽을 서로 참조하도록 개발

### 일대다 : @OneToMany [1 : N]

- 단방향
  - **1** 이 연관관계의 주인 하지만 외래키는 **N** 쪽에 있음
  - 객체와 테이블의 차이 때문에 반대편 테이블의 외래 키를 관리하는 **특이한 구조** ( 사용 자제 )
- 양방향
  - 공식저으로는 존재하지 않음.

### 일대일 : @OneToOne [1 : 1]

- 일대일 관계는 그 반대도 일대일
- 관리할 외래키 위치를 선택 가능
- 매핑 방식은 **@ManyToOne 단방향** 매핑과 유사
  - 외래키가 있는 곳이 연관관계의 주인, 반대편은 **mappedBy** 적용

### 다대다 : @ManyToMany [N : M]

- 이 방식을 사용할 경우 자동으로 **매핑테이블**이 생성됨
- 실제 비즈니스환경의 다양한 요구사항에 유연하게 대응하기 힘든 구조 ( 사용 자제 )
- N : M 은 결국 [1 : N] [M : 1] 두개의 관계로 다시 풀어낼 수 있음.


[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)
[hibernate.org](https://hibernate.org/)
