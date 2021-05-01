https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all 에서 연습하기!

![erd](../images/erd.png)



## 구조

```sql
SELECT [DISTINCT] 컬럼, 그룹 함수(컬럼)
FROM 테이블명
[WHERE 조건]
[GROUP BY 그룹대상]
[HAVING 그룹 함수 포함 조건]
[ORDER BY 정렬대상 [ASC/DESC]]
```



## 집계함수

중복을 고려하지 않는다

중복을 제거하려면 distinct 붙이기 => 예 : `select count(distinct city) from customers`



## Join

join의 조건은 on으로

```sql
select *
from customers c join orders o on c.customerid = o.customerid
```

