<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-06-21 21:38:58
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-06-23 10:28:40
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/notes/SQL题目合集.md
-->

# Leetcode SQL 系列问题合集

### 174 - 组合两个表

无论Person表是否有地址信息，都要提供-> OUTER JOIN

这里就用 LEFT JOIN ON 就可以了 

`INNER JOIN` 是求交集 `LEFT JOIN, RIGHT JOIN, FULL JOIN `是横向拓宽表 不懂可以去搜一下

```SQL
SELECT FirstName, LastName, City, State 
FROM Person LEFT JOIN Address
ON Person.PersonId=Address.PersonId;
```

### 175 - 第二高的薪水

有这样两块需要学一下

- 排序就不用说了，`LIMIT A OFFSET B` 取前几个数据加几个偏移， 比如说`LIMIT 1 OFFSET 1` 就是第二搞的薪水

- `IFNULL(a, b)` 如果a为空 返回b

剩下的就正常

```sql
SELECT IFNULL(
(SELECT DISTINCT Salary FROM Employee 
ORDER BY Salary DESC
LIMIT 1 OFFSET 1)
, NULL) AS SecondHighestSalary
```

### 177 - 第N高的薪水

LIMIT 这个参数是不可以现算的，要在外面算好才行，所以要预先定义变量；

```SQL
DECLARE M INT;
SET M = N - 1;
```

剩下的和上一题一样

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  DECLARE M INT;
  SET M = N - 1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT IFNULL(
      (SELECT DISTINCT Salary 
      FROM Employee
      ORDER BY Salary DESC
      LIMIT 1 OFFSET M), NULL)
  );
END
```

### 178 - 分数排名

这个题是窗口函数的应用， 窗口函数除了聚合函数之外，还有以下几个常用的：

`rank, dense_rank, row_number`

`rank`: 并列有重复： 1，2，3，4 -> 1, 1, 1, 4

`dense_rank`: 并列无重复 1，2，3，4 -> 1, 1, 1, 2

`row_number`: 不考虑并列 1，2，3，4 -> 1，2，3，4

除此之外，RANK是sql的关键字，要记得用单引号括起来

```sql
SELECT Score, dense_rank() over(ORDER BY Score DESC) AS `Rank` FROM Scores
```

### 180 - 连续出现的数字

```sql
SELECT DISTINCT NUM FROM (
SELECT Num,COUNT(1) as SerialCount FROM 
(SELECT Id,Num,
row_number() over(order by id) -
ROW_NUMBER() over(partition by Num order by Id) as SerialNumberSubGroup
FROM Logs) as Sub
GROUP BY Num,SerialNumberSubGroup HAVING COUNT(1) >= 3)
```

记住SQL的运行顺序！

`FROM - WHERE - GROUP BY - HAVING - SELECT - DISTINCT - UNION - ORDERED BY`

需要注意说明：当同时含有where子句、group by 子句 、having子句及聚集函数时，执行顺序如下： 

- 执行where子句查找符合条件的数据； 

- 使用group by 子句对数据进行分组；对group by 子句形成的组运行聚集函数计算每一组的值；最后用having 子句去掉不符合条件的组。 

- having子句和where子句都可以用来设定限制条件以使查询结果满足一定的条件限制。
   
- having子句限制的是组，而不是行。where子句中不能使用聚集函数，而having子句中可以。
  

这里整个思路就是按照[link](https://leetcode-cn.com/problems/consecutive-numbers/solution/sql-server-jie-fa-by-neilsons/)的方法来，先按原顺序标序号，再按照id 分组后



