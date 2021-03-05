# DataBase (MySQL)  
## 제약조건  
### 외래키 (FOREIGN KEY)  
```sql
drop table if exists buyTBL;
create table buyTBL
( num int auto_increment not null primary key,
  userID char(8) not null,
  prodName char(8) not null,
  constraint FK_userTBL_buyTBL foreign key(userID) references userTBL(userID)
);
```
___
```sql
drop table if exists buyTBL;
create table buyTBL
( num int auto_increment not null primary key,
  userID char(8) not null,
  prodName char(8) not null
);
alter table buyTBL
	add constraint FK_userTBL_buyTBL
    foreign key (userID)
    references userTBL(userID);
```
___
```sql
alter table buyTBL
	drop foreign key FK_userTBL_buyTBL; -- 외래키 제거
```
___
```sql
alter table buyTBL
	add constraint FK_userTBL_buyTBL
    foreign key (userID)
    references userTBL(userID)
    on update cascade;
```
___
```sql
set foreign_key_checks = 0; -- 0 : 외래키 체크 OFF 1 : 외래키 체크 ON
```
### UNIQUE  
```sql
drop table if exists buyTBL, userTBL;
create table userTBL
( userID char(8) not null primary key,
  userName varchar(10) not null,
  birthYear int not null,
  email char(30) null unique
);
```
___
```sql
drop table if exists buyTBL, userTBL;
create table userTBL
( userID char(8) not null primary key,
  userName varchar(10) not null,
  birthYear int not null,
  email char(30) null,
  constraint AK_email unique(email)
);
```
### DEFAULT  
```sql
drop table if exists userTBL;
create table userTBL
( userID	char(8) not null primary key,
  userName	varchar(10) not null,
  birthYear int not null default -1,
  addr		char(2) not null default '서울',
  mobile1	char(3) null,
  mobile2	char(8) null,
  height	smallint null default 170,
  mDate		date null
);
```
___
```sql
drop table if exists userTBL;
create table userTBL
( userID	char(8) not null primary key,
  userName	varchar(10) not null,
  birthYear int not null,
  addr		char(2) not null,
  mobile1	char(3) null,
  mobile2	char(8) null,
  height	smallint null,
  mDate		date null
);
alter table userTBL
	alter column birthYear set default -1;
alter table userTBL
	alter column addr set default '서울';
alter table userTBL
	alter column height set default 170;
insert into userTBL values('YBJ', '유병재', default, default, '010', '12345678', default, '2019.12.12');
-- 열 이름이 명시되지 않으면 DEFAULT로 설정된 값을 자동 입력한다
insert into userTBL(userID, userName) values('PNR', '박나래');
-- 열 이름이 명시되면 DEFAULT로 설정된 값은 무시된다.
insert into userTBL values('WB', '원빈', 1982, '대전', '010', '98765432', 176, '2020.5.5');
```