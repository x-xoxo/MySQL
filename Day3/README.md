# DataBase (MySQL)  
## 자료형  
|데이터형식|바이트|설명|
|:--:|:--:|:--:|
|BIT(N)|N/8|1~64bit표현/b'0000'형식으로 저장|
|TINYINT|1|정수 저장|
|BOOL| | |
|BOOLEAN|1|정수저장/TINYINT(1)과 동일/0-false그 외-true|
|SMALLINT|2|정수 저장|
|MEDIUMINT|3| |
|INT| | |
|INTIGER|4|정수 저장|
|BIGINT|8|정수 저장|
|FLOAT|4|소숫점 이하 7자리|
|DOUBLE| | |
|REAL|8|소숫점 이하 15자리|

## 문자타입  
|데이터형식|설명|
|:--:|:--:|
|CHAR(n)|고정길이 문자형|
|VARCHAR(n)|가변길이 문자형|
|BINARY(N)|고정길이 이진데이터값|
|VARBINARY(N)|가변길이 이진데이터값|
|TINYTEXT|작은 TEXT값|
|TEXT|TEXT데이터 값|
|MEDIUMTEXT|중간TEXT데이터값|
|LONGTEXT|큰 TEXT 데이터 값(최대 4GB 저장가능)|
|TINYBLOB|작은 BLOB 데이터 값|
|BLOB|BLOB 데이터 값/파일 자체를 저장할때 사용|
|MEDIUMBLOB|중간 BLOB 데이터 값|
|LONGBLOB|큰 BLOB 데이터값(최대 4GB 저장가능)|
|ENUM|열거형 데이터 값|
|SET|최대 64개의 서로 다른 데이터 값 저장|

## 날짜/시간 타입  
|데이터형식|설명|
|:--:|:--:|
|DATE|YYYY-MM-DD 형식|
|TIME|HH:MM:SS 형식|
|DATETIME|YYYY-MM-DD HH:MM:SS형식, 8byte, 문자형|
|TIMESTAMP|YYYY-MM-DD HH:MM:SS형식, 4byte, 숫자형|
|YEAR|YYYY 형식|
|JSON|설명|
## SQL에서 JSON 응용  
```sql
select JSON_OBJECT('name', userName, 'height', height) AS 'JSON 값' from userTBL where height >=180;
set @json=
'{ "userTBL" :
	[
		{"name":"강호동","height":182},
        {"name":"이휘재","height":180},
        {"name":"남희석","height":180},
        {"name":"박수홍","height":183}
	]
}';
```
```sql
select JSON_VALID(@json) as JSON_VALID; // JSON 형식이면 1을 반환 아니면 0 반환
select JSON_SEARCH(@json, 'one', '남희석') as JSON_SEARCH; // 세번째 파라미터에 주어진 문자열의 위치를 반환. 두번째 파라미터는 one 또는 all 중 하나.
select JSON_EXTRACT(@json, '$.userTBL[2].name') as JSON_EXTRACT; // 지정된 위치의 값을 반환
select JSON_INSERT(@json, '$.userTBL[0].mDate', '2019-09-09') as JSON_INSERT; // 새로운 값을 추가한다.
select JSON_REPLACE(@json, '$.userTBL[0].name', '토마스') as JSON_REPLACE; // 지정된 항목의 값을 변경한다.
select JSON_REMOVE(@json, '$.userTBL[0]') as JSON_REMOVE; // 지정된 항목을 삭제함.
```
## 정렬 관련  
```sql
USE cookDB;
SELECT ROW_NUMBER( ) OVER(order by height desc) "키큰 순위", userName, addr, height FROM userTBL;
SELECT ROW_NUMBER( ) OVER(order by height desc, userName asc) "키큰 순위", userName, addr, height FROM userTBL;
SELECT addr, ROW_NUMBER( ) OVER(partition by addr order by height desc, userName asc) "지역별키큰순위", userName, height FROM userTBL;
SELECT DENSE_RANK( ) OVER(order by height desc) "키큰 순위", userName, addr, height FROM userTBL; -- 동률나오면 같은 등수 표기 후 그 다음 등수에 +1
SELECT RANK( ) OVER(order by height desc) "키큰 순위", userName, addr, height FROM userTBL; -- 동률나오면 같은등수 표기 후 그 다음 등수에 같은등수 나온 수만큼 + ex)3등 3명시 그다음 등수 6등
SELECT NTILE(2) OVER(order by height desc) "반번호", userName, addr, height FROM userTBL;
SELECT NTILE(4) OVER(order by height desc) "반번호", userName, addr, height FROM userTBL;
```
## 피벗 (pivot)  
```sql
select uName,
	SUM(case when season='봄' then amount end) as '봄',
    SUM(case when season='여름' then amount end) as '여름',
    SUM(case when season='가을' then amount end) as '가을',
    SUM(case when season='겨울' then amount end) as '겨울'
from pivotTest group by uName;
```
## 형변환  
```sql
SELECT AVG(amount) AS '평균 구매 개수' from buyTBL;
SELECT CAST(AVG(amount) as signed integer) as '평균 구매 개수' from buyTBL;
SELECT CONVERT(AVG(amount), signed integer) as '평균 구매 개수' from buyTBL;
```
## 파일  
```sql
create database movieDB;
use movieDB;
create table movieTBL
(
	movie_id		int,
    movie_title		varchar(30),
    movie_director	varchar(20),
    movie_star		varchar(20),
    movie_script	longtext,
    movie_film		longblob
) default charset=utf8mb4;
insert into movieTBL values (1, '쉰들러 리스트', '스필버그', '리암 니슨', load_file('D:/MySQL_Work/Movies/Schindler.txt'), load_file('D:/MySQL_Work/Movies/Schindler.mp4'));
insert into movieTBL values (2, '쇼생크 탈출', '프랭크 다라본트', '팀 로빈스', load_file('D:/MySQL_Work/Movies/Shawshank.txt'), load_file('D:/MySQL_Work/Movies/Shawshank.mp4'));
insert into movieTBL values (3, '라스트 모히칸', '마이클 만', '다니엘 데이 루이스', load_file('D:/MySQL_Work/Movies/Mohican.txt'), load_file('D:/MySQL_Work/Movies/Mohican.mp4'));
select * from movieTBL;
```
___
### 파일 복사붙혀넣기
```sql
select movie_script from movieTBL where movie_id=1 into outfile 'D:/MySQL_Work/Movies/Schindler_out.txt' lines terminated by '\\n';
/* lines terminated by '\\n' : 줄바꿈 문자도 그대로 저장하기 위한 옵션 */
select movie_film from movieTBL where movie_id=3 into dumpfile 'D:/MySQL_Work/Movies/Mohican_out.mp4';
```
## 조인 (JOIN)  
> 2개 이상의 테이블을 묶어서 하나의 결과 테이블을 만드는 것  
### 내부조인 (INNER JOIN)  
```sql
use cookDB;
select * from buyTBL inner join userTBL on buyTBL.userID = userTBL.userID where buyTBL.userID = 'KYM';
select userID, userName, prodName, addr, concat(mobile1, mobile2) as '연락처' 
from buyTBL inner join userTBL on buyTBL.userID = userTBL.userID; -- Error userID가 어느 테이블의 속성을 말하는지 명시해줘야함
select buyTBL.userID, userName, prodName, addr, concat(mobile1, mobile2) as '연락처' 
from buyTBL inner join userTBL on buyTBL.userID = userTBL.userID;
```
### 외부조인 (OUTER JOIN)  

## 내장 함수들
```sql
select if (100>200, '참이다', '거짓이다'); -- 수식1이 NULL이 아니면 수식1 반환, NULL이면 수식2반환
select ifnull(NULL, '널이군요'), ifnull(100, '널이군요');
/* nullif는 수식1,2가 다르면 null 같으면 수식 1 반환 */
select nullif(100,100), ifnull(200,100);
select	case 10
		when 1 	then	'일'
        when 5 	then	'오'
        when 10	then	'십'
        else '모름'
end;
select ascii('A'), char(65);
select bit_length('abc'), char_length('abc'), length('abc');
select bit_length('가나다'), char_length('가나다'), length('가나다');
```
___
```sql
select concat_ws('/','2020','01','01');
/*
    elt -> 첫번째 인수 위치에 적힌 문자열 반환
    field -> 찾을 문자열의 위치를 찾아 반환 찾는 문자열 없으면 0을 반환
    find_in_set -> 찾을 문자열을 문자열 리스트에서 찾아 위치를 반환
    instr -> 기준문자열에서 부문문자열을 찾아 그 시작위치 반환
    locate -> instr함수와 동일하지만 파라미터의 순서가 반대
*/
select elt(2, '하나', '둘', '셋'), field('둘', '하나', '둘', '셋'), find_in_set('둘', '하나,둘,셋'), instr('하나둘셋','둘'), locate('둘','하나둘셋');
select format(123456.123456, 4);
select bin(31), hex(31), oct(31);
select insert('abcdefghi', 3, 4, '@@@@'), insert('abcdefghi', 3, 2, '@@@@');
select left('abcdefghi', 3), right('abcdefghi', 3);
select lower('abcdEFGH'), upper('abcdEFGH');
select lpad('쿡북', 5, '##'), rtrim('쿡북    ');
select trim('    쿡북    '), trim(both 'ㅋ' from 'ㅋㅋㅋ재미있어요.ㅋㅋㅋ');
```
___
```sql
select replace('IT 쿡북 MySQL', '쿡북', 'CookBook');
select reverse('MySQL');
select concat('IT', space(10), 'CookBook MySQL');
select substring('대한민국만세', 3, 2);
/*
	문자열에서 구분자가 왼쪽부터 '횟수' 인자에 적힌 숫자번째 나오면 그 이후의 문자열을 버리고 앞에있는 문자열만 출력
    횟수가 음수이면 오른쪽부터 세어 왼쪽은 남은 문자열을 버리고 출력한다.
*/
select substring_index('www.mysql.com', '.', 2), substring_index('www.mysql.com', '.', -2);
select abs(-100);
/*     ceiling-올림   floor-내림   round-반올림 */
select ceiling(4.7), floor(4.7), round(4.7);
/* conv - 첫번째 인자의 값은 두번째인자의 값이고 세번째 인자의 진법으로 값을 출력*/
select conv('AA', 16, 2), conv(100, 10, 8);
select degrees(pi()), radians(180); -- degrees : 라디안값 to 각도 / radians : 각도 to radians
select mod(157, 10), 157 % 10, 157 mod 10; -- 나머지 연산자 사용법
select pow(2, 3), sqrt(9); -- pow : 첫번째 인자값을 두번째 인자값 만큼 제곱함 / sqrt : 인자에 루트를 하여 값을 반환
```
___
```sql
select rand(), floor(1+(rand()*(6-1)));
select sign(100), sign(0), sign(-100.123);
select truncate(12345.12345, 2), truncate(12345.12345, -2);
select adddate('2020-01-01', interval 31 day), adddate('2020-01-01', interval 1 month);
select subdate('2020-01-01', interval 31 day), subdate('2020-01-01', interval 1 month);
select addtime('2020-01-01 23:59:59', '1:1:1'), addtime('15:00:00', '2:10:10');
select subtime('2020-01-01 23:59:59', '1:1:1'), subtime('15:00:00', '2:10:10');
select year(curdate()), month(current_date()), dayofmonth(current_date);
select hour(curtime()), minute(current_time()), second(current_time), microsecond(current_time());
select date(now()), time(now());
select datediff('2023-01-01',now()), timediff('23:59:59', '12:11:10');
```
___
```sql
/*
    dayofweek : 요일 (1 - 일, ~ 7 - 토)
    monthname : 월의 영문이름
    dayofyear : 1년 중 몇 번째날인지 반환
*/
select dayofweek(curdate()), monthname(curdate()), dayofyear(curdate());
select last_day('2020-02-01');
select makedate(2020, 32);
select maketime(12, 11, 10);
select period_add(202001, 11), period_diff(202001, 201812);
select quarter('2020-07-07');
select time_to_sec('12:11:10');
```
___
```sql
select current_user(), database();
use cookDB;
select * from userTBL;
select found_rows(); -- 바로 앞의 SELECT 문에서 조회된 행의 개수 반환
update buyTBL set price=price*2;
select row_count(); -- 바로 앞의 INSERT, UPDATE, DELETE 문에서 삽입 수정 삭제된 행의 개수 반환
select sleep(5);
select '5초후에 이게 보여요';
```
# JSON  
## 기본 형태 (Default Format)  
**"key":"value"**
```json
JSON
{
    "name":"강호동","height":180,
    "name":"유재석","height":178,
    "name":"박수홍","height":183,
}
```