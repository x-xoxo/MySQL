# DataBase (MySQL)
- 대소문자 구분 X
## 스키마 생성
``` sql
CREATE SCHEMA `new_schema` ;
```
## Table 생성
```sql
CREATE TABLE `shopdb`.`membertbl` (
  `memberID` CHAR(8) NOT NULL,
  `memberName` CHAR(5) NOT NULL,
  `memberAddress` CHAR(20) NULL);
```
```sql
CREATE TABLE `shopdb`.`producttbl` (
  `productName` CHAR(4) NOT NULL,
  `cost` INT NULL,
  `makeDate` DATE NULL,
  `company` CHAR(5) NULL,
  `amount` INT NOT NULL,
  PRIMARY KEY (`productName`));
```
## 테이블 삭제  
```sql
DROP TABLE `my testtbl`;
```
## 테이블 검색
```sql
select * from membertbl;
```
```sql
/*테이블 검색*/
select memberName, memberAddress from membertbl;
```
```sql
select * from membertbl where memberName = '토마스';
```
## 속성 이름 변경  
```sql
ALTER TABLE `shopdb`.`membertbl` 
CHANGE COLUMN `memberName` `memberNames` CHAR(5) NOT NULL ;
```
## 속성변경
```sql
ALTER TABLE `shopdb`.`membertbl` 
ADD PRIMARY KEY (`memberID`);
;
```
```sql
UPDATE `shopdb`.`membertbl` SET `memberAddress` = '서울 은평구 증산동' WHERE (`memberID` = 'Edward');
```
## 데이터 추가 (Add Data)  
```sql
INSERT INTO `shopdb`.`membertbl` (`memberID`, `memberName`, `memberAddress`) VALUES ('Thomas', '토마스', '경기 부천시 중동');
INSERT INTO `shopdb`.`membertbl` (`memberID`, `memberName`, `memberAddress`) VALUES ('Edward', '에드워드', '서울 은평구 중산동');
INSERT INTO `shopdb`.`membertbl` (`memberID`, `memberName`, `memberAddress`) VALUES ('Henry', '헨리', '인천 남구 주안동');
INSERT INTO `shopdb`.`membertbl` (`memberID`, `memberName`, `memberAddress`) VALUES ('Gorden', '고든', '경기 성남시 분당구');
```
## 데이터 삭제 (DELETE DATA)
```sql

```
## 테이블에서 데이터 추출해서 데이터 추가
```sql
INSERT INTO `indextbl` SELECT `first_name`,`last_name`,`hire_date` FROM employees LIMIT 500;
/*employees테이블에있는 속성 중에 first_name, last_name, hire_date를 추출해서 indexTBL에 추가 데이터 500개 까지*/
```
## 인덱스 추가  
```sql
create index `idex_indexTBL_firstname` on `indexTBL`(`first_name`);
```

## view 만들기  
```sql
create view uv_memberTBL as SELECT `memberName`, `memberAddress` FROM `memberTBL`;
```

## procedure 만들기 (함수)
```sql
DELIMITER //
create procedure myProc()
begin
	  select * from membertbl where memberName = '토마스';
    select * from producttbl where productName = '냉장고';
end //
DELIMITER ;
```

## procedure 호출  
```sql
call myProc();
```

## Trigger (트리거) 생성  
```sql
DELIMITER //
CREATE TRIGGER trg_deletedMemberTBL -- 트리거 이름
	AFTER DELETE -- 삭제 후에 작동하게 지정
	ON memberTBL -- 트리거를 부착할 테이블
    FOR EACH ROW -- 각 행마다 적용시킴
BEGIN
	-- OLD 테이블의 내용을 백업테이블에 삽입
    INSERT INTO deletedMemberTBL
		VALUES (OLD.memberID, OLD.memberName, OLD.memberAddress, CURDATE() );
END //
DELIMITER ;
```
