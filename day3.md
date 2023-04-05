## 	1407. Top Travellers  

Table: Users, Rides  
| Column Name   | Type    |
| ------------- | ------- |
| id            | int     |
| name          | varchar |

id is the primary key for this table.  
name is the name of the user.  
| Column Name   | Type    |
| ------------- | ------- |
| id            | int     |
| user_id       | int     |
| distance      | int     |

id is the primary key for this table.  
user_id is the id of the user who traveled the distance "distance".  
Write an SQL query to report the distance traveled by each user.  
Return the result table ordered by travelled_distance in descending order, if two or more users traveled the same distance, order them by their name in ascending order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Users table:  

| id   | name      |
| ------------- | ------- |
| 1    | Alice     |
| 2    | Bob       |
| 3    | Alex      |
| 4    | Donald    |
| 7    | Lee       |
| 13   | Jonathan  |
| 19   | Elvis     |

Rides table:  
| id   | user_id  | distance |
| ------------- | ------- |------- |
| 1    | 1        | 120      |
| 2    | 2        | 317      |
| 3    | 3        | 222      |
| 4    | 7        | 100      |
| 5    | 13       | 312      |
| 6    | 19       | 50       |
| 7    | 7        | 120      |
| 8    | 19       | 400      |
| 9    | 7        | 230      |

Output:   

| name     | travelled_distance |
| ------------- | ------- |
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |

A:    
```
select
    b.name,
    case
        when sum(distance) is null then 0
        else sum(distance)
    end as travelled_distance
from
    users b left join rides a
on b.id = a.user_id
group by
    b.id
order by
    travelled_distance desc,
    name;
```
*at first I used rides JOIN users, which caused the id 4 Donald to be missing in the result, and had to change the sequence of the tables.  
After that the result got wrong because the distance for Donald shows NULL instead of 0. Had to add CASE WHEN to tackle this.*


## 1158. Market Analysis I(Medium)   

Table: Users, Orders, Items   

| Column Name    | Type    |
| ------------- | ------- |
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |

user_id is the primary key of this table.  
This table has the info of the users of an online shopping website where users can sell and buy items.  

| Column Name   | Type    |
| ------------- | ------- |
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |

order_id is the primary key of this table.  
item_id is a foreign key to the Items table.  
buyer_id and seller_id are foreign keys to the Users table.  

| Column Name   | Type    |
| ------------- | ------- |
| item_id       | int     |
| item_brand    | varchar |

item_id is the primary key of this table.  
Write an SQL query to find for each user, the join date and the number of orders they made as a buyer in 2019.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Users table:  

| user_id | join_date  | favorite_brand |
| ------------- | ------- | ------- |
| 1       | 2018-01-01 | Lenovo         |
| 2       | 2018-02-09 | Samsung        |
| 3       | 2018-01-19 | LG             |
| 4       | 2018-05-21 | HP             |

Orders table:  

| order_id | order_date | item_id | buyer_id | seller_id |
| ------------- | ------- | ------------- | ------- | ------- |
| 1        | 2019-08-01 | 4       | 1        | 2         |
| 2        | 2018-08-02 | 2       | 1        | 3         |
| 3        | 2019-08-03 | 3       | 2        | 3         |
| 4        | 2018-08-04 | 1       | 4        | 2         |
| 5        | 2018-08-04 | 1       | 3        | 4         |
| 6        | 2019-08-05 | 2       | 2        | 4         |

Items table:  

| item_id | item_brand |
| ------------- | ------- |
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |

Output:   

| buyer_id  | join_date  | orders_in_2019 |
| ------------- | ------- |------- |
| 1         | 2018-01-01 | 1              |
| 2         | 2018-02-09 | 2              |
| 3         | 2018-01-19 | 0              |
| 4         | 2018-05-21 | 0              |

A:  
```
select
    a.user_id as buyer_id,
    a.join_date,
    count(
      case
        when year(b.order_date)='2019' then a.user_id
        else NULL
      end) as orders_in_2019
from
    users a
left join
    orders b
on a.user_id = b.buyer_id
group by a.user_id;
```
*No need to bother item table, didn’t use in this question. At first I tried using*
```
case
	when year(b.order_date)='2019' then count(a.user_id)
	else 0
end as orders_in_2019
```
*soon realize it will not work because you have to count for each order date, so I refer to other people's solution and change it to:*
```
count(
	case
		when year(b.order_date)='2019' then a.user_id
		else 0
	end) as orders_in_2019

```
*but it still gives a wrong answer due to the ELSE 0, have to change it to NULL or just delete the whole ELSE part to get correct answer. Although the answer got accepted but it beats at very low %. Here are alternate answer:*
```
SELECT user_id AS buyer_id , join_date,
CASE WHEN T2.order_id IS NULL  THEN 0 
ELSE COUNT(T2.order_id)  END AS orders_in_2019
FROM 
Users T1
LEFT JOIN 
Orders T2
ON T1.user_id = T2.buyer_id
AND LEFT(T2.order_date, 4) = '2019'
GROUP BY T1.user_id 
```
</br>  


## 182. Duplicate Emails   

Table: Person  

| Column Name | Type    |
| ------------- | ------- |
| id          | int     |
| email       | varchar |

id is the primary key column for this table.  
Each row of this table contains an email. The emails will not contain uppercase letters.  

Write an SQL query to report all the duplicate emails. Note that it's guaranteed that the email field is not NULL.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Person table:  

| id | email   |
| ------------- | ------- |
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |

Output:   

| Email   |
| ------- |
| a@b.com |


A:  
```
SELECT Email FROM Person
group by email
HAVING (COUNT(Email)>1);
```
*At first I did not include the GROUP BY clause and then the answer failed at one testcase.  
I look for the solution and realised I need to add GROUP BY email.  
According to the explantion: The GROUP BY clause groups the records by email, and the HAVING clause selects only those groups that have more than one record, which means that the email is duplicated.*


</br>

## 1050. Actors and Directors Who Cooperated at least 3 times  
Table: ActorDirector  
| Column Name | Type    |
| ------------- | ------- |
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |

timestamp is the primary key column for this table.  
Write a SQL query for a report that provides the pairs (actor_id, director_id) where the actor has cooperated with the director at least three times.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
ActorDirector table:  

| actor_id    | director_id | timestamp   |
| ------------- | ------- | ------- |
| 1           | 1           | 0           |
| 1           | 1           | 1           |
| 1           | 1           | 2           |
| 1           | 2           | 3           |
| 1           | 2           | 4           |
| 2           | 1           | 5           |
| 2           | 1           | 6           |

Output:   

| actor_id    | director_id |
| ------------- | ------- |
| 1           | 1           |

A:  
```
select
    actor_id,
    director_id
from
    actordirector
group by
    actor_id, director_id
having
    count(*) > 2;
```
*At first I used COUNT(CONCAT(actor_id, director_id)) as I thought they must be a pair hence adding CONCAT(), then it failed one testcase where the ids were 2,19 and 21,9 which concatenating them would get the same result hence its wrong.*  


</br>

## 	1587. Bank Account Summary II  

Table: Users, Transactions  

| Column Name  | Type    |
| ------------- | ------- |
| account      | int     |
| name         | varchar |

account is the primary key for this table.  
Each row of this table contains the account number of each user in the bank.  
There will be no two users having the same name in the table.  

| Column Name   | Type    |
| ------------- | ------- |
| trans_id      | int     |
| account       | int     |
| amount        | int     |
| transacted_on | date    |

trans_id is the primary key for this table.   
Each row of this table contains all changes made to all accounts.  
amount is positive if the user received money and negative if they transferred money.  
All accounts start with a balance of 0.  
Write an SQL query to report the name and balance of users with a balance higher than 10000.  
The balance of an account is equal to the sum of the amounts of all transactions involving that account.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Users table:  
| account    | name         |
| ------------- | ------- |
| 900001     | Alice        |
| 900002     | Bob          |
| 900003     | Charlie      |

Transactions table:  

| trans_id   | account    | amount     | transacted_on |
| ------------- | ------- | ------------- | ------- |
| 1          | 900001     | 7000       |  2020-08-01   |
| 2          | 900001     | 7000       |  2020-09-01   |
| 3          | 900001     | -3000      |  2020-09-02   |
| 4          | 900002     | 1000       |  2020-09-12   |
| 5          | 900003     | 6000       |  2020-08-07   |
| 6          | 900003     | 6000       |  2020-09-07   |
| 7          | 900003     | -4000      |  2020-09-11   |

Output: 

| name       | balance    |
| ------------- | ------- |
| Alice      | 11000      |

A:  
```
select
    a.name,
    sum(amount) as balance
from
    users a left join transactions b
on
    a.account = b.account
group by
    a.name
having
    balance > 10000;
```

</br>

## 	1084. Sales Analysis III  

Table: Product, Sales   

| Column Name  | Type    |
| ------------- | ------- |
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |

product_id is the primary key of this table.  
Each row of this table indicates the name and the price of each product.  

| Column Name | Type    |
| ------------- | ------- |
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |

This table has no primary key, it can have repeated rows.  
product_id is a foreign key to the Product table.  
Each row of this table contains some information about one sale.  
Write an SQL query that reports the products that were only sold in the first quarter of 2019. That is, between 2019-01-01 and 2019-03-31 inclusive.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Product table:  

| product_id | product_name | unit_price |
| ------------- | ------- | ------- |
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
  
Sales table:  

| seller_id | product_id | buyer_id | sale_date  | quantity | price |
| ------------- | ------- | ------------- | ------- | ------------- | ------- |
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |

Output:   

| product_id  | product_name |
| ------------- | ------- |
| 1           | S8           |

A:  
*my original answer failed at testcase 4 but I still havent figure the reason*
```
select
    a.product_id,
    b.product_name
from
    sales a left join product b
on
    a.product_id = b.product_id
where
    sale_date < '2019-04-01'
    and a.product_id not in (select product_id from sales where sale_date > '2019-03-31') ;
```
*Then I refer to other solutions and find out using MIN() and MAX() function can get the solution in a clean and neat way.*

```
select
    a.product_id,
    b.product_name
from
    sales a left join product b
on
    a.product_id = b.product_id
group by
    a.product_id
having
    min(a.sale_date) >= '2019-01-01' and max(a.sale_date) <= '2019-03-31';
```

</br>

## 	177. Nth Highest Salary(Medium)  
Table: Employee  

| Column Name | Type |
| ------------- | ------- |
| id          | int  |
| salary      | int  |

id is the primary key column for this table.  
Each row of this table contains information about the salary of an employee.  

Write an SQL query to report the nth highest salary from the Employee table. If there is no nth highest salary, the query should report null.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Employee table:  

| id | salary |
| ------------- | ------- |
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

n = 2  
Output:   

| getNthHighestSalary(2) |
| --------------------- |
| 200                    |

A:  
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
set n=n-1;
  RETURN (
      # Write your MySQL query statement below.
      ifnull((select distinct salary from employee
      order by salary desc
      limit 1 offset n),null)
  );
END
```
*This is my first time trying a function SQL question like this. It is the same question as the highest salary question before it was just in a function.  
I tried to figure out the logic and refer to the solution of others. By using LIMIT and OFFSET to tackle this problem.*  

</br>

## 	178. Rank Scores (Medium)  
Table: Scores  

| Column Name | Type    |
| ------------- | ------- |
| id          | int     |
| score       | decimal |

id is the primary key for this table.  
Each row of this table contains the score of a game. Score is a floating point value with two decimal places.  
Write an SQL query to rank the scores. The ranking should be calculated according to the following rules:  
	• The scores should be ranked from the highest to the lowest.  
	• If there is a tie between two scores, both should have the same ranking.  
	• After a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no holes between ranks.  
Return the result table ordered by score in descending order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Scores table:  

| id | score |
| ------------- | ------- |
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |

Output:   

| score | rank |
| ------------- | ------- |
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |

A:  
```
SELECT
score,
DENSE_RANK() OVER (ORDER BY score DESC) AS 'rank'
FROM Scores
ORDER BY score DESC;
```
*This question is easy if you know how to use DENSE_RANK(), refer to this site to learn more about RANK() related functions* 
https://www.javatpoint.com/mysql-ranking-functions

</br>

## 180. Consecutive Numbers (Medium)  

Table: logs  

| Column Name | Type    |
| ------------- | ------- |
| id          | int     |
| num         | varchar |

id is the primary key for this table.  
id is an autoincrement column.  
Write an SQL query to find all numbers that appear at least three times consecutively.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Logs table:  

| id | num |
| ------------- | ------- |
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |

Output:   

| ConsecutiveNums |
| ------------ |
| 1               |

A:  
```
select
   distinct l1.num 
 from logs l1,logs l2,logs l3
 where 
   l1.id = l2.id+1 
   and l2.Id=l3.Id+1
   and l1.Num=l2.Num 
   and l2.Num=l3.Num;
```
```
  select distinct t.num as ConsecutiveNums
from (
    select
        lag(num) over (order by id) as prev_num,
        num,
        lead(num) over (order by id) as next_num
    from Logs
) t
where t.num = t.prev_num and t.num = t.next_num;
```
*I did not know how to tackle this question at all hence I looked into the solutions. Now I know if we have to compare numbers of different rows in a table, we can do SELF JOIN to do that.*
*The first solution JOIN tables for three times, on the increasing ids. And then compare the num.*
*The second solution requires us to understand LAG() and LEAD() functions: 
The LAG() function is used to get value from row that precedes the current row.
The LEAD() function is used to get value from row that succeeds the current row.
Then compare the nums.
*


</br>

## 181.Employees Earning more than their Managers  

Table: Employee  

| Column Name | Type    |
| ------------- | ------- |
| id          | int     |
| name        | varchar |
| salary      | int     |
| managerId   | int     |

id is the primary key column for this table.  
Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.  
Write an SQL query to find the employees who earn more than their managers.  
Return the result table in any order.  
The query result format is in the following example.  
 
Example 1:  
Input:   
Employee table:  

| id | name  | salary | managerId |
| ------------- | ------- | ------------- | ------- |
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | Null      |
| 4  | Max   | 90000  | Null      |

Output: 

| Employee |
| ---------------- |
| Joe      |


A:  
```
select
    b.name as employee
from
    employee a join employee b
on
    a.id = b.managerid
where
    b.salary > a.salary;
```



