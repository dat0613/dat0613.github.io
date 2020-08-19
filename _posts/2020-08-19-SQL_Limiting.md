오늘 업무를 보다가 헤매게 되어 정리해 본다.

```sql
SELECT * FROM table_name LIMIT 5; -- table_name 테이블의 행을 5개만 SELECT
```
SQL의 `LIMIT`절을 사용하면 `SELECT`되는 행의 갯수를 제한하고 불필요한 쿼리의 실행을 방지할 수 있다.


```sql
SELECT * FROM table_name LIMIT 5 OFFSET 0; -- table_name 테이블의 0번째 행부터 5개 행을 SELECT

SELECT * FROM table_name LIMIT 10 OFFSET 80; -- table_name 테이블의 80번재 행 부터 10개 행을 SELECT
```
`LIMIT`와 `OFFSET`을 함께 사용하면 페이징을 구현할 수 있다.


그렇다면 그룹핑된 테이블에서 각 그룹별로 5개의 행을 `SELECT` 하려면 어떻게 해야할까?

예시를 위해 테이블을 날짜별로 그룹핑한 후 날짜별로 점수가 가장높은 유저 5명을 `SELECT`하는 쿼리를 작성해 보겠다.


```sql
SELECT 
  rank_date,
  score, 
  nickname,
  ROW_NUMBER() OVER (PARTITION BY rank_date ORDER BY score DESC) AS row_number
FROM 
  player_ranking;
```
`PARTITION BY`절을 사용해 날짜 별로 그룹핑한 후 `ORDER BY` ~ `DESC` 절로 score의 내림차순으로 정렬 했다.

쿼리문을 실행하면 각 날짜 별로 점수가 높은 유저 순서로 `SELECT` 된다.


```sql
SELECT
  rank_date,
  score, 
  nickname
FROM
 (
  SELECT 
    rank_date,
    score, 
    nickname,
    ROW_NUMBER() OVER (PARTITION BY rank_date ORDER BY score DESC) AS row_number
  FROM 
    player_ranking
) a
WHERE
  row_number <= 5;
```
날짜 별로 내림차순 정렬된 `SELECT`문을 서브 쿼리로 두고 `WHERE`절 에서 row_number가 5이하인 조건을 걸면

내림차순으로 정렬된 결과에서 앞의 5개만 `SELECT`하기 때문에 날짜 별로 점수가 높은 5명의 유저만 `SELECT`할 수 있다.




`LIMIT`절과는 연관성이 없지만 내가 `LIMIT`로 해결하려고 하다가 실패해서 같이 정리했다.

모든 쿼리문은 [postgreSQL]을 기준으로 한다.

[postgreSQL]:https://www.postgresql.org/
