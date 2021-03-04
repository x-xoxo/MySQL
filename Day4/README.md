# DataBase (MySQL)  
## 외래키를 가진 테이블 생성  
```sql
use cookDB;
create table stdTBL
(	stdName	varchar(10) not null primary key,
	addr		char(4) not null
);
create table clubTBL
(	clubName	varchar(10) not null primary key,
	roomNo		char(4) not null
);
create table stdclubTBL
(	num int auto_increment not null primary key,
	stdName		varchar(10) not null,
    clubName	varchar(10) not null,
    foreign key(stdName) references stdTBL(stdName),
    foreign key(clubName) references clubTBL(clubName)
);
```
## 3개 이상의 이너조인 사용 예제  
```sql
select S.stdName, S.addr, C.clubName, C.roomNo from stdTBL S inner join stdclubTBL SC on S.stdName = SC.stdName 
inner join clubTBL C on SC.clubName = C.clubName order by S.stdName;
select C.clubName, C.roomNo, S.stdName, S.addr from stdTBL S inner join stdclubTBL SC on SC.stdName = S.stdName
inner join clubTBL C on SC.clubName = C.clubName order by C.clubName;
```
## UNION  
```sql
select S.stdName, S.addr, C.clubName, C.roomNo from stdTBL S left outer join stdclubTBL SC on S.stdName = SC.stdName
left outer join clubTBL C on SC.clubName = C.clubName
UNION
select S.stdName, S.addr, C.clubName, C.roomNo from stdTBL S left outer join stdclubTBL SC on S.stdName = SC.stdName
right outer join clubTBL C on SC.clubName = C.clubName;
```
## 외부조인  
> 조인조건을 만족하지 않는 행까지 포함하여 출력하는 조인
### Syntax
```sql
select colList from firstTBL (left|right) outer join secondTBL on condition where condition;
```
## 자체조인  
```sql
create table empTBL (emp char(3), manager char(3), empTel varchar(8));
insert into empTBL values('나사장', null, '0000');
insert into empTBL values('김재무', '나사장', '2222');
insert into empTBL values('김부장', '김재무', '2222-1');
insert into empTBL values('이부장', '김재무', '2222-2');
insert into empTBL values('우대리', '이부장', '2222-2-1');
insert into empTBL values('지사원', '이부장', '2222-2-2');
insert into empTBL values('이영업', '나사장', '1111');
insert into empTBL values('한과장', '이영업', '1111-1');
insert into empTBL values('최정보', '나사장', '3333');
insert into empTBL values('윤차장', '최정보', '3333-1');
insert into empTBL values('이주임', '윤차장', '3333-1-1');
select A.emp as '부하직원', B.emp as '직속상관', B.empTel as '직속상관연락처'
	from empTBL A inner join empTBL B on A.manager = B.emp where A.emp = '우대리';
```
## SQL Logic  
### if  
```sql
drop procedure if exists ifProc; -- 기존에 만든 procedure가 있으면 삭제
DELIMITER $$
create procedure ifProc()
begin
	declare var1 int;
    set var1 = 100;
    
    if var1 = 100 then
		select '100입니다.';
	else
		select '100이 아닙니다.';
	end if;
end $$
DELIMITER ;
```
```sql
drop procedure if exists ifProc2;
DELIMITER //
create procedure ifProc2()
begin
	declare hireDATE DATE; -- 입사일
    declare curDATE DATE; -- 현재 날짜
    declare days int; -- 근무한 일수
    
    select hire_date into hireDATE
		from employees.employees where emp_no = 10001;
	
    set curDATE = current_date();
    set days = datediff(curDATE, hireDATE);
    
    if (days/365) >= 5 then
		select concat('입사한지 ', days, '일이나 지났습니다. 축하합니다!') as '메시지';
	else
		select '입사한지 ' + days + '일밖에 안되었네요 열심히 일하세요.' as '메시지';
	end if;
end //
DELIMITER ;
CALL ifProc2();
```
```sql
drop procedure if exists ifProc3;
DELIMITER //
create procedure ifProc3()
begin
	declare point int;
    declare credit char(1);
    
    set point = 77;
    
    if point >= 90 then set credit = 'A';
    elseif point >= 80 then set credit = 'B';
    elseif point >= 70 then set credit = 'C';
    elseif point >= 60 then set credit = 'D';
    else set credit = 'F';
    end if;
    
    select concat('취득점수==>', point), concat('학점==>', credit);
end //
DELIMITER ;
CALL ifProc3();
```
### case  
```sql
drop procedure if exists caseProc;
DELIMITER //
create procedure caseProc()
begin
	declare point int;
    declare credit char(1);
    
    set point = 77;
    
    case
        when point >= 90 then set credit = 'A';
        when point >= 80 then set credit = 'B';
        when point >= 70 then set credit = 'C';
        when point >= 60 then set credit = 'D';
        else set credit = 'F';
    end case;
    
    select concat('취득점수==>', point), concat('학점==>', credit);
end //
DELIMITER ;
CALL caseProc();
```
### while  
```sql
drop procedure if exists whileProc;
DELIMITER //
create procedure whileProc()
begin
	declare i int;
    declare hap int;
    
    set i = 1;
    set hap = 0;
    
    while (i<=100) do
		set hap = hap+i;
        set i = i+1;
	end while;
    
    select hap;
end //
DELIMITER ;
call whileProc();
```
___
```sql
drop procedure if exists whileProc2;
DELIMITER //
create procedure whileProc2()
begin
	declare i int;
    declare hap int;
    
    set i = 1;
    set hap = 0;
    myWhile: while (i <= 100) do -- while문에 label 지정
		if (i%7 = 0) then
			set i = i + 1;
            iterate myWhile;
		end if;
        set hap = hap + i;
        if (hap > 1000) then
			leave myWhile; -- label 탈출 즉, while문 종료
		end if;
        set i = i + 1;
        end while;
        
        select hap;
end //
DELIMITER ;
call whileProc2();
```
## 제약조건
### 기본키 제약조건  
- 기본키 제약을 설정하면 해당열은 중복된 값이 저장 될 수 없고, NULL값도 저장될 수 없다.
    * 기본키가 없는 테이블이 규칙에 어긋나는 테이블은 아니다.
- 고유한 클러스터형 인덱스가 만들어진다.
- 개체 무결성
### 외래키 제약조건  
- 참조무결성을 위해 사용한다.
- 외래키 제약이 설정되면, 해당 열에 입력할 수 있는 값은 참조되는 열에 이미 존재하는 값이다.
    * 외래키는 다른 릴레이션의 기본키와 연결이 된다.
- NOT NULL을 선언하지 않으면 NULL 입력 가능
- 외래키를 설정하면 참조되는 테이블의 기본키 변경시 제약을 받게된다.
     ex) DELETE 문이 REFERENCE 제약조건 '..' 과 충돌했습니다. 라면서 삭제가 안됨!!!
- 제약을 받지않게 하는 법
    : CASECADE (참조되는 테이블의 기본키 변경/삭제시 함께 변경/삭제되도록 설정)
    : SET NULL (변경/삭제시 NULL로 셋팅)
    : SET DEFAULT (변경/삭제시 정해놓은 기본값으로 셋팅)
### UNIQUE 제약조건  
-  중복되는 값이 없어야하는 제약조건, BUT NULL은 한개는 허용한다.
- 고유한 비클러스터 형 인덱스가 만들어진다.
- FOREIGN KEY 제약의 참조 대상 열이 될 수 있다.
### DEFAULT 제약조건  
- INSERT문 실행 시 특정열에 값을 넣지 않으면 자동으로 셋팅해놓은 값으로 저장해준다.
- UPDATE는 DEFAULT제약에 영향이 없음
### CHECK 제약조건  
INSERT/UPDATE시에 설정된 조건에 부합하지 않으면 입력/수정 거부하는 제약조건