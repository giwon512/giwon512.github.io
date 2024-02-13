---
layout: single
title: Oracle SQL(3) 트랜잭션과 데이터 무결성
categories: [DBMS]
tag: [Oracle, Database]
toc: true
---



# 📖Transaction

여러 쿼리들로 이루어진 논리적 작업의 단위

### 특성

- 원자성 (Atomicity)
    
    트랜잭션과 관련된 일이 모두 수행되거나, 모두 수행되지 않았음을 보장한다. commit, rollback 명령어를 통해 무결성을 보장한다.
    
- 일관성 (Consistency)
    
    허용된 방식으로만 데이터를 변경해야 한다.
    
- 독립성 (Isolation)
    
    트랜잭션 수행 시 서로 끼어들지 못해야 한다.
    
- 지속성 (Durability)
    
    성공적으로 수행된 트랜잭션은 영원히 반영되어야 하고, 문제가 발생해도 원래 상태로 회복이 가능해야 한다.
    

# 📖 데이터 무결성

데이터의 정확성, 일관성, 유효성을 유지하는 것

1) 개체 무결성 

- 중복된 데이터의 방지
- 기본 키의 필드 값은 null이 될 수 없음

2) 도메인 무결성

- 특정 속성 값이 정의된 도메인에 속해야 한다.

3) 참조 무결성

- 참조 관계인 두 테이블은 항상 일관된 값을 유지해야한다.

4) null 무결성

- 특정 속성 값에 null이 올 수 없다는 조건이 주어지면 null이 될 수 없다.

5) 고유 무결성

- 특성 속성 값에 unique 조건이 주어지면 고유한 값을 가진다.

6) 키 무결성 

- 한 릴레이션에는 반드시 키가 하나 이상 존재해야 한다.

### 컬럼의 속성

- NN(Not NULL) : Primary Key
- ND(No Duplicate) : Primary Key, Unique
- NC(No Change) : Foreign Key

## 제약조건 적용해보기

먼저 부서정보를 담는테이블을 만든다.

```sql
create table tblDept(
	deptno char(3), 
	dname varchar2(10));
insert into tbldept values('10', 'sales');
insert into tbldept values('20', 'marketting');
```

![tblDept.png]({{"../../../../assets/images/dbms/sqlTransaction/tblDept.png" | relative_url}})

그 후, 직원 정보를 담는 테이블을 만들고 외래키로 tblDept를 연결해본다.

```sql
create table tblEmp( 
	empno number, 
	ename varchar2(10), 
	hiredate date,
  deptno char(3),
  constraint ft_tblemp_deptno foreign key(deptno) references tbldept(deptno));
```

![foreignErr.png]({{"../../../../assets/images/dbms/sqlTransaction/foreignErr.png" | relative_url}})

### 제약조건 확인 1

외래키로 연결하려는 속성은 고유한 값이 보장되어야 하므로 primary 또는 unique key여야 한다.

```sql
alter table tbldept add constraint pk_tbldept_deptno primary key(deptno);
```

기본키로 바꿔주고 다시 테이블을 만들면 잘 만들어지는 것을 볼 수 있다.

### 제약조건 확인 2

```sql
insert into tblemp values(1, 'hong', sysdate, '10'); #정상실행
insert into tblemp values(1, 'hong', sysdate, '30'); #에러
```

![forErr2.png]({{"../../../../assets/images/dbms/sqlTransaction/forErr2.png" | relative_url}})

연결된 tblDept테이블에 30번 부서는 없기에 에러가 발생한다.

### 제약조건 확인 3

```sql
update tbldept set deptno=100 where deptno=10; 
```

![forErr3.png]({{"../../../../assets/images/dbms/sqlTransaction/forErr3.png" | relative_url}})

이미 참조되고 있는 10번 부서의 번호를 바꿔버리면 에러가 발생한다. 다만 참조되지 않고 있는 20번 부서의 번호는 바꿔도 아무런 문제가 없다.

### 제약조건 확인 4

고유 무결성도 확인해볼 수 있다. 위에서  tblDept의 deptno를 기본키로 바꿔주었는데, 같은 부서 번호를 넣어보자.

```sql
insert into tbldept values('20', 'management');
```

![uniqueErr.png]({{"../../../../assets/images/dbms/sqlTransaction/uniqueErr.png" | relative_url}})

고유 무결성이 지켜짐을 확인할 수 있다.

### 제약조건 확인 5

이번엔 도메인 무결성을 확인해보자.

```sql
create table tblexam(
	id number(3), 
	name varchar2(10), 
	city varchar2(10),
  constraint ck_tblexam_city check(city='seoul' or city='busan'));
```

```sql
insert into tblexam values(1,'a', NULL); #정상실행
insert into tblexam values(2,'b', 'gumi'); #에러
```

![ckErr.png]({{"../../../../assets/images/dbms/sqlTransaction/ckErr.png" | relative_url}})

정해진 도메인 외의 값이 입력되면 에러가 발생하는 것을 볼 수 있다.


### 제약조건 삭제하기

도메인 제약조건을 삭제해보자.

```sql
alter table tblexam drop constraint ck_tblexam_city;
```

위와 같이 제거할 수 있고, 이제는 다른 도시의 이름도 잘 입력이 되는 것을 볼 수 있을 것이다.