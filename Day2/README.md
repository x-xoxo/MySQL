# DataBase (My SQL)  
- DDL (Data Definition Language)
    - create
    - drop
    - alter
    - truncate
    - rollback
    - commit
- DML (Data Manipulation Language)
    - select
    - insert
    - delete
    - update
- DCL (Data Control Language)
    - grant
    - revoke
    - deny

```sql
DROP DATABASE IF EXISTS cookDB; -- 만약 cookDB가 존재하면 우선 삭제한다.
CREATE DATABASE cookDB;

USE cookDB;
CREATE TABLE userTBL -- 회원 테이블
( userID  	CHAR(8) NOT NULL PRIMARY KEY, -- 사용자 아이디(PK)
  userName    	VARCHAR(10) NOT NULL, -- 이름
  birthYear   INT NOT NULL,  -- 출생년도
  addr	  	CHAR(2) NOT NULL, -- 지역(경기,서울,경남 식으로 2글자만입력)
  mobile1	CHAR(3), -- 휴대폰의 국번(011, 016, 017, 018, 019, 010 등)
  mobile2	CHAR(8), -- 휴대폰의 나머지 전화번호(하이픈제외)
  height    	SMALLINT,  -- 키
  mDate    	DATE  -- 회원 가입일
);
CREATE TABLE buyTBL -- 회원 구매 테이블
(  num 		INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번(PK)
   userID  	CHAR(8) NOT NULL, -- 아이디(FK)
   prodName 	CHAR(6) NOT NULL, --  물품명
   groupName 	CHAR(4)  , -- 분류
   price     	INT  NOT NULL, -- 단가
   amount    	SMALLINT  NOT NULL, -- 수량
   FOREIGN KEY (userID) REFERENCES userTBL(userID)
);

INSERT INTO userTBL VALUES('YJS', '유재석', 1972, '서울', '010', '11111111', 178, '2008-8-8');
INSERT INTO userTBL VALUES('KHD', '강호동', 1970, '경북', '011', '22222222', 182, '2007-7-7');
INSERT INTO userTBL VALUES('KKJ', '김국진', 1965, '서울', '019', '33333333', 171, '2009-9-9');
INSERT INTO userTBL VALUES('KYM', '김용만', 1967, '서울', '010', '44444444', 177, '2015-5-5');
INSERT INTO userTBL VALUES('KJD', '김제동', 1974, '경남', NULL , NULL      , 173, '2013-3-3');
INSERT INTO userTBL VALUES('NHS', '남희석', 1971, '충남', '016', '66666666', 180, '2017-4-4');
INSERT INTO userTBL VALUES('SDY', '신동엽', 1971, '경기', NULL , NULL      , 176, '2008-10-10');
INSERT INTO userTBL VALUES('LHJ', '이휘재', 1972, '경기', '011', '88888888', 180, '2006-4-4');
INSERT INTO userTBL VALUES('LKK', '이경규', 1960, '경남', '018', '99999999', 170, '2004-12-12');
INSERT INTO userTBL VALUES('PSH', '박수홍', 1970, '서울', '010', '00000000', 183, '2012-5-5');

INSERT INTO buyTBL VALUES(NULL, 'KHD', '운동화', NULL   , 30,   2);
INSERT INTO buyTBL VALUES(NULL, 'KHD', '노트북', '전자', 1000, 1);
INSERT INTO buyTBL VALUES(NULL, 'KYM', '모니터', '전자', 200,  1);
INSERT INTO buyTBL VALUES(NULL, 'PSH', '모니터', '전자', 200,  5);
INSERT INTO buyTBL VALUES(NULL, 'KHD', '청바지', '의류', 50,   3);
INSERT INTO buyTBL VALUES(NULL, 'PSH', '메모리', '전자', 80,  10);
INSERT INTO buyTBL VALUES(NULL, 'KJD', '책'    , '서적', 15,   5);
INSERT INTO buyTBL VALUES(NULL, 'LHJ', '책'    , '서적', 15,   2);
INSERT INTO buyTBL VALUES(NULL, 'LHJ', '청바지', '의류', 50,   1);
INSERT INTO buyTBL VALUES(NULL, 'PSH', '운동화', NULL   , 30,   2);
INSERT INTO buyTBL VALUES(NULL, 'LHJ', '책'    , '서적', 15,   1);
INSERT INTO buyTBL VALUES(NULL, 'PSH', '운동화', NULL   , 30,   2);

SELECT * FROM userTBL;
SELECT * FROM buyTBL;
```
## ALTER  

```sql
create table testTBL1 (id int, userName char(3), age int);
insert into testTBL1 values (1, '뽀로로', 16);
insert into testTBL1 (id, userName) values (2, '크롱');
insert into testTBL1 (userName, age, id) values ('루피', 14, 3);
create table testTBL2 (id int auto_increment primary key, userName char(3), age int );
insert into testTBL2 values (NULL, '에디', 15);
insert into testTBL2 values (NULL, '포비', 12);
insert into testTBL2 values (NULL, '통통이', 11);
select * from testTBL2;
alter table testTBL2 auto_increment=100;
insert into testTBL2 values (NULL, '패티', 13);
select * from testTBL2;
```

## SET
```sql
create table testTBL3 (id int auto_increment primary key, userName char(3), age int);
alter table testTBL3 auto_increment=1000;
set @@auto_increment_increment=3;
insert into testTBL3 values (NULL, '우디', 20);
insert into testTBL3 values (NULL, '버즈', 18);
insert into testTBL3 values (NULL, '제시', 19);
select * from testTBL3;

insert into testTBL3 values (null, '토이', 17), (null, '스토리', 18), (null, '무비', 19);
select * from testTBL3;
```

## 함수(procedure)  
| 함수명 | 설명 |
|:--:|:--:|
| AVG() | 평균 |
| MIN() | 최소값|
| MAX() | 최대값|
| COUNT() | 갯수|
| COUNT(DISTINCT) | 중복은 1개만 인정한 갯수 |
| STDEV() | 표준편차 |
| VAR_SAMP() | 분산 |


## WHERE 절  
```sql
SELECT * FROM userTBL WHERE userName = '강호동';
```

```sql
SELECT userID, userName FROM userTBL WHERE birthYear >= 1970 and height >=182;
```

```sql
SELECT userName, height from userTBL where height>= 180 and height <=182;
select userName, height from userTBL where height between 180 and 182;
select userName, addr from userTBL where addr='경남' or addr='충남' or addr='경북';
select userName, addr from userTBL where addr in ('경남','충남','경북');
select userName, height from userTBL where userName like '김%';
select userName, height from userTBL where userName like '_경규';
select userName, height from userTBL where height > 177;
```

## 그룹화 (GROUP BY)  
```sql
select userID, amount from buyTBL order by userID;
select userID, SUM(amount) from buyTBL group by userID;
select userID as '사용자 아이디', SUM(amount) as '총 구매개수' from buyTBL group by userID;
select userID as '사용자 아이디', SUM(price*amount) as '총 구매액' from buyTBL group by userID;
```
### HAVING 절
```sql
select userID as '사용자', SUM(price*amount) as '총구매액' from buyTBL group by userID;
select userID as '사용자', SUM(price*amount) as '총구매액' from buyTBL where SUM(price*amount) > 1000 group by userID; -- Error
select userID as '사용자', SUM(price*amount) as '총구매액' from buyTBL group by userID having SUM(price*amount) > 1000;
select userID as '사용자', SUM(price*amount) as '총구매액' from buyTBL group by userID having SUM(price*amount) > 1000 order by SUM(price*amount);
```
### WITH ROLLUP
```sql
select num, groupName, SUM(price*amount) as '비용' from buyTBL group by groupName, num with rollup;
select groupName, SUM(price*amount) as '비용' from buyTBL group by groupName with rollup;
```

## Example  
```sql
select avg(amount) as '평균 구매 개수' from buyTBL;
select userID, avg(amount) as '평균 구매 개수' from buyTBL group by userID;
select userName, MAX(height), MIN(height) from userTBL;
select userName, MAX(height), MIN(height) from userTBL group by userName;
select userName, height from userTBL where height = (select MAX(height) from userTBL) or height = (select MIN(height) from userTBL);
select COUNT(*) from userTBL;
select COUNT(mobile1) as '휴대폰이 있는 사용자' from userTBL;
```

```sql
create table memberTBL (select userID, userName, addr from userTBL limit 3);
alter table memberTBL add constraint pk_memberTBL primary key (userID);
select * from memberTBL;
insert into memberTBL values('KHD', '강후덜', '미국'); -- pk 중복시 오류
insert into memberTBL values('LSM', '이상민', '서울');
insert into memberTBL values('KSJ', '김성주', '경기');
insert ignore into memberTBL values('KHD', '강후덜', '미국'); -- pk 중복시 무시
insert ignore into memberTBL values('LSM', '이상민', '서울');
insert ignore into memberTBL values('KSJ', '김성주', '경기');
select * from memberTBL;
insert into memberTBL values('KHD', '강후덜', '미국') on duplicate key update userName='강후덜', addr='미국'; -- pk 중복 시 data 수정
insert into memberTBL values('DJM', '동짜몽', '일본') on duplicate key update userName='동짜몽', addr='일본';
select * from memberTBL;
```

## 서브 쿼리  
```sql

select userName, height from userTBL where height > (select height from userTBL WHERE userName = '김용만');-- userTBL의 height 중 userTBL의 userName 김용만의 height 보다 크면 userName, height 선택
select userName, height from userTBL where height >= (select height from userTBL where addr = '경기'); -- Error 비교대상 여러개
select userName, height from userTBL where height >= any (select height from userTBL where addr = '경기');
select userName, height from userTBL where height = any (select height from userTBL where addr = '경기');
select userName, height from userTBL where height in (select height from userTBL where addr = '경기');
```

## 데이터 정렬 (ORDER BY)  
```sql
select userName, mDate from userTBL order by mDate;
select userName, mDate from userTBL order by mDate desc;
select userName, height from userTBL order by height desc, userName asc;
select addr from userTBL;
select addr from userTBL order by addr;
select distinct addr from userTBL; -- distinct : 중복 제거
```

## 데이터 갯수 제한 (LIMIT)  
```sql
select emp_no, hire_date from employees order by hire_date asc;
select emp_no, hire_date from employees order by hire_date asc limit 5;
select emp_no, hire_date from employees order by hire_date asc limit 0, 5; -- LIMIT 5 OFFSET 0 과 동일
```

## 데이터 수정 (UPDATE)  
```sql
update testTBL4 set Lname = '없음' where Fname = 'Kyoichi';
select * from testTBL4 where Fname = 'Kyoichi';
update buyTBL set price = price * 1.5;
select * from buyTBL;
```

## 데이터 삭제 (DELETE)  
```sql
select * from testTBL4 where Fname = 'Aamer'; -- Fname이 Aamer인 데이터 조회
delete from testTBL4 where Fname = 'Aamer' limit 5; -- Fname이 Aamer인 데이터 5개까지 삭제
delete from testTBL4 where Fname = 'Aamer'; -- Fname이 Aamer인 데이터 전부 삭제
```
```sql
create table bigTBL1 (select * from employees.employees);
create table bigTBL2 (select * from employees.employees);
create table bigTBL3 (select * from employees.employees);

delete from bigTBL1; -- 테이블 내용 전체 삭제 복구가능 / 느림
drop table bigTBL2; -- 테이블 자체를 삭제
truncate table bigTBL3; -- 테이블 내용 전체 삭제 복구불가 / 빠름
```


## 참조 테이블 생성  
```sql
-- -----------------------------------------------------
-- Table `modelDB`.`buyTBL`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `modelDB`.`buyTBL` (
  `userName` CHAR(3) NOT NULL,
  `prodName` CHAR(3) NOT NULL,
  `price` INT NOT NULL,
  `amount` INT NOT NULL,
  INDEX `fk_buyTBL_UserTBL_idx` (`userName` ASC) VISIBLE,
  CONSTRAINT `fk_buyTBL_UserTBL`
    FOREIGN KEY (`userName`)
    REFERENCES `modelDB`.`UserTBL` (`userName`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;
```

## 테이블 복사  
```sql
create table buyTBL2 (select * from buyTBL);
select * from buyTBL2;

create table buyTBL3 (select userID, prodName from buyTBL);
select * from buyTBL3;
```

## 테이블 생성 (CREAT TABLE)  
```sql
create table testTBL4 (id int, Fname varchar(50), Lname varchar(50));
insert into testTBL4 select emp_no, first_name, last_name from employees.employees;

create table testTBL5 (select emp_no, first_name, last_name from employees.employees);
select * from testTBL5 limit 3;

create table testTBL6 (select emp_no as id, first_name as Fname, last_name as Lname from employees.employees);
select * from testTBL6 limit 3;
```